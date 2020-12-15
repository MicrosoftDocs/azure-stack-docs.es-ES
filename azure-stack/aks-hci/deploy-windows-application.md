---
title: Implementación de una aplicación .NET para Windows en AKS en Azure Stack HCI
description: Descubra cómo implementar una aplicación .NET para Windows en el clúster mediante una imagen personalizada que se almacena en Azure Container Registry.
author: abha
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 844bafbb497228d2e4527a5e974574dadb95ee7d
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612325"
---
# <a name="deploy-windows-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Implementación de aplicaciones Windows en Azure Kubernetes Service en Azure Stack HCI

> Se aplica a: AKS en Azure Stack HCI, entorno en tiempo de ejecución de AKS en Windows Server 2019 Datacenter

En este tutorial implementará una aplicación de ejemplo de ASP.NET de un contenedor de Windows Server en el clúster de Kubernetes y aprenderá a probarla y escalarla. También aprenderá a unir un nodo de Windows a un dominio de Active Directory.
En este tutorial se da por supuesto que tiene un conocimiento básico de los conceptos de Kubernetes. Para obtener más información, consulte [Conceptos básicos de Kubernetes de Azure Kubernetes Service en Azure Stack HCI](kubernetes-concepts.md).

## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene listos los requisitos siguientes:

* Un clúster de Azure Kubernetes Service en Azure Stack HCI que tenga al menos un nodo de trabajo de Windows en funcionamiento. 
* Un archivo kubeconfig para obtener acceso al clúster.
* Tener instalado el módulo de PowerShell correspondiente a Azure Kubernetes Service en Azure Stack HCI.
* Ejecute los comandos de este documento en una ventana administrativa de PowerShell.
* Asegúrese de que las cargas de trabajo específicas del sistema operativo se encuentran en el host de contenedor adecuado. Si tiene un clúster de Kubernetes mixto de nodos de trabajo de Windows y Linux, puede usar selectores de nodo o la opción para rechazar o aceptar esos nodos. Para obtener más información, consulte [Uso de los selectores de nodo y la opción para rechazar o aceptarlos](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Implementación de la aplicación

Un archivo de manifiesto de Kubernetes define un estado deseado del clúster, por ejemplo, qué imágenes de contenedor se van a ejecutar. En este artículo, se utiliza un manifiesto para crear todos los objetos necesarios para ejecutar la aplicación de ejemplo de ASP.NET en un contenedor de Windows Server. Este manifiesto incluye una implementación de Kubernetes para la aplicación de ejemplo de ASP.NET y un servicio de Kubernetes externo para acceder a la aplicación desde Internet.

La aplicación de ejemplo de ASP.NET se proporciona como parte de los ejemplos de .NET Framework y se ejecuta en un contenedor de Windows Server. Azure Kubernetes Service en Azure Stack HCI requiere que los contenedores de Windows Server estén basados en imágenes de *Windows Server 2019*. 

El archivo de manifiesto de Kubernetes también debe definir un selector de nodos que le indique al clúster de AKS que ejecute el pod de la aplicación de ejemplo de ASP.NET en un nodo que pueda ejecutar contenedores de Windows Server.

Cree un archivo denominado `sample.yaml` y cópielo en la siguiente definición de código YAML. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Implemente la aplicación mediante el comando `kubectl apply` y especifique el nombre del manifiesto de YAML:

```console
kubectl apply -f sample.yaml
```

En la salida de ejemplo siguiente se muestran la implementación y el servicio que se crearon correctamente:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Prueba de la aplicación

Cuando se ejecuta la aplicación, un servicio de Kubernetes expone el front-end de la aplicación a Internet. Este proceso puede tardar unos minutos en completarse. En ocasiones, el servicio puede tardar más de unos minutos en aprovisionarse. Espere hasta 10 minutos en estos casos.

Para supervisar el progreso, utilice el comando `kubectl get service` con el argumento `--watch`.

```PowerShell
kubectl get service sample --watch
```

En un primer momento, el parámetro *EXTERNAL-IP* del servicio de *ejemplo* se muestra como *pendiente*.

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Cuando la dirección *EXTERNAL-IP* cambie de *pendiente* a una dirección IP pública real, use `CTRL-C` para detener el proceso de inspección de `kubectl`. En la salida del ejemplo siguiente se muestra una dirección IP pública válida asignada al servicio:

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver la aplicación de ejemplo en acción, abra un explorador web en la dirección IP externa del servicio.

![Imagen de cómo llegar a la aplicación de ejemplo de ASP.NET](media/deploy-windows-application/asp-net-sample-app.png)

Si se agota el tiempo de espera de conexión al intentar cargar la página, verifique si la aplicación de ejemplo está lista con el comando `kubectl get pods --watch`. A veces, la dirección IP externa está disponible antes de que se inicie el contenedor de Windows.

## <a name="scale-application-pods"></a>Escalado de pods de la aplicación

Hemos creado una única réplica del front-end de la aplicación. Para ver el número y el estado de los pods del clúster, use el comando `kubectl get` tal como se indica a continuación:

```console
kubectl get pods -n default
```

Para cambiar el número de pods en la implementación de *ejemplo*, use el comando `kubectl scale`. En el ejemplo siguiente se aumenta el número de pods del front-end a *3*:

```console
kubectl scale --replicas=3 deployment/sample
```

Ejecute `kubectl get pods` de nuevo para verificar que se hayan creado pods adicionales. Tras un minuto aproximadamente, los pods adicionales están disponibles en el clúster:

```console
kubectl get pods -n default
```

## <a name="next-steps"></a>Pasos siguientes

* [Uso de Azure Monitor para supervisar el clúster y la aplicación](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Use un volumen persistente en un clúster de Kubernetes](persistent-volume.md).
