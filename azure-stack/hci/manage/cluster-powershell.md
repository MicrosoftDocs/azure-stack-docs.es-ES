---
title: Administración de clústeres de Azure Stack HCI mediante PowerShell
description: Aprenda a administrar clústeres en Azure Stack HCI mediante PowerShell.
author: v-dasis
ms.topic: article
ms.date: 05/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 922a6188c1529d3461288ac28ff542db6fa9a527
ms.sourcegitcommit: f2203f070c17e19ba1a41d681662bac3ab50ed12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2020
ms.locfileid: "83855707"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Administración de clústeres de Azure Stack HCI mediante PowerShell

> Se aplica solo a Windows Server 2019

Windows PowerShell se puede usar para administrar recursos y configurar características en los clústeres de Azure Stack HCI.

Normalmente, los clústeres se administran desde un equipo remoto que ejecuta Windows 10, en lugar de hacerlo en un servidor host de un clúster. Este equipo remoto se denomina equipo de administración.

> [!NOTE]
> Al ejecutar comandos de PowerShell desde un equipo de administración, incluya el parámetro `-Name` o `-Cluster` con el nombre del clúster que va a administrar. Además, tendrá que especificar el nombre de dominio completo (FQDN) al usar el parámetro `-ComputerName` para un nodo de servidor.

Para obtener la documentación de referencia completa para la administración de clústeres mediante PowerShell, consulte [Referencia de FailoverCluster](https://docs.microsoft.com/powershell/module/failoverclusters/?view=win10-ps).

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

Use los cmdlets `Start-Cluster` y `Stop-Cluster` para agregar o quitar un nodo de servidor para el clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Start-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/start-cluster?view=win10-ps) y [Stop-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/stop-cluster?view=win10-ps).

Inicia el servicio de clúster en todos los nodos de servidor del clúster en el que aún no se ha iniciado:

```powershell
Start-Cluster -Name Cluster1
```

En este ejemplo se detiene el servicio de clúster en todos los nodos del clúster denominado Cluster1, lo cual detendrá todos los servicios y las aplicaciones configurados en el clúster:

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Incorporación o eliminación de un servidor

Use los cmdlets `Add-ClusterNode` y `Remove-ClusterNode` para agregar o quitar un nodo de servidor para el clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Add-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode?view=win10-ps) y [Remove-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode?view=win10-ps).

En este ejemplo se agrega un servidor denominado Node4 a un clúster denominado Cluster1. Asegúrese primero de que el servidor esté encendido y conectado a la red de clústeres.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

En este ejemplo se quita el nodo denominado Node4 del clúster llamado Cluster1:

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="set-quorum-options"></a>Establecimiento de opciones de cuórum

Use el cmdlet `Set-ClusterQuorum` para establecer las opciones de cuórum para el clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Set-ClusterQuorum](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps).

En este ejemplo se cambia la configuración de cuórum para usar un testigo en la nube en el clúster denominado Cluster1:

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

En este ejemplo se cambia la configuración de cuórum a Mayoría de recurso compartido de archivos y nodo en el clúster denominado Cluster1, mediante el recurso de disco en \\fileserver\fsw para el testigo del recurso compartido de archivos.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>Habilitar Espacios de almacenamiento directo

Usa el cmdlet `Enable-ClusterStorageSpacesDirect` para habilitar Espacios de almacenamiento directo en el clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps).

Este ejemplo habilita Espacios de almacenamiento directo en Server1:

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Configuración de un host de Hyper-V

Use el cmdlet `Set-VMHost` para configurar varias opciones de host de Hyper-V, como las rutas de acceso de VHD y de la máquina virtual, migraciones en vivo, migraciones de almacenamiento, autenticación, expansión de NUMA y otras. Para más ejemplos e información de uso, consulte la documentación de referencia de [Enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/hyper-v/Set-VMHost?view=win10-ps).

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

## <a name="validate-a-cluster"></a>Validación de un clúster

