---
title: Eliminación del proveedor de recursos de SQL
titleSuffix: Azure Stack Hub
description: Obtenga información sobre cómo quitar el proveedor de recursos de SQL de la implementación de Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2019
ms.openlocfilehash: 7046eff5aa90504e2f02d7b7d24b1c2a74421270
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697185"
---
# <a name="remove-the-sql-resource-provider"></a>Eliminación del proveedor de recursos de SQL

Antes de quitar el proveedor de recursos de SQL, debe quitar todas las dependencias del proveedor. También necesitará una copia del paquete de implementación que se utilizó para instalar el proveedor de recursos.

> [!NOTE]
> Puede encontrar los vínculos de descarga para los instaladores del proveedor de recursos en [Requisitos previos de Implementación del proveedor de recursos](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Si quita el proveedor de recursos de SQL, se eliminarán los planes y cuotas asociados administrados por el operador. Pero no se eliminarán las bases de datos de inquilino de los servidores host.

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
