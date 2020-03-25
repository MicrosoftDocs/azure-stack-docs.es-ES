---
title: Notas de la versión del Kit de desarrollo de Azure Stack
description: Mejoras, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 03/18/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 6f7bec082564c9f7cb3a0c70cec7e8e7e48fbd35
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511817"
---
# <a name="asdk-release-notes"></a>Notas de la versión del Kit de desarrollo de Azure Stack

En este artículo se proporciona información sobre los cambios, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack (ASDK). Si no está seguro de qué versión ejecuta, [use el portal para comprobarlo](../operator/azure-stack-updates.md).

Para estar al día de las novedades del ASDK, suscríbase a la [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [fuente RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

::: moniker range="azs-2002"
## <a name="build-12002035"></a>Compilación 1.2002.0.35

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de los problemas corregidos, los cambios y las nuevas características de esta versión, consulte las secciones correspondientes en las [notas de la versión de Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- La contraseña de certificación del descifrado es una nueva opción para especificar la contraseña para el certificado autofirmado (.pfx) que contiene la clave privada necesaria para descifrar los datos de copia de seguridad. Esta contraseña solo es necesaria si la copia de seguridad se cifra mediante un certificado.
- Para obtener una lista de problemas conocidos de Azure Stack en esta versión, consulte el artículo [Problemas conocidos](../operator/known-issues.md).
- Tenga en cuenta que las revisiones disponibles de Azure Stack no son aplicables al ASDK.
::: moniker-end

::: moniker range="azs-1910"
## <a name="build-11910058"></a>Compilación 1.1910.0.58

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de los problemas corregidos, los cambios y las nuevas características de esta versión, consulte las secciones correspondientes en las [notas de la versión de Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Se ha corregido un problema con la recopilación de registros y su almacenamiento en un contenedor de blobs de Azure Storage. La sintaxis de esta operación es como sigue:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- Se corrigió un problema de implementación por el que un servicio de administración de trabajos de carga lenta impedía la eliminación de algunas características de Windows y requería un reinicio.
- Para obtener una lista de problemas conocidos de Azure Stack en esta versión, consulte el artículo [Problemas conocidos](../operator/known-issues.md).
- Tenga en cuenta que las revisiones disponibles de Azure Stack no son aplicables al ASDK.
::: moniker-end

::: moniker range="azs-1908"
  
## <a name="build-11908020"></a>Compilación 1.1908.0.20

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1) de las notas de la versión de Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Para ver una lista de problemas conocidos, consulte [este artículo](/azure-stack/operator/known-issues?view=azs-1908).
- Tenga en cuenta que las revisiones disponibles de Azure Stack no son aplicables al ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>Compilación 1.1907.0.20

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update) de las notas de la versión de Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Al crear recursos de VM con algunas imágenes de Marketplace, es posible que no pueda completar la implementación. Como solución alternativa, puede hacer clic en el enlace **Descargar plantilla y parámetros** en la página **Resumen** y haga clic en el botón **Implementar** de la hoja **Plantilla**.
- Para ver una lista de los problemas de Azure Stack resueltos en esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1907#fixes-2) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](/azure-stack/operator/known-issues?view=azs-1907).
- Tenga en cuenta que [las revisiones disponibles de Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2) no son aplicables a Azure Stack ASDK.
::: moniker-end
