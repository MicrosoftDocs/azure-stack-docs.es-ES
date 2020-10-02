---
title: Uso del almacenamiento persistente en un contenedor de Windows
description: Uso de almacenamiento persistente en un contenedor de Windows y preparación de nodos de Windows para las cuentas de servicio administradas de grupo
author: abha
ms.topic: how-to
ms.date: 09/21/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 7436498ea993281f803d7ff8a0c4108728570f59
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949356"
---
# <a name="use-persistent-storage-in-a-windows-container-and-prepare-windows-nodes-for-group-managed-service-accounts"></a>Uso de almacenamiento persistente en un contenedor de Windows y preparación de nodos de Windows para las cuentas de servicio administradas de grupo

Un volumen persistente representa un fragmento de almacenamiento aprovisionado para su uso con pods de Kubernetes. Un volumen persistente puede ser utilizado por uno o varios pods y está pensado para el almacenamiento a largo plazo. Tampoco depende del pod ni del ciclo de vida de los nodos.  En esta sección, verá cómo crear un volumen persistente y cómo usar este volumen en su aplicación Windows.

## <a name="before-you-begin"></a>Antes de empezar

Requisitos para poder comenzar:

* Un clúster de Kubernetes con al menos un nodo de trabajo de Windows.
* Un archivo kubeconfig para acceder al clúster de Kubernetes.


## <a name="create-a-persistent-volume-claim"></a>Creación de una notificación de volumen persistente

Una notificación de volumen persistente se usa para aprovisionar automáticamente el almacenamiento en función de una clase de almacenamiento. Para crear una notificación de volumen, cree en primer lugar un archivo llamado `pvc-akshci-csi.yaml` y cópielo en la siguiente definición de YAML. La notificación solicita un disco que tenga un tamaño de 10 GB con acceso *ReadWriteOnce* . Se especifica la clase de almacenamiento  *predeterminada*  como clase de almacenamiento.  

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


```PowerShell
kubectl create -f pvc-akshci-csi.yaml 
```
La siguiente salida mostrará que la notificación de volumen persistente se ha creado correctamente:

**Salida:**
```PowerShell
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>Uso del volumen persistente

Para usar un volumen persistente, cree un archivo denominado winwebserver.yaml y cópielo en la siguiente definición de YAML. Después creará un pod con acceso a la notificación de volumen persistente y a vhdx. 

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

```PowerShell
Kubectl create -f winwebserver.yaml 
```

Para asegurarse de que el pod está funcionando, ejecute el siguiente comando. Espere unos minutos hasta que el pod esté en un estado de ejecución, ya que la extracción de la imagen lleva tiempo.

```PowerShell
kubectl get pods -o wide 
```
Una vez que se esté ejecutando el pod, puede ver el estado de este mediante la ejecución del siguiente comando: 

```PowerShell
kubectl.exe describe pod %podName% 
```

Para comprobar que el volumen se ha montado en el pod, ejecute el siguiente comando:

```PowerShell
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Eliminación de una notificación de volumen persistente

Antes de eliminar una notificación de volumen persistente, debe eliminar la implementación de la aplicación mediante la ejecución de:

```PowerShell
kubectl.exe delete deployments win-webserver
```

Puede eliminar una notificación de volumen persistente mediante la ejecución de:

```PowerShell
kubectl.exe delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="prepare-windows-nodes-for-group-managed-service-account-support-on-windows-nodes"></a>Preparación de nodos de Windows para la compatibilidad con las cuentas de servicio administradas de grupo en los nodos de Windows

Las cuentas de servicio administradas de grupo son un tipo específico de cuenta de Active Directory que proporciona administración automática de contraseñas, administración simplificada del nombre de entidad de seguridad de servicio (SPN) y la posibilidad de delegar la administración a otros administradores en varios servidores. Para configurar cuentas de servicio administradas de grupo (gMSA) para pods y contenedores que se ejecutarán en los nodos de Windows, primero debe unir los nodos de Windows a un dominio de Active Directory.

Para habilitar la compatibilidad con la cuenta de servicio administrada de grupo, el nombre del clúster de Kubernetes debe tener menos de 4 caracteres. Esto se debe a que la longitud máxima admitida para el nombre de un servidor unido a un dominio es de 15 caracteres y la convención de nomenclatura de clústeres de Azure Kubernetes Service en Azure Stack HCI para un nodo de trabajo agrega varios caracteres predefinidos al nombre de un nodo.

Para unir los nodos de trabajo de Windows a un dominio, inicie sesión en un nodo de trabajo de Windows; para ello, ejecute `kubectl get` y anote el valor `EXTERNAL-IP`.

```PowerShell
kubectl get nodes -o wide
``` 

A continuación, puede conectarse con SSH al nodo mediante `ssh Administrator@ip`. 

Una vez que haya iniciado sesión correctamente en el nodo de trabajo de Windows, ejecute el siguiente comando de PowerShell para unir a un dominio al nodo. Se le pedirá que escriba las credenciales de su **cuenta de administrador de dominio**. También puede utilizar credenciales de usuario con permisos elevados a la que se haya otorgado determinados derechos para unir equipos al dominio concreto. Después, tendrá que reiniciar el nodo de trabajo de Windows.

```PowerShell
add-computer --domainame "YourDomainName" -restart
```

Una vez que todos los nodos de trabajo de Windows se hayan unido a un dominio, siga los pasos que se detallan en [Configuración de gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa) para aplicar las definiciones y webhooks de recursos personalizados de gMSA para Kubernetes en el clúster de Kubernetes.

Para más información sobre el contenedor de Windows con gMSA, consulte [Contenedores de Windows y gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Pasos siguientes
- [Implementación de una aplicación Windows en el clúster de Kubernetes](./deploy-windows-application.md).
- [Conexión del clúster de Kubernetes a Azure Arc para Kubernetes](./connect-to-arc.md).
