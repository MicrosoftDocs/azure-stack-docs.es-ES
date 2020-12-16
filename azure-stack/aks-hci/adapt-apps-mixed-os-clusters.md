---
title: Adaptación de las aplicaciones para su uso en clústeres de Kubernetes con sistema operativo mixto
description: Uso de selectores de nodo o de intolerancias y tolerancias en Azure Kubernetes Service para garantizar que las aplicaciones de clústeres de Kubernetes con sistema operativo mixto que se ejecutan en Azure Stack HCI estén programadas en el sistema operativo del nodo de trabajo correcto
author: abha
ms.topic: how-to
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 0d4fd0e62e10e4afc4a76c9cac2deaed97e23549
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761565"
---
# <a name="adapt-apps-for-mixed-os-kubernetes-clusters-using-node-selectors-or-taints-and-tolerations"></a>Adaptación de las aplicaciones a los clústeres de Kubernetes con sistemas operativos mixtos mediante selectores de nodo o intolerancias y tolerancias

> Se aplica a: AKS en Azure Stack HCI, entorno en tiempo de ejecución de AKS en Windows Server 2019 Datacenter

Azure Kubernetes Service en Azure Stack HCI le permite ejecutar clústeres de Kubernetes con nodos de Windows y Linux, pero requiere que realice pequeños cambios en las aplicaciones para su uso en estos clústeres con sistema operativo mixto. En esta guía paso a paso, aprenderá a asegurarse de que la aplicación se programa en el sistema operativo del host adecuado mediante el uso de selectores de nodo o de intolerancias y tolerancias.

En esta guía paso a paso se presupone un conocimiento básico de los conceptos de Kubernetes. Para más información, consulte [Conceptos básicos de Kubernetes de Azure Kubernetes Service en Azure Stack HCI](kubernetes-concepts.md).

## <a name="node-selector"></a>Selector de nodos

*Selector de nodos* es un campo simple de YAML en la especificación del pod que limita a los pods para que solo se programen en los nodos correctos que coincidan con el sistema operativo. En el YAML de especificación del pod, especifique un `nodeSelector` -Windows o Linux, como se muestra en los ejemplos siguientes. 

```yaml
kubernetes.io/os = Windows
```
o bien,

```yaml
kubernetes.io/os = Linux
```

Para más información sobre nodeSelectors, visite [selectores de nodo](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/). 

## <a name="taints-and-tolerations"></a>Intolerancias y tolerancias

Las *intolerancias* y *tolerancias* funcionan conjuntamente para garantizar que los pods no se programen en los nodos de forma involuntaria. Se puede aplicar una intolerancia a un nodo para que no acepte pods que no admitan explícitamente esa intolerancia. Esto último se logra agregando una tolerancia en el YAML de especificación del pod.

A los nodos del sistema operativo Windows de Azure Kubernetes Service en Azure Stack HCI se les puede aplicar una intolerancia con el siguiente par clave-valor. Los usuarios no deben usar otro diferente.

```yaml
node.kubernetes.io/os=Windows:NoSchedule
```
Ejecute `kubectl get` e identifique los nodos de trabajo de Windows a los que desea aplicar la intolerancia.

```
kubectl get nodes --all-namespaces -o=custom-columns=NAME:.metadata.name,OS:.status.nodeInfo.operatingSystem
```
Salida:
```output
NAME                                     OS
my-aks-hci-cluster-control-plane-krx7j   linux
my-aks-hci-cluster-md-md-1-5h4bl         windows
my-aks-hci-cluster-md-md-1-5xlwz         windows
```

Aplique la intolerancia a los nodos de trabajo del servidor de Windows mediante `kubectl taint node`.

```
kubectl taint node my-aks-hci-cluster-md-md-1-5h4bl node.kubernetes.io/os=Windows:NoSchedule
kubectl taint node my-aks-hci-cluster-md-md-1-5xlwz node.kubernetes.io/os=Windows:NoSchedule
```

Puede especificar una tolerancia para un pod en el YAML de especificación del pod. La siguiente tolerancia "coincide" con la intolerancia que creó la línea de intolerancia de kubectl anterior y, por lo tanto, un pod con esa tolerancia podría programar en my-aks-hci-cluster-md-md-1-5h4bl o my-aks-hci-cluster-md-md-1-5xlwz:

```yaml
tolerations:
- key: node.kubernetes.io/os
  operator: Equal
  value: Windows
  effect: NoSchedule
```
Para más información sobre intolerancias y tolerancias, visite [Intolerancias y tolerancias](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). 

## <a name="next-steps"></a>Pasos siguientes

En esta guía paso a paso, ha aprendido a agregar selectores de nodo o intolerancias y tolerancias a los clústeres de Kubernetes mediante kubectl. A continuación, puede realizar:
- [Implementación de aplicaciones Linux en el clúster de Kubernetes](./deploy-linux-application.md).
- [Implementación de una aplicación de Windows Server en un clúster de Kubernetes](./deploy-windows-application.md).
