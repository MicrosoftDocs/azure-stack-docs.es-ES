---
title: Eliminación de los datos de usuario del portal a petición desde Azure Stack Hub.
description: Como operador de Azure Stack Hub, aprende a borrar los datos de usuario del portal cuando lo soliciten los usuarios de Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: bede8fc72cfcdd6f3376c49a87293fef3626c380
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698341"
---
# <a name="clear-portal-user-data-from-azure-stack-hub"></a>Eliminación de los datos de usuario del portal de Azure Stack Hub

Los operadores de Azure Stack Hub pueden borrar los datos de usuario del portal a petición si los usuarios de Azure Stack Hub lo solicitan. Como usuario de Azure Stack Hub, el portal se puede personalizar mediante el anclaje de los iconos y el cambio del diseño del panel. Los usuarios también pueden cambiar el tema y ajustar el idioma predeterminado para que coincida con las preferencias personales.

Los datos de usuario del portal incluyen favoritos y recursos a los que se ha accedido recientemente en el portal de usuarios de Azure Stack Hub. En este artículo se describe cómo borrar los datos de usuario del portal.

La eliminación de la configuración de usuario del portal solo debe realizarse una vez eliminada la suscripción de usuario.

> [!NOTE]
> Todavía puede haber algunos datos de usuario en la sección del sistema de los registros de eventos después de seguir las instrucciones de este artículo. Estos datos pueden permanecer durante varios días hasta que los registros se reviertan automáticamente.

## <a name="requirements"></a>Requisitos

- [Instale PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).
- [Descargue las herramientas de Azure Stack Hub](azure-stack-powershell-download.md) desde GitHub.
- La cuenta de usuario todavía debe existir en el directorio.
- Las credenciales de administrador de Azure Stack Hub para acceder al punto de conexión de Resource Manager de administración.

> [!NOTE]
> Si intenta eliminar información de usuario del portal de un usuario que se ha invitado desde un directorio invitado (multiinquilino), debe tener permiso de lectura en ese directorio. Para más información, consulte el [escenario CSP más adelante en este artículo](#clear-portal-user-data-in-guest-directory).

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>Eliminación de los datos de usuario del portal mediante un nombre principal de usuario

En este escenario se supone que la suscripción de proveedor predeterminada y el usuario forman parte del mismo directorio o que tiene acceso de lectura al directorio en el que reside el usuario.

Asegúrese de [descargar la versión más reciente de las herramientas de Azure Stack Hub](azure-stack-powershell-download.md) de GitHub antes de continuar.

Para este procedimiento, use un equipo que pueda comunicarse con el punto de conexión de Resource Manager de administración de Azure Stack Hub.

1. Abra una sesión de Windows PowerShell con privilegios elevados (ejecutar como administrador), desplácese a la carpeta raíz del directorio **AzureStack-Tools-master** e importe el módulo de PowerShell necesario:

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Ejecute los comandos siguientes: Asegúrese de sustituir los marcadores de posición por valores que se adapten a su entorno:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $azureStackDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"

   ## Replace the following value with the user directory tenant ID.
   $userDirectoryTenantId = " 7ddf3648-9671-47fd-b63d-eecd82ed040e"

   ## Replace the following value with name of the user principal whose portal user data is to be cleared.
   $userPrincipalName = "myaccount@contoso.onmicrosoft.com"

   Clear-AzsUserDataWithUserPrincipalName -AzsAdminArmEndpoint $adminARMEndpoint `
    -AzsAdminDirectoryTenantId $azureStackDirectoryTenantId `
    -UserPrincipalName $userPrincipalName `
    -DirectoryTenantId $userDirectoryTenantId
   ```

   > [!NOTE]
   > `azureStackDirectoryTenantId` es opcional. Si no especifica este valor, el script busca el nombre principal de usuario en todos los directorios de inquilinos registrados en Azure Stack Hub y, a continuación, borra los datos del portal de todos los usuarios coincidentes.

## <a name="clear-portal-user-data-in-guest-directory"></a>Eliminación de los datos de usuario del portal en el directorio invitado

En este escenario, el operador de Azure Stack Hub no tiene acceso al directorio invitado en el que reside el usuario. Este es un escenario común cuando se trata de un proveedor de soluciones en la nube (CSP).

Para que un operador de Azure Stack Hub quite los datos de usuario del portal, se requiere como mínimo el identificador de objeto de usuario.

El usuario debe consultar el identificador de objeto y proporcionarlo al operador de Azure Stack Hub. El operador no tiene acceso al directorio en el que reside el usuario.

### <a name="user-retrieves-the-user-object-id"></a>El usuario recupera el identificador de objeto de usuario.

1. Abra una sesión de Windows PowerShell con privilegios elevados (ejecutar como administrador), desplácese a la carpeta raíz del directorio **AzureStack-Tools-master** e importe el módulo de PowerShell necesario.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Ejecute los comandos siguientes: Asegúrese de sustituir los marcadores de posición por valores coincidentes con su entorno.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $userARMEndpoint = "https://management.local.azurestack.external"

   ## Replace the following value with the directory tenant ID, which contains the user account.
   $userDirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userPrincipleName = "myaccount@contoso.onmicrosoft.com"

   Get-UserObjectId -DirectoryTenantId $userDirectoryTenantId `
    -AzsArmEndpoint $userARMEndpoint `
    -UserPricinpalName $userPrincipleName
   ```

   > [!NOTE]
   > Como usuario, debe proporcionar el identificador de objeto de usuario, que es la salida del script anterior, al operador de Azure Stack Hub.

## <a name="azure-stack-hub-operator-removes-the-portal-user-data"></a>El operador de Azure Stack Hub quita los datos de usuario del portal.

Después de recibir el identificador de objeto de usuario como un operador de Azure Stack Hub, ejecute los siguientes comandos para quitar los datos de usuario del portal:

1. Abra una sesión de Windows PowerShell con privilegios elevados (ejecutar como administrador), desplácese a la carpeta raíz del directorio **AzureStack-Tools-master** e importe el módulo de PowerShell necesario.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Ejecute los siguientes comandos, asegurándose de ajustar el parámetro para que coincida con el entorno:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $AzsAdminDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"
   
   ## Replace the following value with the directory tenant ID of the user to clear.
   $DirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userObjectID = "s-1-*******"
   Clear-AzsUserDataWithUserObject -AzsAdminArmEndpoint $AzsAdminARMEndpoint `
    -AzsAdminDirectoryTenantId $AzsAdminDirectoryTenantId `
    -DirectoryTenantID $DirectoryTenantId `
    -UserObjectID $userObjectID `
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Regístrese en Azure Stack Hub con Azure](azure-stack-registration.md) y rellene [Marketplace de Azure Stack Hub](azure-stack-marketplace.md) con los elementos que va a ofrecer a los usuarios.
