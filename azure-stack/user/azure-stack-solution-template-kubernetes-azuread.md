---
title: Implementación de Kubernetes en Azure Stack Hub con Azure Active Directory (Azure AD)
description: Obtenga información sobre cómo implementar Kubernetes en Azure Stack Hub con Azure Active Directory (Azure AD).
author: mattbriggs
ms.topic: article
ms.date: 07/24/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: 60a0c26fd6be2399be5f00a8d5bdc3b989e0b441
ms.sourcegitcommit: a5d3cbe1a10c2a63de95b9e72391dd83473ee299
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2020
ms.locfileid: "88920651"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-azure-active-directory"></a>Implementación de Kubernetes en Azure Stack Hub con Azure Active Directory

> [!NOTE]  
> Use únicamente el elemento Kubernetes de Marketplace de Azure Stack para implementar clústeres como prueba de concepto. Para los clústeres de Kubernetes admitidos en Azure Stack, utilice el [motor de AKS](azure-stack-kubernetes-aks-engine-overview.md).

Puede seguir los pasos descritos en este artículo para implementar y configurar los recursos de Kubernetes cuando use Azure Active Directory (Azure AD) como servicio de administración de identidad en una sola operación coordinada.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, asegúrese de tener los permisos adecuados y de que la instancia de Azure Stack Hub está lista.

1. Compruebe que puede crear aplicaciones en el inquilino de Azure Active Directory (Azure AD). Necesitará estos permisos para la implementación de Kubernetes.

    Para obtener instrucciones sobre cómo comprobar sus permisos, consulte [Comprobación de los permisos de Azure Active Directory](/azure/azure-resource-manager/resource-group-create-service-principal-portal).

1. Genere un par de claves SSH pública y privada para iniciar sesión en la máquina virtual de Linux en Azure Stack Hub. Necesitará la clave pública al crear el clúster.

    Para obtener instrucciones sobre cómo generar una clave, consulte [SSH Key Generation](azure-stack-dev-start-howto-ssh-public-key.md) (Generación de claves SSH).

1. Compruebe que tiene una suscripción válida en el portal del inquilino de Azure Stack Hub y que tiene suficientes direcciones IP públicas disponibles para agregar nuevas aplicaciones.

    No se puede implementar el clúster en una suscripción de **Administrador** de Azure Stack Hub. Debe usar una suscripción de **Usuario**. 

1. Si no tiene un clúster de Kubernetes en Marketplace, póngase en contacto con su administrador de Azure Stack Hub.

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

Cree una entidad de servicio en Azure. La entidad de servicio permite que la aplicación acceda a los recursos de Azure Stack Hub.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) global.

1. Compruebe que inició sesión con el inquilino de Azure AD asociado con la instancia de Azure Stack Hub. Para cambiar el inicio de sesión, haga clic en el icono de filtro de la barra de herramientas de Azure.

    ![Seleccionar el inquilino de AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Crear una aplicación de Azure.

    a. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).  
    b. Haga clic en **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro**.  
    c. Proporcione un nombre y una dirección URL para la aplicación.  
    d. Seleccione los **tipos de cuenta admitidos**.  
    e.  Agregue `http://localhost` como identificador URI de la aplicación. Seleccione **Web** para indicar el tipo de aplicación que quiere crear. Después de configurar los valores, seleccione **Registrar**.

1. Anote el **Id. de la aplicación**. Necesitará el identificador al crear el clúster. Se hace referencia al identificador como **Id. de cliente de entidad de servicio**.

1. En la hoja de la entidad de servicio, seleccione **Nuevo secreto de cliente** . **Configuración** > **Claves**. Necesita generar una clave de autenticación para la entidad de servicio.

    a. Escriba la **Descripción**.

    b. Seleccione **Never expires** (Nunca expira) para **Expira**.

    c. Seleccione **Agregar**. Tome nota de la cadena de clave. Necesitará la cadena de clave al crear el clúster. Se hace referencia a la clave como **Secreto de cliente de la entidad de servicio**.

## <a name="give-the-service-principal-access"></a>Dar acceso a la entidad de servicio

Dé a la entidad de servicio acceso a su suscripción para que la entidad pueda crear recursos.

1.  Inicie sesión en el portal de Azure Stack Hub `https://portal.local.azurestack.external/`.

1. Seleccione **Todos los servicios**  > **Suscripciones**.

