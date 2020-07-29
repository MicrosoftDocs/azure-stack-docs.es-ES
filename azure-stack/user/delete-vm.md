---
title: Eliminación de una VM (máquina virtual) con dependencias en Azure Stack Hub
description: Cómo eliminar una VM (máquina virtual) con dependencias en Azure Stack Hub
author: mattbriggs
ms.topic: how-to
ms.date: 07/15/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 07/15/2020
ms.openlocfilehash: 6cdbe22df8e4d5e51605bf4b3b3487c5d5cf5683
ms.sourcegitcommit: 09fbc4e8fc53828647d515bfb556dfe42df28c19
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423297"
---
# <a name="how-to-delete-a-vm-virtual-machine-with-dependencies-on-azure-stack-hub"></a>Cómo eliminar una VM (máquina virtual) con dependencias en Azure Stack Hub

En este artículo, puede encontrar los pasos para quitar una VM y sus dependencias en Azure Stack Hub.

Si quita una VM de Azure Stack Hub, las dependencias de componentes, es decir, los discos de datos, las interfaces de red virtual y los contenedores de diagnóstico, permanecerán en el grupo de recursos. Estos elementos no se eliminarán automáticamente junto con el disco del sistema operativo.

## <a name="delete-a-vm-overview"></a>Introducción a la eliminación de una VM

Cuando se crea una nueva VM, normalmente se crea un nuevo grupo de recursos y se colocan todas las dependencias en ese grupo de recursos. Si quiere eliminar la VM y todas sus dependencias, puede eliminar el grupo de recursos. Azure Resource Manager controlará las dependencias para eliminarlas correctamente. En algunas ocasiones, no se puede eliminar el grupo de recursos para quitar la VM. Por ejemplo, la VM puede contener recursos que no son dependencias de la VM que quiere conservar.

## <a name="delete-a-vm-with-dependencies"></a>Eliminación de una máquina virtual con dependencias

### <a name="with-the-portal"></a>[Con el portal](#tab/portal)

Si no puede eliminar el grupo de recursos, puede que las dependencias no estén en el mismo grupo de recursos o que haya otros recursos. Siga estos pasos:

1. Abra el portal de usuario de Azure Stack.

2. Seleccione **Máquinas virtuales**. Busque la máquina virtual y, a continuación, selecciónela para abrir la hoja de la máquina virtual.  
![Eliminación de VM con dependencias](./media/delete-vm/azure-stack-hub-delete-vm-portal.png)  

3. Anote el grupo de recursos que contiene la VM y las dependencias de VM.

4. Seleccione **Redes** y tome nota de la interfaz de red.

5. Seleccione **Discos** y anote el disco del sistema operativo y los discos de datos.

6. Vuelva a la hoja **Máquina virtual** y seleccione **Eliminar**.

7. Escriba `yes` para confirmar la eliminación y seleccione **Eliminar**.

7. Seleccione **Grupos de recursos** y, a continuación, seleccione el grupo de recursos.

8. Elimine las dependencias seleccionándolas manualmente y, a continuación, seleccione **Eliminar**.
    1. Escriba `yes` para confirmar la eliminación y seleccione **Eliminar**.
    2. Espere a que el recurso se elimine por completo.
    3. Después, puede eliminar la siguiente dependencia.

### <a name="with-powershell"></a>[Con PowerShell](#tab/ps)

Si no puede eliminar el grupo de recursos, puede que las dependencias no estén en el mismo grupo de recursos o que haya otros recursos. Siga los pasos que se indican a continuación.

Conéctese al entorno de Azure Stack Hub y, a continuación, actualice las siguientes variables con el nombre de la VM y el grupo de recursos. Para obtener instrucciones sobre cómo conectarse a la sesión de PowerShell para Azure Stack Hub, consulte [Conexión a Azure Stack Hub con PowerShell como usuario](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzureRmVM -Name $machineName -ResourceGroupName $resGroupName
```

Recupere la información de la VM y el nombre de las dependencias. En la misma sesión, ejecute los siguientes cmdlets:

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzureRmResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Elimine el contenedor de almacenamiento del diagnóstico de arranque. Si el nombre de la máquina es inferior a 9 caracteres, deberá cambiar el índice por la longitud de la cadena en la subcadena al crear la variable `$diagContainer`. 

En la misma sesión, ejecute los siguientes cmdlets:

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzureRmStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Quite la interfaz de red virtual.

```powershell
$machine | Remove-AzureRmNetworkInterface -Force
```

Elimine el disco del sistema operativo.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Quite los discos de datos conectados a la VM.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzureRmStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Finalmente, elimine la VM. El cmdlet tarda un tiempo en ejecutarse. Para auditar los componentes conectados a la VM, revise el objeto de VM en PowerShell. Para revisar el objeto, solo debe hacer referencia a la variable que contiene el objeto de VM. Escriba `$machine`.

Para eliminar la VM, en la misma sesión, ejecute los siguientes cmdlets:

```powershell
$machine | Remove-AzureRmVM -Force
```

## <a name="next-steps"></a>Pasos siguientes

[Características de las máquinas virtuales de Azure Stack Hub](azure-stack-vm-considerations.md)