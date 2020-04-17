---
title: Eliminación del proveedor de recursos MySQL en Azure Stack Hub
description: Obtenga información sobre cómo quitar el proveedor de recursos MySQL de la implementación de Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2
ms.openlocfilehash: 60f8ab926bb868517c7b9feeb1c5c4325212607c
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "80302815"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>Eliminación del proveedor de recursos MySQL en Azure Stack Hub

Antes de quitar el proveedor de recursos de MySQL, debe quitar todas las dependencias del proveedor. También necesitará una copia del paquete de implementación que se utilizó para instalar el proveedor de recursos.

> [!NOTE]
> Puede encontrar los vínculos de descarga para los instaladores del proveedor de recursos en [Requisitos previos de Implementación del proveedor de recursos](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Al eliminar el proveedor de recursos de MySQL se eliminarán los planes y cuotas asociados administrados por el operador. Pero no se eliminarán las bases de datos de inquilino de los servidores host.

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
    - **AzCredential**. Credencial de la cuenta de administrador de servicio de Azure Stack Hub. Use las mismas credenciales que para la implementación de Azure Stack Hub.

## <a name="next-steps"></a>Pasos siguientes

[Oferta de App Services como PaaS](azure-stack-app-service-overview.md)
