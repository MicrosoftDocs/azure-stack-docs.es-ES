---
title: 'Discos administrados de Azure Stack Hub: Diferencias y consideraciones'
description: Obtenga información sobre las diferencias y consideraciones a la hora de trabajar con discos administrados en Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 1abee3556ac45791831c6dcb9fc8dce05477e51e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "80479306"
---
# <a name="azure-stack-hub-managed-disks-differences-and-considerations"></a>Discos administrados de Azure Stack Hub: Diferencias y consideraciones

En este artículo se resumen las diferencias entre los [*discos administrados* de Azure Stack Hub](azure-stack-manage-vm-disks.md) y los [discos administrados de Azure](/azure/virtual-machines/windows/managed-disks-overview). Para más información acerca de las diferencias de alto nivel entre Azure Stack Hub y Azure, consulte el artículo [Consideraciones clave](azure-stack-considerations.md).

Los discos administrados simplifican la administración de discos para las máquinas virtuales (VM) de IaaS, ya que administran las [cuentas de almacenamiento](../operator/azure-stack-manage-storage-accounts.md) asociadas a los discos de las máquinas virtuales.

> [!NOTE]  
> Los discos administrados en Azure Stack Hub están disponibles a partir de la actualización 1808. A partir de la actualización 1811, la característica está habilitada de forma predeterminada al crear máquinas virtuales mediante el portal de Azure Stack Hub.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Hoja de referencia rápida: diferencias entre los discos administrados

| Característica | Azure (global) | Azure Stack Hub |
| --- | --- | --- |
|Cifrado de datos en reposo |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE).     |Cifrado AES de 128 bits de BitLocker      |
|Imagen          | Imagen personalizada administrada |Compatible|
|Opciones de copia de seguridad | Servicio Azure Backup |Todavía no se admite |
|Opciones de recuperación ante desastres | Azure Site Recovery |Todavía no se admite|
|Tipos de disco     |SSD Premium, SSD estándar y HDD estándar. |SSD Premium, HDD estándar |
|Discos premium  |Totalmente compatible. |Se pueden aprovisionar, pero no hay límite de rendimiento o garantía  |
|E/S por segundo de discos premium  |Depende del tamaño del disco.  |2300 E/S por segundo por disco |
|Rendimiento de discos premium |Depende del tamaño del disco. |145 MB/segundo por disco |
|Tamaño del disco  |Disco Premium de Azure: P4 (32 GiB) a P80 (32 TiB)<br>Disco SSD estándar de Azure: E10 (128 GiB) a E80 (32 TiB)<br>Disco HDD estándar de Azure: S4 (32 GiB) a S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GB<br>M15: 256 GiB<br>M20: 512 GB<br>M30: 1023 GiB |
|Copia de instantáneas de discos|Discos administrados de Azure de instantáneas conectados a una máquina virtual en ejecución compatible.|Todavía no se admite |
|Análisis de rendimiento de discos |Compatibilidad con métricas agregadas y por disco. |Todavía no se admite |
|Migración      |Proporciona herramientas para migrar desde máquinas virtuales de Azure Resource Manager no administradas existentes sin necesidad de volver a crear la máquina virtual.  |Todavía no se admite |

> [!NOTE]  
> Las E/S por segundo y el rendimiento de los discos administrados en Azure Stack Hub son números extremos, en lugar de un número aprovisionado, que pueden resultar afectados por el hardware y las cargas de trabajo que se ejecutan en Azure Stack Hub.

## <a name="metrics"></a>Métricas

También hay diferencias en las métricas de almacenamiento:

- Con Azure Stack Hub, los datos de transacción de las métricas de almacenamiento no distinguen el ancho de banda de red interna ni externa.
- Los datos de transacción de Azure Stack Hub en las métricas de almacenamiento no incluyen el acceso de la máquina virtual a los discos montados.

## <a name="api-versions"></a>Versiones de API

Los discos administrados de Azure Stack Hub admiten las versiones de API siguientes:

- 2017-03-30 
- 2017-12-01 (Solo imágenes administradas, sin discos, sin instantáneas)

## <a name="convert-to-managed-disks"></a>Conversión en discos administrados

> [!NOTE]  
> El cmdlet de Azure PowerShell **ConvertTo-AzureRmVMManagedDisk** no se puede usar para convertir un disco no administrado en un disco administrado en Azure Stack Hub. Actualmente, Azure Stack Hub no admite este cmdlet.

Puede usar el script siguiente para convertir una VM aprovisionada actualmente de discos no administrados a administrados. Reemplace los marcadores de posición por sus propios valores:

```powershell
$SubscriptionId = "SubId"

# The name of your resource group where your VM to be converted exists.
$ResourceGroupName ="MyResourceGroup"

# The name of the managed disk to be created.
$DiskName = "mngddisk"

# The size of the disks in GB. It should be greater than the VHD file size.
$DiskSize = "50"

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$VhdUri = "https://rgmgddisks347.blob.local.azurestack.external/vhds/unmngdvm20181109013817.vhd"

# The storage type for the managed disk: PremiumLRS or StandardLRS.
$AccountType = "StandardLRS"

# The Azure Stack Hub location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzureRmVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzureRmDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzureRmDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzureRmDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzureRmPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzureRmVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```

