---
title: Configuración de Azure AD y recursos de almacenamiento para VaaS
titleSuffix: Azure Stack Hub
description: Aprenda a configurar Azure AD y los recursos de almacenamiento para la validación como servicio de Azure Stack Hub.
author: mattbriggs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 3dc72eb4dfac10e6e199b2cbfe9668666f83e122
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625312"
---
# <a name="set-up-azure-ad-and-storage-resources-for-validation-as-a-service"></a>Configuración de Azure AD y los recursos de almacenamiento para la validación como servicio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Validación como servicio (VaaS) es un servicio de Azure que sirve para validar y proporcionar respaldo a soluciones de Azure Stack Hub en el mercado. Siga este artículo antes de usar el servicio para validar la solución.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Prepárese usar VaaS; para ello, configure Azure Active Directory (AD).
> * Cree una cuenta de almacenamiento.

## <a name="configure-an-azure-ad-tenant"></a>Configurar un inquilino de Azure AD

Se usa un inquilino de Azure AD para registrar una organización y autenticar los usuarios mediante VaaS. El asociado usará las características de control de acceso basado en rol (RBAC) del inquilino para administrar qué usuarios de la organización del asociado pueden usar VaaS. Para más información, consulte [¿Qué es Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

### <a name="create-a-tenant"></a>Creación de un inquilino

Cree un inquilino para que la organización lo use para acceder a los servicios de VaaS. Use un nombre descriptivo, como `ContosoVaaS@onmicrosoft.com`.

1. Cree un inquilino de Azure AD en [Azure Portal](https://portal.azure.com) o use un inquilino existente. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Agregue miembros de la organización al inquilino. Estos usuarios serán responsables de usar el servicio para ver o programar las pruebas. Una vez que termine el registro, definirá los niveles de acceso de los usuarios.

    Autorice a los usuarios de su inquilino para ejecutar acciones en VaaS mediante la asignación de uno de los roles siguientes:

    | Nombre de rol | Descripción |
    |---------------------|------------------------------------------|
    | Propietario | Tiene acceso total a todos los recursos. |
    | Lector | Puede ver todos los recursos, pero no crearlos ni administrarlos. |
    | Colaborador en las pruebas | Puede crear y administrar recursos de prueba. |

    Para asignar roles en la aplicación **Azure Stack Hub Validation Service**:

   1. Inicie sesión en [Azure Portal](https://portal.azure.com).
   2. Seleccione **Todos los servicios** > **Azure Active Directory** en la sección **Identidad**.
   3. Seleccione la aplicación **Aplicaciones empresariales** > **Azure Stack Hub Validation Service**.
   4. Seleccione **Usuarios y grupos**. En la hoja **Azure Stack Hub Validation Service: Usuarios y grupos** se enumeran los usuarios con permiso para usar la aplicación.
   5. Seleccione **+ Agregar usuario** para agregar un usuario del inquilino y asignar un rol.

      Si quiere aislar recursos y acciones de la validación como servicio entre diferentes grupos dentro de una organización, puede crear varios directorios de inquilino de Azure AD.

### <a name="register-your-tenant"></a>Registro del inquilino

Este proceso le otorga al inquilino autorización para la aplicación **Azure Stack Hub Validation Service** de Azure AD.

1. Envíe la siguiente información acerca del inquilino a Microsoft, a la dirección de correo electrónico [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

    | data | Descripción |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nombre de la organización | Nombre oficial de la organización. |
    | Nombre del directorio del inquilino de Azure AD | Nombre del directorio de inquilino de Azure AD que se registra. |
    | Identificador del directorio de inquilino de Azure AD | GUID del directorio de inquilino de Azure AD asociado con el directorio. Para obtener información acerca de cómo encontrar el identificador del directorio de inquilino de Azure AD, consulte [Get tenant ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-values-for-signing-in) (Obtención del identificador de inquilino). |

2. Espere la confirmación del equipo de Azure Stack Hub Validation para comprobar que el inquilino puede usar el portal de este servicio.

### <a name="consent-to-the-vaas-app"></a>Consentimiento a la aplicación de validación como servicio

Como administrador de Azure AD, proporcione a la aplicación VaaS de Azure AD los permisos necesarios en nombre de su inquilino:

1. Use las credenciales de administrador global del inquilino para iniciar sesión en el [portal de Azure Stack Hub Validation](https://azurestackvalidation.com/).

2. Seleccione **Mi cuenta**.

3 Cuando se le pida que conceda a VaaS los permisos de Azure AD enumerados, acepte los términos.

## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

Durante la ejecución de prueba, VaaS genera registros de diagnóstico en una cuenta de almacenamiento de Azure. Además de los registros de prueba, la cuenta de almacenamiento también puede usarse para cargar los paquetes de extensión OEM para el flujo de trabajo de validación de paquetes.

La cuenta de almacenamiento de Azure se hospeda en la nube pública de Azure, no en el entorno de Azure Stack Hub.

1. En Azure Portal, seleccione **Todos los servicios** > **Almacenamiento** > **Cuentas de almacenamiento**. En la hoja **Cuentas de almacenamiento**, seleccione **Agregar**.

2. Seleccione la suscripción en la que se va a crear la cuenta de almacenamiento.

3. En **Grupo de recursos**, seleccione **Crear nuevo**. Escriba un nombre para el nuevo grupo de recursos.

4. Revise las [convenciones de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#storage) para las cuentas Azure Storage. Escriba un nombre para la cuenta de almacenamiento.

5. Seleccione la región **Oeste de EE. UU.** para la cuenta de almacenamiento.

    Para asegurarse de no incurrir en cargos de red por el almacenamiento de registros, la cuenta de Azure Storage se puede configurar para que use únicamente la región **Oeste de EE. UU**. Las características de replicación de datos y nivel de acceso frecuente no son necesarias para estos datos. La habilitación de cualquiera de estas características aumentará los costos considerablemente.

6. Deje la configuración en los valores predeterminados, excepto **Tipo de cuenta**:

    - De manera predeterminada, el campo **Modelo de implementación** se establece en **Resource Manager**.
    - El campo **Rendimiento** se establece en **Estándar** de forma predeterminada.
    - En el campo **Tipo de cuenta**, seleccione **Almacenamiento de blobs**.
    - De manera predeterminada, el campo **Replicación** se establece en **Almacenamiento con redundancia local (LRS)** .
    - El **nivel de acceso** se establece en **Frecuente** de forma predeterminada.

7. Seleccione **Revisar y crear** para revisar la configuración de la cuenta de almacenamiento y crear la cuenta.

## <a name="next-steps"></a>Pasos siguientes

Si su entorno no permite conexiones entrantes, siga el tutorial de implementación del agente local para ejecutar una prueba en su hardware.

> [!div class="nextstepaction"]
> [Implementación del agente local](azure-stack-vaas-local-agent.md)
