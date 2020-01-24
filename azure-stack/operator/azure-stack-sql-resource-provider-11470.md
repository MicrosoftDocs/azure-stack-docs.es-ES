---
title: Notas de la versión del proveedor de recursos SQL de Azure Stack Hub 1.1.47.0 | Microsoft Docs
description: Conozca las novedades de la actualización más reciente del proveedor de recursos SQL de Azure Stack Hub, incluidos los problemas conocidos y dónde descargarla.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 758390e9190e75125bad5701b810af08456f2efc
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881882"
---
# <a name="sql-resource-provider-11470-release-notes"></a>Notas de la versión del proveedor de recursos de SQL 1.1.47.0

Estas notas de la versión describen las mejoras y los problemas conocidos de la versión del proveedor de recursos de SQL 1.1.47.0.

## <a name="build-reference"></a>Referencia de compilación
Descargue el archivo binario del proveedor de recursos SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack Hub. La versión mínima de Azure Stack Hub necesaria para instalar esta versión del proveedor de recursos SQL se muestra a continuación:

> |Versión mínima de Azure Stack Hub|Versión del proveedor de recursos SQL|
> |-----|-----|
> |Versión 1910 (1.1910.0.58)|[SQL RP, versión 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Aplique la actualización mínima admitida de Azure Stack Hub al sistema integrado de Azure Stack Hub antes de implementar la versión más reciente del proveedor de recursos SQL.

## <a name="new-features-and-fixes"></a>Nuevas características y correcciones

Esta versión del proveedor de recursos SQL de Azure Stack Hub es una versión de revisión que permite que el proveedor de recursos sea compatible con algunos de los cambios más recientes del portal realizados en la actualización 1910, y no incluye ninguna característica nueva.

También admite el perfil de versión actual más reciente de Azure Stack Hub API 2019-03-01-hybrid y el módulo de PowerShell 1.8.0 para Azure Stack Hub. Por lo tanto, durante la implementación y la actualización, no es necesario instalar ninguna versión específica del historial de los módulos.

Siga el proceso de actualización del proveedor de recursos para aplicar la revisión 1.1.47.0 del proveedor de recursos SQL después de actualizar Azure Stack Hub a la versión 1910. Le ayudará a resolver un problema conocido en el portal de administrador por el que la supervisión de la capacidad del proveedor de recursos de SQL no deja de cargarse.

## <a name="known-issues"></a>Problemas conocidos

Ninguno.

## <a name="next-steps"></a>Pasos siguientes
[Obtener más información sobre el proveedor de recursos SQL](azure-stack-sql-resource-provider.md).

[Preparar la implementación del proveedor de recursos SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Actualizar el proveedor de recursos SQL desde una versión anterior](azure-stack-sql-resource-provider-update.md). 
