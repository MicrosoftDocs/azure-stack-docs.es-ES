---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 305d085447e4b77108d55e6c7fe0afde3cd2e32c
ms.sourcegitcommit: 3e225b30a54159b6b8dbeb2f843a2e5a721b746e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91519438"
---
1. Inicie sesión en el portal de usuarios de Azure Stack Hub.

    Si es un operador de nube que va a crear un disco de plataforma, siga las instrucciones de [Adición de una imagen de plataforma](/azure-stack/operator/azure-stack-add-vm-image#add-a-platform-image) para agregar el disco duro virtual mediante el portal de administración o con los puntos de conexión del administrador.

2. En el portal de usuarios, seleccione **Todos los servicios** > **Discos** > **Agregar**.

3. En **Crear disco administrado**:

    1. Escriba el **nombre** de la imagen.
    2. Seleccione su **suscripción**.
    3. Cree o agregue la imagen a un **grupo de recursos**.
    4. Seleccione la **ubicación**, también denominada región, de su ASDK.
    5. Seleccione el **Tipo de cuenta**.
        - Los **discos Premium (SSD)** están respaldados por unidades de estado sólido y ofrecen coherencia y un rendimiento con baja latencia. Proporcionan el mejor equilibrio entre precio y rendimiento y son ideales para aplicaciones intensivas de E/S y cargas de trabajo de producción.  
        - Los **discos estándar (HDD)** están respaldados por unidades magnéticas y son preferibles para las aplicaciones en la que se accede con poca frecuencia a los datos. Los discos con redundancia de zona tienen el respaldo del almacenamiento con redundancia de zona (ZRS), que replica los datos en varias zonas, por lo que están disponibles aunque si una de las zonas esté inactiva.

    6. Seleccione **Storage Blob** como **Tipo de origen**. Ha creado un disco a partir de un blob de una cuenta de almacenamiento.
    7. Para la selección del origen del disco duro virtual:
        1. La suscripción de origen donde se encuentra la cuenta de almacenamiento.
        1. Seleccione **Examinar** y, después, vaya a la cuenta de almacenamiento, el contenedor y el disco duro virtual. Elija **Seleccionar**.
        1. Seleccione el **tipo de sistema operativo** que coincida con el disco duro virtual.
    8. Seleccione un **tamaño de disco (GiB)** igual o mayor que el del disco duro virtual.
    9. Seleccione **Crear**.

4. Una vez creado el disco, puede usarlo para crear una nueva máquina virtual.
