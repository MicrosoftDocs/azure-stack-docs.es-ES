---
title: Diferencias y consideraciones entre discos administrados e imágenes administradas en Azure Stack | Microsoft Docs
description: Obtenga información sobre las diferencias y consideraciones al trabajar con discos administrados en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: d32d7d8d484d6e9819440fc9261bae52d6395ae2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985911"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Discos administrados de Azure Stack: diferencias y consideraciones

En este artículo se resumen las diferencias conocidas entre los [discos administrados de Azure Stack](azure-stack-manage-vm-disks.md) y los [discos administrados para Azure](/azure/virtual-machines/windows/managed-disks-overview). Para obtener información acerca de las diferencias de alto nivel entre Azure y Azure Stack, consulte el artículo [Key considerations](azure-stack-considerations.md) (Consideraciones clave).

Los discos administrados simplifican la administración de discos para las máquinas virtuales de IaaS, ya que administran las [cuentas de almacenamiento](../operator/azure-stack-manage-storage-accounts.md) asociadas a los discos de la máquina virtual.

> [!Note]  
> Los discos administrados en Azure Stack están disponible desde la actualización 1808. Está habilitado de forma predeterminada al crear máquinas virtuales mediante el portal de Azure Stack a partir de la actualización 1811.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Hoja de referencia rápida: diferencias entre los discos administrados

| Característica | Azure (global) | Azure Stack |
| --- | --- | --- |
|Cifrado de datos en reposo |Storage Service Encryption (SSE) de Azure, Azure Disk Encryption (ADE)     |Cifrado AES de 128 bits de BitLocker      |
|Imagen          | Compatibilidad con la imagen personalizada administrada |Compatible|
|Opciones de copia de seguridad |Compatibilidad con el servicio Azure Backup |Todavía no se admite |
|Opciones de recuperación ante desastres |Compatibilidad con Azure Site Recovery |Todavía no se admite|
|Tipos de disco     |SSD Premium, SSD estándar y HDD estándar |SSD Premium, HDD estándar |
|Discos premium  |Totalmente compatible |Se pueden aprovisionar, pero no hay límite de rendimiento o garantía  |
|E/S por segundo de discos premium  |Depende del tamaño del disco  |2300 E/S por segundo por disco |
|Rendimiento de discos premium |Depende del tamaño del disco |145 MB/segundo por disco |
|Tamaño del disco  |Disco Premium de Azure: P4 (32 GiB) a P80 (32 TiB)<br>Disco SSD estándar de Azure: E10 (128 GiB) a E80 (32 TiB)<br>Disco HDD estándar de Azure: S4 (32 GiB) a S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GB<br>M15: 256 GiB<br>M20: 512 GB<br>M30: 1024 GiB |
|Copia de instantáneas de discos|Discos administrados de Azure de instantáneas conectados a una máquina virtual en ejecución compatible|Todavía no se admite |
|Análisis de rendimiento de discos |Compatibilidad con métricas agregadas y por disco |Todavía no se admite |
|Migración      |Proporciona herramientas para migrar desde máquinas virtuales de Azure Resource Manager no administradas existentes sin necesidad de volver a crear la máquina virtual  |Todavía no se admite |

> [!NOTE]  
> Las E/S por segundo y el rendimiento de los discos administrados en Azure Stack son números extremos, en lugar de un número aprovisionado, que pueden resultar afectados por el hardware y las cargas de trabajo que se ejecutan en Azure Stack.

## <a name="metrics"></a>Métricas

También hay diferencias en las métricas de almacenamiento:

- Con Azure Stack, los datos de transacción de las métricas de almacenamiento no distinguen el ancho de banda de red interna ni externa.
- Los datos de transacción de Azure Stack en las métricas de almacenamiento no incluyen el acceso de la máquina virtual a los discos montados.

## <a name="api-versions"></a>Versiones de API

Los discos administrados de Azure Stack admiten las versiones de API siguientes:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Conversión en discos administrados

Puede usar el script siguiente para convertir una VM aprovisionada actualmente de discos no administrados a administrados. Reemplace los marcadores de posición por sus propios valores:

