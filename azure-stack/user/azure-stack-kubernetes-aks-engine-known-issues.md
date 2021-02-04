---
title: Problemas conocidos del motor de AKS en Azure Stack Hub
description: Obtenga más información sobre los problemas conocidos del motor de AKS en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/11/2020
ms.openlocfilehash: b65fb410cd18e058f4e0ed7299eeff2d9daeb7f6
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246884"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Problemas conocidos del motor de AKS en Azure Stack Hub

En este tema se tratan los problemas conocidos del motor de AKS en Azure Stack Hub.

## <a name="unable-to-resize-cluster-vms-with-the-compute-service"></a>No se puede cambiar el tamaño de las VM del clúster con el servicio de proceso

- **Aplicable a**: Azure Stack Hub, motor de AKS (todo)
- **Descripción**: el cambio de tamaño de las VM del clúster a través del servicio de proceso no funciona con el motor de AKS. El motor de AKS mantiene el estado del clúster en el archivo JSON del modelo de API. Para asegurarse de que el tamaño de la VM deseado se refleja en todas las operaciones de creación, actualización o escalado realizadas con el motor de AKS, debe actualizar el modelo de API antes de ejecutar cualquiera de esas operaciones. Por ejemplo, si cambia el tamaño de una VM en un clúster ya implementado a un tamaño diferente mediante el servicio de proceso, el estado se perderá cuando se ejecute `aks-engine update`.
- **Corrección**: para que este trabajo busque el modelo de API del clúster, cambie el tamaño y, a continuación, ejecute `aks-engine update`.
- **Repetición**: al intentar cambiar el tamaño mediante el servicio de proceso.

## <a name="disk-detach-operation-fails-in-aks-engine-0550"></a>Error en la operación de desasociación de disco en el motor de AKS 0.55.0

- **Aplicable a**: Azure Stack Hub (actualización 2005), motor de AKS 0.55.0
- **Descripción**: al intentar eliminar una implementación que contiene volúmenes de persistencia, la operación de eliminación desencadena una serie de errores de asociación y desasociación. Esto se debe a un error en el proveedor de nube del motor de AKS v0.55.0. El proveedor de nube llama a Azure Resource Manager mediante una versión más nueva de la API que la que admite actualmente Azure Resource Manager en Azure Stack Hub (actualización 2005).
- **Corrección**: puede encontrar más detalles y la mitigación en el [repositorio de GitHub del motor de AKS (problema 3817)](https://github.com/Azure/aks-engine/issues/3817#issuecomment-691329443). Actualice en cuanto esté disponible una nueva compilación del motor de AKS y la imagen correspondiente.
- **Repetición**: al eliminar una implementación que contiene volúmenes de persistencia.



## <a name="upgrade-issues-in-aks-engine-0510"></a>Problemas de actualización del motor de AKS 0.51.0

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
