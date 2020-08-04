---
title: Escalado de un clúster de Kubernetes en Azure Stack Hub
description: Aprenda a escalar un clúster de Kubernetes en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 07/24/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: 293ca21dda02fb2bfb5031f4b9b16a797bcc4f66
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250986"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack-hub"></a>Escalado de un clúster de Kubernetes en Azure Stack Hub

Puede escalar el clúster con el motor de AKS mediante el comando **scale**. El comando **scale** reutiliza el archivo de configuración del clúster (`apimodel.json`) dentro del directorio de salida como entrada para una nueva implementación de Azure Resource Manager. El motor ejecuta la operación de escalado en el grupo de agentes especificado. Cuando se realiza la operación de escalado, el motor actualiza la definición del clúster en ese mismo archivo `apimodel.json` para reflejar el nuevo recuento de nodos con el fin de reflejar la configuración actual y actualizada del clúster.

## <a name="scale-a-cluster"></a>Escalado de un clúster

El comando `aks-engine scale` puede aumentar o disminuir el número de nodos de un grupo de agentes existente en un clúster de Kubernetes `aks-engine`. Los nodos siempre se agregarán o eliminarán a partir del final del grupo de agentes. Los nodos se acordonan y se purgan antes de la eliminación.

### <a name="values-for-the-scale-command"></a>Valores del comando scale

El comando scale usa los siguientes parámetros para buscar el archivo de definición del clúster y actualizar el clúster.

| Parámetro | Ejemplo | Descripción |
| --- | --- | --- | 
| azure-env | AzureStackCloud | Al usar Azure Stack Hub, los nombres de entorno se deben establecer en `AzureStackCloud`. | 
| ubicación | local | Esta es la región de la instancia de Azure Stack Hub. Para una instancia de ASDK, la región se establece en `local`.  | 
| resource-group | kube-rg | El nombre del grupo de recursos que contiene el clúster. | 
| subscription-id |  | El identificador único global de la suscripción que contiene los recursos que usa el clúster. Asegúrese de que la cuota de la suscripción es suficiente para el escalado. | 
| client-id |  | El identificador de cliente de la entidad de servicio que usó al crear el clúster desde el motor de AKS. | 
| client-secret |  | El secreto de la entidad de servicio que se utilizó al crear el clúster. | 
| api-model | kube-rg/apimodel.json | La ruta de acceso al archivo de definición del clúster (apimodel.json). Esta puede estar en:  _output/\<dnsPrefix>/apimodel.json | 
| -new-node-count | 9 | El recuento de nodos que desea. | 
| -master-FQDN |  | FQDN maestro. Es necesario para reducir verticalmente. |
| identity-system | adfs | Opcional. Especifique la solución de administración de identidad si usa los Servicios de federación de Active Directory (AD FS). |

Debe especificar el parámetro **--azure-env** cuando escale un clúster en Azure Stack Hub. Para más información sobre los parámetros y sus valores utilizados en el comando **scale** para el motor de AKS, consulte [Scale: Parámetros](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters).

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

- Obtenga información sobre [el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
- [Actualización de un clúster de Kubernetes en Azure Stack Hub](azure-stack-kubernetes-aks-engine-upgrade.md)