---
title: Uso de bases de datos SQL
titleSuffix: Azure Stack Hub
description: Aprenda a usar el proveedor de recursos de SQL Server para ofrecer bases de datos SQL como servicio en Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 5f09af556a9541ac0d081a986ac4c2befb26b203
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491771"
---
# <a name="use-sql-databases-on-azure-stack-hub"></a>Uso de bases de datos SQL en Azure Stack Hub

Use el proveedor de recursos SQL para ofrecer bases de datos SQL en [Azure Stack Hub](azure-stack-overview.md). Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear:

- Bases de datos SQL para aplicaciones nativas de la nube.
- Bases de datos SQL para aplicaciones web.

Limitaciones a tener en cuenta antes de instalar el proveedor de recursos de SQL:

- Los usuarios solo pueden crear y administrar bases de datos individuales. La instancia del servidor de base de datos no es accesible para los usuarios finales. Esto puede limitar la compatibilidad con las aplicaciones de bases de datos locales que necesitan acceder a bases de datos maestras, temporales o administradas dinámicamente.
- El operador de Azure Stack Hub es responsable de implementar, actualizar, proteger, configurar y mantener los hosts y servidores de bases de datos de SQL. El servicio Proveedor de recursos no proporciona ninguna funcionalidad de administración de instancias de servidor de bases de datos y hosts.
- Las bases de datos de diferentes usuarios de distintas suscripciones se pueden colocar en la misma instancia del servidor de bases de datos. El proveedor de recursos no proporciona ningún mecanismo para aislar las bases de datos en diferentes hosts o instancias del servidor de bases de datos.
- El proveedor de recursos no proporciona ningún informe sobre el uso de los inquilinos de las bases de datos.

En el caso de las cargas de trabajo tradicionales locales de SQL Server, se recomienda la máquina virtual de SQL Server en Azure Stack Hub.

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitectura del adaptador del proveedor de recursos de SQL

El proveedor de recursos consta de los componentes siguientes:

- **La máquina virtual (VM) del adaptador del proveedor de recursos de SQL**, que es una máquina virtual de Windows Server que ejecuta los servicios del proveedor.
- **El proveedor de recursos**, que procesa las solicitudes y accede a los recursos de las bases de datos.
- **Los servidores que hospedan SQL Server**, que proporcionan capacidad para las bases de datos, conocidos como servidores de hospedaje.

Debe crear como mínimo una instancia de SQL Server o proporcionar acceso a instancias externas de SQL Server.

> [!NOTE]
> Los servidores de hospedaje que se instalan en sistemas integrados de Azure Stack Hub se deben crear a partir de una suscripción de inquilino. No se pueden crear a partir de la suscripción de proveedor predeterminada. Se deben crear en el portal de usuarios o con PowerShell con el inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias. El administrador del servicio puede ser el propietario de la suscripción del inquilino.

## <a name="next-steps"></a>Pasos siguientes

[Implementar un proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
