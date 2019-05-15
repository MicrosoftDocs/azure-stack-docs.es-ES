---
title: Arquitectura de identidad para Azure Stack | Microsoft Docs
description: Conozca la arquitectura de identidad que se puede usar con Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: e09451c1bf49a2de075388f555cb7b80e70db63f
ms.sourcegitcommit: 426380a3a27954cd609ba52d1066d9d69f5267fe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2019
ms.locfileid: "65532315"
---
# <a name="identity-architecture-for-azure-stack"></a>Arquitectura de identidad para Azure Stack

Al elegir un proveedor de identidades para usarlo con Azure Stack, debe conocer las diferencias importantes entre las opciones de Azure Active Directory (Azure AD) y Servicios de federación de Active Directory (AD FS).

## <a name="capabilities-and-limitations"></a>Funcionalidades y limitaciones 
El proveedor de identidad que elija puede limitar las opciones, incluida la compatibilidad con la arquitectura multiinquilino. 

  

|Funcionalidad o escenario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Conectado a Internet     |Sí       |Opcional|
|Compatibilidad con arquitectura multiinquilino     |Sí       |Sin       |
|Ofrecer elementos en Marketplace |Sí       |Sí. Requiere el uso de la herramienta [Marketplace Syndication sin conexión](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario).|
|Compatibilidad con la biblioteca de autenticación de Active Directory (ADAL) |Sí |Sí|
|Compatibilidad con herramientas como la CLI de Azure, Visual Studio y PowerShell  |Sí |Sí|
|Creación de entidades de servicio mediante Azure Portal     |Sí |Sin |
|Creación de entidades de servicio con certificados      |Sí |Sí|
|Creación de entidades de servicio con secretos (claves)    |Sí |Sí|
|Las aplicaciones pueden usar el servicio Graph           |Sí |Sin |
|Las aplicaciones pueden utilizar el proveedor de identidades para iniciar sesión |Sí |Sí. Requiere que las aplicaciones se federen con instancias de AD FS locales. |

## <a name="topologies"></a>Topologías
En las siguientes secciones se explican las diferentes topologías de identidad que se pueden usar.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: topología de inquilino único 
De forma predeterminada, si instala Azure Stack y usa Azure AD, Azure Stack usa una topología de un único inquilino. 

Una topología de inquilino único resulta útil cuando:
- Todos los usuarios forman parte del mismo inquilino.
- Un proveedor de servicios hospeda una instancia de Azure Stack para una organización. 

![Topología de Azure Stack con un solo inquilino y Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Esta topología presenta las siguientes características:
- Azure Stack registra todas las aplicaciones y servicios en el mismo directorio del inquilino de Azure AD. 
- Azure Stack autentica solo los usuarios y las aplicaciones desde ese directorio, incluidos los tokens. 
- Las identidades de los administradores (operadores en la nube) y los usuarios del inquilino están en el mismo inquilino de directorio. 
- Para permitir que un usuario de otro directorio acceda e este entorno de Azure Stack, debe [invitar al usuario como invitado](azure-stack-identity-overview.md#guest-users) al directorio de inquilino. 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: topología multiinquilino
Los operadores de la nube pueden configurar Azure Stack para permitir el acceso a las aplicaciones por parte de inquilinos de una o varias organizaciones. Los usuarios acceden a las aplicaciones mediante el portal de usuario. En esta configuración, el portal de administración (usado por el operador en la nube) se limita a los usuarios desde un único directorio. 

Una topología mutiinquilino resulta útil cuando:
- Un proveedor de servicios desea permitir a los usuarios de varias organizaciones tener acceso a Azure Stack.

![Topología multiinquilino de Azure Stack con Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Esta topología presenta las siguientes características:
- El acceso a los recursos debe tener lugar para cada organización. 
- Los usuarios de una organización no deberían poder conceder acceso a los recursos a los usuarios que están fuera de ella. 
- Las identidades de los administradores (operadores de la nube) pueden estar en un inquilino de un directorio distinto del de las identidades de los usuarios. Esta separación proporciona el aislamiento de la cuenta en el nivel del proveedor de identidad. 
 
### <a name="ad-fs"></a>AD FS  
La topología de AD FS es obligatoria cuando alguna de las siguientes condiciones es verdadera:
- Azure Stack no se conecta a Internet.
- Azure Stack puede conectarse a Internet, pero se decide usar AD FS para el proveedor de identidad.
  
![Topología de Azure Stack con AD FS](media/azure-stack-identity-architecture/adfs.png)

Esta topología presenta las siguientes características:
- Para admitir el uso de esta topología en un entorno de producción, es necesario integrar la instancia de AD FS de Azure Stack integrada con una instancia existente de AD FS respaldada por Active Directory (AD), mediante una confianza de federación. 
- También puede integrar el servicio Graph en Azure Stack con la instancia de Active Directory existente. Igualmente, puede usar el servicio Graph API basado en OData que admite API coherentes con Graph API de Azure AD. 

  Para interactuar con la instancia de Active Directory, Graph API requiere credenciales de usuario de su instancia de Active Directory que tengan permiso de solo lectura. 
  - La instancia de AD FS integrada se basa en Windows Server 2016. 
  - Las instancias de AD FS y Active Directory deben estar basadas en Windows Server 2012 o posterior. 
  
  Entre la instancia de Active Directory y la de AD FS integrada, las interacciones no se restringen a OpenID Connect y pueden usar cualquier protocolo que ambas admitan. 
  - Las cuentas de usuario se crean y se administran en la instancia de Active Directory local.
  - Las entidades de servicio y los registros de las aplicaciones se administran en la instancia de Active Directory integrada.



## <a name="next-steps"></a>Pasos siguientes
- [Introducción a las identidades](azure-stack-identity-overview.md)   
- [Integración del centro de datos: identidad](azure-stack-integrate-identity.md)
