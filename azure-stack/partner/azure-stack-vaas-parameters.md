---
title: Parámetros habituales de flujos de trabajos en VaaS
titleSuffix: Azure Stack Hub
description: Más información sobre los parámetros habituales de flujos de trabajo en la validación como servicio de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9a53f489ac1fdf92afdf8ba841b1cbb4a030680b
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625397"
---
# <a name="common-workflow-parameters-in-validation-as-a-service"></a>Parámetros habituales de flujos de trabajo en la validación como servicio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Los parámetros comunes incluyen valores como variables de entorno y las credenciales de usuario necesarias en todas las pruebas de validación como servicio (VaaS). Estos valores se definen en el nivel de flujo de trabajo al crear o cambiar un flujo de trabajo. Al programar las pruebas, estos valores se pasan como parámetros a cada prueba en el flujo de trabajo.

> [!NOTE]
> Cada prueba define su propio conjunto de parámetros. En el momento de la programación, la prueba puede requerir que escriba un valor con independencia de los parámetros comunes o puede permitirle invalidar el valor de los parámetros comunes.

## <a name="environment-parameters"></a>Parámetros del entorno

Los parámetros del entorno describen el entorno de Azure Stack Hub sometido a prueba. Estos valores deben proporcionarse mediante la generación y carga de un archivo de información de marca de Azure Stack Hub para la instancia que se está probando.

> [!NOTE]
> En los flujos de trabajo de validación oficial, no se pueden modificar los parámetros del entorno después de la creación del flujo de trabajo.

### <a name="generate-the-stamp-information-file"></a>Generación del archivo de información de marca

1. Inicie sesión en DVM o en cualquier máquina que tenga acceso al entorno de Azure Stack Hub.
2. Ejecute los siguientes comandos en una ventana de PowerShell con privilegios elevados:

    ```powershell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Ubicación de los valores en el archivo de configuración ECE

También se pueden encontrar manualmente los valores de parámetros de entorno en el **archivo de configuración ECE** ubicado en `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` en la DVM.

## <a name="test-parameters"></a>Parámetros de prueba

Los parámetros de prueba comunes incluyen información confidencial que no se puede almacenar en archivos de configuración. Estos parámetros se deben proporcionar manualmente.

Parámetro    | Descripción
-------------|-----------------
Usuario administrador de inquilinos                            | El administrador de inquilinos de Azure Active Directory (Azure AD) que ha aprovisionado el administrador de servicios en el directorio de AAD. Este usuario realiza acciones a nivel de inquilino, como la implementación de plantillas para configurar los recursos (máquinas virtuales, cuentas de almacenamiento, etc.) y la ejecución de cargas de trabajo. Para más información sobre el aprovisionamiento de cuentas de inquilino, consulte [Adición de un nuevo inquilino de Azure Stack Hub](../operator/azure-stack-add-new-user-aad.md).
Usuario administrador de servicios             | Administrador de Azure AD del inquilino del directorio de Azure AD especificado durante la implementación de Azure Stack Hub. Busque `AADTenant` en el archivo de configuración ECE y seleccione el valor en el elemento `UniqueName`.
Usuario del administrador de la nube               | Cuenta de administrador de dominio de Azure Stack Hub (por ejemplo, `contoso\cloudadmin`). Busque `User Role="CloudAdmin"` en el archivo de configuración ECE y seleccione el valor en el elemento `UserName`.
Cadena de conexión de diagnósticos          | Una URL de SAS a una cuenta de Azure Storage donde se copiarán los registro de diagnóstico durante la ejecución de pruebas. Para obtener instrucciones sobre la generación de la dirección URL de SAS, consulte [Generación de la cadena de conexión de diagnóstico](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> La **cadena de conexión de diagnóstico** debe ser válida antes de continuar.

### <a name="generate-the-diagnostics-connection-string"></a>Generación de la cadena de conexión de diagnóstico

La cadena de conexión de diagnóstico es necesaria para almacenar los registros de diagnóstico durante la ejecución de pruebas. Use la cuenta de Azure Storage que creó durante la configuración (consulte [Configuración de los recursos de validación como servicio](azure-stack-vaas-set-up-resources.md)) para crear una dirección URL de una firma de acceso compartido (SAS) para conceder acceso a VaaS para cargar los registros en la cuenta de almacenamiento.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Seleccione **Blob** en las **opciones de Servicios permitidos**. Anule la selección de las opciones restantes.

1. Seleccione **Servicio**, **Contenedor** y **Objeto** en **Tipos de recursos permitidos**.

1. Seleccione **Lectura**, **Escritura**, **Lista**, **Adición** y **Creación** en **Permisos permitidos** . Anule la selección de las opciones restantes.

1. En **Hora de inicio** especifique la hora actual y en **Hora de finalización** tres meses después de la hora actual.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> La dirección URL de SAS expira a la hora de finalización especificada al generar la dirección URL. Al programar las pruebas, asegúrese de que la dirección URL sea válida durante al menos 30 días y el tiempo necesario para la ejecución de pruebas (se recomiendan tres meses).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Conceptos clave de la validación como servicio](azure-stack-vaas-key-concepts.md)