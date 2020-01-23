---
title: Procedimientos recomendados de SQL Server para optimizar el rendimiento en Azure Stack Hub. | Microsoft Docs
description: Este artículo proporciona procedimientos recomendados de SQL server para ayudar a aumentar el rendimiento y optimizar SQL Server en máquinas virtuales de Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 63fbc121aea2fe83b2ffda860f505fe069a4a5f1
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023066"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack-hub"></a>Procedimientos recomendados de SQL Server para optimizar el rendimiento en Azure Stack Hub.

Este artículo proporciona procedimientos recomendados de SQL Server para optimizar SQL Server y mejorar el rendimiento de las máquinas virtuales de Microsoft Azure Stack Hub. Mientras se ejecuta SQL Server en máquinas virtuales de Azure Stack Hub, use las mismas opciones de ajuste de rendimiento de base de datos que son aplicables a SQL Server en el entorno de servidor local. El rendimiento de una base de datos relacional en una nube de Azure Stack Hub depende de muchos factores, entre los que se incluyen el tamaño de la familia de una máquina virtual y la configuración de los discos de datos.

Al crear imágenes de SQL Server, [considere la posibilidad de aprovisionar las máquinas virtuales en el portal de Azure Stack Hub](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Descargue la extensión IaaS de SQL de Administración de Marketplace en el portal del administrador de Azure Stack Hub y descargue las imágenes de la máquinas virtuales con SQL Server de su elección. Entre ellas se incluyen SQL Server 2016 SP1, SQL Server 2016 SP2 y SQL Server 2017.

> [!NOTE]  
> Si bien en el artículo se describe cómo aprovisionar una máquina virtual de SQL Server mediante Azure Portal global, las instrucciones se aplican también a Azure Stack Hub con las siguientes diferencias: SSD no está disponible para el disco del sistema operativo y hay pequeñas diferencias en la configuración del almacenamiento.

En las imágenes de máquina virtual, para SQL Server, solo puede usar el modelo BYOL (traiga su propia licencia). En Windows Server, el modelo de licencia predeterminado es el de pago por uso (PAYG). Para obtener información detallada sobre el modelo de licencia de Windows Server en una máquina virtual, consulte el artículo [Preguntas frecuentes sobre Windows Server en el Marketplace de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-windows-server-faq#what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server).  

La obtención del *mejor* rendimiento de SQL Server en máquinas virtuales de Azure Stack Hub es el objetivo principal de este artículo. Si su carga de trabajo es menos exigente, podría no necesitar todas las optimizaciones recomendadas. Tenga en cuenta sus necesidades de rendimiento y patrones de carga de trabajo a medida que evalúe estas recomendaciones.

> [!NOTE]  
> Para obtener la guía de rendimiento de SQL Server en máquinas virtuales de Azure, consulte [este artículo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Lista de comprobación de procedimientos recomendados de SQL Server

La siguiente es una lista de comprobación para un rendimiento óptimo de SQL Server en máquinas virtuales de Azure Stack Hub:


|Área|Optimizaciones|
|-----|-----|
|Tamaño de VM |[DS3](azure-stack-vm-sizes.md) o posteriores para SQL Server Enterprise Edition.<br><br>[DS2](azure-stack-vm-sizes.md) o posteriores para ediciones de SQL Server Standard y Web.|
|Storage |Use una familia de máquinas virtuales que admita [almacenamiento Premium](azure-stack-acs-differences.md).|
|Discos |Use un mínimo de dos discos de datos (uno para los archivos de registro y otro para el archivo de datos y TempDB) y elija el tamaño del disco en función de sus necesidades de capacidad. Establezca las ubicaciones predeterminadas de los archivos de datos para estos discos durante la instalación de SQL Server.<br><br>Evite el uso del sistema operativo o de discos temporales para el registro o almacenamiento de bases de datos.<br>Seccione varios discos de datos de Azure para obtener un mayor rendimiento de E/S mediante Espacios de almacenamiento.<br><br>Formato con tamaños de asignación documentados.|
|E/S|Habilite la inicialización instantánea de archivos para archivos de datos.<br><br>Limite el crecimiento automático de las bases de datos con incrementos fijos razonablemente pequeños (64 MB a 256 MB).<br><br>Deshabilite la reducción automática de la base de datos.<br><br>Configure las ubicaciones predeterminadas de los archivos de copia de seguridad y de base de datos en los discos de datos y no en el disco del sistema operativo.<br><br>Habilite páginas bloqueadas.<br><br>Aplique los Service Pack y las actualizaciones acumulativas de SQL Server.|
|Características específicas|Haga una copia de seguridad directamente en Blob Storage (si la versión de SQL Server en uso lo admite).|
|||

Para más información acerca de *cómo* y *por qué* llevar a cabo estas optimizaciones, repase los detalles y la guía que se proporcionan en las siguientes secciones.

## <a name="vm-size-guidance"></a>Orientación sobre el tamaño de máquina virtual

En aplicaciones sensibles al rendimiento, se recomienda usar los siguientes [tamaños de máquinas virtuales](azure-stack-vm-sizes.md):

- **SQL Server Enterprise Edition:** DS3 o superior

- **Ediciones de SQL Server Standard y Web:** DS2 o superior

Con Azure Stack Hub no hay ninguna diferencia de rendimiento entre la familia de máquinas virtuales de las series DS y DS_v2.

## <a name="storage-guidance"></a>Orientación sobre el almacenamiento

Las máquinas virtuales de la serie DS (junto con las de la serie DSv2) de Azure Stack Hub proporcionan el máximo rendimiento (IOPS) en los discos de sistema operativo y en los discos de datos. Una máquina virtual de las series DS o DSv2 proporciona hasta 1000 IOPS para el disco del sistema operativo y hasta 2300 IOPS por disco de datos, independientemente del tipo o el tamaño del disco elegido.

El rendimiento del disco de datos se determina exclusivamente en función de la serie a la que pertenece la familia de máquinas virtuales. También puede [consultar este artículo](azure-stack-vm-sizes.md) para identificar el rendimiento del disco de datos por cada serie de la familia de máquinas virtuales.

> [!NOTE]  
> Para las cargas de trabajo de producción, seleccione una máquina virtual de las series DS o DSv2 para proporcionar el número máximo de IOPS posible en los discos del sistema operativo y en los de datos.

Al crear una cuenta de almacenamiento en Azure Stack Hub, la opción de replicación geográfica no tiene ningún efecto, ya que esta funcionalidad no está disponible en Azure Stack Hub.

## <a name="disks-guidance"></a>Orientación sobre los discos

En una máquina virtual de Azure Stack Hub hay tres tipos de disco principales:

- **Disco del sistema operativo:** al crear una máquina virtual de Azure Stack Hub, la plataforma conectará al menos un disco (etiquetado como unidad **C**) a la máquina virtual como disco del sistema operativo. El disco es un VHD almacenado como blob en páginas en almacenamiento.

- **Disco temporal:** Las máquinas virtuales de Azure Stack Hub contienen otro disco denominado disco temporal (etiquetado como unidad **D**). Se trata de un disco en el nodo que se puede usar para el espacio de desecho.

- **Discos de datos:** Puede conectar más discos a la máquina virtual como discos de datos. Estos se guardarán en el almacenamiento como blobs en páginas.

En las siguientes secciones se incluyen recomendaciones sobre cómo usar cada uno de estos discos.

### <a name="operating-system-disk"></a>Disco del sistema operativo

Un disco del sistema operativo es un VHD que se puede arrancar y montar como una versión en ejecución de un sistema operativo y está etiquetado como la unidad **C** .

### <a name="temporary-disk"></a>Disco temporal

La unidad de almacenamiento temporal, etiquetada como unidad **D**, no es persistente. No almacene en la unidad **D** datos que no desee perder bajo ningún concepto. Aquí se pueden incluir los archivos de base de datos de usuario y los archivos de registro de transacciones de usuario.

Se recomienda almacenar TempDB en un disco de datos, ya que cada disco de datos proporciona un máximo de hasta 2300 IOPS por disco de datos.

### <a name="data-disks"></a>Discos de datos.

- **Uso de discos de datos para archivos de datos y de registro.** Si no va a usar el fraccionamiento en discos, utilice dos discos de datos de una máquina virtual que admita Premium Storage, uno para los archivos de registro y el otro para los archivos de datos y TempDB. Cada disco de datos proporciona un número de IOPS que depende de la familia de máquinas virtuales, como se indica en [Tamaños de máquinas virtuales admitidos en Azure Stack Hub](azure-stack-vm-sizes.md). Si usa una técnica de fragmentación de discos, como Espacios de almacenamiento, coloque todos los archivos en la misma unidad (incluido TempDB). Esta configuración le proporciona el número máximo de IOPS disponible para que los consuma SQL Server, con independencia de las necesidades de archivo en un momento dado.

> [!NOTE]  
> Al aprovisionar una máquina virtual de SQL Server en el portal, tiene la opción de modificar la configuración de almacenamiento. Según la configuración, Azure Stack Hub configura uno o varios discos. Varios discos se combinan en un único grupo de almacenamiento. Los archivos de datos y de registro residen juntos en esta configuración.

- **Seccionamiento del disco:** Para disfrutar de un mayor rendimiento, puede agregar más discos de datos y usar el seccionamiento de discos. Para determinar el número de discos de datos, debe analizar el número de IOPS necesario para los archivos de registro, así como para los datos y los archivos TempDB. Tenga en cuenta que los límites de IOPS son por disco de datos y se basan en la familia de la serie de máquinas virtuales y no en el tamaño de estas. No obstante, los límites en el ancho de banda de la red, se basan en el tamaño de la máquina virtual. Consulte las tablas de [tamaños de máquina virtual de Azure Stack Hub](azure-stack-vm-sizes.md) para más información. Use estas directrices:

  - Para Windows Server 2012 o posterior, use [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739.aspx) con las siguientes directrices:

    1. Configure la intercalación (tamaño de franja) en 64 KB (65 536 bytes) para cargas de trabajo de procesamiento de transacciones en línea (OLTP) y 256 KB (262 144 bytes) para cargas de trabajo de almacenamiento de datos para evitar que el rendimiento se vea afectado debido a la falta de alineación de las particiones. Esta característica debe establecerse con PowerShell.

    2. Establezca recuento de columnas = número de discos físicos. Use PowerShell (no la interfaz de usuario del Administrador del servidor) al configurar más de ocho discos.

       Por ejemplo, aquí PowerShell crea un nuevo grupo de almacenamiento con el tamaño de intercalación establecido en 64 KB y el número de columnas en 2:

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Determine el número de discos asociados al grupo de almacenamiento en función de sus expectativas de carga. Tenga en cuenta que diferentes tamaños de máquina virtual permiten diferentes números de discos de datos conectados. Para más información, consulte [Tamaños de máquinas virtuales admitidos en Azure Stack Hub](azure-stack-vm-sizes.md).
- Para conseguir el máximo número de IOPS posible para los discos de datos, se recomienda agregar el número máximo de discos de datos que admita el [tamaño de la máquina virtual](azure-stack-vm-sizes.md) y usar el fraccionamiento en discos.
- **Tamaño de la unidad de asignación de NTFS:** Al formatear el disco de datos, se recomienda usar un tamaño de unidad de asignación de 64 KB no solo para los archivos de datos y de registro, sino también para TempDB.
- **Procedimientos recomendados de administración de discos:** Al quitar un disco de datos, detenga el servicio SQL Server durante el cambio. Además, no cambie la configuración de caché en los discos ya que esto no supone ninguna mejora del rendimiento.

> [!WARNING]  
> De no detenerse el servicio SQL durante estas operaciones, podrían producirse daños en la base de datos.

## <a name="io-guidance"></a>Orientación sobre E/S

- Considere la posibilidad de habilitar la inicialización instantánea de archivos para reducir el tiempo necesario para la asignación inicial de archivos. Para aprovechar la inicialización instantánea de archivos, se concede la cuenta de servicio de SQL Server (MSSQLSERVER) con **SE_MANAGE_VOLUME_NAME** y se agrega a la directiva de seguridad **Realizar tareas de mantenimiento del volumen**. Si usa una imagen de plataforma de SQL Server para Azure, la cuenta de servicio predeterminada (**NT Service\MSSQLSERVER**) no se agrega a la directiva de seguridad **Realizar tareas de mantenimiento del volumen**. En otras palabras, la inicialización instantánea de archivos no se habilita en una imagen de la plataforma de SQL Server para Azure. Después de agregar la cuenta de servicio de SQL Server a la directiva de seguridad **Realizar tareas de mantenimiento del volumen** , reinicie el servicio de SQL Server. Puede haber consideraciones de seguridad para usar esta característica. Para obtener más información, vea [Inicialización de archivos de base de datos](https://msdn.microsoft.com/library/ms175935.aspx).
- **Crecimiento automático** es una contingencia para el crecimiento inesperado. No administre el crecimiento de los datos y del registro a diario con el crecimiento automático. Si se usa el crecimiento automático, haga crecer previamente el archivo mediante el modificador **Tamaño**.
- Asegúrese de que la **reducción automática** está deshabilitada para evitar una sobrecarga innecesaria que puede afectar negativamente al rendimiento.
- Configure ubicaciones predeterminadas para los archivos de base de datos y de copia de seguridad. Use las recomendaciones de este artículo y realice los cambios necesarios en la ventana Propiedades del servidor. Para obtener instrucciones, consulte [Ver o cambiar las ubicaciones predeterminadas de los archivos de datos y registro (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). En la siguiente captura de pantalla se muestra dónde se realizan estos cambios:

    > ![Ver o cambiar las ubicaciones predeterminadas](./media/sql-server-vm-considerations/image1.png)

- Habilite páginas bloqueadas para reducir la E/S y las actividades de paginación. Para más información, consulte [Habilitar la opción Bloquear páginas en la memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Considere la posibilidad de comprimir los archivos de datos a la hora de transferirlos dentro y fuera de Azure Stack Hub, incluidas las copias de seguridad.

## <a name="feature-specific-guidance"></a>Guía específica de la característica

Algunas implementaciones pueden lograr ventajas de rendimiento adicionales mediante técnicas de configuración más avanzadas. En la siguiente lista se destacan algunas características de SQL Server que pueden ayudarle a lograr un mejor rendimiento:

- **Copia de seguridad en Azure** **Storage.** Al realizar copias de seguridad para SQL Server que se ejecutan en máquinas virtuales de Azure Stack Hub, puede usar Copia de seguridad en URL de SQL Server. Esta característica está disponible a partir de SQL Server 2012 SP1 CU2 y se recomienda para las copias de seguridad en los discos de datos conectados.

    Si realiza operaciones de copia de seguridad o restauración mediante Azure Storage, siga las recomendaciones que se ofrecen en [Prácticas recomendadas y solución de problemas de Copia de seguridad en URL de SQL Server](https://msdn.microsoft.com/library/jj919149.aspx) y [Restauración a partir de copias de seguridad almacenadas en Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). También puede automatizar estas copias de seguridad con [Automated Backup para SQL Server en Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Copia de seguridad en Azure Stack Hub Storage.** Puede hacer una copia de seguridad en Azure Stack Hub Storage de forma parecida a la que lo hace en Azure Storage. Cuando se crea una copia de seguridad en SQL Server Management Studio (SSMS), debe escribir manualmente la información de configuración. No se puede usar SSMS para crear el contenedor de almacenamiento ni la firma de acceso compartido. SSMS solo se conecta a suscripciones de Azure, no a suscripciones de Azure Stack Hub. En su lugar, debe crear la cuenta de almacenamiento, el contenedor y la firma de acceso compartido en el portal de Azure Stack Hub o con PowerShell.


    ![Copia de seguridad de SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > La firma de acceso compartido es el token de SAS del portal de Azure Stack Hub, sin el carácter "?" inicial en la cadena. Si utiliza la función de copia del portal, debe eliminar el carácter "?" inicial del token para trabajar en SQL Server.

    Una vez que haya configurado el destino de la copia de seguridad en SQL Server, puede hacer una copia de seguridad en el almacenamiento de blobs de Azure Stack Hub.

## <a name="next-steps"></a>Pasos siguientes

[Uso de los servicios o creación de aplicaciones para Azure Stack Hub](azure-stack-considerations.md)
