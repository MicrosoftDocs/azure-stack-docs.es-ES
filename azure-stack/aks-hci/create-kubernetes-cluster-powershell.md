---
title: Inicio rápido para crear clústeres de Kubernetes en Azure Stack HCI mediante Windows PowerShell
description: Aprenda a crear clústeres de Kubernetes en Azure Stack HCI con Windows PowerShell.
author: jessicaguan
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: jeguan
ms.openlocfilehash: 35d527b56a2429676d343ba8098fc6821835fb00
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949391"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Inicio rápido: Creación de clústeres de Kubernetes en Azure Stack HCI mediante Windows PowerShell

> Se aplica a: Azure Stack HCI

En este inicio rápido aprenderá a usar Windows PowerShell para crear un clúster de Kubernetes en Azure Stack HCI. Si en su lugar prefiere usar Windows Admin Center, consulte [Configuración de Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center](setup.md).

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar, asegúrese de que:

- Tiene un clúster de Azure Stack HCI de entre 2 y 4 nodos o una instancia de Azure Stack HCI con un nodo único. **Le recomendamos tener un clúster de Azure Stack HCI de entre 2 y 4 nodos.** Si no es así, siga las instrucciones que se indican [aquí](./system-requirements.md) sobre cómo crear un clúster de este tipo.
- Tiene configurado un host de Azure Stack Kubernetes. Si no es así, siga las instrucciones que se indican [aquí](./setup-powershell.md) sobre cómo configurar uno.

## <a name="step-1-create-a-kubernetes-cluster"></a>Paso 1: Creación de un clúster de Kubernetes

Después de instalar el host de Azure Kubernetes Service, estará listo para implementar un clúster de Kubernetes.

Abra PowerShell como administrador y ejecute el siguiente comando.

   ```powershell
   New-AksHciCluster -clusterName
                    [-kubernetesVersion]
                    [-controlPlaneNodeCount]
                    [-linuxNodeCount]
                    [-windowsNodeCount]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-linuxNodeVmSize]
                    [-windowsNodeVmSize]
   ```

### <a name="required-parameters"></a>Parámetros obligatorios

`-clusterName`

Nombre alfanumérico del clúster de Kubernetes.

### <a name="optional-parameters"></a>Parámetros opcionales

`-kubernetesVersion`

Versión de Kubernetes que quiere implementar. El valor predeterminado es v1.18.6. Para obtener una lista de las versiones disponibles, ejecute `Get-AksHciKubernetesVersion`.

`-controlPlaneNodeCount`

Número de nodos del plano de control. El valor predeterminado es 1.

`-linuxNodeCount`

Número de nodos de Linux en el clúster de Kubernetes. El valor predeterminado es 1.

`-windowsNodeCount`

Número de nodos de Windows en el clúster de Kubernetes. El valor predeterminado es 0.

`-controlPlaneVmSize`

Tamaño de la VM del plano de control. El valor predeterminado es Standard_A2_v2. Para obtener una lista de los tamaños de VM disponibles, ejecute `Get-AksHciVmSize`.

`-loadBalancerVmSize`

Tamaño de la VM del equilibrador de carga. El valor predeterminado es Standard_A2_V2. Para obtener una lista de los tamaños de VM disponibles, ejecute `Get-AksHciVmSize`.

`-linuxNodeVmSize`

Tamaño de la VM del nodo de Linux. El valor predeterminado es Standard_K8S3_v1. Para obtener una lista de los tamaños de VM disponibles, ejecute `Get-AksHciVmSize`.

`-windowsNodeVmSize`

Tamaño de la VM del nodo de Windows. El valor predeterminado es Standard_K8S3_v1. Para obtener una lista de los tamaños de VM disponibles, ejecute `Get-AksHciVmSize`.

### <a name="check-your-deployed-clusters"></a>Comprobación de los clústeres implementados

Para obtener una lista del host de Azure Kubernetes Service y los clústeres de Kubernetes implementados, ejecute el siguiente comando.

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>Paso 2: escalado de un clúster de Kubernetes

Si necesita escalar o reducir verticalmente el clúster, puede cambiar el número de nodos del plano de control, los nodos de trabajo de Linux o los nodos de trabajo de Windows.

Para escalar los nodos del plano de control, ejecute el siguiente comando.

```powershell
Set-AksHciClusterNodeCount –clusterName
                           -controlPlaneNodeCount
```

Para escalar los nodos de trabajo, ejecute el siguiente comando.

```powershell
Set-AksHciClusterNodeCount –clusterName
                           -linuxNodeCount
                           -windowsNodeCount
```

Los nodos del plano de control y de trabajo deben escalarse de forma independiente.

## <a name="step-3-upgrade-kubernetes-version"></a>Paso 3: actualización de la versión de Kubernetes

Para ver la versión actual de Kubernetes que está ejecutando, ejecute el siguiente comando.

```powershell
Get-AksHciKubernetesVersion
```

Para actualizar Kubernetes a la siguiente versión, ejecute el siguiente comando.

```powershell
Update-AksHciCluster -clusterName
```

Si quiere usar los nodos de Windows, la versión mínima necesaria es v1.1.8.6.

## <a name="step-4-access-your-clusters-using-kubectl"></a>Paso 4: acceder a los clústeres mediante kubectl

Para acceder al host de Azure Kubernetes Service o al clúster de Kubernetes mediante kubectl, ejecute el siguiente comando. Se usará el archivo kubeconfig del clúster especificado como archivo kubeconfig predeterminado para kubectl.

```powershell
Set-AksHciKubeConfig -clusterName
```

## <a name="delete-a-kubernetes-cluster"></a>Eliminación de un clúster de Kubernetes

Si necesita eliminar un clúster de Kubernetes, ejecute el siguiente comando.

```powershell
Remove-AksHciCluster -clusterName
```

## <a name="get-logs"></a>Obtención de registros

Para obtener los registros de todos los pods, ejecute el siguiente comando. Este comando creará una carpeta de salida comprimida denominada `akshcilogs` en la ruta de acceso `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

En este inicio rápido aprendió a crear, escalar y actualizar un clúster de Kubernetes con PowerShell.

## <a name="next-steps"></a>Pasos siguientes

- [Conexión de los clústeres a Azure Arc para Kubernetes](./connect-to-arc.md).
- [Implementación de una aplicación Linux en el clúster de Kubernetes](./deploy-linux-application.md).
- [Implementación de una aplicación Windows en el clúster de Kubernetes](./deploy-windows-application.md).
