---
title: Administración de clústeres de Azure Stack HCI mediante PowerShell
description: Aprenda a administrar clústeres en Azure Stack HCI mediante PowerShell.
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 88f6ace707d14466de481f60133875968b8edce0
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947442"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Administración de clústeres de Azure Stack HCI mediante PowerShell

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

Windows PowerShell se puede usar para administrar recursos y configurar características en los clústeres de Azure Stack HCI.

Normalmente, los clústeres se administran desde un equipo remoto, en lugar de hacerlo en un servidor host de un clúster. Este equipo remoto se denomina equipo de administración.

> [!NOTE]
> Al ejecutar comandos de PowerShell desde un equipo de administración, incluya el parámetro `-Name` o `-Cluster` con el nombre del clúster que va a administrar. Además, tendrá que especificar el nombre de dominio completo (FQDN) al usar el parámetro `-ComputerName` para un nodo de servidor.

Para obtener la documentación de referencia completa para la administración de clústeres mediante PowerShell, consulte [Referencia de FailoverCluster](/powershell/module/failoverclusters/?view=win10-ps).

## <a name="using-windows-powershell"></a>Uso de Windows PowerShell

Windows PowerShell se usa para realizar todas las tareas de este artículo. Se recomienda anclar la aplicación a la barra de tareas para mayor comodidad.

Si los siguientes cmdlets no están disponibles en la sesión de PowerShell, es posible que deba agregar el módulo `Failover Cluster` para la característica de Windows PowerShell mediante el siguiente comando de PowerShell: `Add-WindowsFeature RSAT-Clustering-PowerShell`.

> [!NOTE]
> A partir de la actualización de octubre de 2018 de Windows 10, RSAT se incluye como un conjunto de "características a petición" desde Windows 10. Simplemente vaya a **Configuración > Aplicaciones > Aplicaciones y características > Características opcionales > Agregar una característica > RSAT: Herramientas de clústeres de conmutación por error** y seleccione **Instalar**. Para ver el progreso de la instalación, haga clic en el botón Atrás para ver el estado en la página "Administrar características opcionales". La característica instalada se conservará en las diferentes actualizaciones de versión de Windows 10.

## <a name="view-cluster-settings-and-resources"></a>Visualización de la configuración y los recursos del clúster

Obtiene información sobre un clúster denominado Cluster1:

```powershell
Get-Cluster -Name Cluster1
```
Obtiene información sobre uno o varios nodos, o servidores, en Cluster1:

```powershell
Get-ClusterNode -Cluster Cluster1
```

Para ver qué características de Windows están instaladas en un nodo de clúster, use el cmdlet `Get-WindowsFeature`. Por ejemplo:

```powershell
Get-WindowsFeature -ComputerName Server1
```

Para ver los adaptadores de red y sus propiedades como la de nombre, direcciones IPv4 e identificador de VLAN:

```powershell
Get-NetAdapter -CimSession Server1 | Where Status -Eq "Up" | Sort InterfaceAlias | Format-Table Name, InterfaceDescription, Status, LinkSpeed, VLANID, MacAddress
```

Para ver los conmutadores virtuales de Hyper-V y cómo se agrupan los adaptadores de red físicos:

```powershell
Get-VMSwitch -ComputerName Server1
```

Para cambiar los adaptadores de red virtuales del host:

```powershell
Get-VMNetworkAdapter -ComputerName Server1
```

Para ver si Espacios de almacenamiento directo está habilitado:

```powershell
Get-CimSession -ComputerName Server1 | Get-ClusterStorageSpacesDirect
```

## <a name="start-or-stop-a-cluster"></a>Inicio o detención de un clúster

Use los cmdlets `Start-Cluster` y `Stop-Cluster` para agregar o quitar un nodo de servidor para el clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Start-Cluster](/powershell/module/failoverclusters/start-cluster?view=win10-ps) y [Stop-Cluster](/powershell/module/failoverclusters/stop-cluster?view=win10-ps).

Inicia el servicio de clúster en todos los nodos de servidor del clúster en el que aún no se ha iniciado:

```powershell
Start-Cluster -Name Cluster1
```

