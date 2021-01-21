---
title: Migración a Azure Stack HCI en hardware nuevo
description: Obtenga información sobre cómo migrar a Azure Stack HCI en hardware nuevo.
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7a0c0ca7a99b3554b74cc80911acbee92793aa52
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254830"
---
# <a name="migrate-to-azure-stack-hci-on-new-hardware"></a>Migración a Azure Stack HCI en hardware nuevo

> Se aplica a Azure Stack HCI, versión 20H2, Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 y Windows Server 2008 R2.

En este tema se describe cómo migrar archivos de máquina virtual en Windows Server 2012 R2, Windows Server 2016 o Windows Server 2019 a un nuevo hardware de servidor con Azure Stack HCI mediante Windows PowerShell y Robocopy. Robocopy es un método sólido para copiar archivos de un servidor a otro. Se reanuda si se desconecta y continúa funcionando desde su último estado conocido. Robocopy también admite la copia de archivos multiproceso con el protocolo Bloque de mensajes del servidor (SMB). Para más información, consulte [Robocopy](/windows-server/administration/windows-commands/robocopy).

> [!NOTE]
> No se admite la Migración en vivo de Hyper-V ni la réplica de Hyper-V desde Windows Server a Azure Stack HCI.

Si tiene máquinas virtuales en Windows 2012 R2 o versiones anteriores que desea migrar, consulte [Migración de máquinas virtuales más antiguas](#migrating-older-vms).

Para migrar a Azure Stack HCI con el mismo hardware, consulte [Migración a Azure Stack HCI en el mismo hardware](migrate-cluster-same-hardware.md).

En el diagrama siguiente se muestra un clúster de origen de Windows Server y un clúster de destino de Azure Stack HCI como ejemplo. También puede migrar máquinas virtuales en servidores independientes.

:::image type="content" source="media/migrate/migrate-cluster.png" alt-text="Migración del clúster a Azure Stack HCI" lightbox="media/migrate/migrate-cluster.png":::

En lo que se refiere al tiempo de inactividad esperado, el uso de una sola NIC con una red RDMA horizontal de derecha a izquierda de 40 GB dual entre los clústeres y Robocopy configurado para 32 multiprocesos, puede obtener velocidades de transferencia de 1,9 TB por hora.

> [!NOTE]
> La migración de máquinas virtuales para clústeres extendidos no se trata en este artículo.

## <a name="before-you-begin"></a>Antes de empezar

Hay varios requisitos y aspectos que se deben tener en cuenta antes de comenzar la migración:

- Todos los comandos de Windows PowerShell se deben ejecutar como administrador.

- Debe tener credenciales de dominio con permisos de administrador para los clústeres de origen y de destino, con derechos completos en la unidad organizativa (UO) de origen y de destino que contiene ambos clústeres.

- Ambos clústeres deben estar en el mismo bosque y dominio de Active Directory para facilitar la autenticación Kerberos entre los clústeres para la migración de máquinas virtuales.

- Ambos clústeres deben residir en una unidad organizativa de Active Directory con la herencia de bloque del objeto de directiva de grupo (GPO) establecida en esta unidad organizativa. Esto garantiza que ningún GPO de nivel de dominio ni ninguna directiva de seguridad puedan afectar a la migración.

- Ambos clústeres deben estar conectados al mismo origen de tiempo para posibilitar una autenticación Kerberos coherente entre los clústeres.

- Anote el nombre del conmutador virtual de Hyper-V que usan las máquinas virtuales del clúster de origen. Debe usar el mismo nombre de conmutador virtual para la "red de máquina virtual" del clúster de destino de Azure Stack HCI antes de importar las máquinas virtuales.

- Elimine todos los archivos de imagen ISO de las máquinas virtuales de origen. Esto se realiza mediante el administrador de Hyper-V en las **propiedades de la máquina virtual**, en la sección **Hardware**. Seleccione **Eliminar** en las unidades de CD y DVD virtuales.

- Apague todas las máquinas virtuales del clúster de origen. Esto es necesario para garantizar que el control de versiones y el estado se mantienen durante todo el proceso de migración.

- Compruebe si Azure Stack HCI es compatible con la versión de las máquinas virtuales que va a importar y actualice las máquinas virtuales según sea necesario. Consulte la sección [Compatibilidad con versiones de máquina virtual y actualización](#vm-version-support-and-update) sobre cómo hacerlo.

- Realice una copia de seguridad de todas las máquinas virtuales del clúster de origen. Complete una copia de seguridad coherente con el bloqueo de todas las aplicaciones y datos y una copia de seguridad coherente con la aplicación de todas las bases de datos. Para realizar una copia de seguridad en Azure, consulte [Uso de Azure Backup para hacer una copia de seguridad de las instancias de Windows Server](../manage/use-azure-backup.md).

- Realice un punto de control de las máquinas virtuales del clúster de origen y del controlador de dominio por si fuera necesario revertir a un estado anterior. Esto no es aplicable a los servidores físicos.

- Asegúrese de que los tamaños máximos de marcos de Jumbo son los mismos entre las redes de almacenamiento del clúster de origen y de destino, específicamente los adaptadores de red RDMA y sus puertos de red de conmutador respectivos para proporcionar el tamaño de paquete de transferencia de un extremo a otro más eficaz.

- Anote el nombre del conmutador virtual de Hyper-V del clúster de origen. Lo volverá a usar en el clúster de destino.

- El hardware de Azure Stack HCI debe tener al menos la misma capacidad y configuración que el hardware de origen.

- Minimice el número de saltos de red o la distancia física entre los clústeres de origen y de destino para facilitar una transferencia de archivos más rápida.

## <a name="vm-version-support-and-update"></a>Compatibilidad con versiones de máquina virtual y actualización

En esta tabla se enumeran las versiones del sistema operativo Windows Server y sus versiones de máquina virtual.

Independientemente de la versión del sistema operativo en la que se pueda estar ejecutando una máquina virtual, la versión mínima de máquina virtual compatible con la migración directa a Azure Stack HCI es la versión 5.0. Representa la versión predeterminada para las máquinas virtuales en Windows Server 2012 R2. Por lo tanto, cualquier máquina virtual que se ejecute en la versión 2.0, 3.0 o 4.0, por ejemplo, se debe actualizar a la versión 5.0 antes de la migración.

|Versión del SO|Versión de VM|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4.0|
|Windows Server 2012 R2|5.0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

En el caso de las máquinas virtuales en Windows Server 2012 R2, Windows Server 2016 y Windows Server 2019, actualice todas las máquinas virtuales a la última versión de máquina virtual admitida en el hardware de origen antes de ejecutar el script de migración de Robocopy. Esto garantiza que todas las máquinas virtuales tengan al menos la versión 5.0 para una importación correcta de la máquina virtual.

En el caso de las máquinas virtuales en Windows Server 2008 SP1, Windows Server 2008 R2-SP1 y Windows 2012, la versión de la máquina virtual será menor que la versión 5.0. Estas máquinas virtuales también usan un archivo .xml para la configuración en lugar de un archivo .vcmx. Por tanto, no se admite la importación directa de la máquina virtual a Azure Stack HCI. En estos casos, tiene dos opciones, como se detalla en [Migración de máquinas virtuales más antiguas](#migrating-older-vms).

### <a name="updating-the-vm-version"></a>Actualización de la versión de la máquina virtual

Los siguientes comandos se aplican a Windows Server 2012 R2 y versiones posteriores. Use el siguiente comando para mostrar todas las versiones de máquina virtual de un solo servidor:

```powershell
Get-VM * | Format-Table Name,Version
```

Para mostrar todas las versiones de máquina virtual de todos los servidores de un clúster:

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

Para actualizar todas las máquinas virtuales a la última versión admitida en todos los servidores:

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="rdma-recommendations"></a>Recomendaciones de RDMA

Si usa Acceso directo a memoria remota (RDMA), Robocopy puede aprovecharlo para copiar las máquinas virtuales entre clústeres. Estas son algunas recomendaciones para usar RDMA:

- Conecte ambos clústeres al mismo conmutador de la parte superior del rack (ToR) para usar la ruta de acceso de red más rápida entre los clústeres de origen y de destino. En el caso de la ruta de acceso de red de almacenamiento, normalmente admite velocidades de 10 GbE/25 GbE o superiores y aprovecha RDMA.

- Si el adaptador RDMA o el estándar son diferentes entre los clústeres de origen y de destino (ROCE frente a iWARP), Robocopy aprovechará SMB sobre TCP/IP a través de la red más rápida disponible. Normalmente, se trata de una velocidad dual de 10 GbE/25 Gbe o superior para la red horizontal de derecha a izquierda, lo que proporciona la forma más óptima de copiar archivos VHDX de máquinas virtuales entre clústeres.

- Para asegurarse de que Robocopy puede aprovechar RDMA entre los clústeres (red horizontal de derecha a izquierda), configure las redes de almacenamiento RDMA para que se puedan enrutar entre los clústeres de origen y de destino.

## <a name="create-the-new-cluster"></a>Creación del nuevo clúster

Antes de poder crear el clúster de Azure Stack HCI, debe instalar el sistema operativo Azure Stack HCI en cada nuevo servidor que vaya a estar en el clúster. Para obtener información sobre cómo hacerlo, consulte [Implementación del sistema operativo Azure Stack HCI](operating-system.md).

Use Windows Admin Center o Windows PowerShell para crear el nuevo clúster. Para obtener información detallada sobre cómo hacerlo, consulte [Creación de un clúster de Azure Stack HCI mediante Windows Admin Center](create-cluster.md) y [Creación de un clúster de Azure Stack HCI mediante Windows PowerShell](create-cluster-powershell.md).

> [!IMPORTANT]
> Los nombres del conmutador virtual de Hyper-V (`VMSwitch`) entre los clústeres deben ser iguales. Asegúrese de que los nombres del conmutador virtual creados en el clúster de destino coinciden con los que se usan en el clúster de origen en todos los servidores. Compruebe igualmente los nombres de los conmutadores antes de importar las máquinas virtuales.

> [!NOTE]
> Debe registrar el clúster de Azure Stack HCI en Azure antes de poder crear nuevas máquinas virtuales en él. Para más información, consulte [Conexión de Azure Stack HCI a Azure](register-with-azure.md).

## <a name="run-the-migration-script"></a>Ejecución del script de migración

El siguiente script de PowerShell `Robocopy_Remote_Server_.ps1` utiliza Robocopy para copiar archivos de máquina virtual y sus directorios y metadatos dependientes desde el origen al clúster de destino. Este script se ha modificado desde el script original de TechNet en: [Copia de archivos con el script Robocoy a un servidor remoto mediante PowerShell y RoboCopy](https://gallery.technet.microsoft.com/scriptcenter/Robocoy-Files-to-Remote-bdfc5154).

El script copia todos los archivos VHD, VHDX y VMCX de máquina virtual en el clúster de destino para un volumen compartido de clúster determinado (CSV). Se migra un CSV cada vez.

El script de migración se ejecuta localmente en cada servidor de origen para aprovechar las ventajas de RDMA y una transferencia de red rápida. Para hacerlo:

1. Asegúrese de que cada nodo del clúster de destino esté establecido en el propietario del CSV para el CSV de destino.

1. Use el siguiente cmdlet para determinar la ubicación de todos los archivos VHD y VHDX de máquina virtual que se van a copiar. Revise el archivo `C:\vmpaths.txt` para determinar la ruta de acceso del archivo de origen superior para que Robocopy empiece en el paso 4:

    ```powershell  
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

    > [!NOTE]
    > Si los archivos VHD y VHDX se encuentran en diferentes rutas de acceso en el mismo volumen, tendrá que ejecutar el script de migración para cada ruta de acceso diferente para copiarlos todos.

1. Cambie las tres variables siguientes para que coincidan la ruta de acceso de la máquina virtual del clúster de origen y la ruta de acceso de la máquina virtual del clúster de destino:

    - `$Dest_Server = "Node01"`
    - `$source  = "C:\Clusterstorage\Volume01"`
    - `$dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"`

1. Ejecute el siguiente script en cada servidor Windows Server de origen:

```powershell
<#
#===========================================================================  
# Script: Robocopy_Remote_Server_.ps1
#===========================================================================  
.DESCRIPTION:
Change the following variables to match your source cluster VM path with the destination cluster VM path. Then run this script on each source Cluster Node CSV owner and make sure the destination cluster node is set to the CSV owner for the destination CSV.

        Change $Dest_Server = "Node01"
        Change $source  = "C:\Clusterstorage\Volume01"
        Change $dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"
#>

$Space       = Write-host ""
$Dest_Server = "Node01"
$source      = "C:\Clusterstorage\Volume01"
$dest        = "\\$Dest_Server\C$\Clusterstorage\Volume01"
$Logfile     = "c:\temp\Robocopy1-$date.txt"
$date        = Get-Date -UFormat "%Y%m%d"
$cmdArgs     = @("$source","$dest",$what,$options)  
$what        = @("/COPYALL")
$options     = @("/E","/MT:32","/R:0","/W:1","/NFL","/NDL","/LOG:$logfile","/xf")
 
## Get Start Time
$startDTM = (Get-Date)
 
$Dest_Server     = "Node01"
$TARGETDIR   = \\$Dest_Server\C$\Clusterstorage\Volume01
$Space
Clear
## Provide Information
Write-host ".....Copying Virtual Machines FROM $Source to $TARGETDIR ....................." -fore Green -back black
Write-Host "........................................." -Fore Green

## Kick off the copy with options defined  
robocopy @cmdArgs
 
## Get End Time
$endDTM = (Get-Date)
 
## Echo Time elapsed
$Time = "Elapsed Time: = $(($endDTM-$startDTM).totalminutes) minutes"  
## Provide time it took
Write-host ""
Write-host " Copy Virtual Machines to $Dest_Server has been completed......" -fore Green -back black
Write-host " Copy Virtual Machines to $Dest_Server took $Time        ......" -fore Cyan
```

## <a name="import-the-vms"></a>Importación de las máquinas virtuales

Un procedimiento recomendado consiste en crear al menos un volumen compartido de clúster (CSV) por cada nodo del clúster para habilitar un equilibrio uniforme de máquinas virtuales para cada propietario de CSV con el fin de aumentar la resistencia, el rendimiento y la escala de las cargas de trabajo de las máquinas virtuales. De forma predeterminada, este equilibrio se produce automáticamente cada cinco minutos y debe tenerse en cuenta al usar Robocopy entre un nodo del clúster de origen y el nodo de clúster de destino para garantizar que los propietarios del CSV de origen y de destino coincidan para proporcionar la velocidad y la ruta de acceso de la transferencia más óptimas.

Siga los pasos que se describen a continuación en el clúster de Azure Stack HCI para importar las máquinas virtuales, hacer que tengan alta disponibilidad e iniciarlas:

1. Ejecute el siguiente cmdlet para mostrar todos los nodos propietarios de CSV:

    ```powershell
    Get-ClusterSharedVolume
    ```

1. Para cada nodo de servidor, vaya a `C:\Clusterstorage\Volume` y establezca la ruta de acceso para todas las máquinas virtuales, por ejemplo `C:\Clusterstorage\volume01`.

1. Ejecute el siguiente cmdlet en cada nodo propietario de CSV para mostrar la ruta de acceso a todos los archivos VMCX de máquina virtual por cada volumen antes de la importación de la máquina virtual. Modifique la ruta de acceso para que coincida con su entorno:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse
    ```

    > [!NOTE]
    > Windows Server 2012 R2 y las máquinas virtuales anteriores usan un archivo XML en lugar de un archivo VCMX. Para más información, consulte la sección **Migración de máquinas virtuales más antiguas**.

1. Ejecute el siguiente cmdlet para cada nodo de servidor para importar, registrar y hacer que las máquinas virtuales tengan alta disponibilidad en cada nodo propietario de CSV. Esto garantiza una distribución uniforme de las máquinas virtuales para una asignación óptima de memoria y procesador:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. Inicie cada máquina virtual de destino en cada nodo:

    ```powershell
    Start-VM -Name
    ```

1. Inicie sesión y compruebe que todas las máquinas virtuales están en ejecución y que todas las aplicaciones y los datos están allí:

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. Actualice las máquinas virtuales a la versión más reciente de Azure Stack HCI para sacar el máximo partido de todos los avances:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. Una vez completado el script, compruebe si hay errores en el archivo de registro de Robocopy y compruebe que todas las máquinas virtuales se han copiado correctamente.

## <a name="migrating-older-vms"></a>Migración de máquinas virtuales más antiguas

Esta sección es de aplicación si dispone de máquinas virtuales con Windows Server 2008 SP1, Windows Server 2008 R2-SP1, Windows Server 2012 o Windows Server 2012 R2. Tiene dos opciones para controlar estas máquinas virtuales:

- Migre estas máquinas virtuales a Windows Server 2012 R2, Windows Server 2016 o Windows Server 2019 en primer lugar, actualice la versión de la máquina virtual y, a continuación, inicie el proceso de migración.

- Use Robocopy para copiar todos los VHD de máquina virtual en Azure Stack HCI. A continuación, cree nuevas máquinas virtuales y conecte los VHD copiados a las máquinas virtuales en Azure Stack HCI. Con esto se omite la limitación de la versión de la máquina virtual para estas máquinas virtuales anteriores.

Windows Server 2012 R2 y los hosts de Hyper-V anteriores usan un formato de archivo XML para la configuración de la máquina virtual, que es diferente del formato de archivo VCMX que se usa para Windows Server 2016 y los hosts de Hyper-V posteriores. Esto requiere un comando de Robocopy diferente para copiar estas máquinas virtuales en Azure Stack HCI.

### <a name="option-1-staged-migration"></a>Opción 1: Migración por fases

Se trata de una migración en dos fases que se usa para las máquinas virtuales hospedadas en Windows Server 2008 SP1, Windows Server 2008 R2-SP y Windows Server 2012. Este es el proceso que se usa:

1. Detecte la ubicación de todos los archivos VHD y VHDX de máquina virtual que se van a copiar y, a continuación, revise el archivo `vmpaths.txt` para determinar la ruta de acceso del archivo de origen superior para que Robocopy empiece. Use el siguiente cmdlet:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

1. Use el siguiente comando Robocopy de ejemplo para copiar máquinas virtuales en Windows Server 2012 R2 usando primero la ruta de acceso superior determinada en el paso 1:

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Compruebe que el nombre del conmutador virtual (`VMSwitch`) que se usa en el clúster de Windows Server 2012 R2 es el mismo que el nombre del conmutador usado en el origen con Windows 2008 R2 o Windows Server 2008 R2-SP1. Para mostrar los nombres de conmutador que se usan en todos los servidores de un clúster, use lo siguiente:

     ```powershell
    Get-VMSwitch -CimSession $Servers | Select-Object Name
    ```

    Cambie el nombre del conmutador en Windows Server 20212 R2 según sea necesario. Para cambiar el nombre del conmutador en todos los servidores del clúster, use lo siguiente:

    ```powershell
    Invoke-Command -ComputerName $Servers -ScriptBlock {rename-VMSwitch -Name $using:vSwitcholdName -NewName $using:vSwitchnewname}
    ```

1. Copie e importe las máquinas virtuales a Windows Server 2012 R2:

     ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. En Windows Server 2012 R2, actualice la versión de la máquina virtual a la 5.0 para todas las máquinas virtuales:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. [Ejecute el script de migración](#run-the-migration-script) para copiar las máquinas virtuales a Azure Stack HCI.

1. Siga el proceso descrito en [Importación de las máquinas virtuales](#import-the-vms), reemplazando los pasos 3 y 4 por los siguientes para tratar los archivos XML e importar las máquinas virtuales a Azure Stack HCI:

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. Complete los pasos restantes que se describen en [Importación de las máquinas virtuales](#import-the-vms).

### <a name="option-2-direct-vhd-copy"></a>Opción 2: Copia directa del archivo VHD

Este método usa Robocopy para copiar los VHD de máquina virtual que se hospedan en Windows 2008 SP1, Windows 2008 R2-SP1 y Windows 2012 en Azure Stack HCI. Con esto se omite la limitación de la versión mínima admitida de la máquina virtual para estas máquinas virtuales anteriores. Se recomienda esta opción para las máquinas virtuales hospedadas en Windows Server 2008 SP1 y Windows Server 2008 R2-SP1.

Las máquinas virtuales hospedadas en Windows 2008 SP1 y Windows 2008 R2-SP1 solo admiten máquinas virtuales de generación 1 con discos duros virtuales de generación 1. Por lo tanto, es necesario crear las máquinas virtuales de generación 1 correspondientes en Azure Stack HCI para que los VHD copiados se puedan conectar a las nuevas máquinas virtuales. Tenga en cuenta que estos VHD no se pueden actualizar a los VHD de generación 2.

> [!NOTE]
> Windows Server 2012 admite máquinas virtuales de generación 1 y de generación 2.

Este es el proceso que se usa:

1. Use el ejemplo de Robocopy para copiar los VHD de máquina virtual directamente en Azure Stack HCI:

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Cree nuevas máquinas virtuales de generación 1. Para obtener información detallada sobre cómo hacerlo, consulte [Administración de máquinas virtuales con Windows Admin Center](../manage/vm.md).

1. Adjunte los archivos VHD copiados a las nuevas máquinas virtuales. Para obtener información detallada, consulte [Administración de discos duros virtuales (VHD)](/windows-server/storage/disk-management/manage-virtual-hard-disks).

Para su información, los siguientes sistemas operativos invitado de Windows Server admiten máquinas virtuales de generación 2:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows 10
- Versiones de 64 bits de Windows 8.1 (64 bits)
- Versiones de 64 bits de Windows 8 (64 bits)
- Linux (consulte [Máquinas virtuales Linux y FreeBSD compatibles con Hyper-V en Windows](/windows-server/virtualization/hyper-v/Supported-Linux-and-FreeBSD-virtual-machines-for-Hyper-V-on-Windows))

## <a name="next-steps"></a>Pasos siguientes

- Valide el clúster después de la migración. Consulte [Validación de un clúster de Azure Stack HCI](validate.md).

- Para migrar a Azure Stack HCI local con el mismo hardware, consulte [Migración a Azure Stack HCI en el mismo hardware](migrate-cluster-same-hardware.md).