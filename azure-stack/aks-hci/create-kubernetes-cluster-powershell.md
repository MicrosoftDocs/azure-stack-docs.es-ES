---
title: Inicio rápido para crear clústeres de Kubernetes en Azure Stack HCI mediante Windows PowerShell
description: Aprenda a crear clústeres de Kubernetes en Azure Stack HCI con Windows PowerShell.
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 6a215308e5eab1fa7991f912f1cd4aaff2391f5e
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612444"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Inicio rápido: Creación de clústeres de Kubernetes en Azure Stack HCI mediante Windows PowerShell

> Se aplica a: AKS en Azure Stack HCI, entorno en tiempo de ejecución de AKS en Windows Server 2019 Datacenter

En este inicio rápido aprenderá a usar Windows PowerShell para crear un clúster de Kubernetes en Azure Stack HCI. A continuación, aprenderá a escalar el clúster de Kubernetes y a actualizar la versión de Kubernetes del clúster. Si en su lugar prefiere usar Windows Admin Center, consulte [Configuración de Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center](setup.md).

## <a name="before-you-begin"></a>Antes de empezar

 - Compruebe que tiene configurado un host de Kubernetes en Azure Stack. Si no lo tiene, consulte [Inicio rápido: Configuración de un host de Azure Kubernetes Service en Azure Stack HCI mediante PowerShell](./setup-powershell.md).
 - En primer lugar, asegúrese de tener el módulo Aks-Hci de PowerShell instalado. Si no lo tiene, consulte [Descargar e instalar el módulo AksHci de PowerShell](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module).

## <a name="step-1-create-a-kubernetes-cluster"></a>Paso 1: Creación de un clúster de Kubernetes

Después de instalar el host de Azure Kubernetes Service, estará listo para implementar un clúster de Kubernetes.

Abra PowerShell como administrador y ejecute el siguiente comando.

   ```powershell
   New-AksHciCluster -clusterName <String>
                    [-kubernetesVersion <String>]
                    [-controlPlaneNodeCount <int>]
                    [-linuxNodeCount <int>]
                    [-windowsNodeCount <int>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-linuxNodeVmSize <VmSize>]
                    [-windowsNodeVmSize <VmSize>]
   ```

### <a name="example"></a>Ejemplo

   ```powershell
   New-AksHciCluster -clusterName mynewcluster -kubernetesVersion v1.18.8 -controlPlaneNodeCount 1 -linuxNodeCount 1 -windowsNodeCount 0 
   ```

### <a name="required-parameters"></a>Parámetros obligatorios

`-clusterName`

Nombre alfanumérico del clúster de Kubernetes.

### <a name="optional-parameters"></a>Parámetros opcionales

`-kubernetesVersion`

Versión de Kubernetes que quiere implementar. El valor predeterminado es v1.18.8. Para obtener una lista de las versiones disponibles, ejecute `Get-AksHciKubernetesVersion`.

`-controlPlaneNodeCount`

Número de nodos del plano de control. El valor predeterminado es 1.

`-linuxNodeCount`

Número de nodos de Linux en el clúster de Kubernetes. El valor predeterminado es 1.

`-windowsNodeCount`

Número de nodos de Windows en el clúster de Kubernetes. El valor predeterminado es 0. Solo puede implementar nodos de Windows si está ejecutando Kubernetes v1.18.8.

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
Set-AksHciClusterNodeCount –clusterName <String>
                           -controlPlaneNodeCount <int>
```

Para escalar los nodos de trabajo, ejecute el siguiente comando.

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

Los nodos del plano de control y de trabajo deben escalarse de forma independiente.

### <a name="example"></a>Ejemplo

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -controlPlaneNodeCount 3
```

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -linuxNodeCount 2 -windowsNodeCount 2 
```

## <a name="step-3-upgrade-kubernetes-version"></a>Paso 3: actualización de la versión de Kubernetes

Para ver la lista de versiones de Kubernetes disponibles, ejecute el siguiente comando.

```powershell
Get-AksHciKubernetesVersion
```

Para actualizar Kubernetes a la siguiente versión, ejecute el siguiente comando.

```powershell
Update-AksHciCluster -clusterName <String>
                     [-patch]
