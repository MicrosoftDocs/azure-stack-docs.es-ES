---
title: Notas de la versión del ASDK | Microsoft Docs
description: Mejoras, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 98e43cfe0226e06ca936484a78da5a61915f5797
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101036"
---
# <a name="asdk-release-notes"></a>Notas de la versión del Kit de desarrollo de Azure Stack

En este artículo se proporciona información sobre cambios, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack (ASDK). Si no está seguro de qué versión ejecuta, [use el portal para comprobarlo](../operator/azure-stack-updates.md).

Para estar al día de las novedades del ASDK, suscríbase a la [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [fuente RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

::: moniker range="azs-1908"
## <a name="build-11908020"></a>Compilación 1.1908.0.20

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1908) de las notas de la versión de Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1908) of the Azure Stack release notes. -->
- Para ver una lista de problemas conocidos, consulte [este artículo](/azure-stack/operator/known-issues?view=azs-1908).
- Tenga en cuenta que las revisiones disponibles de Azure Stack no son aplicables al ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>Compilación 1.1907.0.20

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update-1907) de las notas de la versión de Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Al crear recursos de VM con algunas imágenes de Marketplace, es posible que no pueda completar la implementación. Como solución alternativa, puede hacer clic en el enlace **Descargar plantilla y parámetros** en la página **Resumen** y haga clic en el botón **Implementar** de la hoja **Plantilla**.
- Para ver una lista de los problemas de Azure Stack resueltos en esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1907#fixes-1907) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](/azure-stack/operator/known-issues?view=azs-1907).
- Tenga en cuenta que [las revisiones disponibles de Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-1907) no son aplicables a Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>Compilación 1.1906.0.30

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1906) de las notas de la versión de Azure Stack.

### <a name="changes"></a>Cambios

- Se ha agregado un VM de anillo de soporte **AzS-SRNG01** que hospeda el servicio de recopilación de registros para Azure Stack. Para más información, consulte [Roles de máquina virtual](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Al crear recursos de VM con algunas imágenes de Marketplace, es posible que no pueda completar la implementación. Como solución alternativa, puede hacer clic en el enlace **Descargar plantilla y parámetros** en la página **Resumen** y haga clic en el botón **Implementar** de la hoja **Plantilla**.
- Para ver una lista de los problemas de Azure Stack resueltos en esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1906#fixes-1906) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](/azure-stack/operator/known-issues?view=azs-1906).
- Tenga en cuenta que [las revisiones disponibles de Azure Stack](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-1906) no son aplicables a Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1905"
## <a name="build-11905040"></a>Compilación 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1905#whats-in-this-update-1905) de las notas de la versión de Azure Stack.

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Se ha corregido un problema en que tenía que modificar el script **RegisterWithAzure.psm1** de PowerShell para [registrar el ASDK](asdk-register.md) correctamente.
- Para ver una lista de otros problemas resueltos de Azure Stack en esta versión, consulte [esta sección](/azure-stack/operator/release-notes?view=azs-1905#fixes-1905) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](/azure-stack/operator/known-issues?view=azs-1905).
- Tenga en cuenta que [las revisiones disponibles de Azure Stack](/azure-stack/operator/release-notes?view=azs-1905#hotfixes-1905) no son aplicables a Azure Stack ASDK.
::: moniker-end
