---
title: Administración de la capacidad de almacenamiento en Azure Stack Hub
description: Obtenga información acerca de cómo supervisar y administrar la capacidad de almacenamiento y la disponibilidad en Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 9c0f7213a961847e9d8abfd68419db0cab6d2f2c
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509779"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>Administración de la capacidad de almacenamiento en Azure Stack Hub

Este artículo ayuda a los operadores en la nube de Azure Stack Hub a supervisar y administrar la capacidad de almacenamiento de su implementación de Azure Stack Hub. La infraestructura de almacenamiento de Azure Stack Hub asigna un subconjunto de la capacidad total de almacenamiento de la implementación de Azure Stack Hub como *servicios de almacenamiento*. Los servicios de almacenamiento contienen los datos del inquilino en recursos compartidos de volúmenes que corresponden a los nodos de la implementación.

Como operador en la nube, dispone de una cantidad limitada de almacenamiento con la que trabajar. La cantidad de almacenamiento se define por la solución que se implementa. La solución la proporciona el proveedor OEM cuando se usa una solución de varios nodos, o el hardware en el que se instala el Kit de desarrollo de Azure Stack (ASDK).

Dado que Azure Stack Hub no admite la expansión de la capacidad de almacenamiento, es importante [supervisar](#monitor-shares) el almacenamiento disponible para garantizar que se mantiene la eficiencia de las operaciones.

Si la capacidad libre restante de un recurso compartido está limitada, planee [ administrar el espacio disponible](#manage-available-space) para evitar que los recursos compartidos se queden sin capacidad.

Entre las opciones para administrar la capacidad se incluyen:
- Reclamación de la capacidad.
- Migración de un contenedor.

Si se utiliza un recurso compartido al 100 %, el servicio de almacenamiento ya no funciona para ese recurso. Para obtener ayuda para restaurar las operaciones del recurso compartido, póngase en contacto con el soporte técnico de Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Conocimiento de los volúmenes y recursos compartidos, contenedores y discos
### <a name="volumes-and-shares"></a>Volúmenes y recursos compartidos
El *servicio de almacenamiento* crea particiones del almacenamiento disponible en volúmenes independientes e iguales que se asignan para almacenar los datos del inquilino. El número de volúmenes es igual al número de nodos en la implementación de Azure Stack Hub:

- En una implementación de cuatro nodos, hay cuatro volúmenes. Cada volumen tiene un único recurso compartido. En una implementación de varios nodos, el número de recursos compartidos no se reduce si un nodo se quita o no funciona correctamente.
- Si usa el ASDK, hay un único volumen con un solo recurso compartido.

Debido a que los recursos compartidos de los servicios de almacenamiento son para el uso exclusivo de los servicios de almacenamiento, no debe modificar, agregar o quitar directamente ningún archivo en los recursos compartidos. Solo los servicios de almacenamiento deberían funcionar en los archivos almacenados en estos volúmenes.

Los recursos compartidos en los volúmenes contienen datos de inquilino. Los datos de inquilino incluyen blobs en páginas, blobs en bloques, blobs en anexos, tablas, colas, bases de datos y almacenes de metadatos relacionados. Debido a que los objetos de almacenamiento (por ejemplo, blobs) se encuentran contenidos individualmente en un solo recurso compartido, el tamaño máximo de cada objeto no puede exceder el tamaño de un recurso compartido. El tamaño máximo de un nuevo objeto depende de la capacidad que queda en un recurso compartido como espacio no utilizado cuando se crea ese nuevo objeto.

Cuando un recurso compartido tiene poco espacio libre y las acciones para [reclamar](#reclaim-capacity) el espacio no son correctas o no están disponibles, los operadores en la nube de Azure Stack Hub pueden migrar los contenedores de blobs de un recurso compartido a otro.

Para obtener información acerca de cómo trabajan los usuarios del inquilino con el almacenamiento de blobs en Azure Stack Hub, consulte los [servicios de Azure Stack Hub Storage](/azure-stack/user/azure-stack-storage-overview).

### <a name="containers"></a>Contenedores
Los usuarios inquilinos crean contenedores que se usan para almacenar los datos de blobs. Aunque el usuario decide en qué contenedor va a colocar los blobs, el servicio de almacenamiento utiliza un algoritmo para determinar en qué volumen colocar el contenedor. El algoritmo normalmente elige el volumen con el mayor espacio disponible.  

Una vez que el blob se coloca en un contenedor, ese blob puede aumentar para usar más espacio. A medida que agrega nuevos blobs y aumentan los existentes, el espacio disponible en el volumen que contiene ese contenedor se reduce.  

Los contenedores no se limitan a un único recurso compartido. Cuando los datos de blobs combinados en un contenedor aumentan hasta usar un 80 % o más de espacio disponible, el contenedor entra en modo de *desbordamiento*. Si el contenedor se encuentra en modo de desbordamiento, los nuevos blobs que se creen en ese contenedor se asignarán a otro volumen que tenga espacio suficiente. Con el tiempo, un contenedor en modo de desbordamiento puede tener blobs que se distribuyen entre varios volúmenes.

Cuando se utiliza el 80 % (y después el 90 %) del espacio disponible en un volumen, el sistema genera alertas en el portal del administrador de Azure Stack Hub. Los operadores en la nube deben revisar la capacidad de almacenamiento disponible y planear el reequilibrio del contenido. El servicio de almacenamiento deja de funcionar cuando se utiliza un disco al 100 % y no se generan alertas adicionales.

### <a name="disks"></a>Discos
Los inquilinos agregan discos de máquinas virtuales a los contenedores e incluyen un disco del sistema operativo. Las máquinas virtuales también pueden tener uno o más discos de datos. Ambos tipos de discos se almacenan como blobs en páginas. La guía para los inquilinos consiste en colocar cada disco en un contenedor independiente para mejorar el rendimiento de la máquina virtual.

- Cada contenedor que contiene un disco, o blob en páginas, de una máquina virtual, se considera un contenedor asociado a la máquina virtual que posee el disco.
- El contenedor que no contiene ningún disco de una máquina virtual se considera un contenedor libre.

Las opciones para liberar espacio en un contenedor asociado son limitadas. Para más información, consulte [Traslado de discos de máquinas virtuales](#move-vm-disks).

>[!TIP]  
> Los operadores en la nube no administran directamente los discos, que están asociados a las VM que los inquilinos pueden agregar a un contenedor. Sin embargo, al planear la administración del espacio en los recursos compartidos de almacenamiento, puede ser útil comprender cómo se relacionan los discos con los contenedores y los recursos compartidos.

## <a name="monitor-shares"></a>Supervisión de recursos compartidos
Utilice Azure PowerShell o el portal de administración para supervisar los recursos compartidos de manera que pueda comprender cuándo está limitado el espacio libre. Cuando utiliza el portal, recibe alertas sobre los recursos compartidos que se están quedando sin espacio.

### <a name="use-powershell"></a>Uso de PowerShell
Como operador en la nube, puede supervisar la capacidad de almacenamiento de un recurso compartido mediante el cmdlet `Get-AzsStorageShare` de PowerShell. El cmdlet devuelve el espacio total, el asignado y el disponible en bytes en cada uno de los recursos compartidos.

![Ejemplo: Devolución del espacio libre para recursos compartidos](media/azure-stack-manage-storage-shares/free-space.png)

- **Capacidad total**: el espacio total en bytes que está disponible en el recurso compartido. Este espacio se utiliza para los datos y metadatos que se mantienen en los servicios de almacenamiento.
- **Capacidad usada**: es la cantidad de datos en bytes que utilizan todas las extensiones de los archivos que almacenan los datos del inquilino y los metadatos asociados.

### <a name="use-the-administrator-portal"></a>Uso del portal de administración
Como operador en la nube, puede utilizar el portal de administración para ver la capacidad de almacenamiento de todos los recursos compartidos.

1. Inicie sesión en el [portal de administración](https://adminportal.local.azurestack.external).
2. Seleccione **Todos los servicios** > **Almacenamiento** > **Recursos compartidos de archivos** para abrir la lista de recursos compartidos de archivos donde puede ver la información de uso.

    ![Ejemplo: Recursos compartidos de almacenamiento en el portal del administrador de Azure Stack Hub](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total**: el espacio total en bytes que está disponible en el recurso compartido. Este espacio se utiliza para los datos y metadatos que se mantienen en los servicios de almacenamiento.
   - **Utilizado**: es la cantidad de datos en bytes que utilizan todas las extensiones de los archivos que almacenan los datos del inquilino y los metadatos asociados.

### <a name="storage-space-alerts"></a>Alertas de espacio de almacenamiento
Cuando utiliza el portal de administración, recibe alertas sobre los recursos compartidos que se están quedando sin espacio.

> [!IMPORTANT]
> Como operador en la nube, debe evitar que los recursos compartidos alcancen el uso total. Si se utiliza un recurso compartido al 100 %, el servicio de almacenamiento ya no funciona para ese recurso. Para recuperar el espacio libre y restaurar las operaciones en un recurso compartido que se utiliza al 100 %, debe ponerse en contacto con el soporte técnico de Microsoft.

* **Advertencia**: si la utilización de un recurso compartido de archivos supera el 80 %, recibirá una alerta de tipo *Advertencia* en el portal de administración:

  ![Ejemplo: Alerta de advertencia en el portal de administración de Azure Stack Hub](media/azure-stack-manage-storage-shares/alert-warning.png)

* **Crítico**: si la utilización de un recurso compartido de archivos supera el 90 %, recibirá una alerta de tipo *Crítica* en el portal de administración:

  ![Ejemplo: Alerta crítica en el portal de administración de Azure Stack Hub](media/azure-stack-manage-storage-shares/alert-critical.png)

* **Ver detalles**: en el portal de administración, puede abrir los detalles de una alerta para ver las opciones de mitigación:

  ![Ejemplo: Visualización de detalles de la alerta en el portal de administración de Azure Stack Hub](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>Administración del espacio disponible
Cuando sea necesario liberar espacio en un recurso compartido, primero utilice los métodos menos invasivos. Por ejemplo, intente recuperar espacio antes de elegir migrar un contenedor.  

### <a name="reclaim-capacity"></a>Reclamación de capacidad
*Esta opción se aplica a las implementaciones de varios nodos y al Kit de desarrollo de Azure Stack.*

Puede reclamar la capacidad que utilizan las cuentas de inquilinos que se han eliminado. Esta capacidad se reclama de manera automática cuando se alcanza el [período de retención](azure-stack-manage-storage-accounts.md#set-the-retention-period) de datos, o bien puede actuar para reclamarla de forma inmediata.

Para más información, consulte la sección "Reclamación de capacidad" de [Administración de cuentas de almacenamiento de Azure Stack Hub](azure-stack-manage-storage-accounts.md#reclaim).

### <a name="migrate-a-container-between-volumes"></a>Migración de un contenedor entre volúmenes
*Esta opción solo se aplica a sistemas integrados de Azure Stack Hub.*

Debido a los patrones de uso de inquilinos, algunos recursos compartidos de inquilino usan más espacio que otros. El resultado puede ser un recurso compartido que se queda sin espacio antes que otros recursos compartidos que están relativamente sin usar.

Puede tratar de liberar espacio en un recurso compartido sobreutilizado al migrar manualmente algunos contenedores de blobs a otro recurso compartido. Puede migrar varios contenedores más pequeños a un solo recurso compartido con capacidad para contenerlos todos. Puede utilizar la migración para mover contenedores *libres*. Los contenedores libres son contenedores que no contienen un disco para una VM.

La migración consolida todos los blobs de contenedores en el nuevo recurso compartido.

- Si un contenedor ha entrado en modo de desbordamiento y ha colocado blobs en volúmenes adicionales, el nuevo recurso compartido debe tener capacidad suficiente para contener todos los blobs del contenedor que migre. Esto incluye los blobs que se encuentran en recursos compartidos adicionales.

- El cmdlet de PowerShell `Get-AzsStorageContainer` identifica únicamente el espacio en uso en el volumen inicial de un contenedor. El cmdlet no identifica el espacio que utilizan los blobs colocados en volúmenes adicionales. Por lo tanto, el tamaño completo de un contenedor puede no ser evidente. Es posible que la consolidación de un contenedor en un nuevo recurso compartido pueda enviar ese nuevo recurso compartido a una condición de desbordamiento donde se coloquen datos en recursos compartidos adicionales. Como resultado, es posible que tenga que volver a equilibrar los recursos compartidos.

- Si carece de permisos para un determinado grupo de recursos y no puede utilizar PowerShell para consultar los volúmenes adicionales de datos de desbordamiento, colabore con el propietario de esos grupos de recursos y contenedores para comprender la cantidad total de datos que se van a migrar antes de realizar la migración.  

> [!IMPORTANT]
> La migración de blobs para un contenedor es una operación sin conexión que requiere el uso de PowerShell. Hasta que se completa la migración, todos los blobs del contenedor que está migrando permanecen sin conexión y no se pueden utilizar. También debe evitar la actualización de Azure Stack Hub hasta que se completen todas las migraciones en curso.

#### <a name="migrate-containers-by-using-powershell"></a>Migración de contenedores mediante PowerShell
1. Confirme que ha [instalado y configurado Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Para más información, consulte [Administración de recursos de Azure con Azure PowerShell](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Examine el contenedor para entender qué datos hay sobre el recurso compartido que planea migrar. Para identificar los mejores contenedores candidatos para la migración en un volumen, utilice el cmdlet `Get-AzsStorageContainer`:

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   A continuación, examine $containers:

   ```powershell
   $containers
   ```

   ![Ejemplo: $containers](media/azure-stack-manage-storage-shares/containers.png)

3. Identifique los mejores recursos compartidos de destino para albergar el contenedor que va a migrar:

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   A continuación, examine $destinationshares:

   ```powershell 
   $destinationshares
   ```

   ![Ejemplo: Recursos compartidos de $destination](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Inicie la migración de un contenedor. La migración es asincrónica. Si comienza la migración de contenedores adicionales antes de que se complete la primera migración, utilice el identificador de trabajo para realizar un seguimiento del estado de cada una.

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   A continuación, examine $jobId. En el ejemplo siguiente, reemplace *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* por el identificador de trabajo que quiera examinar:

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Use el identificador de trabajo para comprobar el estado del trabajo de migración. Cuando finalice la migración del contenedor, **MigrationStatus** se establece como *Completado*.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Ejemplo: Estado de migración](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Puede cancelar un trabajo de migración en curso. Los trabajos de migración cancelados se procesan de forma asincrónica. Puede realizar el seguimiento de las cancelaciones mediante $jobid:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Ejemplo: Estado de reversión](media/azure-stack-manage-storage-shares/rollback.png)

7. Puede ejecutar de nuevo el comando desde el paso 6, hasta que el estado de la migración sea *Cancelado*:  

    ![Ejemplo: Estado cancelado](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Traslado de discos de máquinas virtuales
*Esta opción solo se aplica a sistemas integrados de Azure Stack Hub.*

El método más extremo para administrar el espacio implica el traslado de discos de máquinas virtuales. Como mover un contenedor asociado (uno que contiene un disco de máquina virtual) es complejo, póngase en contacto con el soporte técnico de Microsoft para realizar esta acción.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo ofrecer máquinas virtuales a los usuarios, consulte [Administración de la capacidad de almacenamiento en Azure Stack Hub](azure-stack-tutorial-tenant-vm.md).
