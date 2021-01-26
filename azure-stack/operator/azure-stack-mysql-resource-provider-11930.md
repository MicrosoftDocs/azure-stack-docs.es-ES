---
title: Notas de la versión del proveedor de recursos de MySQL 1.1.93.0 de Azure Stack Hub
description: Consulte las notas de la versión para ver las novedades de la actualización 1.1.93.0 del proveedor de recursos MySQL de Azure Stack Hub.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 225c765642dc48b299c899d71eb0d0ce13d4852d
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571372"
---
# <a name="mysql-resource-provider-1193x-release-notes"></a>Notas de la versión del proveedor de recursos MySQL 1.1.93.x

Estas notas de la versión describen las mejoras y los problemas conocidos de la versión del proveedor de recursos MySQL 1.1.93.x.

## <a name="build-reference"></a>Referencia de compilación
Descargue el archivo binario del proveedor de recursos MySQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack Hub. La versión mínima de Azure Stack Hub necesaria para instalar esta versión del proveedor de recursos MySQL se muestra a continuación:

> |Versión de Azure Stack Hub compatible|Versión del proveedor de recursos MySQL|
> |-----|-----|
> |Versión 2008, 2005|[MySQL RP, versión 1.1.93.1](https://aka.ms/azshmysqlrp11931)|  
> |     |     |

> [!IMPORTANT]
> Aplique la actualización mínima admitida de Azure Stack Hub al sistema integrado de Azure Stack Hub antes de implementar la versión más reciente del proveedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Nuevas características y correcciones

Esta versión del proveedor de recursos MySQL de Azure Stack Hub incluye las siguientes mejoras y correcciones:

- **Actualice la máquina virtual base a un servidor de Windows Server especializado.** Esta versión de Windows Server está especializada en la infraestructura del complemento Azure Stack Hub RP y no es visible para el marketplace de inquilinos. Asegúrese de que descarga la imagen **Complemento de Microsoft Azure Stack RP Windows Server** antes de implementar o actualizar a esta versión del proveedor de recursos MySQL.
- **Admite la eliminación de metadatos huérfanos de base de datos y metadatos del servidor de hospedaje.** Cuando ya no se pueda conectar un servidor de hospedaje, el inquilino tendrá la opción de quitar los metadatos huérfanos de la base de datos del portal. Si no hay metadatos huérfanos de base de datos vinculados al servidor de hospedaje, el operador podrá quitar los metadatos huérfanos del servidor de hospedaje del portal de administración.
- **Convierta KeyVaultPfxPassword en un argumento opcional al realizar la rotación de secretos.** Consulte [este documento](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) para obtener información detallada.
- **Otras correcciones de errores.**

Es recomendable que aplique el proveedor de recursos MySQL 1.1.93.1 después de actualizar Azure Stack Hub a la versión 2005.

## <a name="known-issues"></a>Problemas conocidos
Se puede producir un error en la implementación de la versión 1.1.93.0 si se usa el contexto AzureRmContext erróneo. Se recomienda actualizar a la versión 1.1.93.1 directamente. Si ya ha actualizado correctamente a la versión 1.1.93.0, puede omitir la versión 1.1.93.1 de forma segura.

Al volver a implementar el proveedor de recursos de MySQL cuando ya se había implementado la misma versión (por ejemplo, cuando el proveedor de recursos de MySQL versión 1.1.93.1 ya está implementado y se vuelve a implementar la misma versión), se detendrá la máquina virtual que hospeda el proveedor de recursos de MySQL. Para corregir este problema, vaya al portal de administración, busque y reinicie la máquina virtual llamada mysqlvm\<version\> en el grupo de recursos llamado system.\<region\>.mysqladapter.

## <a name="next-steps"></a>Pasos siguientes

- [Obtener más información sobre el proveedor de recursos MySQL](azure-stack-mysql-resource-provider.md).
- [Preparar la implementación del proveedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Actualizar el proveedor de recursos MySQL desde una versión anterior](azure-stack-mysql-resource-provider-update.md).