```powershell
$subscriptionId = 'subid'

# The name of your resource group where your VM to be converted exists
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM's configuration (network config, vm name, location).
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Delete old VM, but keep the OS disk
Remove-AzureRmVm -Name $virtualMachineName -ResourceGroupName $resourceGroupName

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Imágenes administradas

Azure Stack admite las *imágenes administradas*, que le permiten crear un objeto de imagen administrada en una VM generalizada (tanto administrada como no administrada) que, de aquí en adelante, solo puede crear VM de discos administrados. Las imágenes administradas permiten los dos escenarios siguientes:

- Tiene VM no administradas generalizadas y quiere usar discos administrados a partir de ahora.
- Tiene una VM administrada generalizada y quiere crear varias VM administradas similares.

### <a name="step-1-generalize-the-vm"></a>Paso 1: Generalización de la máquina virtual

Para Windows, siga la sección [Generalización de VM con Windows mediante Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep). Para Linux, siga el paso 1 [aquí](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Asegúrese de generalizar la máquina virtual. La creación de una máquina virtual a partir de una imagen que no se ha generalizado correctamente provocará un error **VMProvisioningTimeout**.

### <a name="step-2-create-the-managed-image"></a>Paso 2: Creación de la imagen administrada

Puede usar el portal, PowerShell o CLI para crear la imagen administrada. Siga los pasos descritos en el artículo de Azure [aquí](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>Paso 3: Elección del caso de uso

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Caso 1: Migración de VM a discos administrados

Asegúrese de generalizar la máquina virtual correctamente antes de realizar este paso. Después de la generalización, ya no puede utilizar esta máquina virtual. La creación de una máquina virtual a partir de una imagen que no se ha generalizado correctamente provocará un error **VMProvisioningTimeout**.

Siga las instrucciones que se indican [aquí](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vhd-in-a-storage-account) para crear una imagen administrada a partir de un VHD generalizado en una cuenta de almacenamiento. Puede utilizar esta imagen en el futuro para crear máquinas virtuales gestionadas.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Caso 2: Creación de una máquina virtual administrada a partir de una imagen administrada mediante Powershell

Después de crear una imagen a partir de una máquina virtual de disco administrado existente mediante el script que se indica [aquí](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell), el siguiente script de ejemplo crea una máquina virtual Linux similar a partir de un objeto de imagen existente:

Módulo de PowerShell de Azure Stack 1.7.0 o posterior: siga las instrucciones [aquí](/azure/virtual-machines/windows/create-vm-generalized-managed).

Módulo de PowerShell de Azure Stack versión 1.6.0 o anterior:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

También puede usar el portal para crear una máquina virtual desde una imagen administrada. Para obtener más información, consulte los artículos de imagen administrada de Azure [Captura de una imagen administrada de una máquina virtual generalizada en Azure](/azure/virtual-machines/windows/capture-image-resource) y [Creación de una máquina virtual a partir de una imagen administrada](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Configuración

Después de aplicar la actualización 1808 o posterior, debe realizar la siguiente configuración antes de usar los discos administrados:

- Si una suscripción se ha creado antes de la actualización 1808, siga estos pasos para actualizar la suscripción. En caso contrario, la implementación de máquinas virtuales en esta suscripción podría producir un error con un mensaje del tipo "Error interno en el administrador de discos".
   1. En el portal del inquilino, vaya a **Suscripciones** y busque la suscripción. Haga clic en **Proveedores de recursos**, después en **Microsoft.Compute** y luego en **Volver a registrar**.
   2. En la misma suscripción vaya a **Control de acceso (IAM)** y compruebe que **Azure Stack – Managed Disk** (Azure Stack - Disco administrado) aparece en la lista.
- Si usa un entorno de varios inquilinos, pida a su operador de nube (puede ser de su propia organización o del proveedor de servicios) que vuelva a configurar cada uno de los directorios de invitado siguiendo los pasos que se indican en [este artículo](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). En caso contrario, la implementación de VM en una suscripción asociada con ese directorio de invitado podría producir un error con un mensaje de error "Error interno en el administrador de discos".

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre máquinas virtuales de Azure Stack](azure-stack-compute-overview.md)
