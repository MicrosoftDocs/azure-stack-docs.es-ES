---
title: Reemplazar un disco físico en Azure Stack | Microsoft Docs
description: Se describe el proceso de cómo reemplazar un disco físico en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 5da479853487dfd93467bd1413159d6e602b93c6
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277671"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Reemplazar un disco físico en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se describe el proceso general para reemplazar un disco físico en Azure Stack. Si se produce un error en un disco físico, deberá reemplazarlo tan pronto como sea posible.

Puede usar este procedimiento para sistemas integrados y para implementaciones del kit de desarrollo que tienen discos intercambiables en caliente.

Los pasos para reemplazar el disco varían en función del fabricante del hardware del fabricante de equipos originales (OEM). Consulte la documentación de Field Replaceable Unit (FRU) del proveedor para obtener pasos detallados específicos para su sistema.

## <a name="review-disk-alert-information"></a>Revisar la información de las alertas de disco
Cuando se produce un error en un disco, recibirá una alerta que indica que se perdió la conectividad a un disco físico.

![Alerta que muestra que se perdió la conectividad al disco físico](media/azure-stack-replace-disk/DiskAlert.png)

Si abre la alerta, la descripción de la misma contiene el nodo de la unidad de escalado y la ubicación exacta del espacio físico del disco que debe reemplazar. Azure Stack también le ayuda a identificar el disco erróneo utilizando las funcionalidades del indicador LED.

## <a name="replace-the-disk"></a>Reemplazar el disco

Siga las instrucciones de FRU del fabricante de hardware del OEM para reemplazar el disco actual.

> [!note]
> Reemplace los discos para un nodo de la unidad de escalado de una vez. Espere a que finalicen los trabajos de reparación de los discos antes de pasar al siguiente nodo de la unidad de escalado.

Para evitar el uso de un disco no compatible en un sistema integrado, el sistema bloquea los discos que no son compatibles con el proveedor. Si intenta usar un disco no compatible, una nueva alerta le indica que un disco se ha puesto en cuarentena debido a un modelo no admitido o al firmware.

Después de reemplazar el disco, Azure Stack descubre automáticamente el nuevo disco e inicia el proceso de reparación del disco virtual.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>Comprobar el estado de reparación del disco virtual mediante PowerShell de Azure Stack

Después de reemplazar el disco, puede supervisar el estado de mantenimiento del disco virtual y el progreso del trabajo de reparación mediante PowerShell de Azure Stack.

1. Asegúrese de que tiene instalado PowerShell de Azure Stack. Para más información, consulte [Instalación de PowerShell de Azure Stack](azure-stack-powershell-install.md).
2. Conéctese a Azure Stack con PowerShell como operador. Para obtener más información, vea [Configuración del entorno de PowerShell de Azure Stack](azure-stack-powershell-configure-admin.md).
3. Ejecute los siguientes cmdlets para comprobar el estado del disco virtual y el estado de reparación:
    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Estado de volúmenes de Azure Stack](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Valide el estado del sistema de Azure Stack. Para obtener instrucciones, consulte [Validación del estado del sistema de Azure Stack](azure-stack-diagnostic-test.md).
5. Si quiere, puede ejecutar el siguiente comando para comprobar el estado del disco físico reemplazado.

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![Discos físicos reemplazados en Azure Stack](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>Comprobar el estado de la reparación del disco virtual mediante el punto de conexión con privilegios
 
Después de reemplazar el disco, puede supervisar el estado de mantenimiento del disco virtual y el progreso del trabajo de reparación con el punto de conexión con privilegios. Siga estos pasos desde cualquier equipo que tenga conectividad de red con el punto de conexión con privilegios.

1. Abra una sesión de Windows PowerShell y conéctese al punto de conexión con privilegios.
    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. Ejecute el siguiente comando para ver el mantenimiento del disco virtual:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![Salida de PowerShell del comando Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Ejecute el comando siguiente para ver el estado del trabajo de almacenamiento actual:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Salida de PowerShell del comando Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. Valide el estado del sistema de Azure Stack. Para obtener instrucciones, consulte [Validación del estado del sistema de Azure Stack](azure-stack-diagnostic-test.md).


## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Solucionar problemas de reparación del disco virtual mediante el punto de conexión con privilegios

Si el trabajo de reparación del disco virtual parece que no avanza, ejecute el siguiente comando para reiniciar el trabajo:
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
