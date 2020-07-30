---
title: Implementación de Kubernetes en Azure Stack Hub con los Servicios de federación de Active Directory (AD FS)
description: Obtenga información sobre cómo implementar Kubernetes en Azure Stack Hub con los Servicios de federación de Active Directory (AD FS).
author: mattbriggs
ms.topic: article
ms.date: 07/24/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: 45395524fa921560f6c2b0fef94b26b47595e957
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2020
ms.locfileid: "87251003"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-active-directory-federated-services"></a>Implementación de Kubernetes en Azure Stack Hub con los Servicios de federación de Active Directory

> [!Note]  
> Use únicamente el elemento Kubernetes de Marketplace de Azure Stack para implementar clústeres como prueba de concepto. Para los clústeres de Kubernetes admitidos en Azure Stack, utilice el [motor de AKS](azure-stack-kubernetes-aks-engine-overview.md).

Puede seguir los pasos descritos en este artículo para implementar y configurar los recursos de Kubernetes. Siga estos pasos cuando los Servicios de federación de Active Directory (AD FS) sean su servicio de administración de identidad.

## <a name="prerequisites"></a>Requisitos previos 

Para empezar, asegúrese de tener los permisos adecuados y de que la instancia de Azure Stack Hub está lista.

1. Genere un par de claves SSH pública y privada para iniciar sesión en la máquina virtual de Linux en Azure Stack Hub. Necesita la clave pública al crear el clúster.

    Para obtener instrucciones sobre cómo generar una clave, consulte [SSH Key Generation](azure-stack-dev-start-howto-ssh-public-key.md) (Generación de claves SSH).

1. Compruebe que tiene una suscripción válida en el portal del inquilino de Azure Stack Hub y que tiene suficientes direcciones IP públicas disponibles para agregar nuevas aplicaciones.

    No se puede implementar el clúster en una suscripción de **Administrador** de Azure Stack Hub. Debe usar una suscripción de **Usuario**. 

1. Si no tiene un clúster de Kubernetes en Marketplace, póngase en contacto con su administrador de Azure Stack Hub.

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

Deberá trabajar con el administrador de Azure Stack Hub para que configure la entidad de servicio cuando se use AD FS como solución de identidad. La entidad de servicio permite que la aplicación acceda a los recursos de Azure Stack Hub.

1. El administrador de Azure Stack Hub le proporciona información de la entidad de servicio. La información de la entidad de servicio debe ser similar a esto:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. Asigne un rol de colaborador en su suscripción a la nueva entidad de servicio. Para obtener instrucciones, consulte la sección sobre cómo [asignar un rol](../operator/azure-stack-add-users-adfs.md).

## <a name="deploy-kubernetes"></a>Implementación de Kubernetes

1. Abra el portal de Azure Stack Hub `https://portal.local.azurestack.external`.

1. Seleccione **+ Crear un recurso** > **Proceso** > **Clúster de Kubernetes**. Seleccione **Crear**.

    ![Implementar plantilla de solución](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Aspectos básicos

1. Seleccione **Aspectos básicos** en Crear un clúster de Kubernetes.

    ![Implementar plantilla de solución](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Seleccione el identificador de **suscripción**.

1. Escriba el nombre del nuevo grupo de recursos o seleccione uno existente. El nombre del recurso debe ser alfanumérico y estar en minúsculas.

1. Seleccione la **ubicación** del grupo de recursos. Esta es la región que ha elegido para la instalación de Azure Stack Hub.

### <a name="2-kubernetes-cluster-settings"></a>2. Configuración de un clúster de Kubernetes

1. Seleccione **Kubernetes Cluster Settings** (Configuración de clúster de Kubernetes) en Crear un clúster de Kubernetes.

    ![Implementar plantilla de solución](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Escriba el **nombre de usuario del administrador de la VM Linux**. Nombre de usuario de las máquinas virtuales de Linux que forman parte del clúster de Kubernetes y DVM.

1. Escriba la **clave pública SSH** utilizada para la autorización en todas las máquinas Linux creadas como parte del clúster de Kubernetes y DVM.

1. Escriba el **prefijo del DNS del perfil maestro** que es único para la región. Debe ser un nombre único para la región, como `k8s-12345`. Como procedimiento recomendado, intente elegirlo de modo que sea el mismo que el nombre del grupo de recursos.

    > [!Note]  
    > Para cada clúster, use un prefijo de DNS del perfil principal nuevo y único.

1. Seleccione la opción **Kubernetes master pool profile count** ( Recuento de perfiles del grupo maestro de Kubernetes). El recuento contiene el número de nodos en el grupo maestro. Puede haber entre 1 y 7. Este valor debe ser un número impar.

1. Seleccione **The VMSize of the Kubernetes master VMs** (VMSize de las máquinas virtuales maestras de Kubernetes).

1. Seleccione la opción **Kubernetes node pool profile count** ( Recuento de perfiles del grupo de nodos de Kubernetes). El recuento contiene el número de agentes en el clúster. 

1. Seleccione **The VMSize of the Kubernetes node VMs** (VMSize de las VM del nodo de Kubernetes). Así se especifica el tamaño de máquina virtual de las máquinas virtuales del nodo de Kubernetes. 

1. Seleccione **ADFS** como **sistema de identidad de Azure Stack Hub** para la instalación de Azure Stack Hub.

1. Escriba el **id. de cliente de la entidad de servicio**. El proveedor de nube de Azure Kubernetes usa este identificador. El identificador de cliente indicado como identificador de la aplicación cuando el administrador de Azure Stack Hub creó la entidad de servicio.

1. Escriba el **Secreto de cliente de la entidad de servicio**. Este es el secreto de cliente que se le proporcionó para la entidad de servicio de AD FS desde el Administrador de Azure Stack Hub.

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