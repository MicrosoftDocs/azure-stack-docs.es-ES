---
title: Notas de la versión de Azure Stack HCI
description: Notas de la versión de Azure Stack HCl, versión 20H2.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/13/2020
ms.openlocfilehash: 2432a7fb28ba65f08b0540113ec5d3f90f742509
ms.sourcegitcommit: 64060ff02d2450c6cf91cb21cdefdcf6b720a75f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2020
ms.locfileid: "92009837"
---
# <a name="release-notes-for-azure-stack-hci-public-preview"></a>Notas de la versión de Azure Stack HCl, versión preliminar pública

> Se aplica a: Azure Stack HCI, versión 20H2

En este artículo se describe el contenido de los paquetes de actualización de la versión preliminar pública de Azure Stack Hub.

## <a name="october-13-2020-security-update-kb4580363"></a>13 de octubre de 2020, actualización de seguridad (KB4580363)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización contiene varias mejoras de seguridad relacionadas con la funcionalidad interna del sistema operativo. No se han documentado problemas adicionales para esta versión.

Para más información sobre las vulnerabilidades resueltas de seguridad, consulte la [guía de actualizaciones de seguridad](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 13 de octubre de 2020 (KB4580363) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1288), descargue la [información de archivo de la actualización acumulativa 4580363](https://download.microsoft.com/download/f/7/8/f78801f0-e7e5-4a3d-a971-f642ccd24ee4/4580363.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="october-13-2020-servicing-stack-update-kb4583287"></a>Actualización de la pila de servicio del 13 de octubre de 2020 (KB4583287)

Esta actualización incluye mejoras de calidad para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización realiza mejoras de calidad en la pila de servicio, que es el componente que instala las actualizaciones. Las actualizaciones de la pila de servicio (SSU) garantizan que tiene una pila de servicio potente y confiable para que los dispositivos puedan recibir e instalar actualizaciones de Microsoft.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de la pila de servicio del 13 de octubre de 2020 (KB4583287) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Información de reinicio
No es necesario reiniciar el equipo después de aplicar esta actualización.

### <a name="removal-information"></a>Información de eliminación
Las actualizaciones de la pila de servicio (SSU) realizan cambios en cómo se instalan las actualizaciones y no se pueden desinstalar del dispositivo.

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1287), descargue la [información de archivo de la actualización acumulativa 4583287](https://download.microsoft.com/download/b/8/5/b85160fb-85d9-49f9-b9d5-7dbc0158a944/4583287.csv).

### <a name="references"></a>Referencias

Para más información sobre las actualizaciones de la pila de servicio, consulte los artículos siguientes:

- [Actualizaciones de la pila de servicio](/windows/deployment/update/servicing-stack-updates)
- [Actualizaciones de la pila de servicio: Preguntas más frecuentes](https://support.microsoft.com/help/4535697)

Obtenga información sobre la [terminología](https://support.microsoft.com/help/824684) que Microsoft usa para describir las actualizaciones de software.

## <a name="september-17-2020-preview-update-kb4577629"></a>Actualización de la versión preliminar del 17 de septiembre de 2020 (KB4577629)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización no relacionada con la seguridad incluye mejoras de calidad. Los cambios principales son:
- Se ha solucionado un problema en el que el tráfico del equilibrador de carga del software que pasaba por el multiplexor se redirigía a un host diferente lo cual producía un problema de conexión de la aplicación.

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 17 de septiembre de 2020 (KB4577629) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1259), descargue la [información de archivo de la actualización acumulativa 4577629](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="september-8-2020-security-update-kb4577470"></a>Actualización de seguridad del 8 de septiembre de 2020 (KB4577470)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización contiene varias mejoras de seguridad relacionadas con la funcionalidad interna del sistema operativo. No se han documentado problemas adicionales para esta versión.

Para más información sobre las vulnerabilidades resueltas de seguridad, consulte la [guía de actualizaciones de seguridad](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 8 de septiembre de 2020 (KB4577470) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1226), descargue la [información de archivo de la actualización acumulativa 4577470](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>Actualización de la pila de servicio del 8 de septiembre de 2020 (KB4577558)

Esta actualización incluye mejoras de calidad para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización realiza mejoras de calidad en la pila de servicio, que es el componente que instala las actualizaciones. Las actualizaciones de la pila de servicio (SSU) garantizan que tiene una pila de servicio potente y confiable para que los dispositivos puedan recibir e instalar actualizaciones de Microsoft.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de la pila de servicio del 8 de septiembre de 2020 (KB4577558) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Información de reinicio 
No es necesario reiniciar el equipo después de aplicar esta actualización.

### <a name="removal-information"></a>Información de eliminación
Las actualizaciones de la pila de servicio (SSU) realizan cambios en cómo se instalan las actualizaciones y no se pueden desinstalar del dispositivo.

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1220), descargue la [información de archivo de la actualización acumulativa 4577558](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv).

### <a name="references"></a>Referencias

Para más información sobre las actualizaciones de la pila de servicio, consulte los artículos siguientes:

- [Actualizaciones de la pila de servicio](/windows/deployment/update/servicing-stack-updates)
- [Actualizaciones de la pila de servicio: Preguntas más frecuentes](https://support.microsoft.com/help/4535697)

Obtenga información sobre la [terminología](https://support.microsoft.com/help/824684) que Microsoft usa para describir las actualizaciones de software.

## <a name="august-11-2020-security-update-kb4574585"></a>Actualización de seguridad del 11 de agosto de 2020 (KB4574585)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización contiene varias mejoras de seguridad relacionadas con la funcionalidad interna del sistema operativo. No se han documentado problemas adicionales para esta versión.

Para más información sobre las vulnerabilidades resueltas de seguridad, consulte la [guía de actualizaciones de seguridad](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 11 de agosto de 2020 (KB4574585) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1162), descargue la [información de archivo de la actualización acumulativa 4574585](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.