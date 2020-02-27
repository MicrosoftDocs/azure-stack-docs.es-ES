---
title: Decisiones de implementación conectada a Azure en sistemas integrados de Azure Stack Hub
description: Determine las decisiones sobre el planeamiento de las implementaciones conectadas a Azure de sistemas integrados de Azure Stack Hub, incluidas la facturación y la identidad.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: a4ab00ff732bcffd66d984757a279a3ad030b33d
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77510136"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Decisiones sobre planeamiento de una implementación conectada a Azure de sistemas integrados de Azure Stack Hub
Una vez que haya decidido [cómo va a integrar Azure Stack Hub en el entorno de nube híbrida](azure-stack-connection-models.md), puede concluir sus decisiones sobre la implementación de Azure Stack Hub.

Si implementa Azure Stack Hub conectado a Azure, significa que puede tener Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS) para el almacén de identidades. También puede elegir el modelo de facturación: pago por uso o según la capacidad. Una implementación conectada es la opción predeterminada porque permite a los clientes sacar el máximo partido de Azure Stack Hub, especialmente en los escenarios de nube híbrida que implican tanto a Azure como a Azure Stack Hub.

## <a name="choose-an-identity-store"></a>Elección del almacén de identidades
Con una implementación conectada, puede elegir entre Azure AD o AD FS para el almacén de identidades. En una implementación desconectada, sin conectividad a Internet, solo puede usar AD FS.

Su elección de almacén de identidades no repercute en las máquinas virtuales (VM) de inquilinos. Las máquinas virtuales del inquilino pueden elegir el almacén de identidades al que desean conectarse en función de cómo se vayan a configurar: Azure AD, Windows Server Active Directory unido a un dominio, grupo de trabajo, etc. Esto no está relacionado con la decisión del proveedor de identidad de Azure Stack Hub.

Por ejemplo, si implementa máquinas virtuales de inquilinos de IaaS a partir de Azure Stack Hub y desea unirse a un dominio de Active Directory corporativo y usar las cuentas desde allí, puede seguir haciéndolo. No se le pedirá que use el almacén de identidades de Azure AD que seleccione aquí para esas cuentas.

### <a name="azure-ad-identity-store"></a>Almacén de identidades de Azure AD
El uso de Azure AD para el almacén de identidades requiere dos cuentas de Azure AD: una de administrador global y otra de facturación. Estas cuentas pueden ser las mismas o diferentes. Aunque usar la misma cuenta de usuario podría ser más sencillo y útil si se tiene un número limitado de cuentas de Azure, las necesidades empresariales podrían sugerir que se empleen dos cuentas:

1. **Cuenta de administrador global** (solo necesaria para las implementaciones conectadas). Se trata de una cuenta de Azure que se usa para crear aplicaciones y entidades de servicio para servicios de infraestructura de Azure Stack Hub en Azure AD. Esta cuenta tiene que tener permisos de administrador de directorio en el directorio en el que se implementará el sistema de Azure Stack Hub. Se convertirá en el administrador global del "operador en la nube" del usuario de Azure AD y se usará para las siguientes tareas:

    - Para aprovisionar y delegar aplicaciones y entidades de servicio para todos los servicios de Azure Stack Hub que tienen que interactuar con Azure AD y Graph API.
    - Como cuenta del administrador del servicio. Esta cuenta es la propietaria de la suscripción de proveedor predeterminada (que puede cambiar más adelante). Puede iniciar sesión en el portal del administrador de Azure Stack Hub con esta cuenta y usarla para crear ofertas y planes, establecer las cuotas y realizar otras funciones administrativas en Azure Stack Hub.

2. **Cuenta de facturación** (necesaria tanto para las implementaciones conectadas como para las desconectadas). Esta cuenta de Azure se utiliza para establecer la relación de facturación entre el sistema integrado de Azure Stack Hub y el back-end de comercio de Azure. Se trata de la cuenta en la que se le facturará por los honorarios de Azure Stack Hub. Esta cuenta también se usará para ofertar elementos de Marketplace y otros escenarios híbridos.

### <a name="ad-fs-identity-store"></a>Almacén de identidades de AD FS
Elija esta opción si desea usar su propio almacén de identidades, como su entidad de Active Directory corporativa, para las cuentas de administrador de servicios.  

## <a name="choose-a-billing-model"></a>Elección de un modelo de facturación
Puede elegir el modelo de facturación de **pago por uso** o por **capacidad**. Las implementaciones del modelo de facturación de pago por uso deben ser capaces de informar del uso a través de una conexión a Azure al menos una vez cada 30 días. Por lo tanto, el modelo de facturación de pago por uso solo está disponible para implementaciones conectadas.  

### <a name="pay-as-you-use"></a>Pago por uso
Con el modelo de facturación de pago por uso, el uso se cobra en una suscripción de Azure. Solo se paga cuando se utilizan los servicios de Azure Stack Hub. Si este es el modelo que decide usar, necesitará una suscripción de Azure y el identificador de cuenta asociado a esa suscripción (por ejemplo, serviceadmin@contoso.onmicrosoft.com). Se admiten suscripciones CSL, EA y CSP. Los informes de uso se configuran durante el [registro de Azure Stack Hub](azure-stack-registration.md).

> [!NOTE]
> En la mayoría de los casos, los clientes de empresa utilizarán las suscripciones EA y los proveedores de servicios utilizarán las suscripciones CSP o CSL.

Si va a usar una suscripción CSP, repase la tabla siguiente para identificar cuál usar, ya que el enfoque correcto depende del escenario de CSP exacto:

|Escenario|Opciones de suscripción y de dominio|
|-----|-----|
|Es un **partner directo de CSP** o un **proveedor de CSP indirecto**, y usará Azure Stack Hub|Utilice una suscripción CSL (nivel de servicio común).<br>     or<br>Cree un inquilino de Azure AD con un nombre descriptivo en el Centro de partners. Por ejemplo, &lt;su organización > CSPAdmin con una suscripción a Azure CSP asociada a él.|
|Es un **revendedor de CSP indirecto** y usará Azure Stack Hub|Pida a su proveedor de CSP indirecto que cree un inquilino de Azure AD para su organización y una suscripción de Azure CSP asociada a él mediante el Centro de partners.|

### <a name="capacity-based-billing"></a>Facturación por capacidad
Si decide usar el modelo de facturación basado en la capacidad, tiene que adquirir un SKU del plan de capacidad de Azure Stack Hub que se base en la capacidad de su sistema. Tiene que saber el número de núcleos físicos en Azure Stack Hub para adquirir la cantidad correcta.

La facturación de la capacidad requiere una suscripción de Azure con Contrato Enterprise (EA) para el registro. El motivo es que el registro configura la disponibilidad de artículo en Marketplace, lo que requiere una suscripción a Azure. La suscripción no se utiliza para el uso de Azure Stack Hub.

## <a name="learn-more"></a>Más información
- Para obtener información acerca de los casos de uso, las compras, los asociados y los distribuidores de hardware de OEM, consulte la página del producto de [Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/).
- Para más información sobre la hoja de ruta y la disponibilidad geográfica de los sistemas integrados de Azure Stack Hub, consulte las notas del producto: [Azure Stack Hub: una extensión de Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Para más información acerca de los paquetes y precios de Microsoft Azure Stack Hub [descargue el archivo .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Pasos siguientes
[Integración de la red del centro de datos](azure-stack-network.md)