---
title: Eliminación del proveedor de recursos MySQL en Azure Stack Hub | Microsoft Docs
description: Obtenga información sobre cómo quitar el proveedor de recursos MySQL de la implementación de Azure Stack Hub.
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
ms.openlocfilehash: c1551badd87ba55432ff416aae1230abf317e1b3
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811046"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>Eliminación del proveedor de recursos MySQL en Azure Stack Hub

Antes de quitar el proveedor de recursos de MySQL, debe quitar todas las dependencias del proveedor. También necesitará una copia del paquete de implementación que se utilizó para instalar el proveedor de recursos.

> [!NOTE]
> Puede encontrar los vínculos de descarga para los instaladores del proveedor de recursos en [Requisitos previos de Implementación del proveedor de recursos](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Al quitar el proveedor de recursos MySQL no se eliminan las bases de datos de inquilino de los servidores de hospedaje.

## <a name="dependency-cleanup"></a>Limpieza de dependencias

Hay varias tareas de limpieza que debe realizar antes de ejecutar el script DeployMySqlProvider.ps1 para quitar el proveedor de recursos.

El operador de Azure Stack Hub es responsable de las siguientes tareas de limpieza:

* Elimine los planes que hacen referencia al adaptador de MySQL.
* Elimine toda cuota asociada al adaptador de MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Para quitar el proveedor de recursos de MySQL

1. Compruebe que ha quitado todas las dependencias existentes del proveedor de recursos de MySQL.

   > [!NOTE]
   > La desinstalación del proveedor de recursos de MySQL continuará incluso si los recursos dependientes utilizan el proveedor de recursos.
  
2. Obtenga una copia del paquete de instalación del proveedor de recursos de MySQL y ejecute el extractor automático para extraer el contenido en un directorio temporal.
3. Abra una nueva ventana de consola de PowerShell con privilegios elevados y cambie al directorio en el que extrajo los archivos de instalación del proveedor de recursos de MySQL.
4. Ejecute el script DeployMySqlProvider.ps1 con los siguientes parámetros:
    - **Uninstall**. Quita el proveedor de recursos y todos los recursos asociados.
    - **PrivilegedEndpoint**. Dirección IP o nombre DNS del punto de conexión con privilegios.
    - **AzureEnvironment**. El entorno de Azure usado para implementar Azure Stack Hub. Requerido solo para implementaciones de Azure AD.
    - **CloudAdminCredential**. Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios.
    - **DirectoryTenantID**
    - **AzCredential**. Credencial de la cuenta de administrador de servicio de Azure Stack Hub. Use las mismas credenciales que para la implementación de Azure Stack Hub.

## <a name="next-steps"></a>Pasos siguientes

[Oferta de App Services como PaaS](azure-stack-app-service-overview.md)
