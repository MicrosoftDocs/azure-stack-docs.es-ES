---
title: Problemas conocidos del motor de AKS en Azure Stack Hub
description: Obtenga más información sobre los problemas conocidos del motor de AKS en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 09/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 106aafd33441c2961cf933606cc8f48cbe4fc60d
ms.sourcegitcommit: b80d529ff47b15b8b612d8a787340c7b0f68165b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2020
ms.locfileid: "89473219"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Problemas conocidos del motor de AKS en Azure Stack Hub

En este tema se tratan los problemas conocidos del motor de AKS en Azure Stack Hub.

## <a name="upgrade-issues-in-aks-engine-0510"></a>Problemas de actualización del motor de AKS 0.51.0

* Durante la actualización (aks-engine upgrade) de un clúster de Kubernetes de la versión 1,15.x a 1.16.x, la actualización de los siguientes componentes de Kubernetes requiere pasos manuales adicionales: **kube-proxy**, **azure-cni-networkmonitor**, **csi-secrets-store**, **kubernetes-dashboard**. A continuación se describe lo que puede observar y cómo solucionar los problemas.

  * En entornos conectados, este problema no es evidente, porque no hay ningún signo en el clúster de que los componentes afectados no se hayan actualizado. Todo parece funcionar según lo previsto.
  <!-- * In disconnected environments, you can see this problem when you run a query for the system pods status and see that the pods for the components mentioned below are not in "Ready" state: -->

    ```bash  
    kubectl get pods -n kube-system
    ```

  * Una alternativa para solucionar este problema para cada uno de los componentes, ejecute el comando de la columna Solución alternativa de la tabla siguiente.

    |Nombre de componente |Solución alternativa |Escenarios afectados|
    |---------------|-----------|------------------|
    |**kube-proxy**     | `kubectl delete ds kube-proxy -n kube-system` |Conectado, desconectado |
    |**azure-cni-networkmonitor**   | `kubectl delete ds azure-cni-networkmonitor -n kube-system`   | Conectado, desconectado |
    |**csi-secrets-store**  |`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/secrets-store-csi-driver.yaml`<br>`kubectl delete ds csi-secrets-store -n kube-system` | Escenario desconectado |
    |**kubernetes-dashboard** |Ejecute el siguiente comando en cada nodo maestro:<br>`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/kubernetes-dashboard.yaml` |Escenario desconectado |

* No se admite Kubernetes 1.17 en esta versión. Aunque hay solicitudes de incorporación de cambios de GitHub que hacen referencia a la versión 1.17, esta no se admite.

## <a name="aks-engine-get-versions-command-limitations"></a>Limitaciones del comando aks-engine get-versions

La salida del comando **aks-engine** `get-versions` solo se relaciona con Azure global, no con Azure Stack Hub. Para más información sobre las distintas rutas de actualización, consulte [Pasos para actualizar a una versión más reciente de Kubernetes](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version).

## <a name="next-steps"></a>Pasos siguientes

[Introducción al motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)
