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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 3f11a7b5066d0b50d85a40be1df47dfe1a5ade38
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025841"
---
# <a name="asdk-release-notes"></a>Notas de la versión del Kit de desarrollo de Azure Stack

En este artículo se proporciona información sobre cambios, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack (ASDK). Si no está seguro de qué versión ejecuta, [use el portal para comprobarlo](../operator/azure-stack-updates.md).

Para estar al día de las novedades del ASDK, suscríbase a la [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [fuente RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11907020"></a>Compilación 1.1907.0.20

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) de las notas de la versión de Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Al crear recursos de VM con algunas imágenes de Marketplace, es posible que no pueda completar la implementación. Como solución alternativa, puede hacer clic en el enlace **Descargar plantilla y parámetros** en la página **Resumen** y haga clic en el botón **Implementar** de la hoja **Plantilla**.
- Para ver una lista de los problemas de Azure Stack resueltos en esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1907.md#fixes) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](../operator/azure-stack-release-notes-known-issues-1907.md).
- Tenga en cuenta que [las revisiones disponibles de Azure Stack](../operator/azure-stack-release-notes-1907.md#hotfixes) no son aplicables al ASDK de Azure Stack.

## <a name="build-11906030"></a>Compilación 1.1906.0.30

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) de las notas de la versión de Azure Stack.

### <a name="changes"></a>Cambios

- Se ha agregado un VM de anillo de soporte **AzS-SRNG01** que hospeda el servicio de recopilación de registros para Azure Stack. Para más información, consulte [Roles de máquina virtual](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Al crear recursos de VM con algunas imágenes de Marketplace, es posible que no pueda completar la implementación. Como solución alternativa, puede hacer clic en el enlace **Descargar plantilla y parámetros** en la página **Resumen** y haga clic en el botón **Implementar** de la hoja **Plantilla**.
- Para ver una lista de los problemas de Azure Stack resueltos en esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1906.md#fixes) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](../operator/azure-stack-release-notes-known-issues-1906.md).
- Tenga en cuenta que las [revisiones disponibles de Azure Stack](../operator/azure-stack-release-notes-1906.md#hotfixes) no son aplicables al ASDK de Azure Stack.

## <a name="build-11905040"></a>Compilación 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) de las notas de la versión de Azure Stack.

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Se ha corregido un problema en que tenía que modificar el script **RegisterWithAzure.psm1** de PowerShell para [registrar el ASDK](asdk-register.md) correctamente.
- Para ver una lista de otros problemas resueltos de Azure Stack en esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1905.md#fixes) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](../operator/azure-stack-release-notes-known-issues-1905.md).
- Tenga en cuenta que las [revisiones disponibles de Azure Stack](../operator/azure-stack-release-notes-1905.md#hotfixes) no son aplicables al ASDK de Azure Stack.

## <a name="build-11904036"></a>Compilación 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) de las notas de la versión de Azure Stack.

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Debido a un tiempo de espera de la entidad de servicio mientras se ejecutaba el script de registro, para [registrar el ASDK](asdk-register.md) correctamente, debe editar el script de PowerShell **RegisterWithAzure.psm1**. Haga lo siguiente:

  1. En el equipo host de ASDK, abra el archivo **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** en un editor con permisos elevados.
  2. En la línea 1249, agregue un parámetro `-TimeoutInSeconds 1800` al final. Esta adición es necesaria debido a un tiempo de espera de la entidad de servicio al ejecutar el script de registro. Ahora, la línea 1249 debe tener un aspecto similar al siguiente:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Se ha corregido el problema de conexión de VPN identificado aquí, en la versión 1902.

- Para ver una lista de otros problemas resueltos de Azure Stack en esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1904.md#fixes) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](../operator/azure-stack-release-notes-known-issues-1904.md).
- Tenga en cuenta que las [revisiones disponibles de Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) no son aplicables al ASDK de Azure Stack.

