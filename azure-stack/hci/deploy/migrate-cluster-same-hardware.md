---
title: Migración a Azure Stack HCI en el mismo hardware
description: Obtenga información acerca de cómo migrar un clúster a Azure Stack HCI en el mismo hardware.
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: ea19dbbdd85f29eb036a0220828bbbb7bca33ea7
ms.sourcegitcommit: d91d44762383790a0bcfc4a85f43050c8528d5d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2020
ms.locfileid: "97069820"
---
# <a name="migrate-to-azure-stack-hci-on-same-hardware"></a>Migración a Azure Stack HCI en el mismo hardware

> Se aplica a Azure Stack HCI, versión 20H2, Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 y Windows Server 2008 R2.

En este tema se describe cómo migrar un clúster con Windows Server 2016 o Windows Server 2019 a Azure Stack HCI con el hardware de servidor existente. Este proceso instala el nuevo sistema operativo Azure Stack HCI, conserva la configuración y el almacenamiento del clúster existentes e importa las máquinas virtuales.

En el diagrama siguiente se muestra la migración local del clúster con Windows Server con el mismo hardware de servidor. Después de apagar el clúster, Azure Stack HCI está instalado, se vuelve a conectar el almacenamiento y las máquinas virtuales se importan y se hace que sean de alta disponibilidad (HA).

:::image type="content" source="media/migrate/migrate-cluster-same-hardware.png" alt-text="Migración del clúster a Azure Stack HCI en el mismo hardware" lightbox="media/migrate/migrate-cluster-same-hardware.png":::

Para migrar las máquinas virtuales a un nuevo hardware con Azure Stack HCI, consulte [Migración a Azure Stack HCI en hardware nuevo](migrate-cluster-new-hardware.md).

> [!NOTE]
> La migración de clústeres extendidos no se trata en este artículo.

## <a name="before-you-begin"></a>Antes de empezar

Hay varios requisitos y aspectos que se deben tener en cuenta antes de comenzar la migración:

- Todos los comandos de Windows PowerShell se deben ejecutar como administrador.

- Debe tener credenciales de dominio con permisos de administrador para Azure Stack HCI.

