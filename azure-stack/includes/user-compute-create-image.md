---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: ac28bcee33cbe7cd54d8f94408620f6f4b0d9d11
ms.sourcegitcommit: 3e225b30a54159b6b8dbeb2f843a2e5a721b746e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91519439"
---
1. Inicie sesión en el portal de usuarios de Azure Stack Hub.

    Si es un operador de nube que va a crear una imagen de plataforma, siga las instrucciones de [Adición de una imagen de plataforma](/azure-stack/operator/azure-stack-add-vm-image#add-a-platform-image) para agregar el disco duro virtual mediante el portal de administración o con los puntos de conexión del administrador.

2. En el portal de usuarios, seleccione **Todos los servicios** > **Imágenes** > **Agregar**.

3. En **Crear imagen**:

    1. Escriba el **nombre** de la imagen.
    2. Seleccione su **suscripción**.
    3. Cree o agregue la imagen a un **grupo de recursos**.
    4. Seleccione la **ubicación**, también denominada región, de su ASDK.
    5. Seleccione un **tipo de sistema operativo** que coincida con la imagen.
    6. Seleccione **Examinar** y, después, vaya a la cuenta de almacenamiento, el contenedor y el disco duro virtual. Elija **Seleccionar**.
    5. Seleccione el **Tipo de cuenta**.
        - Los **discos Premium (SSD)** están respaldados por unidades de estado sólido y ofrecen coherencia y un rendimiento con baja latencia. Proporcionan el mejor equilibrio entre precio y rendimiento y son ideales para aplicaciones intensivas de E/S y cargas de trabajo de producción.  
        - Los **discos estándar (HDD)** están respaldados por unidades magnéticas y son preferibles para las aplicaciones en la que se accede con poca frecuencia a los datos. Los discos con redundancia de zona tienen el respaldo del almacenamiento con redundancia de zona (ZRS), que replica los datos en varias zonas, por lo que están disponibles aunque si una de las zonas esté inactiva.

    8. Seleccione **Lectura/escritura** para el almacenamiento en caché del host.
    9. Seleccione **Crear**.

4. Una vez creada la imagen, use la imagen para crear una nueva máquina virtual.
