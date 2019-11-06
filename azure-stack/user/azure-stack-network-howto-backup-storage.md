---
title: Copia de seguridad de las cuentas de almacenamiento en Azure Stack | Microsoft Docs
description: Aprenda a hacer una copia de seguridad de las cuentas de almacenamiento en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 3f6ed5604bd2d32d9d030ceb4b5f67567362cc34
ms.sourcegitcommit: 4d7611d81da6f2f8ef50adab3c09f9122a75bc9d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73145839"
---
# <a name="back-up-your-storage-accounts-on-azure-stack"></a>Copia de seguridad de las cuentas de almacenamiento en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se examina la protección y la recuperación de las cuentas de almacenamiento dentro de una cuenta de Azure Storage en Azure Stack.

## <a name="elements-of-the-solution"></a>Elementos de la solución

En esta sección se examina la estructura general de la solución y las partes principales.

![Copia de seguridad del almacenamiento de Azure Stack](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>Nivel de aplicación

Los datos se pueden replicar entre las cuentas de almacenamiento en unidades de escalado de Azure Stack independientes mediante la emisión de varias operaciones [PUT Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob) o [Put Block](https://docs.microsoft.com/rest/api/storageservices/put-block) para escribir objetos en varias ubicaciones. Como alternativa, la aplicación puede emitir la operación [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para copiar el blob en una cuenta de almacenamiento hospedada en una unidad de escalado independiente después de que se complete la operación Put en la cuenta principal.

### <a name="scheduled-copy-task"></a>Tarea de copia programada

AzCopy es una excelente herramienta que se puede usar para copiar datos desde sistemas de archivos locales, almacenamiento en la nube de Azure, almacenamiento de Azure Stack y s3. Actualmente, AzCopy no puede copiar datos entre dos cuentas de almacenamiento de Azure Stack. La copia de objetos desde una cuenta de almacenamiento de Azure Stack de origen a una cuenta de almacenamiento de Azure Stack de destino requiere un sistema de archivos local intermediario.

Para más información, consulte AzCopy en el artículo [Uso de herramientas de transferencia de datos en el almacenamiento de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer?view=azs-1908#azcopy).

### <a name="azure-stack-source"></a>Azure Stack (origen)

Este es el origen de los datos de la cuenta de almacenamiento de los que desea hacer una copia de seguridad.

Necesitará la dirección URL de la cuenta de almacenamiento de origen y el token de SAS. Para obtener instrucciones sobre cómo trabajar con una cuenta de almacenamiento, consulte [Introducción a las herramientas de desarrollo de almacenamiento de Azure Stack](azure-stack-storage-dev.md).

### <a name="azure-stack-target"></a>Azure Stack (destino)

Este es el destino donde se almacenarán los datos de la cuenta para los que desea hacer una copia de seguridad. La instancia de Azure Stack de destino debe estar en una ubicación diferente de la de Azure Stack de destino. Y el origen tendrá que ser capaz de conectarse al destino.

Necesitará la dirección URL de la cuenta de almacenamiento de origen y el token de SAS. Para obtener instrucciones sobre cómo trabajar con una cuenta de almacenamiento, consulte [Introducción a las herramientas de desarrollo de almacenamiento de Azure Stack](azure-stack-storage-dev.md).

### <a name="intermediary-local-filesystem"></a>Sistema de archivos local intermedio

Necesitará un lugar para ejecutar AzCopy y para almacenar datos al copiar desde el origen y, a continuación, escribir en la instancia de Azure Stack de destino. Se trata de un servidor intermedio en la instancia de Azure Stack de origen.

Puede crear un servidor Windows Server o Linux como servidor intermedio. El servidor necesitará disponer de espacio suficiente para almacenar todos los objetos en los contenedores de la cuenta de almacenamiento de origen.
- Para obtener instrucciones sobre la configuración de un servidor Linux, consulte [Creación de una máquina virtual de servidor Linux mediante el portal de Azure Stack](azure-stack-quick-linux-portal.md).  
- Para obtener instrucciones sobre la configuración de un servidor Windows Server, consulte [Creación de una máquina virtual Windows Server en el portal de Azure Stack](azure-stack-quick-windows-portal.md).  

Cuando haya configurado el servidor Windows Server, tendrá que instalar [PowerShell en Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) y las [herramientas de Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json).

## <a name="set-up-backup-for-storage-accounts"></a>Configuración de copias de seguridad para cuentas de almacenamiento

1. Recupere el punto de conexión de blobs para las cuentas de almacenamiento de origen y de destino.

    ![Copia de seguridad del almacenamiento de Azure Stack](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. Cree y registre los tokens de SAS para las cuentas de almacenamiento de origen y de destino.

    ![Copia de seguridad del almacenamiento de Azure Stack](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. Instale [AzCopy](https://github.com/Azure/azure-storage-azcopy) en el servidor intermedio y establezca la versión de la API en cuenta para las cuentas de almacenamiento de Azure Stack.

    - Para un servidor Windows Server:

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - Para un servidor Linux (Ubuntu):

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. En el servidor intermedio, cree un script. Actualice este comando con la **cuenta de almacenamiento**, la **clave SAS** y la **ruta de acceso del directorio local**. Ejecutará el script para copiar los datos de forma incremental desde la cuenta de almacenamiento de **origen**.

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  Escriba la **cuenta de almacenamiento**,** la clave SAS ** y la **ruta de acceso del directorio local.  Los usará para copiar los datos incrementalmente en la cuenta de almacenamiento de **destino**.
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  Use Cron o el Programador de tareas de Windows para programar la copia de la cuenta de almacenamiento de origen de Azure Stack en el almacenamiento local en el servidor intermedio. Después, copie desde el almacenamiento local en el servidor intermedio a la cuenta de almacenamiento de destino de Azure Stack.

    El objetivo de punto de recuperación (RPO) que se puede lograr con esta solución estará determinado por el valor del parámetro /MO y el ancho de banda de red entre la cuenta de origen y el servidor intermedio y el servidor intermedio y la cuenta de destino.

    - Para un servidor Linux (Ubuntu):

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | Parámetro | Nota: | 
    | ---- | ---- |
    | /SC | Use una programación por minuto. |
    | /MO | Un intervalo de *XX* minutos. |
    | /TN | El nombre de la tarea. |
    | /TR | La ruta de acceso al archivo `script.bat`. |


    - Para un servidor Windows Server:

    Para obtener información sobre el uso de la programación de tareas de Windows, consulte [Programador de tareas para desarrolladores](https://docs.microsoft.com/windows/win32/taskschd/task-scheduler-start-page).
    

## <a name="use-your-storage-account-in-a-disaster"></a>Uso de la cuenta de almacenamiento en un desastre

Cada cuenta de almacenamiento de Azure Stack posee un nombre DNS único derivado del nombre de la propia región de Azure Stack, por ejemplo, `https://krsource.blob.east.asicdc.com/`. Las aplicaciones que escriben y leen este nombre DNS deberán acomodar el cambio del nombre DNS de la cuenta de almacenamiento cuando la cuenta de destino, por ejemplo, `https://krtarget.blob.west.asicdc.com/` deba usarse durante un desastre.

Se pueden modificar las cadenas de conexión de la aplicación después de que se declare un desastre para tener en cuenta la reubicación de los objetos o, si se usa un registro CNAME delante de un equilibrador de carga que actúa como front-end de las cuentas de almacenamiento de origen y de destino, se puede configurar el equilibrador de carga con un algoritmo de conmutación por error manual que permitirá al administrador declarar el destino

Si la aplicación usa SAS en lugar de AAD o AD FS, el método anterior no funcionará y las cadenas de conexión de la aplicación deberán actualizarse con la dirección URL de la cuenta de almacenamiento de destino y las claves SAS generadas para la cuenta de almacenamiento de destino.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a las herramientas de desarrollo de almacenamiento de Azure Stack](azure-stack-storage-dev.md)