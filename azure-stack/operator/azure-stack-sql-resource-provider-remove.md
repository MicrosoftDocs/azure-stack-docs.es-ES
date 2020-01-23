---
title: Eliminación del proveedor de recursos de SQL
titleSuffix: Azure Stack Hub
description: Obtenga información sobre cómo quitar el proveedor de recursos de SQL de la implementación de Azure Stack Hub.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 15178627cbc58cd466a09f7ef534e7ec115d9c1a
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814412"
---
# <a name="remove-the-sql-resource-provider"></a>Eliminación del proveedor de recursos de SQL

Antes de quitar el proveedor de recursos de SQL, debe quitar todas las dependencias del proveedor. También necesitará una copia del paquete de implementación que se utilizó para instalar el proveedor de recursos.

> [!NOTE]
> Puede encontrar los vínculos de descarga para los instaladores del proveedor de recursos en [Requisitos previos de Implementación del proveedor de recursos](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Al quitar el proveedor de recursos de SQL no se eliminan las bases de datos de inquilino de los servidores de hospedaje.

## <a name="dependency-cleanup"></a>Limpieza de dependencias

Hay varias tareas de limpieza que debe realizar antes de ejecutar el script DeploySqlProvider.ps1 para quitar el proveedor de recursos.

El operador de Azure Stack Hub es responsable de las siguientes tareas de limpieza:

* Elimine los planes que hacen referencia al adaptador de SQL.
* Elimine toda cuota asociada al adaptador de SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Proceso para quitar el proveedor de recursos de SQL

1. Compruebe que ha quitado todas las dependencias del proveedor de recursos de SQL existentes.

   > [!NOTE]
   > La desinstalación del proveedor de recursos de SQL continuará incluso si los recursos dependientes utilizan el proveedor de recursos.
  
2. Obtenga una copia del paquete de instalación del proveedor de recursos de SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal.

3. Abra una nueva ventana de consola de PowerShell con privilegios elevados y cambie al directorio en el que extrajo los archivos de instalación del proveedor de recursos de SQL.

4. Ejecute el script DeploySqlProvider.ps1 con los siguientes parámetros:

    * **Uninstall**. Quita el proveedor de recursos y todos los recursos asociados.
    * **PrivilegedEndpoint**. Dirección IP o nombre DNS del punto de conexión con privilegios.
    * **AzureEnvironment**. El entorno de Azure usado para implementar Azure Stack Hub. Requerido solo para implementaciones de Azure AD.
    * **CloudAdminCredential**. Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios.
    * **AzCredential**. Credencial de la cuenta de administrador de servicio de Azure Stack Hub. Use las mismas credenciales que para la implementación de Azure Stack Hub.

## <a name="next-steps"></a>Pasos siguientes

[Oferta de App Services como PaaS](azure-stack-app-service-overview.md)
