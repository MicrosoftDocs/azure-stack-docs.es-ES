---
title: Arquitectura de identidad para Azure Stack Hub
description: Obtenga información sobre la arquitectura de identidad para Azure Stack Hub y las diferencias entre Azure AD y AD FS.
author: BryanLa
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 0175e0880edbecc4cb30e05df94cf26e518b8d0e
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2020
ms.locfileid: "82173988"
---
# <a name="identity-architecture-for-azure-stack-hub"></a>Arquitectura de identidad para Azure Stack Hub

Al elegir un proveedor de identidades para usarlo con Azure Stack Hub, debe conocer las importantes diferencias entre las opciones de Azure Active Directory (Azure AD) y Servicios de federación de Active Directory (AD FS).

## <a name="capabilities-and-limitations"></a>Funcionalidades y limitaciones

El proveedor de identidad que elija puede limitar las opciones, incluida la compatibilidad con la arquitectura multiinquilino.

|Funcionalidad o escenario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Conectado a Internet     |Sí       |Opcional|
|Compatibilidad con arquitectura multiinquilino     |Sí       |No      |
|Ofrecer elementos en Marketplace |Sí       |Sí (requiere el uso de la herramienta [Marketplace Syndication sin conexión](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario))|
|Compatibilidad con la biblioteca de autenticación de Active Directory (ADAL) |Sí |Sí|
|Compatibilidad con herramientas como la CLI de Azure, Visual Studio y PowerShell  |Sí |Sí|
|Creación de entidades de servicio mediante Azure Portal     |Sí |No|
|Creación de entidades de servicio con certificados      |Sí |Sí|
|Creación de entidades de servicio con secretos (claves)    |Sí |Sí|
|Las aplicaciones pueden usar el servicio Graph           |Sí |No|
|Las aplicaciones pueden utilizar el proveedor de identidades para iniciar sesión |Sí |Sí (requiere que las aplicaciones se federen con instancias de AD FS locales) |

## <a name="topologies"></a>Topologías

En las siguientes secciones se explican las diferentes topologías de identidad que se pueden usar.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: topología de inquilino único

De forma predeterminada, si instala Azure Stack Hub y usa Azure AD, Azure Stack Hub usa una topología de inquilino único.

Una topología de inquilino único resulta útil cuando:
- Todos los usuarios forman parte del mismo inquilino.
- Un proveedor de servicios hospeda una instancia de Azure Stack Hub para una organización.

![Topología de inquilino único de Azure Stack Hub y Azure AD](media/azure-stack-identity-architecture/single-tenant.svg)

Esta topología presenta las siguientes características:

- Azure Stack Hub registra todas las aplicaciones y servicios en el mismo directorio del inquilino de Azure AD.
- Azure Stack Hub autentica solo los usuarios y las aplicaciones desde ese directorio, incluidos los tokens.
- Las identidades de los administradores (operadores en la nube) y los usuarios del inquilino están en el mismo inquilino de directorio.
- Para permitir que un usuario de otro directorio acceda e este entorno de Azure Stack Hub, debe [invitar al usuario como invitado](azure-stack-identity-overview.md#guest-users) al directorio de inquilino.

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: topología multiinquilino

Los operadores de la nube pueden configurar Azure Stack Hub para permitir el acceso a las aplicaciones por parte de inquilinos de una o varias organizaciones. Los usuarios acceden a las aplicaciones mediante el portal de usuarios de Azure Stack Hub. En esta configuración, el portal de administración (usado por el operador en la nube) se limita a los usuarios desde un único directorio.

Una topología mutiinquilino resulta útil cuando:

- Un proveedor de servicios desea permitir que los usuarios de varias organizaciones accedan a Azure Stack Hub.

![Topología multiinquilino de Azure Stack Hub con Azure AD](media/azure-stack-identity-architecture/multi-tenant.svg)

Esta topología presenta las siguientes características:

- El acceso a los recursos debe tener lugar para cada organización.
- Los usuarios de una organización no deberían poder conceder acceso a los recursos a los usuarios que están fuera de ella.
- Las identidades de los administradores (operadores de la nube) pueden estar en un inquilino de un directorio distinto del de las identidades de los usuarios. Esta separación proporciona el aislamiento de la cuenta en el nivel del proveedor de identidad.
 
### <a name="ad-fs"></a>AD FS

La topología de AD FS es obligatoria cuando alguna de las siguientes condiciones es verdadera:

- Azure Stack Hub no se conecta a Internet.
- Azure Stack Hub puede conectarse a Internet, pero se decide usar AD FS para el proveedor de identidades.
  
![Topología de Azure Stack Hub con AD FS](media/azure-stack-identity-architecture/adfs.svg)

Esta topología presenta las siguientes características:

- Para admitir el uso de esta topología en un entorno de producción, es necesario integrar la instancia de AD FS de Azure Stack Hub integrada con una instancia existente de AD FS respaldada por Active Directory, mediante una confianza de federación.
- También puede integrar el servicio Graph en Azure Stack Hub con la instancia de Active Directory existente. Igualmente, puede usar el servicio Graph API basado en OData que admite API coherentes con Graph API de Azure AD.

  Para interactuar con la instancia de Active Directory, Graph API requiere credenciales de usuario de su instancia de Active Directory que tengan permiso de solo lectura.
  - La instancia de AD FS integrada se basa en Windows Server 2016.
  - Las instancias de AD FS y Active Directory deben estar basadas en Windows Server 2012 o posterior.
  
  Entre la instancia de Active Directory y la de AD FS integrada, las interacciones no se restringen a OpenID Connect y pueden usar cualquier protocolo que ambas admitan.
  - Las cuentas de usuario se crean y se administran en la instancia de Active Directory local.
  - Las entidades de servicio y los registros de las aplicaciones se administran en la instancia de Active Directory integrada.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a las identidades](azure-stack-identity-overview.md)
- [Integración del centro de datos: identidad](azure-stack-integrate-identity.md)