Use el cmdlet `Test-Cluster` para ejecutar pruebas de validación en un clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Test-Cluster](https://docs.microsoft.com/powershell/module/failoverclusters/test-cluster?view=win10-ps).

En este ejemplo se ejecutan todas las pruebas de validación de clúster aplicables en un clúster denominado Cluster1:

```powershell
Test-Cluster -Cluster Cluster1
```

En este ejemplo se enumeran los nombres de todas las pruebas y categorías de la validación de clústeres. Especifique estos nombres de prueba con los parámetros *Ignore* o *Include* para ejecutar pruebas específicas:


```powershell
Test-Cluster -Cluster Cluster1 -List
Category                                DisplayName                             Description
--------                                -----------                             -----------
Cluster Configuration                   List Cluster Core Groups                List information about the available...
Cluster Configuration                   List Cluster Network Information        List cluster-specific network settin...
Cluster Configuration                   List Cluster Resources                  List the resources that are configur...
Cluster Configuration                   List Cluster Volumes                    List information for the volumes in ...
Cluster Configuration                   List Clustered Roles                    List information about clustered roles.
Cluster Configuration                   Validate Quorum Configuration           Validate that the current quorum con...
Cluster Configuration                   Validate Resource Status                Validate that cluster resources are ...
Cluster Configuration                   Validate Service Principal Name         Validate that a Service Principal Na...
Cluster Configuration                   Validate Volume Consistency             If any volumes are flagged as incons...
Hyper-V Configuration                   List Information About Servers Runni... List Hyper-V related information on ...
Hyper-V Configuration                   Validate Compatibility of Virtual Fi... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Memory Resource Poo... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Network Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Processor Resource ... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Role Installed         Validate that all the nodes have the...
Hyper-V Configuration                   Validate Hyper-V Storage Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Matching Processor Manufact... Validate that all specified nodes sh...
Hyper-V Configuration                   List Hyper-V Virtual Machine Informa... List Hyper-V virtual machine informa...
Hyper-V Configuration                   Validate Hyper-V Integration Service... Validate that the Hyper-V integratio...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Net... Validate that all virtual machines o...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Sto... Validate that all virtual machines o...
Inventory                               List Fibre Channel Host Bus Adapters    List Fibre Channel host bus adapters...
Inventory                               List iSCSI Host Bus Adapters            List iSCSI host bus adapters on each...
Inventory                               List SAS Host Bus Adapters              List Serial Attached SCSI (SAS) host...
Inventory                               List BIOS Information                   List BIOS information from each node...
Inventory                               List Environment Variables              List environment variables set on ea...
Inventory                               List Memory Information                 List memory information for each node.
Inventory                               List Operating System Information       List information about the operating...
Inventory                               List Plug and Play Devices              List Plug and Play devices on each n...
Inventory                               List Running Processes                  List the running processes on each n...
Inventory                               List Services Information               List information about the services ...
Inventory                               List Software Updates                   List software updates that have been...
Inventory                               List System Drivers                     List the system drivers on each node.
Inventory                               List System Information                 List system information such as comp...
Inventory                               List Unsigned Drivers                   List the unsigned drivers on each node.
Network                                 List Network Binding Order              List the order in which networks are...
Network                                 Validate Cluster Network Configuration  Validate the cluster networks that w...
Network                                 Validate IP Configuration               Validate that IP addresses are uniqu...
Network                                 Validate Multiple Subnet Properties     For clusters using multiple subnets,...
Network                                 Validate Network Communication          Validate that servers can communicat...
Network                                 Validate Windows Firewall Configuration Validate that the Windows Firewall i...
Storage                                 List Disks                              List all disks visible to one or mor...
Storage                                 List Potential Cluster Disks            List disks that will be validated fo...
Storage                                 Validate CSV Network Bindings           Validate that network bindings requi...
Storage                                 Validate CSV Settings                   Validate that settings and configura...
Storage                                 Validate Disk Access Latency            Validate acceptable latency for disk...
Storage                                 Validate Disk Arbitration               Validate that a node that owns a dis...
Storage                                 Validate Disk Failover                  Validate that a disk can fail over s...
Storage                                 Validate File System                    Validate that the file system on dis...
Storage                                 Validate Microsoft MPIO-based disks     Validate that disks that use Microso...
Storage                                 Validate Multiple Arbitration           Validate that in a multiple-node arb...
Storage                                 Validate SCSI device Vital Product D... Validate uniqueness of inquiry data ...
Storage                                 Validate SCSI-3 Persistent Reservation  Validate that storage supports the S...
Storage                                 Validate Simultaneous Failover          Validate that disks can fail over si...
System Configuration                    Validate Active Directory Configuration Validate that all the nodes have the...
System Configuration                    Validate All Drivers Signed             Validate that tested servers contain...
System Configuration                    Validate Cluster Service and Driver ... Validate startup settings used by se...
System Configuration                    Validate Memory Dump Settings           Validate that none of the nodes curr...
System Configuration                    Validate Operating System Edition       Validate that all tested servers are...
System Configuration                    Validate Operating System Installati... Validate that the operating systems ...
System Configuration                    Validate Operating System Version       Validate that the operating systems ...
System Configuration                    Validate Required Services              Validate that services required for ...
System Configuration                    Validate Same Processor Architecture    Validate that all servers run as 64-...
System Configuration                    Validate Service Pack Levels            Validate that all servers with same ...
System Configuration                    Validate Software Update Levels         Validate that all tested servers hav...
System Configuration                    Validate System Drive Variable          Validate that all nodes have the sam...
```

## <a name="remove-cluster-and-resources"></a>Eliminación de clústeres y recursos

Use el cmdlet `Remove-ClusterResource` para eliminar uno o todos los recursos de un clúster. Para más ejemplos e información de uso, consulte la documentación de referencia de [Remove-ClusterResource](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps).

> [!NOTE]
> Tendrá que habilitar temporalmente la autenticación del proveedor de servicios de seguridad de credenciales (CredSSP) para eliminar un clúster. Para más información, consulte [Enable-WSManCredSSP](https://docs.microsoft.com/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

En el siguiente ejemplo se eliminan los recursos de clúster por nombre en el clúster denominado Cluster1:

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

En este ejemplo se quita el clúster denominado Cluster1 por completo con el cmdlet `Remove-Cluster`:

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a administrar los clústeres mediante Windows Admin Center. Consulte [Administración de clústeres en Azure Stack HCI mediante Windows Admin Center](cluster.md).