1. Seleccione la suscripción creada por el operador para usar el clúster de Kubernetes.

1. Seleccione **Control de acceso (IAM)** > **Agregar asignación de roles**.

1. Seleccione el rol **Colaborador**.

1. Seleccione el nombre de aplicación creado para la entidad de servicio. Puede que tenga que escribir el nombre en el cuadro de búsqueda.

1. Haga clic en **Save**(Guardar).

## <a name="deploy-kubernetes"></a>Implementación de Kubernetes

1. Abra el portal de Azure Stack Hub `https://portal.local.azurestack.external`.

1. Seleccione **+ Crear un recurso** > **Proceso** > **Clúster de Kubernetes**. Haga clic en **Crear**.

    ![Implementar plantilla de solución](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Aspectos básicos

1. Seleccione **Aspectos básicos** en Crear un clúster de Kubernetes.

    ![Implementar plantilla de solución](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Seleccione el identificador de **suscripción**.

1. Escriba el nombre del nuevo grupo de recursos o seleccione uno existente. El nombre del recurso debe ser alfanumérico y estar en minúsculas.

1. Seleccione la **ubicación** del grupo de recursos. Esta es la región que ha elegido para la instalación de Azure Stack Hub.

### <a name="2-kubernetes-cluster-settings"></a>2. Configuración de un clúster de Kubernetes

1. Seleccione **Kubernetes Cluster Settings** (Configuración de clúster de Kubernetes) en Crear un clúster de Kubernetes.

    ![Implementar plantilla de solución](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Escriba el **nombre de usuario del administrador de la VM Linux**. Nombre de usuario de las máquinas virtuales de Linux que forman parte del clúster de Kubernetes y DVM.

1. Escriba la **clave pública SSH** utilizada para la autorización en todas las máquinas Linux creadas como parte del clúster de Kubernetes y DVM.

1. Escriba el **prefijo del DNS del perfil maestro** que es único para la región. Debe ser un nombre único para la región, como `k8s-12345`. Como procedimiento recomendado, intente elegirlo de modo que sea el mismo que el nombre del grupo de recursos.

    > [!NOTE]  
    > Para cada clúster, use un prefijo de DNS del perfil principal nuevo y único.

1. Seleccione la opción **Kubernetes master pool profile count** ( Recuento de perfiles del grupo maestro de Kubernetes). El recuento contiene el número de nodos en el grupo maestro. Puede haber entre 1 y 7. Este valor debe ser un número impar.

1. Seleccione **The VMSize of the Kubernetes master VMs** (VMSize de las máquinas virtuales maestras de Kubernetes). Así se especifica el tamaño de VM de las máquinas virtuales del maestro de Kubernetes. 

1. Seleccione la opción **Kubernetes node pool profile count** ( Recuento de perfiles del grupo de nodos de Kubernetes). El recuento contiene el número de agentes en el clúster. 

1. Seleccione **The VMSize of the Kubernetes node VMs** (VMSize de las VM del nodo de Kubernetes). Así se especifica el tamaño de máquina virtual de las máquinas virtuales del nodo de Kubernetes. 

1. Seleccione **Azure AD** como **sistema de identidad de Azure Stack Hub** para la instalación de Azure Stack.

1. Escriba el **id. de cliente de la entidad de servicio**. El proveedor de nube de Azure Kubernetes usa este identificador. El identificador de cliente indicado como identificador de la aplicación cuando el administrador de Azure Stack Hub creó la entidad de servicio.

1. Escriba el **Secreto de cliente de la entidad de servicio**. Este es el secreto de cliente que configuró al crear el servicio.

1. Escriba la **Versión de Kubernetes**. Esta es la versión para el proveedor de Kubernetes Azure. Azure Stack Hub publica una compilación de Kubernetes personalizada para cada versión de Azure Stack Hub.

### <a name="3-summary"></a>3. Resumen

1. Seleccione Summary (Resumen). La hoja muestra un mensaje de validación de la configuración del clúster de Kubernetes.

    ![Implementar plantilla de solución](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Revise la configuración.

3. Seleccione **Ok** (Aceptar) para implementar el clúster.

> [!TIP]  
>  Si tiene preguntas acerca de la implementación, puede publicarlas o ver si alguien ya ha respondido a las mismas preguntas en el [foro de Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>Pasos siguientes

[Conexión al clúster](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Habilitación del panel de Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)
