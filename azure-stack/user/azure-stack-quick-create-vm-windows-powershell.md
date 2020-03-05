---
title: Creación de una máquina virtual con Windows Server mediante PowerShell en Azure Stack Hub
description: Cree una máquina virtual con Windows Server mediante PowerShell en Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 14192029c2bbac427b6609abfc62b06e0ef937cb
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703747"
---
# <a name="quickstart-create-a-windows-server-vm-by-using-powershell-in-azure-stack-hub"></a>Inicio rápido: Creación de una máquina virtual con Windows Server mediante PowerShell en Azure Stack Hub

Puede crear una máquina virtual Windows Server 2016 mediante el uso de PowerShell de Azure Stack Hub. Siga los pasos descritos en este artículo para crear y usar una máquina virtual. En este artículo también se proporcionan los pasos para:

* Conectarse a la máquina virtual con un cliente remoto.
* Instalar al servidor web IIS y ver la página principal predeterminada.
* Realizar la limpieza de los recursos.

> [!NOTE]
>  Puede seguir los pasos descritos en este artículo desde el Kit de desarrollo de Azure Stack o desde un cliente externo basado en Windows, si se conecta a través de VPN.

## <a name="prerequisites-for-windows-server-vm"></a>Requisitos previos para la máquina virtual de Windows Server

* Asegúrese de que el operador de Azure Stack Hub haya agregado la imagen **Windows Server 2016** a Marketplace de Azure Stack Hub.

* Azure Stack Hub necesita una versión específica de Azure PowerShell para crear y administrar los recursos. Si no tiene PowerShell configurado para Azure Stack Hub, siga estos pasos para [instalar](../operator/azure-stack-powershell-install.md) PowerShell.

* Con PowerShell configurado para Azure Stack Hub, debe conectarse al entorno de Azure Stack Hub. Para obtener instrucciones, consulte [Configuración del entorno de PowerShell del usuario de Azure Stack Hub](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure Stack Hub. Desde el kit de desarrollo o el sistema integrado de Azure Stack Hub, ejecute el siguiente bloque de código para crear un grupo de recursos. 

> [!NOTE]
> Se asignan valores para todas las variables en los ejemplos de código. Sin embargo, puede asignar nuevos valores si lo desea.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Creación de recursos de almacenamiento

Cree una cuenta de almacenamiento para almacenar la salida de los diagnósticos de arranque.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```

## <a name="create-networking-resources"></a>Creación de los recursos de red principales

Cree una red virtual, una subred, una dirección IP pública. Estos recursos se utilizan para proporcionar conectividad de red a la máquina virtual.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Creación de un grupo de seguridad de red y una regla de grupo de seguridad de red

El grupo de seguridad de red protege la máquina virtual con reglas de entrada y de salida. Se creará una regla de entrada para el puerto 3389 para permitir las conexiones entrantes de Escritorio remoto y una regla de entrada para el puerto 80 que permita el tráfico web entrante.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-vm"></a>Creación de una tarjeta de red para la máquina virtual

La tarjeta de red conecta la máquina virtual a una subred, un grupo de seguridad de red y una dirección IP pública.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-vm"></a>Crear una VM

Creación de una configuración de máquina virtual Esta configuración incluye los valores usados al implementar la máquina virtual. Por ejemplo: credenciales, tamaño e imagen de la máquina virtual.

```powershell
# Define a credential object to store the username and password for the VM
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential -ProvisionVMAgent

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a VM.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the VM.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

Para conectar de forma remota con la máquina virtual que creó en el paso anterior, necesita su dirección IP pública. Ejecute el comando siguiente para obtenerla:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Utilice el comando siguiente para crear una sesión del Escritorio remoto con la máquina virtual. Reemplace la dirección IP por el valor de *publicIPAddress* de la máquina virtual. Cuando se le pida, especifique el nombre de usuario y la contraseña que proporcionó cuando creó la máquina virtual.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalación de IIS mediante PowerShell

Ahora que ha iniciado sesión en la máquina virtual de Azure, puede usar una sola línea de PowerShell para instalar IIS y habilitar la regla de firewall local para permitir el tráfico web. Abra una ventana de PowerShell y ejecute el siguiente comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Página principal de IIS

Con IIS instalado y el puerto 80 abierto en la máquina virtual, puede usar cualquier explorador para ver la página principal de IIS. Use la dirección *publicIpAddress* que anotó en la sección anterior para visitar la página predeterminada.

![Sitio predeterminado de IIS](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-vm"></a>Eliminación de la máquina virtual

Cuando ya no sea necesario, use el siguiente comando para quitar el grupo de recursos que contiene la máquina virtual y sus recursos relacionados:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado una máquina virtual Windows sencilla. Para más información acerca de las máquinas virtuales de Azure Stack Hub, vaya a [Características de la máquina virtual de Azure Stack Hub](azure-stack-vm-considerations.md).