## <a name="managed-images"></a>Imágenes administradas

Azure Stack Hub admite *imágenes administradas* que le permiten crear un objeto de imagen administrada en una máquina virtual generalizada (tanto administrada como no administrada) que, de aquí en adelante, solo puede crear máquinas virtuales de discos administrados. Las imágenes administradas permiten los dos escenarios siguientes:

- Tiene VM no administradas generalizadas y quiere usar discos administrados a partir de ahora.
- Tiene una VM administrada generalizada y quiere crear varias VM administradas similares.

### <a name="step-1-generalize-the-vm"></a>Paso 1: Generalización de la máquina virtual

Para Windows, siga la sección [Generalización de VM con Windows mediante Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep). Para Linux, siga el paso 1 [aquí](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Asegúrese de generalizar la máquina virtual. La creación de una máquina virtual a partir de una imagen que no se ha generalizado correctamente puede producir un error **VMProvisioningTimeout**.

### <a name="step-2-create-the-managed-image"></a>Paso 2: Creación de la imagen administrada

Puede usar el portal, PowerShell o la CLI para crear la imagen administrada. Siga los pasos que se indican en el tema sobre la [creación de una imagen administrada](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>Paso 3: Elección del caso de uso

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Caso 1: Migración de VM a discos administrados

Asegúrese de generalizar la máquina virtual correctamente antes de realizar este paso. Después de la generalización, ya no puede utilizar esta máquina virtual. La creación de una máquina virtual a partir de una imagen que no se ha generalizado correctamente provocará un error **VMProvisioningTimeout**.

Siga las instrucciones de [Creación de una imagen a partir de una máquina virtual que usa una cuenta de almacenamiento](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vm-that-uses-a-storage-account) para crear una imagen administrada a partir de un VHD generalizado en una cuenta de almacenamiento. Puede usar esta imagen en el futuro para crear máquinas virtuales administradas.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Caso 2: Creación de una máquina virtual administrada a partir de una imagen administrada mediante Powershell

Después de crear una imagen a partir de una máquina virtual de disco administrado existente mediante el script de [Creación de una imagen a partir de un disco administrado mediante PowerShell](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell), el siguiente script de ejemplo crea una máquina virtual Linux similar a partir de un objeto de imagen existente.

Módulo de PowerShell de Azure Stack Hub versión 1.7.0 o posterior: Siga las instrucciones de [Creación de una máquina virtual a partir de una imagen administrada](/azure/virtual-machines/windows/create-vm-generalized-managed).

Módulo de PowerShell de Azure Stack Hub versión 1.6.0 o anterior:

```powershell
# Variables for common values
$ResourceGroupName = "MyResourceGroup"
$Location = "local"
$VirtualMachineName = "MyVM"
$ImageRG = "managedlinuxrg"
$ImageName = "simplelinuxvmm-image-2019122"

# Create credential object
$Cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleRDP"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzureRmNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzureRmImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzureRmVMSourceImage -Id $Image.Id | `
Add-AzureRmVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```

También puede usar el portal para crear una máquina virtual desde una imagen administrada. Para obtener más información, consulte los artículos de imagen administrada de Azure [Creación de una imagen administrada de una máquina virtual generalizada en Azure](/azure/virtual-machines/windows/capture-image-resource) y [Creación de una máquina virtual a partir de una imagen administrada](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Configuración

Después de aplicar la actualización 1808 o posterior, debe realizar el siguiente cambio en la configuración antes de usar los discos administrados:

- Si una suscripción se ha creado antes de la actualización 1808, siga estos pasos para actualizar la suscripción. En caso contrario, la implementación de máquinas virtuales en esta suscripción podría producir un error con un mensaje del tipo "Error interno en el administrador de discos".
   1. En el portal de usuarios de Azure Stack Hub, vaya a **Suscripciones** y busque la suscripción. Haga clic en **Proveedores de recursos**, después en **Microsoft.Compute** y luego en **Volver a registrar**.
   2. En la misma suscripción, vaya a **Control de acceso (IAM)** y compruebe que **Azure Stack Hub – Managed Disk** (Azure Stack Hub - Disco administrado) aparece en la lista.
- Si usa un entorno de varios inquilinos, pida a su operador de nube (puede ser de su propia organización o del proveedor de servicios) que vuelva a configurar cada uno de los directorios de invitado siguiendo los pasos que se indican en [este artículo](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-hub-with-the-guest-directory). En caso contrario, la implementación de máquinas virtuales en una suscripción asociada con ese directorio de invitado podría producir un error con el mensaje "Error interno en el administrador de discos".

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre máquinas virtuales de Azure Stack Hub](azure-stack-compute-overview.md).
