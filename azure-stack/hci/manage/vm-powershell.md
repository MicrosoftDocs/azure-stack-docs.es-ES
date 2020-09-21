---
title: Administración de máquinas virtuales mediante Windows PowerShell en Azure Stack HCI
description: Administración de máquinas virtuales en Azure Stack HCI mediante Windows PowerShell
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 8d5f654ed5af1fe66c9a28bbfd6bebb09672fa88
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573520"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-powershell"></a>Administración de máquinas virtuales en Azure Stack HCI mediante Windows PowerShell

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

Windows PowerShell se puede usar para crear y administrar máquinas virtuales en Azure Stack HCI.

Normalmente, las máquinas virtuales se administran desde un equipo remoto, en lugar de hacerlo en un servidor host de un clúster. Este equipo remoto se denomina equipo de administración.

> [!NOTE]
> Al ejecutar comandos de PowerShell desde un equipo de administración, incluya el parámetro -ComputerName con el nombre del servidor host que va a administrar. Se permiten los nombres NetBIOS, direcciones IP y nombres de dominio completos.

Para obtener la documentación de referencia completa para la administración de máquinas virtuales mediante PowerShell, consulte [Referencia de Hyper-V](/powershell/module/hyper-v/?view=win10-ps).

## <a name="create-a-vm"></a>Crear una VM  

El cmdlet `New-VM` se usa para crear una máquina virtual. Para obtener información detallada sobre el uso, consulte la documentación de referencia de [New-VM](/powershell/module/hyper-v/new-vm?view=win10-ps).

Esta es la configuración que puede especificar al crear una nueva máquina virtual con un disco duro virtual existente, donde:

- **-Name** es el nombre que se proporciona para la máquina virtual que se va a crear.

- **-MemoryStartupBytes** es la cantidad de memoria que está disponible para la máquina virtual al iniciarse.

- **-BootDevice** es el dispositivo en el que se arranca la máquina virtual cuando se inicia.
 Normalmente, se trata de un disco duro virtual (VHD), un archivo .iso para el arranque basado en DVD o un adaptador de red (NetworkAdapter) para el arranque de red.

- **-VHDPath** es la ruta de acceso al disco de la máquina virtual que desea usar.

- **-Path** es la ruta de acceso para almacenar los archivos de configuración de la máquina virtual.

- **-Generation** es la generación de la máquina virtual. Use la generación 1 para VHD y la generación 2 para VHDX.

- **-Switch** es el nombre del conmutador virtual que desea que la máquina virtual use para conectarse a otras máquinas virtuales o a la red. Obtenga el nombre del conmutador virtual mediante [Get-VMSwitch](/powershell/module/hyper-v/get-vmswitch?view=win10-ps). Por ejemplo:  

El comando completo como se indica a continuación para crear una máquina virtual denominada VM1:

 ```powershell
 New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes <Memory> -BootDevice <BootDevice> -VHDPath <VHDPath> -Path <Path> -Generation <Generation> -Switch <SwitchName>
 ```

En el ejemplo siguiente se crea una máquina virtual de generación 2 con 4 GB de memoria. Se inicia desde la carpeta VMs\Win10.vhdx en el directorio actual y usa el conmutador virtual denominado ExternalSwitch. Los archivos de configuración de la máquina virtual se almacenan en la carpeta VMData.  

``` powershell
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -VHDPath .\VMs\Win10.vhdx -Path .\VMData -Generation 2 -Switch ExternalSwitch
```

Los siguientes parámetros se usan para especificar discos duros virtuales.

Para crear una máquina virtual con un nuevo disco duro virtual, reemplace el parámetro **-VHDPath** del ejemplo anterior por **-NewVHDPath** y agregue el parámetro **-NewVHDSizeBytes** como se indica aquí:  

``` powershell  
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -NewVHDPath .\VMs\Win10.vhdx -Path .\VMData -NewVHDSizeBytes 20GB -Generation 2 -Switch ExternalSwitch  
 ```  

Para crear una máquina virtual con un nuevo disco virtual que arranque en una imagen de sistema operativo, consulte el ejemplo de PowerShell en el [tutorial de creación de una máquina virtual para Hyper-V en Windows 10](/virtualization/hyper-v-on-windows/quick-start/create-virtual-machine).  

