---
title: Uso de bases de datos MySQL como PaaS en Azure Stack Hub
description: Aprenda cómo implementar el proveedor de recursos MySQL y proporcionar bases de datos MySQL como servicio en Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 73c916a193492662cdbb3ba20031c58932721053
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698596"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack-hub"></a>Uso de bases de datos MySQL en Microsoft Azure Stack Hub

Use el proveedor de recursos MySQL para ofrecer bases de datos MySQL en [Azure Stack Hub](azure-stack-overview.md). Después de implementar el proveedor de recursos y conectarlo a una o varias instancias de servidor MySQL, puede crear:

* Bases de datos MySQL para aplicaciones nativas de la nube.
* Bases de datos MySQL para aplicaciones web.  

Hay varias limitaciones a tener en cuenta antes de instalar el proveedor de recursos de MySQL:

- Los usuarios solo pueden crear y administrar bases de datos individuales. La instancia del servidor de base de datos no es accesible para los usuarios finales. Esto puede limitar la compatibilidad con las aplicaciones de base de datos locales que necesitan acceder a bases de datos maestras, temporales o administradas dinámicamente.
- El operador de Azure Stack Hub es responsable de la implementación, la actualización, la protección, la configuración y el mantenimiento de los hosts y servidores de bases de datos MySQL. El servicio Proveedor de recursos no proporciona ninguna funcionalidad de administración de instancias de servidor de bases de datos y hosts. 
- Las bases de datos de diferentes usuarios de distintas suscripciones se pueden colocar en la misma instancia del servidor de bases de datos. El proveedor de recursos no proporciona ningún mecanismo para aislar las bases de datos en diferentes hosts o instancias del servidor de bases de datos.
- El proveedor de recursos no proporciona ningún informe sobre el uso de los inquilinos de las bases de datos.

## <a name="mysql-resource-provider-adapter-architecture"></a>Arquitectura del adaptador del proveedor de recursos de MySQL

El proveedor de recursos consta de los componentes siguientes:

* **La máquina virtual (VM) del adaptador del proveedor de recursos de MySQL**, que es una máquina virtual Windows Server que ejecuta los servicios del proveedor.
* **El proveedor de recursos**, que procesa las solicitudes y accede a los recursos de las bases de datos.
* **Los servidores que hospedan el servidor MySQL**, que proporcionan capacidad para las bases de datos, denominados servidores host. Puede crear instancias de MySQL usted mismo o proporcionar acceso a instancias de MySQL externas. En la [Galería de inicio rápido de Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) encontrará una plantilla de ejemplo que puede usar para hacer lo siguiente:

  * Crear un servidor MySQL en su lugar.
  * Descargar e implementar un servidor MySQL desde Azure Marketplace.

> [!NOTE]
> Los servidores de hospedaje que se instalan en sistemas integrados de Azure Stack Hub se deben crear a partir de una suscripción de inquilino. No se pueden crear a partir de la suscripción de proveedor predeterminada. Se deben crear en el portal de usuarios o en una sesión de PowerShell con un inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias. El administrador del servicio puede ser el propietario de la suscripción de inquilino.

### <a name="required-privileges"></a>Privilegios necesarios

Esta cuenta del sistema debe tener los siguientes privilegios:

* **Base de datos:** crear, eliminar
* **Inicio de sesión:** crear, establecer, eliminar, conceder, revocar  

## <a name="next-steps"></a>Pasos siguientes

[Implementación del proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)
