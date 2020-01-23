---
title: Introducción a los proveedores de identidades en Azure Stack Hub| Microsoft Docs
description: Conozca los proveedores de identidades que se pueden usar con Azure Stack Hub.
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
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f8c5fdaa638c7ab5fa0669ffee57611e89c192ff
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882562"
---
# <a name="overview-of-identity-providers-for-azure-stack-hub"></a>Introducción a los proveedores de identidades en Azure Stack Hub

Azure Stack Hub requiere un proveedor de identidades como Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS) respaldados por Active Directory (AD). La elección de un proveedor es una decisión puntual que solo se toma la primera vez que implementa Azure Stack Hub. Los conceptos y la información detallada sobre la autorización que encontrará en este artículo pueden ayudarle a realizar su elección entre los proveedores de identidades.

La decisión entre Azure AD o AD FS viene determinada por el modo en el que se implementa Azure Stack Hub:

- Cuando se implementa en modo conectado, puede usar Azure AD o AD FS.
- Cuando se implementa en modo desconectado, sin una conexión a Internet, solo se admite AD FS.

Para más información sobre estas opciones, que dependen de su entorno de Azure Stack Hub, consulte los siguientes artículos:

- Kit de implementación de Azure Stack Hub: [Consideraciones de identidad](azure-stack-datacenter-integration.md#identity-considerations).
- Sistemas integrados de Azure Stack Hub: [decisiones sobre el planeamiento de una implementación en sistemas integrados de Azure Stack Hub](azure-stack-connection-models.md).

## <a name="common-concepts-for-identity-providers"></a>Conceptos comunes sobre los proveedores de identidades

En las secciones siguientes se tratan conceptos comunes sobre proveedores de identidades y su uso en Azure Stack Hub.

![Terminología de proveedores de identidades](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Organizaciones e inquilinos de directorios

Un directorio es un contenedor que alberga información sobre *usuarios*, *aplicaciones*, *grupos* y *entidades de servicio*.

Un inquilino de directorio es una *organización*, como Microsoft o su propia compañía.

- Azure AD admite varios inquilinos y puede admitir varias organizaciones, cada uno en su propio directorio. Si usa Azure AD y tiene varios inquilinos, puede conceder a las aplicaciones y los usuarios de un inquilino acceso a otros inquilinos de ese mismo directorio.
- AD FS solo admite un inquilino y, por lo tanto, una sola organización.

### <a name="users-and-groups"></a>Usuarios y grupos

Las cuentas de usuario (identidades) son cuentas estándar que autentican a los usuarios mediante un identificador de usuario y una contraseña. Los grupos pueden incluir usuarios u otros grupos.

El modo en que cree y administre usuarios y grupos depende de la solución de identidad que use.

En Azure Stack Hub, las cuentas de usuario:

- Se crean con el formato *nombre de usuario\@dominio*. Aunque AD FS asigna las cuentas de usuario a una instancia de Active Directory, AD FS no admite el uso del formato *\\\<dominio>\\\<alias>* .
- Se pueden configurar para usar autenticación multifactor.
- Están restringidas al directorio donde primero se registran, que es el directorio de la organización.
- Se pueden importar desde los directorios locales. Para más información, consulte [Integración de los directorios locales con Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Al iniciar sesión en el portal de usuarios de su organización, use la dirección URL *https:\//portal.local.azurestack.external*. Al iniciar sesión en el portal de Azure Stack Hub desde dominios que no sean el utilizado para registrar Azure Stack Hub, el nombre de dominio usado para registrar Azure Stack Hub se debe anexar a la dirección URL del portal. Por ejemplo, si Azure Stack Hub se ha registrado en fabrikam.onmicrosoft.com y el registro de la cuenta de usuario es admin@contoso.com, la dirección URL que se usa para iniciar sesión en el portal de usuarios sería: https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Usuarios invitados

Los usuarios invitados son cuentas de usuario de otros inquilinos de directorio a los que se les ha concedido acceso a recursos de su directorio. Para admitir usuarios invitados, use Azure AD y habilite la compatibilidad multiinquilino. Una vez habilitada la compatibilidad, puede invitar a usuarios invitados a acceder a los recursos de su inquilino de directorio, lo que permite la colaboración con organizaciones externas.

Para invitar a usuarios invitados, los operadores y usuarios de nube pueden usar la [colaboración B2B de Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Los usuarios invitados obtienen acceso a documentos, recursos y aplicaciones de su directorio, y usted mantiene el control sobre sus propios recursos y datos.

Como usuario invitado, puede iniciar sesión en otro inquilino de directorio de otras organizaciones. Para ello, debe anexar el nombre de directorio de esas organizaciones a la dirección URL del portal. Por ejemplo, si pertenece a la organización Contoso y quiere iniciar sesión en el directorio de Fabrikam, debe usar https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="apps"></a>Aplicaciones

Puede registrar aplicaciones en Azure AD o AD FS y luego ofrecérselas a los usuarios de su organización.

Las aplicaciones incluyen:

- **Aplicaciones web**: algunos ejemplos son Azure Portal y Azure Resource Manager. Estas aplicaciones admiten llamadas API web.
- **Cliente nativo**: algunos ejemplos son Azure PowerShell, Visual Studio y la CLI de Azure.

Las aplicaciones pueden admitir dos tipos de inquilinos:

- **Inquilino único**: admiten usuarios y servicios solo del mismo directorio donde está registrada la aplicación.

  > [!NOTE]
  > Dado que AD FS solo admite un único directorio, las aplicaciones que se crean en una topología de AD FS son, por naturaleza, de un único inquilino.

- **Multiinquilino**: los pueden usar los usuarios y servicios tanto del directorio donde la aplicación está registrada como de otros directorios de inquilino. Con las aplicaciones multiinquilino, los usuarios de otro directorio de inquilino (otro inquilino de Azure AD) pueden iniciar sesión en la aplicación.

  Para más información sobre la arquitectura multiinquilino, consulte [Habilitación de servicios multiinquilino](azure-stack-enable-multitenancy.md).

  Para más información sobre cómo desarrollar una aplicación multiinquilino, consulte [Aplicaciones multiinquilino](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Al registrar una aplicación, se crean dos objetos:

- **Objeto de aplicación**: representación global de la aplicación en todos los inquilinos. Esta relación es de uno a uno con la aplicación de software y solo existe en el directorio donde la aplicación se registra primero.

- **Objeto de entidad de servicio**: credencial que se crea para una aplicación en el directorio en que la aplicación se registró por primera vez. Una entidad de servicio también se crea en el directorio de cada inquilino adicional donde se usa esa aplicación. Esta relación puede ser de uno a varios con la aplicación de software.

Para más información sobre los objetos de aplicación y de entidad de servicio, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory (Azure AD)](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Entidades de servicio

Una entidad de servicio es un conjunto de *credenciales* para una aplicación o servicio que conceden acceso a los recursos de Azure Stack Hub. El uso de una entidad de servicio separa los permisos de aplicación de los permisos del usuario de la aplicación.

Una entidad de servicio se crea en cada inquilino donde se usa la aplicación. La entidad de servicio establece una identidad para el inicio de sesión y el acceso a los recursos (por ejemplo, los usuarios) que están protegidos por ese inquilino.

- Una aplicación de inquilino único tiene solo una entidad de servicio, en el directorio donde se crea la primera vez. Esta entidad de servicio se crea y permite que se use durante el registro de la aplicación.
- Una API o aplicación web multiinquilino tiene una entidad de servicio que se crea en cada inquilino donde un usuario de ese inquilino permite el uso de la aplicación.

Las credenciales de las entidades de servicio pueden ser una clave generada mediante Azure Portal o un certificado. El uso de un certificado es adecuado para la automatización porque los certificados se consideran más seguros que las claves.

> [!NOTE]
> Cuando se usa AD FS con Azure Stack Hub, solo el administrador puede crear entidades de servicio. Con AD FS, las entidades de servicio requieren certificados y se crean mediante el punto de conexión con privilegios (PEP). Para más información, consulte [Uso de una identidad de aplicación para acceder a recursos](azure-stack-create-service-principals.md).

Para más información sobre las entidades de servicio de Azure Stack Hub, consulte [Creación de entidades de servicio](azure-stack-create-service-principals.md).

### <a name="services"></a>Servicios

Los servicios de Azure Stack Hub que interactúan con el proveedor de identidades se registran en él como aplicaciones. Al igual que las aplicaciones, el registro permite que un servicio se autentique con el sistema de identidad.

Todos los servicios de Azure usan protocolos [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) y tokens [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) para establecer su identidad. Debido a que Azure AD y AD FS usan los protocolos de forma coherente, puede usar la [Biblioteca de autenticación de Active Directory](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) de Azure para autenticarse en el entorno local o en Azure (en un escenario conectado). ADAL también permite el uso de herramientas como Azure PowerShell y la CLI de Azure para la administración de recursos locales y entre nubes.

### <a name="identities-and-your-identity-system"></a>Identidades y el sistema de identidad

Las identidades de Azure Stack Hub incluyen cuentas de usuario, grupos y entidades de servicio.

Cuando se instala Azure Stack Hub, varias aplicaciones y servicios integrados se registran automáticamente en el proveedor de identidades del inquilino de directorio. Algunos servicios que se registran se usan para la administración. Otros servicios están disponibles para los usuarios. Los registros predeterminados proporcionan identidades de servicio básicas que pueden interactuar unas con otras, y con identidades que se agreguen posteriormente.

Si configura Azure AD con servicios multiinquilino, algunas aplicaciones se propagan a los nuevos directorios.

## <a name="authentication-and-authorization"></a>Autenticación y autorización

### <a name="authentication-by-apps-and-users"></a>Autenticación de usuarios y aplicaciones

![Identidad entre capas de Azure Stack Hub](media/azure-stack-identity-overview/identity-layers.png)

En el caso de aplicaciones y usuarios, la arquitectura de Azure Stack Hub se define en cuatro capas. Las interacciones entre cada una de estas capas pueden usar diferentes tipos de autenticación.

|Nivel    |Autenticación entre capas  |
|---------|---------|
|Herramientas y clientes, como el portal de administración     | Para acceder a un recurso de Azure Stack Hub o modificarlo, las herramientas y los clientes usan un [token web JSON](/azure/active-directory/develop/active-directory-token-and-claims) para realizar una llamada a Azure Resource Manager. <br>Azure Resource Manager valida el token web JSON e inspecciona las *notificaciones* del token emitido para estimar el nivel de autorización que el usuario o la entidad de servicio tienen en Azure Stack Hub. |
|Azure Resource Manager y sus servicios principales     |Azure Resource Manager se comunica con los proveedores de recursos para transferir la comunicación de los usuarios. <br> Las transferencias usan llamadas *imperativas directas* o llamadas *declarativas* mediante [plantillas de Azure Resource Manager](/azure-stack/user/azure-stack-arm-templates).|
|Proveedores de recursos     |Las llamadas pasadas a los proveedores de recursos están protegidas con autenticación basada en certificados. <br>Azure Resource Manager y el proveedor de recursos permanecen en comunicación mediante una API. El proveedor de recursos utiliza ese certificado para validar cada llamada recibida que se recibe de Azure Resource Manager.|
|Infraestructura y lógica de negocios     |Los proveedores de recursos se comunican con la lógica de negocios y la infraestructura mediante el modo de autenticación de su elección. Los proveedores de recursos predeterminados que se envían con Azure Stack Hub usan la autenticación de Windows para proteger esta comunicación.|

![Información necesaria para la autenticación](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Autenticarse en Azure Resource Manager

Para autenticarse con el proveedor de identidades y recibir un token JWT, debe tener la siguiente información:

1. **Dirección URL del sistema de identidad (autoridad)** : dirección URL a través de la que se puede acceder al proveedor de identidades. Por ejemplo, *https:\//login.windows.net*.
2. **Identificador URI del identificador de aplicación de Azure Resource Manager**: el identificador único de Azure Resource Manager que se registra en el proveedor de identidades. También es único para cada instalación de Azure Stack Hub.
3. **Credenciales**: la credencial que se usa para autenticarse con el proveedor de identidades.
4. **Dirección URL de Azure Resource Manager**: la dirección URL es la ubicación del servicio Azure Resource Manager. Por ejemplo, *https:\//management.azure.com* o *https:\//management.local.azurestack.external*.

Cuando una entidad de seguridad (cliente, aplicación o usuario) realiza una solicitud de autenticación para acceder a un recurso, esa solicitud debe incluir:

- Las credenciales de la entidad de seguridad.
- El URI de identificador de aplicación del recurso al que la entidad de seguridad desea acceder.

Las credenciales se validan mediante el proveedor de identidades. El proveedor de identidades también valida que el URI del identificador de aplicación sea para una aplicación registrada y que la entidad de seguridad tenga los privilegios correctos para obtener un token para ese recurso. Si la solicitud es válida, se concede un JSON Web Token.

El token debe pasar entonces el encabezado de una solicitud a Azure Resource Manager. Azure Resource Manager realiza lo siguiente, sin un orden específico:

- Valida la notificación *issuer* (iss) para confirmar que el token procede del proveedor de identidades correcto.
- Valida la notificación *audience* (aud) para confirmar que el token se emitió a Azure Resource Manager.
- Valida que el token JWT esté firmado con un certificado que se haya configurado mediante OpenID y que sea conocido para Azure Resource Manager.
- Revisa las notificaciones *issued at* (iat) y *expiry* (exp) para confirmar que el token está activo y se puede aceptar.

Una vez realizadas todas las validaciones, Azure Resource Manager usa las notificaciones *object id* (oid) y *groups* para crear una lista de los recursos a los que puede acceder la entidad de seguridad.

![Diagrama del protocolo de intercambio de tokens](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Después de la implementación, no se necesita el permiso de administrador global de Azure Active Directory. Sin embargo, algunas operaciones pueden requerir las credenciales de administrador global (por ejemplo, un script del instalador del proveedor de recursos o una nueva característica que necesita la concesión de un permiso). Puede restablecer temporalmente los permisos de administrador global de la cuenta o usar una cuenta de administrador global independiente que sea propietaria de la *suscripción del proveedor predeterminada*.

### <a name="use-role-based-access-control"></a>Uso del control de acceso basado en rol

El control de acceso basado en rol (RBAC) de Azure Stack Hub es coherente con la implementación de Microsoft Azure. Puede administrar el acceso a los recursos mediante la asignación del rol de RBAC adecuado a los usuarios, los grupos y las aplicaciones. Para más información sobre cómo usar RBAC con Azure Stack Hub, consulte los artículos siguientes:

- [Introducción al control de acceso basado en roles en Azure Portal](/azure/role-based-access-control/overview)
- [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](/azure/role-based-access-control/role-assignments-portal)
- [Creación de roles personalizados para el control de acceso basado en roles de Azure](/azure/role-based-access-control/custom-roles)
- [Administración del control de acceso basado en rol](azure-stack-manage-permissions.md) en Azure Stack Hub.

### <a name="authenticate-with-azure-powershell"></a>Autenticación con Azure PowerShell

Para más información sobre el uso de Azure PowerShell para autenticarse con Azure Stack Hub, consulte [Configuración del entorno de PowerShell del usuario de Azure Stack Hub](../user/azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Autenticación con la CLI de Azure

Para más información sobre cómo usar Azure PowerShell para autenticarse con Azure Stack Hub, consulte [Instalación y configuración de la CLI de Azure para su uso con Azure Stack Hub](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="next-steps"></a>Pasos siguientes

- [Arquitectura de identidad](azure-stack-identity-architecture.md)
- [Integración del centro de datos: identidad](azure-stack-integrate-identity.md)
