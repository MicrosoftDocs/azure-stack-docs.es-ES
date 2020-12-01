---
title: Notas de la versión del proveedor de recursos de SQL 1.1.93.0 de Azure Stack Hub
description: Consulte las notas de la versión para ver las novedades de la actualización 1.1.93.0 del proveedor de recursos de SQL de Azure Stack Hub.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 5becaf3bca62d4e37bac266649bf3821dc5a652e
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812676"
---
# <a name="sql-resource-provider-11930-release-notes"></a>Notas de la versión del proveedor de recursos de SQL 1.1.93.0

Estas notas de la versión describen las mejoras y los problemas conocidos de la versión del proveedor de recursos de SQL 1.1.93.0.

## <a name="build-reference"></a>Referencia de compilación
Descargue el archivo binario del proveedor de recursos SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack Hub. La versión mínima de Azure Stack Hub necesaria para instalar esta versión del proveedor de recursos SQL se muestra a continuación:

> |Versión de Azure Stack Hub compatible|Versión del proveedor de recursos SQL|
> |-----|-----|
> |Versión 2008, 2005|[SQL RP, versión 1.1.93.0](https://aka.ms/azshsqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> Aplique la actualización mínima admitida de Azure Stack Hub al sistema integrado de Azure Stack Hub antes de implementar la versión más reciente del proveedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Nuevas características y correcciones

Esta versión del proveedor de recursos SQL de Azure Stack Hub incluye las siguientes mejoras y correcciones:

- **Actualice la máquina virtual base a un servidor de Windows Server especializado.** Esta versión de Windows Server está especializada en la infraestructura del complemento Azure Stack Hub RP y no es visible para el marketplace de inquilinos. Asegúrese de que descarga la imagen de **Complemento de Microsoft Azure Stack RP Windows Server (SOLO INTERNO)** antes de implementar o actualizar a esta versión del proveedor de recursos de SQL.
- **Admite la eliminación de metadatos huérfanos de base de datos y metadatos del servidor de hospedaje.** Cuando ya no se pueda conectar un servidor de hospedaje, el inquilino tendrá la opción de quitar los metadatos huérfanos de la base de datos del portal. Si no hay metadatos huérfanos de base de datos vinculados al servidor de hospedaje, el operador podrá quitar los metadatos huérfanos del servidor de hospedaje del portal de administración.
- **Convierta KeyVaultPfxPassword en un argumento opcional al realizar la rotación de secretos.** Consulte [este documento](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) para obtener información detallada.
- **Otras correcciones de errores.**

Se recomienda que aplique la revisión del proveedor de recursos SQL 1.1.93.0 después de actualizar Azure Stack Hub a la versión 2005.

## <a name="known-issues"></a>Problemas conocidos
Ninguno.

## <a name="next-steps"></a>Pasos siguientes

- [Obtener más información sobre el proveedor de recursos SQL](azure-stack-sql-resource-provider.md).
- [Preparar la implementación del proveedor de recursos SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Actualizar el proveedor de recursos SQL desde una versión anterior](azure-stack-sql-resource-provider-update.md).

