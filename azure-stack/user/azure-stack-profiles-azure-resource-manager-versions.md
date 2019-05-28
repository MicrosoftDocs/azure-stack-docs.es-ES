---
title: Versiones de API del proveedor de recursos compatibles con perfiles en Azure Stack | Microsoft Docs
description: Obtenga información sobre las versiones de la API de Azure Resource Manager compatibles con perfiles en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 36fd25897e2d45d454f8e1f956f5e72991d45059
ms.sourcegitcommit: 1655b2ef4d01d69ceeb52bc16f922bdc19cb968d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/15/2019
ms.locfileid: "65706261"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Versiones de API del proveedor de recursos compatibles con perfiles en Azure Stack

Puede encontrar el proveedor de recursos y los números de versión para cada perfil de API usado por Azure Stack en este artículo. En las tablas de este artículo se enumeran las versiones compatibles con cada proveedor de recursos y las versiones de API de los perfiles. Cada proveedor de recursos contiene un conjunto de tipos de recursos y números de versión específicos.

El perfil de API usa tres convenciones de nomenclatura:

- **más reciente**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

Para obtener una explicación de los perfiles de API y el ritmo de lanzamientos de versiones para Azure Stack, consulte [Administración de perfiles de la versión de API en Azure Stack](azure-stack-version-profiles.md).

> [!Note]
> El perfil de API **más reciente** contiene la versión de API del proveedor de recursos más reciente y no se enumera en este artículo.

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>Introducción al perfil 2019-03-01-hybrid

| Proveedor de recursos | Versión de API |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>VPN Gateway será 2017-10-01. |
| Microsoft.Storage (plano de datos) | 2017-10-01 |
| Microsoft.Storage (plano de control) | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01 (sin cambios) |
| Microsoft.Resources (el propio Azure Resource Manager) | 2016-06-01 |
| Microsoft.Authorization (operaciones de directiva) | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

