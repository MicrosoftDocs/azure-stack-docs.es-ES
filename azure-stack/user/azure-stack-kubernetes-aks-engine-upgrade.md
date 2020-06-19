---
title: Actualización de un clúster de Kubernetes en Azure Stack Hub
description: Aprenda a actualizar un clúster de Kubernetes en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 4/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 4/23/2020
ms.openlocfilehash: 347d790a4e70e37e83be43019f25b875b7ff2302
ms.sourcegitcommit: f0ee2a3af78dd6d6e2806710681d52b763948967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2020
ms.locfileid: "84533729"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack-hub"></a>Actualización de un clúster de Kubernetes en Azure Stack Hub

## <a name="upgrade-a-cluster"></a>Actualizar un clúster

El motor de AKS permite actualizar el clúster que se implementó originalmente mediante la herramienta. Puede mantener los clústeres mediante el motor de AKS. Las tareas de mantenimiento son similares a cualquier sistema IaaS. Debe tener en cuenta la disponibilidad de nuevas actualizaciones y usar el motor de AKS para aplicarlas.

El comando de actualización actualiza la versión de Kubernetes y la imagen de sistema operativo base. Cada vez que se ejecuta el comando de actualización, para cada nodo del clúster, el motor de AKS crea una nueva máquina virtual con la imagen base de AKS asociada a la versión de **aks-engine**. Puede usar el comando `aks-engine upgrade` para mantener la actualización de cada nodo principal y de agente del clúster. 

Microsoft no administra el clúster. Sin embargo, Microsoft proporciona la herramienta y la imagen de máquina virtual que puede usar para administrar el clúster. 

Para una cobertura de actualizaciones de clúster implementada:

-   Kubernetes
-   Proveedor de Kubernetes en Azure Stack Hub
-   Sistema operativo base

Al actualizar un clúster de producción, tenga en cuenta lo siguiente:

-   ¿Está usando la especificación de clúster correcta (`apimodel.json`) y el grupo de recursos para el clúster de destino?
-   ¿Está usando una máquina confiable para que la máquina cliente ejecute el motor de AKS y desde el que realiza las operaciones de actualización?
-   Asegúrese de que tiene un clúster de copia de seguridad y que está operativo.
-   Si es posible, ejecute el comando desde una máquina virtual en el entorno de Azure Stack Hub para disminuir los saltos de red y los posibles errores de conectividad.
-   Asegúrese de que la suscripción tiene espacio suficiente para todo el proceso. El proceso asigna nuevas máquinas virtuales durante el proceso.
-   No se prevén actualizaciones del sistema ni tareas programadas.
-   Configure una actualización por fases en un clúster que esté configurado exactamente como el clúster de producción y pruebe la actualización allí antes de hacerlo en el clúster de producción.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Pasos para actualizar a una versión más reciente de Kubernetes

> [!Note]  
> La imagen de base de AKS también se actualiza si usa una versión más reciente de aks-engine y la imagen está disponible en Marketplace.

Las instrucciones siguientes usan los pasos mínimos para realizar la actualización. Si desea más detalles, consulte el artículo [Actualización de clústeres de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md).

1. Primero debe determinar las versiones a las que puede dirigirse para la actualización. Esta versión depende de la versión que tiene actualmente y, después, utilice ese valor de versión para realizar la actualización. Las versiones de Kubernetes compatibles con la actualización más reciente son 1.14.7 y 1.15.10. Consulte esta tabla para ver las actualizaciones disponibles:

| Versión actual | Actualización disponible |
| --- | --- |
|1.14.7 | 1.15.10 |
|1.14.8 | 1.15.10 |
|1.15.4 | 1.15.10 |
|1.15.5 | 1.15.10 |

Para más información sobre una asignación completa del motor de AKS, las versiones de imagen base de AKS y de Kubernetes, consulte [versiones de motor de AKS compatibles](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions).

2. Recopile la información que va a necesitar para ejecutar el comando `upgrade`. La actualización usa los parámetros siguientes:

    | Parámetro | Ejemplo | Descripción |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Para indicar al motor de AKS que la plataforma de destino es Azure Stack Hub, utilice `AzureStackCloud`. |
    | ubicación | local | El nombre de la región de la instancia de Azure Stack Hub. Para ASDK, la región se establece en `local`. |
    | resource-group | kube-rg | Escriba el nombre del nuevo grupo de recursos o seleccione uno existente. El nombre del recurso debe ser alfanumérico y estar en minúsculas. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | escriba el identificador de la suscripción. Para más información, consulte [Suscripción a una oferta](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |
    | api-model | ./kubernetes-azurestack.json | Ruta de acceso al archivo de configuración del clúster o modelo de API. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Escriba el GUID de la entidad de servicio. El identificador de cliente indicado como identificador de la aplicación cuando el administrador de Azure Stack Hub creó la entidad de servicio. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Escriba el secreto de la entidad de servicio. Este es el secreto de cliente que configuró al crear el servicio. |
    | identity-system | adfs | Opcional. Especifique la solución de administración de identidad si usa los Servicios de federación de Active Directory (AD FS). |

3. Con los valores en su lugar, ejecute el comando siguiente:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud \
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  Si, por cualquier motivo, la operación de actualización encuentra un error, puede volver a ejecutar el comando de actualización después de solucionar el problema. El motor de AKS reanudará la operación en la que se produjo el error la hora anterior.

## <a name="steps-to-only-upgrade-the-os-image"></a>Pasos para actualizar únicamente la imagen del sistema operativo

1. Revise [la tabla supported-kubernetes-versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions) y determine si tiene la versión de aks-engine y la imagen base de AKS que planea para su actualización. Para ver la versión de aks-engine, ejecute: `aks-engine version`.
2. Actualice el motor de AKS según corresponda, en la máquina en la que ha instalado aks-engine ejecute `./get-akse.sh --version vx.xx.x` y reemplace **x.xx.x** por la versión de destino.
3. Pida a su operador de Azure Stack Hub que agregue la versión de la imagen base de AKS que necesita en Marketplace de Azure Stack Hub.
4. Ejecute el comando `aks-engine upgrade` con la misma versión de Kubernetes que ya está usando, pero agregue `--force`. Puede ver un ejemplo en [Forzado de una actualización](#forcing-an-upgrade).


## <a name="forcing-an-upgrade"></a>Forzado de una actualización

Puede haber situaciones en las que desee forzar una actualización del clúster. Por ejemplo, el primer día se implementa un clúster en un entorno desconectado mediante la última versión de Kubernetes. El siguiente día Ubuntu publica una revisión para una vulnerabilidad para la que Microsoft genera una instancia de la **imagen base de AKS**. Puede aplicar la nueva imagen mediante el forzado de una actualización con la misma versión de Kubernetes que ya ha implementado.

```bash  
aks-engine upgrade \
--azure-env AzureStackCloud   
--location <for an ASDK is local> \
--resource-group kube-rg \
--subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--api-model kube-rg/apimodel.json \
--upgrade-version 1.13.5 \
--client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

Para obtener instrucciones, consulte [Forzado de actualización](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
- [Escalado de un clúster de Kubernetes en Azure Stack Hub](azure-stack-kubernetes-aks-engine-scale.md)