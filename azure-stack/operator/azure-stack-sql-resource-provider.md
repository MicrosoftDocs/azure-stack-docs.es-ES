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
ms.openlocfilehash: d2ce6c0af2912a2658db80301c9a64c8e3d5c066
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780735"
---
# <a name="use-sql-databases-on-azure-stack"></a>Uso de bases de datos SQL en Azure Stack

Use el proveedor de recursos de SQL Server para ofrecer bases de datos SQL como servicio en [Azure Stack](azure-stack-overview.md). Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear:

- Bases de datos para las aplicaciones nativas de la nube.
- Sitios web que usan SQL.
- Cargas de trabajo que usan SQL.

El proveedor de recursos no proporciona todas las capacidades de administración de bases de datos de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Por ejemplo, no se admiten grupos elásticos que asignan recursos automáticamente. Sin embargo, el proveedor de recursos admite operaciones similares de creación, lectura, actualización y eliminación (CRUD) en una base de datos de SQL Server.

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