## <a name="get-a-list-of-vms"></a>Obtención de una lista de máquinas virtuales

En el ejemplo siguiente se devuelve una lista de todas las máquinas virtuales de Server1.

```powershell
Get-VM -ComputerName Server1
```

En el ejemplo siguiente se devuelve una lista de todas las máquinas virtuales en ejecución en un servidor agregando un filtro mediante el comando `Where-Object`. Para más información, consulte la documentación de [Uso de Where-Object](/previous-versions/windows/it-pro/windows-powershell-1.0/ee177028(v=technet.10)).

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Running"
```

En el ejemplo siguiente se devuelve una lista de todas las máquinas virtuales apagadas del servidor.

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Off"
```

## <a name="start-and-stop-a-vm"></a>Inicio y detención de una máquina virtual

Use los comandos `Start-VM` y `Stop-VM` para iniciar o detener una máquina virtual. Para obtener información detallada, consulte la documentación de referencia de [Start-VM](/powershell/module/hyper-v/start-vm?view=win10-ps) y [Stop-VM](/powershell/module/hyper-v/stop-vm?view=win10-ps).

En el ejemplo siguiente se muestra cómo iniciar una máquina virtual denominada VM1:

```powershell
Start-VM -Name VM1 -ComputerName Server1
```

En el ejemplo siguiente se muestra cómo apagar una máquina virtual denominada TestVM:

```powershell
Stop-VM -Name VM1 -ComputerName Server1
```

## <a name="move-a-vm"></a>Traslado de una máquina virtual

El cmdlet `Move-VM` mueve una máquina virtual a otro servidor. Para más información, consulte la documentación de referencia de [Move-VM](/powershell/module/hyper-v/move-vm?view=win10-ps).

 En el ejemplo siguiente se muestra cómo trasladar una máquina virtual a Server2 cuando la máquina virtual está almacenada en un recurso compartido de SMB de Server1:

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2
 ```

En el ejemplo siguiente se muestra cómo trasladar una máquina virtual a Server2 desde Server1 y cómo trasladar todos los archivos asociados con la máquina virtual a D:\VM_nombreEnElEquipoRemoto:

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2 -IncludeStorage -DestinationStoragePath D:\VM_name
```

## <a name="import-or-export-a-vm"></a>Importación o exportación de una máquina virtual

Los cmdlets `Import-VM` y `Export-VM` permiten importar y exportar una máquina virtual. A continuación se muestran un par de ejemplos. Para más información, consulte la documentación de referencia de [Import-VM](/powershell/module/hyper-v/import-vm?view=win10-ps) y [Export-VM](/powershell/module/hyper-v/export-vm?view=win10-ps).

El siguiente ejemplo muestra cómo importar una máquina virtual desde su archivo de configuración. La máquina virtual se registra en contexto, por lo que no se copian sus archivos:

```powershell
Import-VM -ComputerName Server1 -Name VM1 -Path 'C:\<vm export path>\2B91FEB3-F1E0-4FFF-B8BE-29CED892A95A.vmcx'
```

En el ejemplo siguiente se exporta una máquina virtual a la raíz de la unidad D:

```powershell
Export-VM -ComputerName Server1 -Name VM1 -Path D:\
```

## <a name="rename-a-vm"></a>Cambio de nombre de una máquina virtual

Se usa el cmdlet `Rename-VM` para cambiar el nombre de una máquina virtual. Para obtener información detallada, consulte la documentación de referencia de [Rename-VM](/powershell/module/hyper-v/rename-vm?view=win10-ps).

En el ejemplo siguiente se cambia el nombre de VM1 a VM2 y se muestra la máquina virtual con el nombre cambiado:

```powershell
Rename-VM -ComputerName Server1 -Name VM1 -NewName VM2
```

## <a name="create-a-vm-checkpoint"></a>Creación de un punto de comprobación de la máquina virtual

El cmdlet `Checkpoint-VM` se usa para crear un punto de comprobación para una máquina virtual. Para obtener información detallada, consulte la documentación de referencia de [Checkpoint-VM](/powershell/module/hyper-v/checkpoint-vm?view=win10-ps).

En el ejemplo siguiente se crea un punto de comprobación denominado BeforeInstallingUpdates para la máquina virtual denominada Test.

