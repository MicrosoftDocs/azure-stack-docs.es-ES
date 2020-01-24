---
title: Creación del almacenamiento en disco de máquinas virtuales en Azure Stack Hub | Microsoft Docs
description: Creación de discos de máquina virtual en Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 66147be9158726ab9ba01d011ba0fa2fd8f141bc
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883514"
---
# <a name="create-vm-disk-storage-in-azure-stack-hub"></a>Creación del almacenamiento en disco de máquinas virtuales en Azure Stack Hub

En este artículo se describe cómo crear el almacenamiento en disco de máquinas virtuales mediante el portal de Azure Stack Hub o PowerShell.

## <a name="overview"></a>Información general

A partir de la versión 1808, Azure Stack Hub admite el uso de discos administrados y no administrados en máquinas virtuales, que pueden ser tanto discos del sistema operativo como disco de datos. Con las versiones anteriores a la 1808 solo se pueden usar discos no administrados.

Los [discos administrados](/azure/virtual-machines/windows/managed-disks-overview) simplifican la administración de discos para las máquinas virtuales de Azure IaaS, ya que administran las cuentas de almacenamiento asociadas a los discos de las máquinas virtuales. Solo tiene que especificar el tamaño del disco que necesita y Azure Stack Hub creará y administrará el disco automáticamente.

Los discos no administrados no requieren la creación de una cuenta de almacenamiento para almacenarlos. Los discos creados se conocen como discos de máquina virtual y se almacenan en los contenedores de la cuenta de almacenamiento.

## <a name="best-practice-guidelines"></a>Guías de procedimientos recomendados

Se recomienda usar discos administrados para la máquina virtual para facilitar la administración y el equilibrio de la capacidad. No tiene que preparar contenedores ni una cuenta de almacenamiento antes de usar discos administrados. Al crear varios discos administrados, estos se distribuyen en varios volúmenes para equilibrar su capacidad.  

En el caso de los discos no administrados, para mejorar el rendimiento y reducir el costo total, es recomendable poner cada disco no administrado en un contenedor independiente. Aunque puede poner los discos de sistema operativo y los discos de datos en el mismo contenedor, es recomendable que un contenedor contenga el disco de sistema operativo o el disco de datos, pero no ambos al mismo tiempo.

Si agrega uno o más discos de datos a una máquina virtual, use contenedores adicionales como ubicación para almacenar esos discos. Los discos del sistema operativo de máquinas virtuales adicionales también deben estar en sus propios contenedores.

Al crear máquinas virtuales, puede volver a usar la misma cuenta de almacenamiento con cada nueva máquina virtual. Solo los contenedores que cree deben ser únicos.

## <a name="adding-new-disks"></a>Adición de nuevos discos

En la tabla siguiente se resume cómo agregar discos desde el portal y desde PowerShell:

