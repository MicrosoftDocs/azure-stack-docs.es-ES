---
title: 'Azure Stack: actualización del firmware para el almacenamiento extendido del Centro de datos modular'
description: En este artículo se proporcionan instrucciones sobre cómo realizar actualizaciones de firmware en el almacenamiento extendido para el almacenamiento de blobs del Centro de datos modular.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 23430fd05254b1509677bf2d7ddedab75c7c2355
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183460"
---
# <a name="updating-onefs-and-firmware-for-the-datacenter-extended-storage"></a>Actualización de OneFS y del firmware para el almacenamiento extendido del centro de datos

En este artículo se proporcionan instrucciones para actualizar OneFS y el firmware del hardware de almacenamiento extendido del centro de datos. 

Los pasos finales de la integración e implementación de hardware de almacenamiento extendido son los pasos que validan que el hardware está en buen estado y listo para la configuración. Los pasos se describen en [Comprobación del estado del almacenamiento extendido](extended-storage-health-checks.md).

## <a name="update-onefs"></a>Actualización de OneFS

Actualice OneFS para que se apliquen las revisiones recientes del sistema operativo antes de actualizar el firmware. 

1. Compruebe que el clúster tiene suficiente espacio libre para instalar esta revisión.
1. Abra una conexión SSH en cualquier nodo del clúster e inicie sesión con la cuenta raíz.

   >[!NOTE]
   >Si la revisión se está instalando en un clúster de modo de cumplimiento, inicie sesión con la cuenta de compadmin.

1. Copie el archivo 8.2.2_GA-RUP_2020-07_PSP-40.tgz en el directorio /ifs/data/Isilon_Support del clúster.
1. Ejecute el siguiente comando para cambiar al directorio /ifs/data/Isilon_Support: 

   ```
   /ifs/data/Isilon_Support
   pwd
   ```

1. Para extraer el archivo de revisión, ejecute el comando siguiente:

   ```
   -zxvf 8.2.2_GA-RUP_2020-07_PSP-40.tgz
   ```

1. Para instalar esta revisión, ejecute el siguiente comando:
   >[!WARNING]
   >El siguiente comando reiniciará todos los nodos del clúster simultáneamente. Para instalar la revisión y reiniciar cada nodo sucesivamente, omita el parámetro `-simultaneous`. Para obtener más información, consulte la sección sobre el impacto de la instalación o la eliminación en el archivo LÉAME.

   ```
   isi upgrade patches install 8.2.2_GA-RUP_2020-07_PSP-40.pkg --simultaneous
   ```

1. Para comprobar si la revisión está instalada, ejecute el comando siguiente:

   ```
   isi upgrade patches list
   ```

1. Compruebe si 8.2.2_GA-RUP_2020-07_PSP-40 aparece en la lista de paquetes instalados y que el estado está establecido en Instalado.