```
Cada versión de Kubernetes tiene una versión principal, una versión secundaria y una versión de revisión. Por ejemplo, en v1.18.6, 1 es la versión principal, 18 es la secundaria y 6 es la de revisión. Con el tiempo, AKS-HCI admitirá una versión principal, tres versiones secundarias y dos revisiones por versión secundaria para un total de 6 versiones compatibles. Sin embargo, para esta versión preliminar, se admite un total de 4 versiones:  v1.16.10, v1.16.15, v1.17.11, v1.18.8. 

Cuando se agrega el parámetro `patch` mientras se ejecuta `Update-AksHciCluster`, el comando se actualiza a la versión de revisión siguiente (si existe) para la versión secundaria. Cuando el comando se ejecuta sin el parámetro `patch`, la experiencia de actualización predeterminada se lleva a cabo con la siguiente versión secundaria. Para facilitarlo, la tabla siguiente contiene todas las experiencias de actualización posibles:

| Versión actual           | Versión actualizada de Kubernetes sin revisión         | Versión actualizada de Kubernetes con revisión
| ---------------------------- | ------------ | -------------------------------- |
| v1.16.10           |     v1.17.11      | v1.16.15
| v1.16.15            | v1.17.11 | actualización local de complementos
| v1.17.11           |  v1.18.8          | actualización local de complementos
| v1.18.8             | actualización local de complementos   | actualización local de complementos

En la actualización local de complementos, se actualizan todos los complementos de Kubernetes, como CSI, que AKS-HCI administra automáticamente. Esta actualización no cambia la versión del sistema operativo del nodo. Tampoco cambia la versión de Kubernetes.

### <a name="example---upgrade-kubernetes-version-to-the-next-minor-version"></a>Ejemplo: actualización de la versión de Kubernetes a la siguiente versión secundaria

```powershell
Update-AksHciCluster -clusterName mynewcluster
```

### <a name="example---upgrade-kubernetes-version-to-the-next-patch-version"></a>Ejemplo: actualización de la versión de Kubernetes a la siguiente versión de revisión

```powershell
Update-AksHciCluster -clusterName mynewcluster -patch
```


## <a name="step-4-access-your-clusters-using-kubectl"></a>Paso 4: acceder a los clústeres mediante kubectl

Para acceder a los clústeres de Kubernetes con kubectl, ejecute el siguiente comando. Se usará el archivo kubeconfig del clúster especificado como archivo kubeconfig predeterminado para kubectl.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Ejemplo

```powershell
Get-AksHciCredential -clusterName mynewcluster
```

### <a name="required-parameters"></a>Parámetros obligatorios

`clusterName`

Nombre del clúster.

### <a name="optional-parameters"></a>Parámetros opcionales

`outputLocation`

La ubicación en la que desea descargar kubeconfig. El valor predeterminado es `%USERPROFILE%\.kube`.

## <a name="delete-a-kubernetes-cluster"></a>Eliminación de un clúster de Kubernetes

Si necesita eliminar un clúster de Kubernetes, ejecute el siguiente comando.

```powershell
Remove-AksHciCluster -clusterName
```

### <a name="example"></a>Ejemplo

```powershell
Remove-AksHciCluster -clusterName mynewcluster
```

## <a name="get-logs"></a>Obtención de registros

Para obtener los registros de todos los pods, ejecute el siguiente comando. Este comando creará una carpeta de salida comprimida denominada `akshcilogs` en la ruta de acceso `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

En este inicio rápido aprendió a crear, escalar y actualizar la versión de Kubernetes de un clúster con PowerShell.

## <a name="next-steps"></a>Pasos siguientes

- [Conexión de los clústeres a Azure Arc para Kubernetes](./connect-to-arc.md).
- [Implementación de una aplicación Linux en el clúster de Kubernetes](./deploy-linux-application.md).
- [Implementación de una aplicación Windows en el clúster de Kubernetes](./deploy-windows-application.md).
