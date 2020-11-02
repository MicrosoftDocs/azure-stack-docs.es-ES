---
title: Habilitación de la característica Service Account Token Volume Projection para el motor de AKS en Azure Stack Hub
description: Obtenga información sobre cómo habilitar la característica Service Account Token Volume Projection para el motor de AKS en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 5e712577bb01f7f084c24eadfd3931ebb67f23d9
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906186"
---
# <a name="enabling-service-account-token-volume-projection-for-the-aks-engine-on-azure-stack-hub"></a>Habilitación de la característica Service Account Token Volume Projection para el motor de AKS en Azure Stack Hub

Istio es una capa de malla de servicio de código abierto configurable que conecta, supervisa y protege los contenedores en un clúster de Kubernetes. Istio 1.3 y las versiones posteriores usan una característica de Kubernetes llamada *Service Account Token Volume Projection* . Esta característica no está habilitada de forma predeterminada en los clústeres de Kubernetes implementados por el motor de AKS. En este artículo, puede encontrar las propiedades JSON del modelo de API en el elemento `apiServerConfig` que muestra las marcas de servidor de la API de Kubernetes necesarias para habilitar la característica Service Account Token Volume Projection para el clúster.

Para obtener más información acerca de la característica Service Account Token Volume Projection, consulte [Service Account Token Volume Projection](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection).

## <a name="enable-service-account-token-volume-projection"></a>Habilitación de la característica Service Account Token Volume Projection

Para habilitar la característica Service Account Token Volume Projection, agregue la configuración siguiente al archivo JSON del modelo de API. 

```json
{
    "kubernetesConfig": {
        "apiServerConfig": {
            "--service-account-api-audiences": "api,istio-ca",
            "--service-account-issuer": "kubernetes.default.svc",
            "--service-account-signing-key-file": "/etc/kubernetes/certs/apiserver.key"
        }
    }
}
```

> [!Note]  
> Es posible que tenga que ajustar `--service-account-api-audiences` y `--service-account-issuer` a su caso de uso específico.

Para obtener un modelo de API de ejemplo completo, consulte [istio.json](https://github.com/Azure/aks-engine/blob/master/examples/service-mesh/istio.json).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
- [Actualización de un clúster de Kubernetes en Azure Stack Hub](azure-stack-kubernetes-aks-engine-upgrade.md)
