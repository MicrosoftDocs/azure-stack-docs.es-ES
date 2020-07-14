---
title: Problemas conocidos del motor de AKS en Azure Stack Hub
description: Obtenga más información sobre los problemas conocidos del motor de AKS en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 07/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 07/02/2020
ms.openlocfilehash: 4012f350fff45e3d09b543692fa315ffeeec1240
ms.sourcegitcommit: a5bb340c5689f7dcf1ef3a340416f7f337782170
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2020
ms.locfileid: "85942309"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Problemas conocidos del motor de AKS en Azure Stack Hub

En este tema se tratan los problemas conocidos del motor de AKS en Azure Stack Hub.

## <a name="upgrade-issues-in-aks-engine-0510"></a>Problemas de actualización del motor de AKS 0.51.0

* Durante la actualización (aks-engine upgrade) de un clúster de Kubernetes de la versión 1,15.x a 1.16.x, la actualización de los siguientes componentes de Kubernetes requiere pasos manuales adicionales: **kube-proxy**, **azure-cni-networkmonitor**, **csi-secrets-store**, **kubernetes-dashboard**. A continuación se describe lo que puede observar y cómo solucionar los problemas.

  * En entornos conectados, este problema no es evidente, porque no hay ningún signo en el clúster de que los componentes afectados no se hayan actualizado. Todo parece funcionar según lo previsto.
  * En entornos desconectados, puede ver este problema al ejecutar una consulta sobre el estado de los pods del sistema y ver que los pods de los componentes que se mencionan a continuación no están en estado "Ready" (Listo):

    ```PowerShell
    kubectl get pods -n kube-system
    ```

  * Una alternativa para solucionar este problema para cada uno de los componentes, ejecute el comando de la columna Solución alternativa de la tabla siguiente.

    |Nombre de componente |Solución alternativa |Escenarios afectados|
    |---------------|-----------|------------------|
    |**kube-proxy**     | `kubectl delete ds kube-proxy -n kube-system` |Conectado, desconectado |
    |**azure-cni-networkmonitor**   | `kubectl delete ds azure-cni-networkmonitor -n kube-system`   | Conectado, desconectado |
    |**csi-secrets-store**  |`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/secrets-store-csi-driver.yaml`<br>`kubectl delete ds csi-secrets-store -n kube-system` | Escenario desconectado |
    |**kubernetes-dashboard** |Ejecute el siguiente comando en cada nodo maestro:<br>`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/kubernetes-dashboard.yaml` |Escenario desconectado |

* No se admite Kubernetes 1.17 en esta versión. Aunque hay solicitudes de incorporación de cambios que aluden a la versión 1.17, en realidad esta no se admite.

## <a name="basic-load-balancer-sku-limitations"></a>Limitaciones de la SKU básica de Load Balancer

* Limitación de un solo grupo de agentes. Actualmente, Azure Stack Hub solo admite la SKU básica de Load Balancer. Esta SKU [limita](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) los puntos de conexión del grupo de back-end a las máquinas virtuales que están en un único conjunto de disponibilidad (o conjunto de escalado de máquinas virtuales). Esto implica que todas las réplicas de un servicio LoadBalancer deben implementarse en el mismo grupo de agentes, y también que cada clúster individual puede tener un servicio LoadBalancer de Linux o un servicio LoadBalancer de Windows.

  Puede hacer que Kubernetes cree pods en un grupo de agentes específico agregando [node selector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) "agentpool: MI_NOMBRE_DE_GRUPO" en la plantilla de pod.

  ```powershell
  nodeSelector:

        agentpool: linuxpool
  ```
  
  Si ya se ha creado un servicio LoadBalancer en el clúster, puede averiguar qué grupo de agentes se seleccionó como grupo de back-end de Load Balancer; para ello, inspeccione la hoja de grupos de back-end de Load Balancer en el portal de Azure Stack Hub. Cuando tenga esa información, puede especificar el grupo de agentes de destino actualizando el código YAML de la implementación o el pod (como se explicó en el párrafo anterior).

* Ámbito de comando para `get-versions`. La salida del comando `get-versions` solo pertenece a Azure y no a las nubes de Azure Stack Hub. Para más información acerca de las distintas rutas de actualización, consulte [Pasos para actualizar a una versión más reciente de Kubernetes](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version).

## <a name="next-steps"></a>Pasos siguientes

[Introducción al motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)