## <a name="update-firmware-on-nodes"></a>Actualización del firmware en los nodos

   1. Revise el firmware de la unidad actual y la compatibilidad de las unidades con las [versiones de los paquetes](https://aka.ms/currentextstoragerelease).
   1. Descargue los archivos en la máquina local.  
   1. Cargue los paquetes de firmware de unidad y nodo en el recurso compartido de archivos Isilon, y coloque los archivos en los siguientes directorios respectivos:
    
        * Drive_Support_*.tgz: /ifs/data/Isilon_Support/dsp/
        * IsiFw_Package_*.tar: /ifs/data 

## <a name="verify-disk-drive-firmware"></a>Comprobación del firmware de la unidad de disco

Realice la actualización o comprobación de compatibilidad de la unidad en primer lugar. Al hacerlo, se comprobará si el firmware de la unidad de disco está actualizado con el paquete proporcionado. Si no es así, el paquete se puede usar para realizar las actualizaciones necesarias:

   1. Desde una sesión de ssh activa, ejecute:

       ```
       cd /ifs/data/Isilon_Support/dsp/
       ```

   1. Asegúrese de que Drive_Support_*.tgz está en este directorio mediante la ejecución del comando ls.
   1. Expanda el archivo .tgz mediante la ejecución del comando siguiente. Asegúrese de que tiene el nombre de archivo completo y reemplace * por el número de versión real del paquete:
       ```
       tar -zxvf Drive_Support_v1.*.tgz
       ```
       Por ejemplo, si la versión del paquete es Drive_Support_v1.33.tgz, ejecute el siguiente comando:
       ```
       tar -zxvf Drive_Support_v1.33.tgz
       ```
   1. Se expande a tres archivos:
    
        * Drive_Support_v1.33.tar
        * Drive_Support_v1.33.tar.md5
        * Drive_Support_v1.33.tar.sha256
   1. Ejecute el siguiente comando para instalar o comprobar el software de compatibilidad de las unidades:
       ```
       isi_dsp_install Drive_Support_v1.*.tar
       ```
       Por ejemplo, si la versión del paquete es Drive_Support_v1.33.tgz, ejecute el siguiente comando:
       ```
       isi_dsp_install Drive_Support_v1.33.tar
       ```
   1. Este comando ejecuta comprobaciones y solicita una instalación para el paquete de compatibilidad de las unidades. Si el paquete es necesario, se instalará; de lo contrario, indicará que está instalado y omitirá las demás actualizaciones del software de compatibilidad de las unidades. La salida de ejemplo de v1.33 es la siguiente:
    
        ```
        Running local CHECKS for patch Drive_Support_v1.33
        INSTALL operation has been requested for patch Drive_Support_v1.33
        Installed Drive_Support_v1.33
        DELETE operation has been requested for patch
        Drive_Support_v1.33
        Removed Drive_Support_v1.33 package files
        DSP Install Succeeded, 170.605088949 sec elapsed
        ```
        
   1. La confirmación final se puede realizar ejecutando el siguiente comando. Cualquier elemento distinto de "-" en la columna "Desired" indica que la unidad debe actualizarse:
       ```
       isi devices drive firmware list --node-lnn all
       ```
   1. Si hay algunas unidades que deben actualizarse, se debe usar el siguiente comando para realizar las actualizaciones de firmware del disco:
       ```
       isi devices drive firmware update start all --node-lnn all
       ```
   1. Este proceso puede supervisarse con el siguiente comando:
       ```
       isi devices drive firmware update list
       ```
   1. Una vez completado, el estado de las unidades se puede comprobar con el siguiente comando:
       ```
       isi devices drive list --node-lnn all
       ```
## <a name="install-node-firmware"></a>Instalación del firmware de nodo

Una vez que se han completado o comprobado las actualizaciones del software de compatibilidad de las unidades, se puede instalar el firmware del nodo mediante el archivo IsiFw_Package_*.tar.

   1. Desde una sesión de ssh activa, ejecute:
        ```
        cd /ifs/data/
        ```
   1. Asegúrese de que el archivo IsiFw_Package_*.tar está en este directorio mediante la ejecución del comando ls.
   1. Ejecute el siguiente comando para el almacenamiento provisional y la instalación del firmware en los nodos. Asegúrese de que tiene el nombre de archivo completo y reemplace * por el número de versión real del paquete:
       ```
       isi upgrade patches install IsiFw_Package_v*.tar --rolling=false
       ```
       Por ejemplo, si el número de versión es v10.3.3.tar, ejecute el siguiente comando:
       ```
       isi upgrade patches install IsiFw_Package_v10.3.3.tar --rolling=false
       ```
   1. Una vez ejecutado, se enviará el siguiente mensaje como confirmación:
       ```
        Requested install of patch IsiFw_Package_v10.3.3.
       ```
   1. Compruebe el estado del almacenamiento provisional o la instalación con el siguiente comando:
       ```
       isi upgrade patches list
       ```
   1. La columna "Status" se actualizará cuando se complete el proceso y se mostrará el estado siguiente: Pending, Unknown, Installing o Installed (Pendiente, Desconocido, Instalando o Instalado), tal como se muestra en el siguiente ejemplo de salida:
        
       ![Estado de actualización del firmware](media/extended-storage-firmware-updates/firmware-update-status.png)
        
   1. Ahora que se ha realizado el almacenamiento provisional o la instalación del archivo IsiFw_Package*.tar, se puede iniciar la implementación del firmware actualizado en los nodos con el siguiente comando:
        ```
        isi upgrade cluster firmware start --no-verify
        ```
   1. Se le pedirá la siguiente confirmación y debe escribir "yes" para continuar:
       ```
       You are about to start a Rolling Firmware UPGRADE, are you sure? (yes/[no]):
       ```
        
       > [!NOTE]
       > La actualización del firmware del nodo puede tardar varias horas y los nodos se desconectarán durante el proceso. Dado que la ejecución de todos estos comandos se realiza desde el nodo 1, la conexión se eliminará, ya que es el primer nodo que se va a actualizar. Lo mismo sucede con el portal de OneFS: espere de 5 a 10 minutos antes de intentar volver a conectarse para comprobar el estado (si es necesario, también es posible la conexión a través de otra interfaz de administración que no se esté actualizando).
   1. Desde una conexión o interfaz de administración activa, se puede realizar un seguimiento del estado en curso de esta actualización de firmware con el siguiente comando:
       ```
       isi upgrade cluster nodes firmware progress list
       ```
       O bien, mediante el portal de OneFS, en Cluster Management - Patches and firmware (Administración de clústeres - Revisiones y firmware):
        
       ![Administración de clústeres](media/extended-storage-firmware-updates/cluster-management.png)
        
       Cuando se complete la operación, el estado cambiará de **Unknown** (Desconocido) a **Installed** (Instalado).

## <a name="remove-firmware-packages"></a>Supresión de paquetes de firmware

Quite los paquetes de firmware una vez que se hayan instalado por completo en las unidades y los nodos con los siguientes comandos. Asegúrese de que tiene el nombre de archivo completo y reemplace v* por la versión del paquete real:
   ```
   isi upgrade patches uninstall IsiFw_Package_ v*.tar --rolling=false
   ```
   Por ejemplo, si la versión del paquete es IsiFw_Package_v10.3.1.tar, ejecute el siguiente comando:
   ```
   isi upgrade patches uninstall IsiFw_Package_ v10.3.1.tar --rolling=false
   ```
Desde una sesión de ssh activa, ejecute:
   ```
   cd /ifs/data/
   rm -f Drive_Support_v*.tgz
   rm -f Drive_Support_v*.tar
   rm -f Drive_Support_v*.tar.md5
   rm -f Drive_Support_v*.tar.sha256
   ```
   Por ejemplo:
   ```
   cd /ifs/data/
   rm -f Drive_Support_v10.3.1.tgz
   rm -f Drive_Support_v10.3.1.tar
   rm -f Drive_Support_v10.3.1.tar.md5
   rm -f Drive_Support_v10.3.1.tar.sha256
   ```


        
<!-- ## Contact Dell EMC for support -->

Póngase en contacto con el soporte técnico de Microsoft para obtener ayuda si tiene algún problema.

## <a name="next-steps"></a>Pasos siguientes

- [Comprobaciones de estado de almacenamiento extendido](extended-storage-health-checks.md)
