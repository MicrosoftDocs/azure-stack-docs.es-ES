---
title: Instalación de firmware mediante iDRAC
description: Obtenga información sobre cómo instalar firmware mediante iDRAC.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3b6a257d5f5f42c951313c2220559306577de15d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910251"
---
# <a name="installing-firmware-using-the-idrac-interface"></a>Instalación de firmware mediante la interfaz de iDRAC

La tarjeta de acceso remoto de Dell integrada (iDRAC) permite actualizar de forma remota el firmware (uno por uno) mediante la característica **Actualización y reversión**. Funciona incluso si el servidor está en ejecución.

Vaya a la página [Documentación del sistema integrado para Microsoft Azure Stack Hub 14G](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs) para descargar la matriz de compatibilidad más reciente, que contiene una lista de versiones de firmware admitidas.

Use el procedimiento siguiente para actualizar el firmware de un solo dispositivo mediante la interfaz web de iDRAC.

**Pasos**

1.  Vaya a **Maintenance** \**System Update** (Mantenimiento > Actualización del sistema). Se muestra la página **Firmware Update** (Actualización de firmware).

    ![Captura de pantalla en la que se muestra la página de mantenimiento de un controlador integrado de acceso remoto de Dell 9 de tipo Enterprise.](media/image-85.png)

2.  En la pestaña **Update** (Actualización), seleccione **Local** como ubicación del archivo.

3.  Seleccione **Browse** (Examinar), seleccione la imagen de firmware para el componente requerido y, a continuación, seleccione **Upload** (Cargar).

4.  Una vez completada la carga, la sección **Update Details** (Detalles de la actualización) muestra cada archivo de firmware que se carga en iDRAC y su estado. Si el archivo de imagen de firmware es válido y se ha cargado correctamente, la columna **Contents** (Contenido) muestra un icono (+) junto al nombre de archivo de la imagen de firmware. Expanda el nombre para ver la información correspondiente a **Device Name** (Nombre de dispositivo), **Current** (Actual) y **Available firmware version** (Versión de firmware disponible).

5.  Seleccione el archivo de firmware necesario y realice una de las siguientes acciones:

    -   En el caso de las imágenes de firmware que no requieren un reinicio del sistema host, seleccione **Install** (Instalar).

    -   En el caso de las imágenes de firmware que requieren un reinicio del sistema host, seleccione **Install and Reboot** (Instalar y reiniciar) o **Install Next Reboot** (Instalar en el reinicio siguiente).

    -   Para cancelar la actualización de firmware, seleccione **Cancel** (Cancelar).

6.  Para mostrar la página **Job Queue** (Cola de trabajos), seleccione **Job Queue** (Cola de trabajos). Use esta página para ver y administrar las actualizaciones de firmware preconfiguradas o seleccione

    **OK** (Aceptar) para actualizar la página actual y ver el estado de la actualización de firmware.
    
