---
title: Uso de bases de datos SQL en Azure Stack | Microsoft Docs
description: Aprenda a implementar las bases de datos SQL como un servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de SQL Server.
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
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 12815bff535e45f42481d17029467227650e8aea
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618426"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Uso de bases de datos SQL en Microsoft Azure Stack

Use el proveedor de recursos de SQL Server para ofrecer las bases de datos de SQL como un servicio de [Azure Stack](azure-stack-overview.md). Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear:

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
> Los servidores de hospedaje que se instalan en sistemas integrados de Azure Stack se deben crear mediante una suscripción de inquilino. No se pueden crear a partir de la suscripción de proveedor predeterminada. Se deben crear desde el portal de inquilino o mediante PowerShell con el inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener licencias. El administrador del servicio puede ser el propietario de la suscripción de inquilino.

## <a name="next-steps"></a>Pasos siguientes

[Implementar un proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
