---
title: Reemplazo de un disco físico
titleSuffix: Azure Stack Hub
description: Aprenda a reemplazar un disco físico en Azure Stack Hub.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 12/02/2019
ms.openlocfilehash: 17c8a963ec49ebefff08f56a3411e71ae9d68011
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869498"
---
# <a name="replace-a-physical-disk-in-azure-stack-hub"></a>Reemplazo de un disco físico en Azure Stack Hub

En este artículo se describe el proceso general para reemplazar un disco físico en Azure Stack Hub. Si se produce un error en un disco físico, deberá reemplazarlo tan pronto como sea posible.

> [!Note]  
> Reemplazar una unidad de datos física **no** requiere que el nodo de la unidad de escalado se ponga en modo de mantenimiento (purga) por adelantado. Además, una vez reemplazada la unidad física, no es necesario reparar el nodo de la unidad de escalado mediante el portal de administrador de Azure Stack Hub. En el artículo sobre [cómo reemplazar un componente de hardware en un nodo de unidad de escalado de Azure Stack Hub](azure-stack-replace-component.md) se proporciona más información en caso de ser necesaria una reparación.

Puede usar este procedimiento para implementaciones que tengan discos intercambiables en caliente.

Los pasos para reemplazar el disco varían en función del fabricante del hardware del fabricante de equipos originales (OEM). Consulte la documentación de Field Replaceable Unit (FRU) del proveedor para obtener pasos detallados específicos para su sistema.

## <a name="review-disk-alert-information"></a>Revisar la información de las alertas de disco
Cuando se produce un error en un disco, recibirá una alerta que indica que se perdió la conectividad a un disco físico.

![Alerta que muestra la pérdida de conectividad al disco físico en la administración de Azure Stack Hub](media/azure-stack-replace-disk/DiskAlert.png)

Si abre la alerta, la descripción de la misma contiene el nodo de la unidad de escalado y la ubicación exacta del espacio físico del disco que debe reemplazar. Azure Stack Hub también le ayuda a identificar el disco erróneo utilizando las funcionalidades del indicador LED.

## <a name="replace-the-physical-disk"></a>Reemplazo del disco físico

Siga las instrucciones de FRU del fabricante de hardware del OEM para reemplazar el disco actual.

> [!note]
> Reemplace los discos para un nodo de la unidad de escalado de una vez. Espere a que finalicen los trabajos de reparación de los discos virtuales antes de pasar al siguiente nodo de la unidad de escalado.

Para evitar el uso de un disco no compatible en un sistema integrado, el sistema bloquea los discos que no son compatibles con el proveedor. Si intenta usar un disco no compatible, una nueva alerta le indica que un disco se ha puesto en cuarentena debido a un modelo o un firmware no admitidos.

Después de reemplazar el disco, Azure Stack Hub detecta automáticamente el nuevo disco e inicia el proceso de reparación del disco virtual.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell"></a>Comprobación del estado de reparación del disco virtual mediante PowerShell de Azure Stack Hub

Después de reemplazar el disco, puede supervisar el estado de mantenimiento del disco virtual y el progreso del trabajo de reparación mediante PowerShell de Azure Stack Hub.

1. Asegúrese de tener instalado PowerShell de Azure Stack Hub. Para más información, consulte [Instalación de PowerShell para Azure Stack Hub](powershell-install-az-module.md).
2. Conexión a Azure Stack Hub como operador con PowerShell Para más información, consulte [Conexión a Azure Stack Hub como operador con PowerShell](azure-stack-powershell-configure-admin.md).
3. Ejecute los siguientes cmdlets para comprobar el estado del disco virtual y el estado de reparación:

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Mantenimiento de los volúmenes de Azure Stack Hub en PowerShell](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Valide el estado del sistema de Azure Stack Hub. Si necesita instrucciones, consulte [Validación del estado del sistema de Azure Stack Hub](azure-stack-diagnostic-test.md).
5. Si quiere, puede ejecutar el siguiente comando para comprobar el estado del disco físico reemplazado.

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

    Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
    ```

    ![Discos físicos reemplazados en Azure Stack Hub con PowerShell](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

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

4. Valide el estado del sistema de Azure Stack Hub. Si necesita instrucciones, consulte [Validación del estado del sistema de Azure Stack Hub](azure-stack-diagnostic-test.md).

## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Solucionar problemas de reparación del disco virtual mediante el punto de conexión con privilegios

Si el trabajo de reparación del disco virtual parece que no avanza, ejecute el siguiente comando para reiniciar el trabajo:

```powershell
Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
```
