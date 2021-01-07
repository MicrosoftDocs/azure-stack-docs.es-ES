---
title: Notas de la versión del proveedor de recursos de MySQL 1.1.47.0 de Azure Stack Hub
description: Consulte las notas de la versión para ver las novedades de la actualización 1.1.47.0 del proveedor de recursos MySQL de Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 11/26/2019
ms.author: patricka
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: d98a4d4c7de76d5a73cab3005893dd279a6e1025
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869855"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Notas de la versión del proveedor de recursos de MySQL 1.1.47.0

Estas notas de la versión describen las mejoras y los problemas conocidos de la versión del proveedor de recursos de MySQL 1.1.47.0.

## <a name="build-reference"></a>Referencia de compilación
Descargue el archivo binario del proveedor de recursos MySQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack Hub. La versión mínima de Azure Stack Hub necesaria para instalar esta versión del proveedor de recursos MySQL se muestra a continuación:

> |Versión mínima de Azure Stack Hub|Versión del proveedor de recursos MySQL|
> |-----|-----|
> |Versión 1910 (1.1910.0.58)|[MySQL RP, versión 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Aplique la actualización mínima admitida de Azure Stack Hub al sistema integrado de Azure Stack Hub o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar la versión más reciente del proveedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Nuevas características y correcciones

Esta versión del proveedor de recursos MySQL de Azure Stack Hub es una versión de revisión que permite que el proveedor de recursos sea compatible con algunos de los cambios más recientes del portal realizados en la actualización 1910. No hay características nuevas en esta versión.

También admite el perfil de versión más reciente de Azure Stack Hub API 2019-03-01-hybrid y el módulo de PowerShell 1.8.0 para Azure Stack Hub. Por lo tanto, durante la implementación y la actualización, no es necesario instalar ninguna versión específica del historial de los módulos.

Se recomienda aplicar la revisión del proveedor de recursos MySQL 1.1.47.0 después de actualizar Azure Stack Hub a la versión 1910.

## <a name="known-issues"></a>Problemas conocidos

Al [rotar el certificado](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation) de los sistemas integrados de Azure Stack Hub, el argumento KeyVaultPfxPassword es obligatorio incluso si no hay ninguna intención de actualizar la contraseña del certificado de Key Vault.

## <a name="next-steps"></a>Pasos siguientes

- [Obtener más información sobre el proveedor de recursos MySQL](azure-stack-mysql-resource-provider.md).
- [Preparar la implementación del proveedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Actualizar el proveedor de recursos MySQL desde una versión anterior](azure-stack-mysql-resource-provider-update.md).
