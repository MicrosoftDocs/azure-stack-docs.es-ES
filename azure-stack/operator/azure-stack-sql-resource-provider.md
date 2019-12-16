---
title: Uso de bases de datos SQL
titleSuffix: Azure Stack
description: Vea cómo usar el proveedor de recursos de SQL Server para ofrecer bases de datos SQL como servicio en Azure Stack.
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
ms.openlocfilehash: 438cf2d8a34046f29d156aadc1cc82571e4b8a12
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993890"
---
# <a name="use-sql-databases-on-azure-stack"></a>Uso de bases de datos SQL en Azure Stack

Use el proveedor de recursos de SQL para ofrecer bases de datos SQL como servicio en [Azure Stack](azure-stack-overview.md). Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear:

- Bases de datos para las aplicaciones nativas de la nube.
- Sitios web que usan SQL.
- Cargas de trabajo que usan SQL.

Hay varias limitaciones a tener en cuenta antes de instalar el proveedor de recursos de MySQL:

- Los usuarios solo pueden crear y administrar bases de datos individuales. La instancia del servidor de base de datos no es accesible para los usuarios finales. Esto puede limitar la compatibilidad con las aplicaciones de base de datos locales que necesitan acceder a bases de datos maestras, temporales o administradas dinámicamente.
- El operador de Azure Stack es responsable de la implementación, actualización, protección, configuración y mantenimiento de los hosts y servidores de bases de datos de MySQL. El servicio Proveedor de recursos no proporciona ninguna funcionalidad de administración de instancias de servidor de bases de datos y hosts. 
- Las bases de datos de diferentes usuarios de distintas suscripciones se pueden colocar en la misma instancia del servidor de bases de datos. El proveedor de recursos no proporciona ningún mecanismo para aislar las bases de datos en diferentes hosts o instancias del servidor de bases de datos.
- El proveedor de recursos no proporciona ningún informe sobre el uso de los inquilinos de las bases de datos.

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitectura del adaptador del proveedor de recursos de SQL

El proveedor de recursos consta de los componentes siguientes:

- **La máquina virtual (VM) del adaptador del proveedor de recursos de SQL**, que es una máquina virtual de Windows Server que ejecuta los servicios del proveedor.
- **El proveedor de recursos**, que procesa las solicitudes y accede a los recursos de las bases de datos.
- **Los servidores que hospedan SQL Server**, que proporcionan capacidad para las bases de datos, conocidos como servidores de hospedaje.

Debe crear como mínimo una instancia de SQL Server o proporcionar acceso a instancias externas de SQL Server.

> [!NOTE]
> Los servidores de hospedaje que se instalan en sistemas integrados de Azure Stack se deben crear mediante una suscripción de inquilino. No se pueden crear a partir de la suscripción de proveedor predeterminada. Se deben crear desde el portal de inquilino o mediante PowerShell con el inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias. El administrador del servicio puede ser el propietario de la suscripción del inquilino.

## <a name="next-steps"></a>Pasos siguientes

[Implementar un proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
