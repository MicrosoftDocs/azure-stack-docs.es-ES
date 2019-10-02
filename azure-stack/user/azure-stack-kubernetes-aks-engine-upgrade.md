---
title: Implementación de un clúster de Kubernetes en Azure Stack | Microsoft Docs
description: Aprenda a actualizar un clúster de Kubernetes en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/25/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: 377857019e6a4d55e6a9372296817e1776c081c9
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2019
ms.locfileid: "71279164"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack"></a>Actualización de un clúster de Kubernetes en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="upgrade-a-cluster"></a>Actualización de un clúster

AKS-Engine permite actualizar el clúster que se implementó originalmente mediante la herramienta. Puede mantener los clústeres mediante AKS-Engine. Las tareas de mantenimiento son similares a cualquier sistema IaaS. Debe tener en cuenta la disponibilidad de nuevas actualizaciones y usar AKS-Engine para aplicarlas.

Microsoft no administra el clúster.

Para una cobertura de actualizaciones de clúster implementada:

-   kubernetes
-   Proveedor de Kubernetes en Azure Stack
-   Sistema operativo base

Al actualizar un clúster de producción, tenga en cuenta lo siguiente:

-   ¿Está usando la especificación de clúster correcta (`apimodel.json`) y el grupo de recursos para el clúster de destino?
-   ¿Está usando una máquina confiable para que la máquina cliente ejecute AKS-Engine y desde el que realiza las operaciones de actualización?
-   Asegúrese de que tiene un clúster de copia de seguridad y que está operativo.
-   Si es posible, ejecute el comando desde una máquina virtual en el entorno de Azure Stack para disminuir los saltos de red y los posibles errores de conectividad.
-   Asegúrese de que la suscripción tiene espacio suficiente para todo el proceso. El proceso asigna nuevas máquinas virtuales durante el proceso.
-   No se prevén actualizaciones del sistema ni tareas programadas.
-   Configure una actualización por fases en un clúster que esté configurado exactamente como el clúster de producción y pruebe la actualización allí antes de hacerlo en el clúster de producción.

## <a name="steps-to-upgrade"></a>Pasos para actualizar

1. Siga las instrucciones del artículo [Actualización de clústeres de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md). 
2. Primero debe determinar las versiones a las que puede dirigirse para la actualización. Esta versión depende de la versión que tiene actualmente y, después, utilice ese valor de versión para realizar la actualización.

    Ejecute los comandos siguientes:

    ```bash  
    $ aks-engine get-versions
    Version Upgrades
    1.15.0
    1.14.3  1.15.0
    1.14.1  1.14.3, 1.15.0
    1.13.7  1.14.1, 1.14.3
    1.13.5  1.13.7, 1.14.1, 1.14.3
    1.12.8  1.13.5, 1.13.7
    1.12.7  1.12.8, 1.13.5, 1.13.7
    1.11.10 1.12.7, 1.12.8
    1.11.9  1.11.10, 1.12.7, 1.12.8
    1.10.13 1.11.9, 1.11.10
    1.10.12 1.10.13, 1.11.9, 1.11.10
    1.9.11  1.10.12, 1.10.13
    1.9.10  1.9.11, 1.10.12, 1.10.13
    1.6.9   1.9.10, 1.9.11
    ```

    Por ejemplo, según la salida del comando `get-versions`, si la versión actual de Kubernetes es "1.13.5", puede actualizar a "1.13.7, 1.14.1, 1.14.3".

3. Recopile la información que va a necesitar para ejecutar el comando `upgrade`. La actualización usa los parámetros siguientes:

    | Parámetro | Ejemplo | DESCRIPCIÓN |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Para indicar a AKS-Engine que la plataforma de destino es Azure Stack, utilice `AzureStackCloud`. |
    | location | local | El nombre de la región de la instancia de Azure Stack. Para ASDK, la región se establece en `local`. |
    | resource-group | kube-rg | Escriba el nombre del nuevo grupo de recursos o seleccione uno existente. El nombre del recurso debe ser alfanumérico y estar en minúsculas. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | escriba el identificador de la suscripción. Para más información, consulte [Suscripción a una oferta](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |
    | api-model | ./kubernetes-azurestack.json | Ruta de acceso al archivo de configuración del clúster o modelo de API. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Escriba el GUID de la entidad de servicio. El id. de cliente identificado como identificador de la aplicación cuando el administrador de Azure Stack creó la entidad de servicio. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Escriba el secreto de la entidad de servicio. Este es el secreto de cliente que configuró al crear el servicio. |
    | identity-system | adfs | Opcional. Especifique la solución de administración de identidad si usa los Servicios de federación de Active Directory (AD FS). |

4. Con los valores en su lugar, ejecute el comando siguiente:

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
    --identity-system adfs # required if using AD FS
    ```

5.  Si, por cualquier motivo, la operación de actualización encuentra un error, puede volver a ejecutar el comando de actualización después de solucionar el problema. AKS-Engine reanudará la operación en la que se produjo el error la hora anterior.

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
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    --force
    ```

Para obtener instrucciones, consulte [Forzado de actualización](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [AKS-Engine en Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).
- [Escalado de un clúster de Kubernetes en Azure Stack](azure-stack-kubernetes-aks-engine-scale.md)