En este ejemplo se detiene el servicio de clúster en todos los nodos del clúster denominado Cluster1, lo cual detendrá todos los servicios y las aplicaciones configurados en el clúster:

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Incorporación o eliminación de un servidor

Use los cmdlets `Add-ClusterNode` y `Remove-ClusterNode` para agregar o quitar un nodo de servidor para el clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Add-ClusterNode](/powershell/module/failoverclusters/add-clusternode?view=win10-ps) y [Remove-ClusterNode](/powershell/module/failoverclusters/remove-clusternode?view=win10-ps).

En este ejemplo se agrega un servidor denominado Node4 a un clúster denominado Cluster1. Asegúrese primero de que el servidor se ejecute y esté conectado a la red de clústeres.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

En este ejemplo se quita el nodo denominado Node4 del clúster llamado Cluster1:

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="setup-the-cluster-witness"></a>Configuración de un testigo del clúster

Use el cmdlet `Set-ClusterQuorum` para establecer las opciones de testigo del cuórum para el clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Set-ClusterQuorum](/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps).

En este ejemplo se cambia la configuración de cuórum para usar un testigo en la nube en el clúster denominado Cluster1:

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

En este ejemplo se cambia la configuración de cuórum a Mayoría de recurso compartido de archivos y nodo en el clúster denominado Cluster1, mediante el recurso de disco en \\fileserver\fsw para el testigo del recurso compartido de archivos.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>Habilitar Espacios de almacenamiento directo

Usa el cmdlet `Enable-ClusterStorageSpacesDirect` para habilitar Espacios de almacenamiento directo en el clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Enable-ClusterStorageSpacesDirect](/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps).

Este ejemplo habilita Espacios de almacenamiento directo en Server1:

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Configuración de un host de Hyper-V

Use el cmdlet `Set-VMHost` para configurar varias opciones de host de Hyper-V, como las rutas de acceso de VHD y de la máquina virtual, migraciones en vivo, migraciones de almacenamiento, autenticación, expansión de NUMA y otras. Para más ejemplos e información de uso, consulte la documentación de referencia de [Enable-ClusterStorageSpacesDirect](/powershell/module/hyper-v/set-vmhost?view=win10-ps).

En este ejemplo se especifican nuevas ubicaciones predeterminadas para discos duros virtuales y máquinas virtuales en el servidor host denominado Server1:

```powershell
Set-VMHost -ComputerName Server1 -VirtualHardDiskPath "C:\Hyper-V\Virtual Hard Disks" -VirtualMachinePath "C:\Hyper-V\Configuration Files"
```

En este ejemplo se configura el servidor host Server1 para permitir 10 migraciones en vivo simultáneas y migraciones de almacenamiento:

```powershell
Set-VMHost -ComputerName Server1 -MaximumVirtualMachineMigrations 10 -MaximumStorageMigrations 10
```

En este ejemplo se configura el servidor host Server1 para utilizar Kerberos para autenticar las migraciones en vivo entrantes:

```powershell
Set-VMHost -ComputerName Server1 -VirtualMachineMigrationAuthenticationType Kerberos
```

## <a name="remove-cluster-and-resources"></a>Eliminación de clústeres y recursos

Use el cmdlet `Remove-ClusterResource` para eliminar uno o todos los recursos de un clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Remove-ClusterResource](/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps).

> [!NOTE]
> Tendrá que habilitar temporalmente la autenticación del proveedor de servicios de seguridad de credenciales (CredSSP) para eliminar un clúster. Para más información, consulte [Enable-WSManCredSSP](/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

En el siguiente ejemplo se eliminan los recursos de clúster por nombre en el clúster denominado Cluster1:

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

En este ejemplo se quita el clúster denominado Cluster1 por completo con el cmdlet `Remove-Cluster`:

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Pasos siguientes

- Debe validar el clúster después de realizar los cambios. Consulte [Validación de un clúster de Azure Stack HCI](../deploy/validate.md) para obtener más información.
- Aprenda a administrar los clústeres mediante Windows Admin Center. Consulte [Administración de clústeres en Azure Stack HCI mediante Windows Admin Center](cluster.md).
