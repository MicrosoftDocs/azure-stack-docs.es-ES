---
title: Notas de la versión del proveedor de recursos de MySQL 1.1.30.0 de Azure Stack Hub
description: Consulte las notas de la versión para ver las novedades de la actualización 1.1.30.0 del proveedor de recursos MySQL de Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 710b5d48e4ce906eb357c5e17cff4132bcd747ba
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698936"
---
# <a name="mysql-resource-provider-11300-release-notes"></a>Notas de la versión del proveedor de recursos de MySQL 1.1.30.0

Estas notas de la versión describen las mejoras y los problemas conocidos de la versión del proveedor de recursos MySQL 1.1.30.0.

## <a name="build-reference"></a>Referencia de compilación
Descargue el archivo binario del proveedor de recursos MySQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack Hub. La versión mínima de Azure Stack Hub necesaria para instalar esta versión del proveedor de recursos MySQL se muestra a continuación:

> |Versión mínima de Azure Stack Hub|Versión del proveedor de recursos MySQL|
> |-----|-----|
> |Actualización de Azure Stack Hub 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Aplique la actualización mínima admitida de Azure Stack Hub al sistema integrado de Azure Stack Hub o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar la versión más reciente del proveedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Nuevas características y correcciones
Esta versión del proveedor de recursos MySQL de Azure Stack Hub incluye las siguientes mejoras y correcciones:

- **Telemetría habilitada para las implementaciones del proveedor de recursos MySQL**. La colección de telemetría se ha habilitado para las implementaciones del proveedor de recursos MySQL. La telemetría recopilada incluye la implementación del proveedor de recursos, las horas de inicio y fin, el estado de salida, los mensajes de salida y los detalles del error (si procede).

- **Actualización de cifrado TLS 1.2**. Se ha habilitado la compatibilidad solo de TLS 1.2 para la comunicación del proveedor de recursos con los componentes internos de Azure Stack Hub. 

### <a name="fixes"></a>Correcciones

- **Compatibilidad del proveedor de recursos MySQL con PowerShell de Azure Stack Hub**. El proveedor de recursos MySQL se ha actualizado para funcionar con el perfil de PowerShell de Azure Stack Hub 2018-03-01-hybrid y para proporcionar compatibilidad con AzureRM 1.3.0 y versiones posteriores.

- **Hoja de cambio de contraseña de inicio de sesión MySQL**. Se ha corregido un problema que impedía cambiar la contraseña en la hoja de cambio de contraseña. Se han quitado los vínculos de las notificaciones de cambio de contraseña.

## <a name="known-issues"></a>Problemas conocidos

- **Las SKU de MySQL pueden tardar hasta una hora en estar visibles en el portal**. Las SKU recién creadas pueden tardar hasta una hora en estar visible para su uso al crear nuevas bases de datos MySQL.

    **Solución alternativa**: Ninguno.

- **Inicios de sesión MySQL reutilizados**. Al intentar crear un nuevo inicio de sesión MySQL con el mismo nombre de usuario que un inicio de sesión existente en la misma suscripción, se reutilizará el mismo inicio de sesión y la contraseña existente.

    **Solución alternativa**: use diferentes nombres de usuario al crear nuevos inicios de sesión en la misma suscripción o crear inicios de sesión con el mismo nombre de usuario en diferentes suscripciones.

- **Requisito de compatibilidad con TLS 1.2**. Si intenta implementar o actualizar el proveedor de recursos MySQL desde un equipo donde TLS 1.2 no está habilitado, se puede producir un error en la operación. Ejecute el siguiente comando de PowerShell en el equipo que se usa para implementar o actualizar el proveedor de recursos a fin de comprobar que TLS 1.2 se devuelve como compatible:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Si **Tls12** no se incluye en la salida del comando, TLS 1.2 no está habilitado en el equipo.

    **Solución alternativa**: Ejecute el siguiente comando de PowerShell para habilitar TLS 1.2 y, a continuación, inicie la implementación del proveedor de recursos o actualice el script desde la misma sesión de PowerShell:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Problemas conocidos para los administradores de la nube que trabajan con Azure Stack Hub
Consulte la documentación de las [Notas de la versión de Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Pasos siguientes
[Obtener más información sobre el proveedor de recursos MySQL](azure-stack-mysql-resource-provider.md).

[Preparar la implementación del proveedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Actualizar el proveedor de recursos MySQL desde una versión anterior](azure-stack-mysql-resource-provider-update.md). 