```powershell
Checkpoint-VM -ComputerName Server1 -Name VM1 -SnapshotName BeforeInstallingUpdates
```

## <a name="create-a-vhd-for-a-vm"></a>Creación de un disco duro virtual para una máquina virtual

El cmdlet `New-VHD` se usa para crear un nuevo disco duro virtual para una máquina virtual. Para obtener información detallada sobre cómo usarlo, consulte la documentación de referencia de [New-VHD](/powershell/module/hyper-v/new-vhd?view=win10-ps).

En el siguiente ejemplo se crea un disco duro virtual dinámico con formato VHDX y 10 GB de tamaño. La extensión de nombre de archivo determina el formato y se usa el tipo predeterminado dinámico porque no se ha especificado ningún tipo.

```powershell
New-VHD -ComputerName Server1 -Name VM1 -Path c:\Base.vhdx -SizeBytes 10GB
```

## <a name="add-a-network-adapter-to-a-vm"></a>Incorporación de un adaptador de red a una máquina virtual

El cmdlet `Add-VMNetworkAdapter` se usa para agregar un adaptador de red virtual a una máquina virtual. A continuación se muestran un par de ejemplos. Para obtener información detallada sobre cómo usarlo, consulte la documentación de referencia de [Add-VMNetworkAdapter](/powershell/module/hyper-v/add-vmnetworkadapter?view=win10-ps).

En el ejemplo siguiente se agrega un adaptador de red virtual denominado Redmond NIC1 a una máquina virtual denominada VM1:

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -Name "Redmond NIC1"
```

En este ejemplo se agrega un adaptador de red virtual a una máquina virtual denominada VM1 y esta se conecta a un conmutador virtual denominado Network:

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -SwitchName Network
```

## <a name="create-a-virtual-switch-for-a-vm"></a>Creación de un conmutador virtual para una máquina virtual

El cmdlet `New-VMSwitch` se usa para el nuevo conmutador virtual en un host de máquina virtual. Para obtener información detallada sobre cómo usarlo, consulte la documentación de referencia de [New-VMSwitch](/powershell/module/hyper-v/new-vmswitch?view=win10-ps).

En el ejemplo siguiente se crea un nuevo conmutador denominado "QoS switch", que se enlaza a un adaptador de red denominado Wired Ethernet Connection 3 y admite ancho de banda mínimo basado en el peso.

```powershell
New-VMSwitch "QoS Switch" -NetAdapterName "Wired Ethernet Connection 3" -MinimumBandwidthMode Weight
```

## <a name="set-memory-for-a-vm"></a>Establecimiento de memoria para una máquina virtual

El cmdlet `Set-VMMemory` se usa para configurar la memoria de una máquina virtual. Para obtener información detallada sobre cómo usarlo, consulte la documentación de referencia de [Set-VMMemory](/powershell/module/hyper-v/set-vmmemory?view=win10-ps).

En el ejemplo siguiente se habilita la memoria dinámica en una máquina virtual denominada VM1, se establece la memoria mínima, de inicio y máxima, su prioridad de memoria y su búfer.

```powershell
Set-VMMemory -ComputerName Server1 -Name VM1 -DynamicMemoryEnabled $true -MinimumBytes 64MB -StartupBytes 256MB -MaximumBytes 2GB -Priority 80 -Buffer 25
```

## <a name="set-virtual-processors-for-a-vm"></a>Establecimiento de procesadores virtuales para una máquina virtual

El cmdlet `Set-VMProcessor` se usa para configurar los procesadores virtuales para una máquina virtual. Para obtener información detallada sobre cómo usarlo, consulte la documentación de referencia de [Set-VMProcessor](/powershell/module/hyper-v/set-vmprocessor?view=win10-ps).

En el ejemplo siguiente se configura una máquina virtual denominada VM1 con dos procesadores virtuales, una reserva del 10 %, un límite del 75 % y un peso relativo de 200.

```powershell
Set-VMProcessor -ComputerName Server1 -Name VM1 -Count 2 -Reserve 10 -Maximum 75 -RelativeWeight 200
```

## <a name="next-steps"></a>Pasos siguientes  

También puede crear y administrar máquinas virtuales mediante Windows Admin Center. Para más información, consulte [Windows Admin Center](vm.md).
