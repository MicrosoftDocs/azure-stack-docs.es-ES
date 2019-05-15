---
title: Eliminación del proveedor de recursos de MySQL en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo puede quitar el proveedor de recursos de MySQL de la implementación de Azure Stack.
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: e26553193d0310101f928efe454844a06da7b0e6
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617964"
---
# <a name="remove-the-mysql-resource-provider"></a>Eliminación del proveedor de recursos de MySQL

Antes de quitar el proveedor de recursos de MySQL, debe quitar todas las dependencias del proveedor. También necesitará una copia del paquete de implementación que se utilizó para instalar el proveedor de recursos.

> [!NOTE]
> Puede encontrar los vínculos de descarga para los instaladores del proveedor de recursos en [Requisitos previos de Implementación del proveedor de recursos](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Quitar el proveedor de recursos MySQL no elimina las bases de datos de inquilino de los servidores de hospedaje.

## <a name="dependency-cleanup"></a>Limpieza de dependencias

Hay varias tareas de limpieza que debe realizar antes de ejecutar el script DeployMySqlProvider.ps1 para quitar el proveedor de recursos.

El operador de Azure Stack es responsable de las siguientes tareas de limpieza:

* Elimine los planes que hacen referencia al adaptador de MySQL.
* Elimine toda cuota asociada al adaptador de MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Para quitar el proveedor de recursos de MySQL

1. Compruebe que ha quitado todas las dependencias existentes del proveedor de recursos de MySQL.

   > [!NOTE]
   > La desinstalación del proveedor de recursos de MySQL continuará incluso si los recursos dependientes utilizan el proveedor de recursos.
  
2. Obtenga una copia del paquete de instalación del proveedor de recursos de MySQL y ejecute el extractor automático para extraer el contenido en un directorio temporal.
3. Abra una nueva ventana de consola de PowerShell con privilegios elevados y cambie al directorio en el que extrajo los archivos de instalación del proveedor de recursos de MySQL.
4. Ejecute el script DeployMySqlProvider.ps1 con los siguientes parámetros:
    - **Desinstalación**. Quita el proveedor de recursos y todos los recursos asociados.
    - **PrivilegedEndpoint**. Dirección IP o nombre DNS del punto de conexión con privilegios.
    - **AzureEnvironment**. El entorno de Azure usado para implementar Azure Stack. Requerido solo para implementaciones de Azure AD.
    - **CloudAdminCredential**. Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios.
    - **DirectoryTenantID**
    - **AzCredential**. Credencial de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

[Oferta de App Services como PaaS](azure-stack-app-service-overview.md)
