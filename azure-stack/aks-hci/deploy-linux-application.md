---
title: 'Tutorial: implementación de una aplicación de Linux en AKS en Azure Stack HCI'
description: En este tutorial deberá implementar una aplicación Linux con varios contenedores en el clúster mediante una imagen personalizada que se almacena en Azure Container Registry.
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 6fd907a44cdaad5f5dfe7ccb3a29f5fc6a0152b6
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948745"
---
# <a name="tutorial-deploy-linux-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Tutorial: Implementación de aplicaciones Linux en Azure Kubernetes Service en Azure Stack HCI

En este tutorial, implementará una aplicación de varios contenedores que incluye un front-end web y una instancia de base de datos de Redis en el clúster de Azure Kubernetes Service en Azure Stack HCI. A continuación, podrá ver cómo se puede probar y escalar la aplicación. 

En este tutorial se da por supuesto que tiene un conocimiento básico de los conceptos de Kubernetes. Para obtener más información, consulte [Conceptos básicos de Kubernetes de Azure Kubernetes Service en Azure Stack HCI](kubernetes-concepts.md).

## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene listos los requisitos siguientes:

* Un clúster de Azure Kubernetes Service en Azure Stack HCI que tenga al menos un nodo de trabajo de Linux en funcionamiento. 
* Un archivo kubeconfig para obtener acceso al clúster.
* Tener instalado el módulo de PowerShell correspondiente a Azure Kubernetes Service en Azure Stack HCI.
* Ejecute los comandos de este documento en una ventana administrativa de PowerShell.
* Asegúrese de que las cargas de trabajo específicas del sistema operativo se encuentran en el host de contenedor adecuado. Si tiene un clúster de Kubernetes mixto de nodos de trabajo de Windows y Linux, puede usar selectores de nodo o la opción para rechazar o aceptar esos nodos. Para obtener más información, consulte [Uso de los selectores de nodo y la opción para rechazar o aceptarlos](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Implementación de la aplicación

Un archivo de manifiesto de Kubernetes define un estado deseado del clúster, por ejemplo, qué imágenes de contenedor se van a ejecutar. En este inicio rápido se usa un manifiesto para crear todos los objetos necesarios para ejecutar la [aplicación Azure Vote](https://github.com/Azure-Samples/azure-voting-app-redis). Este manifiesto incluye dos implementaciones de Kubernetes, una para las aplicaciones Python de Azure Vote de ejemplo y otra para una instancia de Redis. También se crean dos servicios de Kubernetes: uno interno para la instancia de Redis y otro externo para acceder a la aplicación Azure Vote desde Internet.

Cree un archivo denominado `azure-vote.yaml` y cópielo en la siguiente definición de código YAML.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Implemente la aplicación mediante el comando `kubectl apply` y especifique el nombre del manifiesto de YAML:

```PowerShell
kubectl apply -f azure-vote.yaml
```

En la salida de ejemplo siguiente se muestran las implementaciones y los servicios creados correctamente:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Prueba de la aplicación

Cuando se ejecuta la aplicación, un servicio de Kubernetes expone el front-end de la aplicación a Internet. Este proceso puede tardar unos minutos en completarse.

Para supervisar el progreso, utilice el comando `kubectl get service` con el argumento `--watch`.

```PowerShell
kubectl get service azure-vote-front --watch
```

En un primer momento, el parámetro *EXTERNAL-IP* del servicio *azure-vote-front* se muestra como *pendiente*.

```output
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27      <pending>     80:30572/TCP   22m
```

Cuando la dirección *EXTERNAL-IP* cambie de *pendiente* a una dirección IP pública real, use `CTRL-C` para detener el proceso de inspección de `kubectl`. En la salida del ejemplo siguiente se muestra una dirección IP pública válida asignada al servicio:

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   24m
```

Para ver la aplicación Azure Vote en acción, abra un explorador web en la dirección IP externa del servicio.

![Imagen del clúster de Kubernetes en Azure](media/deploy-linux-application/azure-vote.png)

## <a name="scale-application-pods"></a>Escalado de pods de la aplicación

Hemos creado una única réplica del front-end de Azure Vote y de la instancia de Redis. Para ver el número y el estado de los pods del clúster, use el comando `kubectl get` tal como se indica a continuación:

```console
kubectl get pods -n default
```

La salida del ejemplo siguiente muestra un pod de front-end y un pod de back-end:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1       Running   0          25m
azure-vote-front-84c8bf64fc-cdq86   1/1       Running   0          25m
```

Para cambiar el número de pods en la implementación *azure-vote-front*, use el comando `kubectl scale`. El ejemplo siguiente aumenta el número de pods de front-end a *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Ejecute `kubectl get pods` de nuevo para verificar que se hayan creado pods adicionales. Tras un minuto aproximadamente, los pods adicionales están disponibles en el clúster:

```console
kubectl get pods -n default

Name                                READY   STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1     Running   0          31m
azure-vote-front-84c8bf64fc-cdq86   1/1     Running   0          31m
azure-vote-front-84c8bf64fc-56h64   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-djkp8   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-jmmvs   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-znc6z   1/1     Running   0          80s
```

## <a name="next-steps"></a>Pasos siguientes

* [Uso de Azure Monitor para supervisar el clúster y la aplicación](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
