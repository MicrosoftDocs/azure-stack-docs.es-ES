---
title: Escalado de un clúster de Kubernetes en Azure Stack | Microsoft Docs
description: Aprenda a escalar un clúster de Kubernetes en Azure Stack.
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
ms.openlocfilehash: 39c651649aea60ab12aae9daf35b0d07615bf697
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595295"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack"></a>Escalado de un clúster de Kubernetes en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede escalar el clúster con el motor de AKS mediante el comando **scale**. El comando **scale** reutiliza el archivo de configuración del clúster (`apimodel.json`) dentro del directorio de salida como entrada para una nueva implementación de Azure Resource Manager. El motor ejecuta la operación de escalado en el grupo de agentes especificado. Cuando se realiza la operación de escalado, el motor actualiza la definición del clúster en ese mismo archivo `apimodel.json` para reflejar el nuevo recuento de nodos con el fin de reflejar la configuración actual y actualizada del clúster.

## <a name="scale-a-cluster"></a>Escalado de un clúster

El comando `aks-engine scale` puede aumentar o disminuir el número de nodos de un grupo de agentes existente en un clúster de Kubernetes `aks-engine`. Los nodos siempre se agregarán o eliminarán a partir del final del grupo de agentes. Los nodos se acordonan y se purgan antes de la eliminación.

### <a name="values-for-the-scale-command"></a>Valores del comando scale

El comando scale usa los siguientes parámetros para buscar el archivo de definición del clúster y actualizar el clúster.

| Parámetro | Ejemplo | DESCRIPCIÓN |
| --- | --- | --- | 
| azure-env | AzureStackCloud | Al usar Azure Stack, los nombres de entorno se deben establecer en `AzureStackCloud`. | 
| location | local | Esta es la región de la instancia de Azure Stack. Para una instancia de ASDK, la región se establece en `local`.  | 
| resource-group | kube-rg | El nombre del grupo de recursos que contiene el clúster. | 
| subscription-id |  | El identificador único global de la suscripción que contiene los recursos que usa el clúster. Asegúrese de que la cuota de la suscripción es suficiente para el escalado. | 
| client-id |  | El identificador de cliente de la entidad de servicio que usó al crear el clúster desde el motor de AKS. | 
| client-secret |  | El secreto de la entidad de servicio que se utilizó al crear el clúster. | 
| api-model | kube-rg/apimodel.json | La ruta de acceso al archivo de definición del clúster (apimodel.json). Esta puede ser:  _output/\<dnsPrefix>/apimodel.json | 
| -new-node-count | 9 | El recuento de nodos que desea. | 
| -master-FQDN |  | FQDN maestro. Es necesario para reducir verticalmente. |
| identity-system | adfs | Opcional. Especifique la solución de administración de identidad si usa los Servicios de federación de Active Directory (AD FS). |

Debe especificar el parámetro **--azure-env** cuando escale un clúster en Azure Stack. Para más información sobre los parámetros y sus valores utilizados en el comando **scale** para el motor de AKS, consulte [Scale: Parámetros](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters).

### <a name="command-to-scale-your-cluster"></a>Comando para escalar el clúster

Para escalar el clúster, ejecute el comando siguiente:

```bash
aks-engine scale \
    --azure-env AzureStackCloud   \
    --location <for an ASDK is local> \
    --resource-group <cluster resource group>
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model <path to your apomodel.json file>
    --new-node-count <desired node count> \
    --master-FQDN <master FQDN> \
    --identity-system adfs # required if using AD FS
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).
- [Actualización de un clúster de Kubernetes en Azure Stack](azure-stack-kubernetes-aks-engine-upgrade.md)