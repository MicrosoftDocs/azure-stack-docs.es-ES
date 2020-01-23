---
title: Notas de la versión del proveedor de recursos MySQL de Azure Stack Hub 1.1.33.0 | Microsoft Docs
description: Consulte las notas de la versión para ver las novedades de la actualización 1.1.33.0 del proveedor de recursos MySQL de Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 4067068090a4ae243b40af4f382a80c8bfc14455
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882239"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Notas de la versión del proveedor de recursos de MySQL 1.1.33.0

Estas notas de la versión describen las mejoras y los problemas conocidos de la versión del proveedor de recursos de MySQL 1.1.33.0.

## <a name="build-reference"></a>Referencia de compilación
Descargue el archivo binario del proveedor de recursos MySQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack Hub. La versión mínima de Azure Stack Hub necesaria para instalar esta versión del proveedor de recursos MySQL se muestra a continuación:

> |Versión mínima de Azure Stack Hub|Versión del proveedor de recursos MySQL|
> |-----|-----|
> |Versión 1808 (1.1808.0.97)|[MySQL RP, versión 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Aplique la actualización mínima admitida de Azure Stack Hub al sistema integrado de Azure Stack Hub o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar la versión más reciente del proveedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Nuevas características y correcciones
Esta versión del proveedor de recursos MySQL de Azure Stack Hub incluye las siguientes mejoras y correcciones:

### <a name="fixes"></a>Correcciones

- **La extensión del portal del proveedor de recursos de MySQL puede elegir la suscripción incorrecta**. El proveedor de recursos de MySQL utiliza las llamadas de Azure Resource Manager para determinar la primera suscripción de administrador de servicio que se va a utilizar, que podría no ser la *suscripción de proveedor predeterminado*. Si esto sucede, el proveedor de recursos MySQL no funciona con normalidad.

- **El servidor de hospedaje de MySQL no muestra las bases de datos hospedadas.** Es posible que las bases de datos creadas por el usuario no aparezcan en la lista cuando se visualizan los recursos de los inquilinos para servidores de hospedaje de MySQL.

- **La implementación anterior del proveedor de recursos de MySQL (1.1.30.0) podría producir un error si TLS 1.2 no está habilitado**. Se ha actualizado el proveedor de recursos de MySQL 1.1.33.0 para habilitar TLS 1.2 al implementar el proveedor de recursos, actualizar el proveedor de recursos o cambiar los secretos.

- **Se produce un error al cambiar los secretos del proveedor de recursos de MySQL**. Se ha corregido un error que daba como resultado el siguiente código de error al cambiar los secretos: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problemas conocidos

- **Las SKU de MySQL pueden tardar hasta una hora en estar visibles en el portal**. Las SKU recién creadas pueden tardar hasta una hora en estar visible para su uso al crear nuevas bases de datos MySQL. 

    **Solución alternativa**: Ninguno.

- **Inicios de sesión MySQL reutilizados**. Al intentar crear un nuevo inicio de sesión MySQL con el mismo nombre de usuario que un inicio de sesión existente en la misma suscripción, se reutilizará el mismo inicio de sesión y la contraseña existente.

    **Solución alternativa**: use diferentes nombres de usuario al crear nuevos inicios de sesión en la misma suscripción o crear inicios de sesión con el mismo nombre de usuario en diferentes suscripciones.

- **Los inicios de sesión MySQL compartidos causan incoherencia de datos**. Si se comparte un inicio de sesión MySQL para varias bases de datos MySQL en la misma suscripción, cambiar la contraseña de inicio de sesión provocará una incoherencia de datos.

    **Solución alternativa**: use siempre inicios de sesión diferentes para distintas bases de datos en la misma suscripción.


### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Problemas conocidos para los administradores de la nube que trabajan con Azure Stack Hub
Consulte la documentación de las [Notas de la versión de Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Pasos siguientes
[Obtener más información sobre el proveedor de recursos MySQL](azure-stack-mysql-resource-provider.md).

[Preparar la implementación del proveedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Actualizar el proveedor de recursos MySQL desde una versión anterior](azure-stack-mysql-resource-provider-update.md). 