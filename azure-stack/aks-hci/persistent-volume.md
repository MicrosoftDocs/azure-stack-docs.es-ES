---
title: Uso de un volumen persistente en un clúster de AKS en Azure Stack HCI
description: Uso de almacenamiento persistente en un contenedor de Windows y preparación de nodos de Windows para las cuentas de servicio administradas de grupo
author: v-susbo
ms.topic: how-to
ms.date: 12/02/2020
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 4baaf83c16b3fc290d9dc1339aaf432dc05d89b7
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612614"
---
# <a name="use-persistent-volume-in-an-aks-on-azure-stack-hci-cluster"></a>Uso de un volumen persistente en un clúster de AKS en Azure Stack HCI

> Se aplica a: AKS en Azure Stack HCI, entorno en tiempo de ejecución de AKS en Windows Server 2019 Datacenter

Un volumen persistente representa un fragmento de almacenamiento aprovisionado para su uso con pods de Kubernetes. Un volumen persistente puede ser utilizado por uno o varios pods y está pensado para el almacenamiento a largo plazo. Tampoco depende del pod ni del ciclo de vida de los nodos. Aunque puede aprovisionar un PVC para nodos de Windows y Linux, en esta sección verá cómo crear un volumen persistente y cómo usar este volumen en su aplicación Windows. Para más información, consulte [Volúmenes persistentes en Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

## <a name="before-you-begin"></a>Antes de empezar

Requisitos para poder comenzar:

* Un clúster de Kubernetes con al menos un nodo de trabajo de Windows.
* Un archivo kubeconfig para acceder al clúster de Kubernetes.

## <a name="create-a-persistent-volume-claim"></a>Creación de una notificación de volumen persistente

Una notificación de volumen persistente se usa para aprovisionar automáticamente el almacenamiento en función de una clase de almacenamiento.  Para crear una notificación de volumen, cree en primer lugar un archivo llamado `pvc-akshci-csi.yaml` y cópielo en la siguiente definición de YAML. La notificación solicita un disco que tenga un tamaño de 10 GB con acceso *ReadWriteOnce* . Se especifica la clase de almacenamiento  *predeterminada*  como clase de almacenamiento.  

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: pvc-akshci-csi
spec:
 accessModes:
 - ReadWriteOnce
 resources:
  requests:
   storage: 10Gi
```
Cree el volumen mediante la ejecución de los siguientes comandos en una sesión administrativa de PowerShell en uno de los servidores del clúster de Azure Stack HCI (mediante un método como [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession) o Escritorio remoto para conectarse al servidor): 

```
kubectl create -f pvc-akshci-csi.yaml 
```
La siguiente salida mostrará que la notificación de volumen persistente se ha creado correctamente:

**Salida:**
```
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>Uso del volumen persistente

Para usar un volumen persistente, cree un archivo denominado winwebserver.yaml y cópielo en la siguiente definición de YAML.  Después creará un pod con acceso a la notificación de volumen persistente y a vhdx. 

En la definición de YAML siguiente, *mountPath* es la ruta de acceso para montar un volumen dentro de un contenedor. Después de crear correctamente el pod, verá que se ha creado el subdirectorio *mnt* en *C:\\* y el subdirectorio *akshciscsi* dentro de *mnt*.


```yaml
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  labels: 
    app: win-webserver 
  name: win-webserver 
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: win-webserver 
  template: 
    metadata: 
      labels: 
        app: win-webserver 
      name: win-webserver 
    spec: 
     containers: 
      - name: windowswebserver 
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019 
        ports:  
          - containerPort: 80    
        volumeMounts: 
            - name: akshciscsi 
              mountPath: "/mnt/akshciscsi" 
     volumes: 
        - name: akshciscsi 
          persistentVolumeClaim: 
            claimName:  pvc-akshci-csi 
     nodeSelector: 
      kubernetes.io/os: windows 
```

Para crear un pod con la definición de YAML anterior, ejecute:
```
kubectl create -f winwebserver.yaml 
```

Para asegurarse de que el pod está funcionando, ejecute el siguiente comando. Espere unos minutos hasta que el pod esté en un estado de ejecución, ya que la extracción de la imagen lleva tiempo. 
```
kubectl get pods -o wide 
```
Una vez que se esté ejecutando el pod, puede ver el estado de este mediante la ejecución del siguiente comando: 
```
kubectl.exe describe pod %podName% 
```

Para comprobar que el volumen se ha montado en el pod, ejecute el siguiente comando:
```
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Eliminación de una notificación de volumen persistente

Antes de eliminar una notificación de volumen persistente, debe eliminar la implementación de la aplicación mediante la ejecución de:
```
kubectl delete deployments win-webserver
```

Puede eliminar una notificación de volumen persistente mediante la ejecución de:
```
kubectl delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="next-steps"></a>Pasos siguientes
- [Implementación de una aplicación Windows en el clúster de Kubernetes](./deploy-windows-application.md).
- [Conexión del clúster de Kubernetes a Azure Arc para Kubernetes](./connect-to-arc.md).
