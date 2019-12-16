---
title: Usar bases de datos MySQL como PaaS en Azure Stack | Microsoft Docs
description: Aprenda cómo implementar el proveedor de recursos de MySQL y proporcionar bases de datos MySQL como servicio en Azure Stack.
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
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: b66a72ce872d64f8fde3cb80ced5e6ad33d80b4d
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993781"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bases de datos MySQL en Microsoft Azure Stack

Las bases de datos de MySQL se usan normalmente en los sitios web y admiten muchas plataformas de sitio web. Por ejemplo, puede crear sitios web de WordPress con el complemento del proveedor de recursos (PaaS) de App Services.

Después de implementar el proveedor de recursos y conectarlo a una o varias instancias de servidor MySQL, puede:

* Crear bases de datos de MySQL mediante las plantillas de implementación de Azure Resource Manager.
* Proporcionar bases de datos MySQL como servicio.  

Hay varias limitaciones a tener en cuenta antes de instalar el proveedor de recursos de MySQL:

- Los usuarios solo pueden crear y administrar bases de datos individuales. La instancia del servidor de base de datos no es accesible para los usuarios finales. Esto puede limitar la compatibilidad con las aplicaciones de base de datos locales que necesitan acceder a bases de datos maestras, temporales o administradas dinámicamente.
- El operador de Azure Stack es responsable de la implementación, actualización, protección, configuración y mantenimiento de los hosts y servidores de bases de datos de MySQL. El servicio Proveedor de recursos no proporciona ninguna funcionalidad de administración de instancias de servidor de bases de datos y hosts. 
- Las bases de datos de diferentes usuarios de distintas suscripciones se pueden colocar en la misma instancia del servidor de bases de datos. El proveedor de recursos no proporciona ningún mecanismo para aislar las bases de datos en diferentes hosts o instancias del servidor de bases de datos.
- El proveedor de recursos no proporciona ningún informe sobre el uso de los inquilinos de las bases de datos.

## <a name="mysql-resource-provider-adapter-architecture"></a>Arquitectura del adaptador del proveedor de recursos de MySQL

El proveedor de recursos consta de los componentes siguientes:

* **La máquina virtual (VM) del adaptador del proveedor de recursos de MySQL**, que es una máquina virtual Windows Server que ejecuta los servicios del proveedor.
* **El proveedor de recursos**, que procesa las solicitudes y accede a los recursos de las bases de datos.
* **Los servidores que hospedan el servidor MySQL**, que proporcionan capacidad para las bases de datos, denominados servidores host. Puede crear instancias de MySQL usted mismo o proporcionar acceso a instancias de MySQL externas. Visite la [Galería de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) para obtener una plantilla de ejemplo que pueda usar para hacer lo siguiente:

  * Crear un servidor MySQL en su lugar.
  * Descargar e implementar un servidor MySQL desde Azure Marketplace.

> [!NOTE]
> Los servidores de hospedaje que se instalan en sistemas integrados de Azure Stack se deben crear mediante una suscripción de inquilino. No se pueden crear a partir de la suscripción de proveedor predeterminada. Se deben crear desde el portal de inquilino o desde una sesión de PowerShell con un inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias. El administrador del servicio puede ser el propietario de la suscripción de inquilino.

### <a name="required-privileges"></a>Privilegios necesarios

Esta cuenta del sistema debe tener los siguientes privilegios:

* **Base de datos:** crear, eliminar
* **Inicio de sesión:** crear, establecer, eliminar, conceder, revocar  

## <a name="next-steps"></a>Pasos siguientes

[Implementación del proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)