Para obtener una lista de las versiones de cada tipo de recursos de los proveedores del perfil de API, consulte [Detalles del perfil 2019-03-01-hybrid](#details-for-the-2019-03-01-hybrid-profile).

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>Detalles del perfil 2019-03-01-hybrid

### <a name="microsoftauthorization"></a>Microsoft.Authorization

El control de acceso basado en rol le permite administrar las acciones que los usuarios de su organización pueden realizar en los recursos. Puede definir roles, asignar roles a los usuarios o grupos y obtener información sobre los permisos. Para más información, consulte [Autorización](/rest/api/authorization/).

| Tipos de recursos | Versiones de API |
|---------------------|--------------------|
| Bloqueos | 2016-09-01 |
| Operaciones | 2015-07-01 |
| Permisos | 2015-07-01 |
| Asignaciones de directiva | 2016-12-01 |
| Definiciones de directiva | 2016-12-01 |
| Operaciones del proveedor | 2015-07-01 |
| Asignaciones de roles | 2015-07-01 |
| Definiciones de roles | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Tipo de recurso | Versión de API |
|----------------------------------|----------------------|
| Suscripciones de proveedor delegado | 2015-06-01 - preview |
| Agregados de uso delegado | 2015-06-01 - preview |
| Gasto estimado de recursos | 2015-06-01 - preview |
| Operaciones | 2015-06-01 - preview |
| Agregados de uso de suscriptor | 2015-06-01 - preview |
| Agregados de uso | 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

Las API de Azure Compute proporcionan acceso mediante programación a las máquinas virtuales y sus recursos de apoyo. Para más información, consulte [Azure Compute](/rest/api/compute/).

| Tipo de recurso | Versión de API |
|---------------------------------------------------------------|-------------|
| Conjuntos de disponibilidad | 2017-12-01 |
| Ubicaciones | 2017-12-01 |
| Ubicaciones/operaciones | 2017-12-01 |
| Ubicaciones/publicadores | 2017-12-01 |
| Ubicaciones/usos | 2017-12-01 |
| Ubicaciones/vmSizes | 2017-12-01 |
| Operaciones | 2017-12-01 |
| Virtual Machines | 2017-12-01 |
| Virtual Machines/extensiones | 2017-12-01 |
| Virtual Machine Scale Sets | 2017-12-01 |
| Virtual Machine Scale Sets/extensiones | 2017-12-01 |
| Virtual Machine Scale Sets/Interfaces de red | 2017-12-01 |
| Virtual Machine Scale Sets/Máquinas virtuales | 2017-12-01|
| Virtual Machines Scale Sets/virtualMachines/networkInterfaces | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Tipo de recurso | Versión de API |
|------------------|-------------|
| Protección | 2015-04-01 |
| Contenido de protección | 2015-04-01 |
| Extracción de protección | 2015-04-01 |
| Elementos de la galería | 2015-04-01 |
| Operaciones | 2015-04-01 |
| Portal | 2015-04-01 |
| Search | 2015-04-01 |
| Sugerir | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Tipos de recursos | Versiones de API |
|--------------------|--------------------|
| Operaciones | 2015-04-01 |
| Tipos de eventos | 2015-04-01 |
| Categorías de eventos | 2015-04-01 |
| Definiciones de métricas | 2018-01-01 |
| Métricas | 2018-01-01 |
| Configuración de diagnóstico | 2017-05-01-versión preliminar |
| Categorías de configuración de diagnóstico | 2017-05-01-versión preliminar |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Administre su instancia de Key Vault, así como las claves, los secretos y los certificados correspondientes. Para más información, consulte el artículo de [referencia de la API REST de Azure Key Vault](/rest/api/keyvault/).

| Tipos de recursos | Versiones de API |
|-------------------------|--------------|
| Operaciones | 2016-10-01 |
| Almacenes | 2016-10-01 |
| Almacenes/directivas de acceso | 2016-10-01 |
| Almacenes/secretos | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

El resultado de la llamada a las operaciones es una representación de la lista de operaciones de nube de red disponibles. Para más información, consulte la [API de REST de operación](/rest/api/operation/).

| Tipos de recursos | Versiones de API |
|---------------------------|--------------|
| Conexiones | 2017-10-01 |
| Zonas DNS | 2016-04-01 |
| Equilibradores de carga | 2017-10-01 |
| Puerta de enlace de red local | 2017-10-01 |
| Ubicaciones | 2017-10-01|
| Ubicación/operationResults | 2017-10-01 |
| Ubicaciones/operaciones | 2017-10-01 |
| Ubicaciones/usos |2017-10-01 |
| Interfaces de red | 2017-10-01 |
| Grupos de seguridad de red | 2017-10-01 |
| Operaciones | 2017-10-01 |
| Dirección IP pública | 2017-10-01 |
| Tablas de ruta | 2017-10-01 |
| Puerta de enlace de red virtual | 2017-10-01 |
| Virtual Networks | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager le permite implementar y administrar la infraestructura para las soluciones de Azure. Puede organizar los recursos relacionados en grupos de recursos e implementar sus recursos con plantillas JSON. Para una introducción a la implementación y la administración de recursos con Resource Manager, consulte [Introducción a Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

| Tipos de recursos | Versiones de API |
|-----------------------------------------|-------------------|
| Implementaciones | 2018-05-01 |
| Implementaciones/operaciones | 2018-05-01 |
| Vínculos | 2018-05-01 |
| Ubicaciones | 2018-05-01 |
| Operaciones | 2018-05-01 |
| Proveedores | 2018-05-01 |
| ResourceGroups| 2018-05-01 |
| Recursos | 2018-05-01/ |
| Suscripciones | 2018-05-01 |
| Subscriptions/locations | 2016-06-01 |
| Subscriptions/operationresults | 2018-05-01 |
| Suscripciones/proveedores | 2018-05-01 |
| Subscriptions/ResourceGroups | 2018-05-01 |
| Suscripciones/resourceGroups/recursos | 2018-05-01 |
| Suscripciones/recursos | 2018-05-01 |
| Suscripciones/tagNames | 2018-05-01 |
| Suscripciones/tagNames/tagValues | 2018-05-01 |
| Inquilinos | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

El proveedor de recursos de almacenamiento (SRP) le permite administrar mediante programación la cuenta de almacenamiento y las claves. Para más información, consulte [Azure Storage Resource Provider REST API reference](/rest/api/storagerp/) (Referencia de API REST del proveedor de recursos de Azure Storage).

| Tipos de recursos | Versiones de API |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| Ubicaciones | 2017-10-01 |
| Ubicaciones/cuotas | 2017-10-01 |
| Operaciones | 2017-10-01 |
| StorageAccounts | 2017-10-01 |
| Usos | 2017-10-01 |

## <a name="next-steps"></a>Pasos siguientes

* [Install PowerShell for Azure Stack](../operator/azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)  