| Método | Opciones
|-|-|
|Portal de usuarios|- Agregue discos de datos nuevos a una máquina virtual existente. Azure Stack Hub crea nuevos discos. </br> </br> - Agregue un archivo (.vhd) de un disco existente a una máquina virtual creada anteriormente. Para ello, primero debe preparar el archivo .vhd y, después, cargarlo en Azure Stack Hub. |
|[PowerShell](#use-powershell-to-add-multiple-disks-to-a-vm) | -Crear una nueva máquina virtual con un disco del sistema operativo y, al mismo tiempo, agregar uno o más discos de datos a esa máquina virtual. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Uso del portal para agregar discos a una máquina virtual

De forma predeterminada, cuando se usa el portal para crear una máquina virtual para la mayoría de los elementos de Marketplace, solo se crea el disco del sistema operativo.

Después de crear una máquina virtual, puede usar el portal para:

* Crear un disco de datos y asociarlo a la máquina virtual.
* Cargar un disco de datos existente y asociarlo a la máquina virtual.

Cada disco no administrado que agregue se debe colocar en un contenedor independiente.

>[!NOTE]  
>Los discos creados y administrados por Azure se denominan [discos administrados](/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Uso del portal para crear y asociar un nuevo disco de datos

1. En el portal, seleccione **Todos los servicios** y **Máquinas virtuales**.
   ![Ejemplo: Panel de máquinas virtuales](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. Seleccione una VM que se haya creado anteriormente.
   ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. En la VM, seleccione **Discos** y, después, **Agregar disco de datos**.
   ![Ejemplo: Conexión de un nuevo disco a la máquina virtual](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. Para el disco de datos:
   * Escriba el **LUN**. El número de unidad lógica (LUN) tiene que ser un número válido.
   * Seleccione **Crear disco**.
   ![Ejemplo: Conexión de un nuevo disco a la máquina virtual](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. En la hoja **Crear disco administrado**:
   * Escriba el **Nombre** del disco.
   * Seleccione un **grupo de recursos** existente, o bien cree uno.
   * Seleccione la **Ubicación**. De forma predeterminada, la ubicación se establece en el mismo contenedor que alberga el disco del sistema operativo.
   * Seleccione el **Tipo de cuenta**.
      ![Ejemplo: Conexión de un nuevo disco a la máquina virtual](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **SSD Premium**  
      Los discos Premium (SSD) están respaldados por unidades de estado sólido y ofrecen coherencia y un rendimiento con baja latencia. Proporcionan el mejor equilibrio entre precio y rendimiento, y son ideales para aplicaciones intensivas de E/S y cargas de trabajo de producción.

      **HDD estándar**  
      Los discos estándar (HDD) están respaldados por unidades magnéticas y son preferibles para las aplicaciones en la que se accede con poca frecuencia a los datos. Los discos con redundancia de zona tienen el respaldo del almacenamiento con redundancia de zona (ZRS), que replica los datos en varias zonas, lo que garantiza la disponibilidad de los datos aunque una de las zonas esté inactiva.

   * Seleccione el **Tipo de origen**.

     Cree un disco desde una instantánea de otro disco, un blob en una cuenta de almacenamiento, o cree un disco vacío.

      **Instantánea**: Seleccione una instantánea, si está disponible. La instantánea tiene que estar en disponible en la suscripción y ubicación de la máquina virtual.

      **Blob de almacenamiento**:
     * Agregue el identificador URI del blob de almacenamiento que contiene la imagen de disco.  
     * Seleccione **Examinar** para abrir la hoja de cuentas de almacenamiento. Para obtener instrucciones al respecto, consulte [Incorporación de un disco de datos desde una cuenta de almacenamiento](#add-a-data-disk-from-a-storage-account).
     * Seleccione el tipo de sistema operativo de la imagen: **Windows**, **Linux** o **Ninguno (disco de datos)** .

   * Seleccione el **Tamaño (GiB)** .

     Los costos de disco Estándar aumentan en función del tamaño del disco. Los costos y el rendimiento de los discos Premium aumentan en función del tamaño del disco. Para más información, consulte [Precios de Managed Disks](https://go.microsoft.com/fwlink/?linkid=843142).

   * Seleccione **Crear**. Azure Stack Hub crea y valida el disco administrado.

6. Una vez que Azure Stack Hub crea el disco y lo asocia a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en **DISCOS DE DATOS**.

   ![Ejemplo: Ver disco](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Incorporación de un disco de datos desde una cuenta de almacenamiento

Para más información acerca de cómo trabajar con cuentas de almacenamiento en Azure Stack, consulte [Introducción a Azure Stack Hub Storage](azure-stack-storage-overview.md).

1. Seleccione la **cuenta de almacenamiento** que se usará.
2. Seleccione el **Contenedor** en el que desea colocar el disco de datos. En la hoja **Contenedores**, puede crear un nuevo contenedor si así lo desea. A continuación, puede cambiar la ubicación del nuevo disco por la de su propio contenedor. Cuando use un contenedor independiente para cada disco, distribuya la colocación del disco de datos que mejora el rendimiento.
3. Haga clic en **Seleccionar** para guardar la selección.

    ![Ejemplo: Seleccionar un contenedor](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Incorporación de un disco de datos existente a una VM

1. [Prepare un archivo .vhd](/azure/virtual-machines/windows/classic/createupload-vhd) para usarlo como disco de datos de una máquina virtual. Cargue dicho archivo .vhd en una cuenta de almacenamiento que use con la máquina virtual a la que desee asociarlo.

    - Considere usar un contenedor diferente para el archivo .vhd en lugar del que alberga el disco del sistema operativo.  
    - Antes de cargar los VHD en Azure, debe consultar [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
    - Revise [Plan for the migration to Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/on-prem-to-azure#plan-for-the-migration-to-managed-disks) (Planeación de la migración a Managed Disks) antes de comenzar la migración a [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).
    
    ![Ejemplo: Carga de un archivo VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)



2. Una vez cargado el archivo .vhd, está listo para asociar el disco duro virtual a una VM. En el menú de la izquierda, haga clic en **Máquinas virtuales**.  
 ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. Seleccione la VM en la lista.

    ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. En la página de la VM, seleccione **Discos** y, después, **Asociar existente**.

    ![Ejemplo: un disco existente](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. En la página **Asociar disco existente**, seleccione **Archivo VHD**. Se abre la página **Cuentas de almacenamiento**.

    ![Ejemplo: Seleccionar un archivo de disco duro virtual](media/azure-stack-manage-vm-disks/select-vhd.png)

6. En **Cuentas de almacenamiento**, seleccione la cuenta que quiere usar y, a continuación, elija un contenedor para hospedar el archivo .vhd cargado anteriormente. Seleccione el archivo .vhd y, a continuación, haga clic en **Seleccionar** para guardar la selección.

    ![Ejemplo: Seleccionar un contenedor](media/azure-stack-manage-vm-disks/select-container2.png)

7. En **Asociar disco existente**, el archivo que seleccionó aparece en **Archivo VHD**. Actualice el valor de **Almacenamiento en caché de host** del disco y luego seleccione **Aceptar** para guardar la configuración del nuevo disco de la máquina virtual.

    ![Ejemplo: Adjuntar el archivo de disco duro virtual](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Una vez que Azure Stack Hub crea el disco y lo asocia a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en **Discos de datos**.

    ![Ejemplo: Finalizar la conexión del disco](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-disks-to-a-vm"></a>Uso de PowerShell para agregar varios discos a una máquina virtual

Puede usar PowerShell para aprovisionar una máquina virtual y agregar nuevos discos de datos, o bien asociar un disco administrado o un archivo .vhd previamente existente como disco de datos.

El cmdlet **Add-AzureRmVMDataDisk** agrega un disco de datos a una VM. Puede agregar un disco de datos al crear una VM o lo puede agregar a una VM existente. Si es un disco no administrado, especifique el parámetro **VhdUri** para distribuir los discos a distintos contenedores.

### <a name="add-data-disks-to-a-new-vm"></a>Incorporación de discos de datos a una máquina virtual **nueva**

En los ejemplos siguientes se usan comandos de PowerShell para crear una máquina virtual existente con tres discos de datos. Los comandos se proporcionan con varias partes debido a las pequeñas diferencias que se producen cuando se usan discos administrados o no administrados. 

#### <a name="create-virtual-machine-configuration-and-network-resources"></a>Creación de la configuración y los recursos de red de una máquina virtual

El siguiente script crea un objeto de máquina virtual y, después, lo almacena en la variable `$VirtualMachine`. Los comandos asignan un nombre y un tamaño a la máquina virtual y, después, crean los recursos de red (red virtual, subred, adaptador de red virtual, grupo de seguridad de red y dirección IP pública) para la máquina virtual:

```powershell
# Create new virtual machine configuration
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"

# Set variables
$rgName = "myResourceGroup"
$location = "local"

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
                                  -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
                                  -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
                                                -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
                                                -SourceAddressPrefix Internet -SourcePortRange * `
                                                -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
                                       -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
                                   -Location $location -SubnetId $vnet.Subnets[0].Id `
                                   -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

```

#### <a name="add-managed-disk"></a>Incorporación de un disco administrado
>[!NOTE]  
>Esta sección es solo para agregar discos administrados. 

Los tres comandos siguientes agregan discos de datos administrados a la máquina virtual almacenada en `$VirtualMachine`. Cada comando especifica el nombre y las propiedades adicionales del disco:

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -CreateOption Empty
```

El siguiente comando agrega un disco de sistema operativo como disco administrado a la máquina virtual almacenada en `$VirtualMachine`.

```powershell
# Set OS Disk
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName  `
                                      -CreateOption FromImage -Windows
```

#### <a name="add-unmanaged-disk"></a>Incorporación de un disco no administrado

>[!NOTE]  
>Esta sección es solo para agregar discos no administrados. 

Los tres comandos siguientes asignan rutas de acceso de tres discos de datos no administrados a las variables `$DataDiskVhdUri01`, `$DataDiskVhdUri02` y `$DataDiskVhdUri03`. Defina otro nombre de ruta de acceso en la dirección URL para distribuir los discos a diferentes contenedores:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Los tres comandos siguientes agregan discos de datos a la máquina virtual almacenada en `$VirtualMachine`. Cada comando especifica el nombre y las propiedades adicionales del disco. El identificador URI de cada disco se almacena en `$DataDiskVhdUri01`, `$DataDiskVhdUri02` y `$DataDiskVhdUri03`:

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

Los siguientes comandos agregan un disco de sistema operativo no administrado a la máquina virtual almacenada en `$VirtualMachine`.

```powershell
# Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
                                      -CreateOption FromImage -Windows
```


#### <a name="create-new-virtual-machine"></a>Creación de una máquina virtual
Use los siguientes comandos de PowerShell para establecer una imagen del sistema operativo, agregar una configuración de red a la máquina virtual y, después, iniciar la nueva máquina virtual.

```powershell
#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine -ProvisionVMAgent | `
                  Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
                  -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```


### <a name="add-data-disks-to-an-existing-vm"></a>Incorporación de discos de datos a una máquina virtual **existente**
En los ejemplos siguientes se usan comandos de PowerShell para agregar tres discos de datos a una máquina virtual existente.

#### <a name="get-virtual-machine"></a>Obtención de una máquina virtual

 El primer comando obtiene la VM llamada **VirtualMachine** mediante el cmdlet **Get-AzureRmVM**. El comando almacena la VM en la variable `$VirtualMachine`:

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

#### <a name="add-managed-disk"></a>Incorporación de un disco administrado

>[!NOTE]  
>Esta sección es solo para agregar discos administrados.

Los tres comandos siguientes agregan los discos de datos administrados a la máquina virtual almacenada en la variable `$VirtualMachine`. Cada comando especifica el nombre y las propiedades adicionales del disco:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" -Lun 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" -Lun 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" -Lun 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="add-unmanaged-disk"></a>Incorporación de un disco no administrado

>[!NOTE]  
>Esta sección es solo para agregar discos no administrados. 

Los tres comandos siguientes asignan las rutas de acceso de tres discos de datos a las variables `$DataDiskVhdUri01`, `$DataDiskVhdUri02` y `$DataDiskVhdUri03`. Los diferentes nombres de ruta de acceso de los identificadores URI de los discos duros virtuales indican contenedores diferentes para la colocación de los discos:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Los tres comandos siguientes agregan los discos de datos a la VM almacenada en la variable `$VirtualMachine`. Cada comando especifica el nombre, la ubicación y propiedades adicionales del disco. El identificador URI de cada disco se almacena en `$DataDiskVhdUri01`, `$DataDiskVhdUri02` y `$DataDiskVhdUri03`:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="update-virtual-machine-state"></a>Actualización del estado de una máquina virtual

Este comando actualiza el estado de la máquina virtual almacenada en `$VirtualMachine` en `-ResourceGroupName`:

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las máquinas virtuales de Azure Stack, consulte el artículo [Consideraciones sobre máquinas virtuales en Azure Stack Hub](azure-stack-vm-considerations.md).