- Realice una copia de seguridad de todas las máquinas virtuales del clúster de origen. Complete una copia de seguridad coherente con el bloqueo de todas las aplicaciones y datos y una copia de seguridad coherente con la aplicación de todas las bases de datos.  Para realizar una copia de seguridad en Azure, consulte [Uso de Azure Backup para hacer una copia de seguridad de las instancias de Windows Server](https://docs.microsoft.com/azure-stack/hci/manage/use-azure-backup).

- Recopile el inventario y la configuración de todos los nodos del clúster y la nomenclatura del clúster, la configuración de red, la resistencia y la capacidad del volumen compartido de clúster (CSV) y el testigo de cuórum.

- Apague las máquinas virtuales del clúster, desconecte el CSV, los bloques de almacenamiento y el servicio del clúster.
- Deshabilite el objeto de nombres del clúster (CNO) (se reutilizará más adelante) y:
    - Compruebe que el CNO tiene derechos para crear objetos en su propia unidad organizativa (UO).
    - Compruebe que se ha establecido la directiva heredada de bloqueo en la unidad organizativa.
    - Establezca la directiva necesaria para Azure Stack HCI en esta unidad organizativa.

## <a name="vm-version-support-and-update"></a>Compatibilidad con versiones de máquina virtual y actualización

En la tabla siguiente se enumeran las versiones admitidas del sistema operativo Windows Server y sus versiones de máquina virtual para la migración local en el mismo hardware.

Independientemente de la versión del sistema operativo en la que se pueda estar ejecutando una máquina virtual, la versión mínima de máquina virtual compatible con la migración a Azure Stack HCI es la versión 5.0. Por lo tanto, todas las máquinas virtuales que se ejecuten en la versión 2.0, 3.0 o 4.0 en el clúster con Windows Server 2016 o Windows Server 2019 se deben actualizar a la versión 5.0 antes de la migración.

|Versión del SO|Versión de VM|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4.0|
|Windows Server 2012 R2|5.0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

En el caso de máquinas virtuales en clústeres con Windows Server 2008 SP1, Windows Server 2008 R2-SP1 y Windows 2012, no se admite la migración directa a Azure Stack HCI. En estos casos, tiene dos opciones:

- Migre estas máquinas virtuales a Windows Server 2012 R2, Windows Server 2016 o Windows Server 2019 en primer lugar, actualice la versión de la máquina virtual y, a continuación, inicie el proceso de migración.

- Use Robocopy para copiar todos los VHD de máquina virtual en Azure Stack HCI. A continuación, cree nuevas máquinas virtuales y conecte los VHD copiados a las máquinas virtuales respectivas en Azure Stack HCI. Con esto se omite la limitación de la versión de la máquina virtual para estas máquinas virtuales anteriores.

## <a name="updating-the-vm-version"></a>Actualización de la versión de la máquina virtual

Use el siguiente comando para mostrar todas las versiones de máquina virtual de un solo servidor:

```powershell
Get-VM * | Format-Table Name,Version
```

Para mostrar todas las versiones de máquina virtual en todos los nodos del clúster con Windows Server:

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

Para actualizar todas las máquinas virtuales a la última versión en todos los nodos con Windows Server:

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="updating-the-servers-and-cluster"></a>Actualización de los servidores y el clúster

La migración consiste en ejecutar el programa de instalación de Azure Stack HCI en la implementación de Windows Server para realizar una instalación limpia del sistema operativo con las máquinas virtuales y el almacenamiento intactos. Esto reemplaza el sistema operativo actual por Azure Stack HCI. Para obtener información detallada, consulte [Implementación del sistema operativo Azure Stack HCI](operating-system.md). Después, cree un nuevo clúster de Azure Stack HCI, vuelva a conectar el almacenamiento e importe las máquinas virtuales.

1. Apague las máquinas virtuales existentes en el clúster, desconecte el CSV, los bloques de almacenamiento y el servicio del clúster.

1. Vaya a la ubicación donde descargó los archivos binarios de Azure Stack HCI y ejecute la instalación de Azure Stack HCI en cada nodo con Windows Server.

1. Durante la instalación, seleccione **Custom: Install the newer version of Azure Stack HCI only (advanced)** (Personalizada: instalar solo la versión más reciente de Azure Stack HCI [avanzada]). Repita el procedimiento para cada servidor.

1. Cree el nuevo clúster de Azure Stack HCI. Puede usar Windows Admin Center o Windows PowerShell para hacerlo, tal como se describe a continuación.  

> [!IMPORTANT]
> El nombre del conmutador virtual de Hyper-V (`VMSwitch`) debe ser el mismo nombre capturado en el inventario de configuración del clúster. Asegúrese de que el nombre del conmutador virtual usado en el clúster de Azure Stack HCI coincida con el nombre del conmutador virtual de origen original antes de importar las máquinas virtuales.

> [!NOTE]
> Debe registrar el clúster de Azure Stack HCI en Azure antes de poder crear nuevas máquinas virtuales en él. Para más información, consulte [Conexión de Azure Stack HCI a Azure](register-with-azure.md).

### <a name="using-windows-admin-center"></a>Uso de Windows Admin Center

Si usa Windows Admin Center para crear el clúster de Azure Stack HCI, el Asistente para crear clústeres instalará automáticamente todos los roles y características necesarios en cada nodo de servidor.

Para información detallada sobre cómo crear el clúster, consulte [Creación de un clúster de Azure Stack HCI mediante Windows Admin Center](create-cluster.md).

> [!IMPORTANT]
> Omita el paso **4.1 Limpieza de las unidades** en el Asistente para crear clústeres. De lo contrario, eliminará las máquinas virtuales y el almacenamiento existentes.

1. Inicie el Asistente para crear clústeres. Cuando llegue a **Paso 4: Almacenamiento**:

1. omita el paso **4.1 Limpieza de las unidades**. No lo haga.

1. Abandone temporalmente el asistente.

1. Abra PowerShell y ejecute el siguiente cmdlet para crear el nuevo identificador `Storagesubsystem Object`, vuelva a detectar todos los contenedores de almacenamiento y asigne números de unidad de SES:

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

    Si realiza la migración desde Windows Server 2016, también se crea el nuevo volumen ReFS `ClusterperformanceHistory` y se asigna al grupo de recursos del clúster de SDDC.

    Si realiza la migración desde Windows Server 2019, también se agrega el volumen ReFS `ClusterperformanceHistory` existente y se asigna al grupo de recursos del clúster de SDDC.

1. Vuelva al asistente. En el paso **4.2 Comprobar las unidades**, compruebe que todas las unidades aparecen enumeradas sin advertencias ni errores.

1. Complete el asistente.

### <a name="using-windows-powershell"></a>Uso de Windows PowerShell

Si usa PowerShell para crear el clúster de Azure Stack HCI, se deben instalar los siguientes roles y características en cada nodo del clúster de Azure Stack HCI mediante el siguiente cmdlet:

```powershell
Install-WindowsFeature -Name Hyper-V, Failover-Clustering, FS-Data-Deduplication, Bitlocker, Data-Center-Bridging, RSAT-AD-PowerShell -IncludeAllSubFeature -IncludeManagementTools -Verbose
```

Para más información sobre cómo crear el clúster con PowerShell, consulte [Creación de un clúster de Azure Stack HCI mediante Windows PowerShell](create-cluster-powershell.md).

> [!NOTE]
> Vuelva a usar el mismo nombre para el objeto de nombres del clúster deshabilitado anteriormente.

1. Ejecute el siguiente cmdlet para crear el clúster:

    ```powershell
    New-cluster –name "clustername" –node Server01,Server02 –staticaddress xx.xx.xx.xx –nostorage
    ```

1. Ejecute el siguiente cmdlet para crear el nuevo identificador `Storagesubsystem Object`, vuelva a detectar todos los contenedores de almacenamiento y asigne números de unidad de SES:

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

1. Si realiza la migración desde Windows Server 2016, también se crea el nuevo volumen ReFS `ClusterperformanceHistory` y se asigna al grupo de recursos del clúster de SDDC.

    > [!NOTE]
    > Si un bloque de almacenamiento muestra errores de disco minoritarios (visible en el administrador de clústeres), vuelva a ejecutar el cmdlet `Enable-ClusterS2D -verbose`.

1. Con el administrador de clústeres, habilite cada CSV excepto el volumen `ClusterperformanceHistory`, que es un volumen ReFS (asegúrese de que no sea un CSV de ReFS).

1. Si va a migrar desde Windows Server 2019, vuelva a ejecutar el cmdlet `Enable-ClusterS2D -verbose`. Esto asociará el volumen ReFS `ClusterperformanceHistory` con el grupo de recursos del clúster de SDDC.

1. Para determinar la versión y el nombre del bloque de almacenamiento actual, ejecute lo siguiente:

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Ahora, determine el nombre y la versión del nuevo bloque de almacenamiento:

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Cree el testigo de cuórum. Para obtener información sobre cómo hacerlo, consulte [Configuración de un testigo del clúster](https://docs.microsoft.com/azure-stack/hci/deploy/witness).

1. Utilice lo siguiente para comprobar que los trabajos de reparación del almacenamiento se han completado:

    ```powershell
    Get-StorageJob
    ```

    > [!NOTE]
    >Esto puede tardar un tiempo considerable en función del número de máquinas virtuales que se ejecutan durante la actualización.

1. Compruebe que todos los discos se encuentran en estado correcto:

    ```powershell
    Get-VirtualDisk
    ```

1. Determine la versión del nodo del clúster, que muestra `ClusterFunctionalLevel` y `ClusterUpgradeVersion`. Ejecute lo siguiente para obtener esta información:

    ```powershell
    Get-ClusterNodeSupportedVersion
    ```

    > [!NOTE]
    > `ClusterFunctionalLevel` se establece automáticamente en `10` y no requiere actualización debido al nuevo sistema operativo y a la creación del clúster.

1. Actualice el bloque de almacenamiento de la siguiente manera:

    ```powershell
    Get-StoragePool | Update-StoragePool
    ```

## <a name="refs-volumes"></a>Volúmenes ReFS

Si se realiza la migración desde Windows Server 2016, se admiten los volúmenes del Sistema de archivos resistente (ReFS), pero estos volúmenes no se benefician de las siguientes mejoras de rendimiento de Azure Stack HCI:

- Paridad acelerada por reflejo
- Omisión del registro de MAP

Estas mejoras requieren la creación de un nuevo volumen ReFS con el cmdlet `New-Volume`.

> [!NOTE]
> En el caso de los volúmenes con paridad acelerada por reflejo de Windows Server 2016, la compactación de ReFS no está disponible, por lo que volver a conectar estos volúmenes es correcto, pero tendrán menos rendimiento en comparación con la creación de un nuevo volumen MAP en un clúster de Azure Stack HCI.

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

1. Finalmente, actualice las máquinas virtuales a la versión más reciente de Azure Stack HCI para sacar el máximo partido de todos los avances:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

## <a name="next-steps"></a>Pasos siguientes

- Valide el clúster después de la migración. Consulte [Validación de un clúster de Azure Stack HCI](validate.md).
- Para migrar las máquinas virtuales con Windows Server a un nuevo hardware con Azure Stack HCI, consulte [Migración a Azure Stack HCI en hardware nuevo](migrate-cluster-new-hardware.md).