---
title: Introducción a los servicios, los planes, las ofertas y las suscripciones de Azure Stack Hub
description: Introducción a los servicios, los planes, las ofertas y las suscripciones de Azure Stack Hub.
author: BryanLa
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: 2ebbc1d7a2d6a159c4d35303c833875452c20c01
ms.sourcegitcommit: bd775dfb298ba1dc67ac9ac7d591794179151026
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2020
ms.locfileid: "85764602"
---
# <a name="azure-stack-hub-services-plans-offers-subscriptions-overview"></a>Introducción a los servicios, los planes, las ofertas y las suscripciones de Azure Stack Hub

[Microsoft Azure Stack Hub](azure-stack-overview.md) es una nueva plataforma de nube híbrida que le permite proporcionar servicios desde su centro de datos. Los servicios incluyen máquinas virtuales (VM), bases de datos de SQL Server, SharePoint, Exchange e incluso [elementos de Azure Marketplace](azure-stack-marketplace-azure-items.md). Como proveedor de servicios, puede ofrecer servicios a los inquilinos. Dentro de una empresa u organismo gubernamental, puede ofrecer servicios locales a los empleados.

## <a name="overview"></a>Información general

Como operador de Azure Stack Hub, debe configurar y entregar esos servicios por medio de ofertas, planes y suscripciones. Las ofertas contienen uno o varios planes y cada plan incluye uno o varios servicios, cada uno configurado con cuotas. Al crear planes y combinarlos en diferentes ofertas, los usuarios pueden suscribirse a sus ofertas e implementar recursos. Esta estructura le permite administrar:

- Los servicios y los recursos a los que pueden acceder los usuarios.
- La cantidad de recursos que los usuarios pueden consumir.
- Qué regiones tienen acceso a los recursos.

Para ofrecer un servicio, siga estos pasos de alto nivel:

1. Planee la oferta de servicio mediante lo siguiente:

   - Servicios fundamentales, como los de proceso, almacenamiento, redes o Key Vault.
   - Servicios de valor agregado como Event Hubs, App Service, SQL Server o MySQL Server.

2. Cree un plan compuesto por uno o más servicios. Al crear un plan, seleccione o cree cuotas que definan los límites de recursos de cada servicio en el plan.
3. Cree una oferta que tenga uno o varios planes. La oferta puede incluir planes base y planes complementarios opcionales.

Después de crear la oferta, los usuarios pueden suscribirse a ella para acceder a los servicios e implementar los recursos. Los usuarios pueden suscribirse a todas las ofertas que quieran. En la siguiente ilustración se muestra un ejemplo sencillo de un usuario que se ha suscrito a dos ofertas. Cada oferta tiene uno o dos planes, y cada plan les da acceso a servicios específicos.

![Suscripción de inquilinos con ofertas y planes](media/azure-stack-key-features/image4.png)

## <a name="services"></a>Servicios

Puede ofrecer servicios de [Infraestructura como servicio](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) que permiten a los usuarios crear una infraestructura de computación a petición, aprovisionada y administrada desde el portal de usuarios de Azure Stack Hub.

También puede implementar servicios de [Plataforma como servicio](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) para Azure Stack Hub de Microsoft y proveedores de terceros. Los servicios de PaaS que se pueden implementar incluyen, entre otros:

- [Event Hubs](event-hubs-rp-overview.md)
- [App Service](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [MySQL Server](azure-stack-mysql-resource-provider-deploy.md)

También puede combinar servicios para integrar y crear soluciones complejas para los distintos usuarios.

### <a name="quotas"></a>Cuotas

Para ayudarle a administrar la capacidad de la nube, puede usar *cuotas* configuradas previamente o crear una nueva para cada servicio de un plan. Las cuotas definen los límites de recursos superiores que puede aprovisionar o consumir una suscripción de usuario. Por ejemplo, una cuota podría permitir que un usuario creara hasta cinco máquinas virtuales.

> [!IMPORTANT]
> Pueden pasar hasta dos horas hasta que las nuevas cuotas estén disponibles en el portal del usuario o antes de que se aplique una cuota modificada.

Puede configurar cuotas por región. Por ejemplo, un plan que proporciona servicios de proceso para la región A puede tener una cuota de dos máquinas virtuales.

>[!NOTE]
>En el Kit de desarrollo de Azure Stack (ASDK), solo hay una región (denominada *local*) disponible.

Más información sobre los [tipos de cuota en Azure Stack Hub](azure-stack-quota-types.md).

## <a name="plans"></a>Planes

Los planes son agrupaciones de uno o varios servicios. Como operador de Azure Stack Hub, [creará planes](azure-stack-create-plan.md) para ofrecer a sus usuarios. A su vez, los usuarios se suscriben a las ofertas para usar los planes y los servicios que incluyen. Cuando cree los planes, asegúrese de establecer las cuotas, definir los planes base y considerar la inclusión de planes complementarios opcionales.

### <a name="base-plan"></a>Plan base

Al crear una oferta, el administrador de servicios puede incluir un plan base. Estos planes se incluyen de forma predeterminada cuando un usuario se suscribe a la oferta. Cuando un usuario se suscribe, tiene acceso a todos los proveedores de recursos especificados en dichos planes base (con las cuotas correspondientes).

### <a name="add-on-plans"></a>Planes complementarios

Los planes complementarios son planes opcionales que puede agregar a una oferta. Dichos planes no se incluyen de forma predeterminada en la suscripción. Los planes complementarios son planes adicionales (cuotas) disponibles en una oferta que un suscriptor puede agregar a sus suscripciones. Por ejemplo, puede ofrecer un plan base con recursos limitados para una versión de prueba y un plan complementario con recursos más abundantes para los clientes que decidan adoptar el servicio.

## <a name="offers"></a>Ofertas

Las ofertas son grupos de uno o varios planes que se crean para que los usuarios puedan suscribirse a ellas. Por ejemplo: La Oferta Alfa puede incluir el Plan A, que proporciona un conjunto de servicios Compute, y el Plan B, que ofrece un conjunto de servicios de red y almacenamiento.

Cuando se [crea una oferta](azure-stack-create-offer.md), debe incluir al menos un plan base, pero también puede crear planes complementarios que los usuarios pueden agregar a su suscripción.

Cuando planifique las ofertas, tenga en cuenta los puntos siguientes:

**Ofertas de prueba**: utilice las ofertas de prueba para atraer a usuarios nuevos, que luego pueden actualizar a servicios adicionales. Para crear una oferta de prueba, cree un pequeño [plan base](service-plan-offer-subscription-overview.md#base-plan) con un plan de complementos opcional mayor. Como alternativa, puede crear una oferta de evaluación compuesta por un plan base pequeño y una oferta independiente con un plan de "pago por uso" mayor.

**Planificación de capacidad**: es posible que le preocupe que los usuarios acaparen grandes cantidades de recursos y atasquen el sistema para todos los usuarios. Para mejorar el rendimiento, puede [configurar sus planes con cuotas](service-plan-offer-subscription-overview.md#plans) para limitar el uso.

**Proveedores delegados**: puede conceder a otros usuarios la capacidad de crear ofertas en su entorno. Por ejemplo, si es proveedor de servicios, puede [delegar](azure-stack-delegated-provider.md) esta funcionalidad en sus distribuidores. O bien, si es una organización, puede delegar en otras divisiones o subsidiarias.

## <a name="subscriptions"></a>Suscripciones

Las suscripciones permiten a los usuarios acceder a sus ofertas. Si es un operador de Azure Stack Hub de un proveedor de servicios, los usuarios (inquilinos) le compran sus servicios mediante la suscripción a sus ofertas. Si es un operador de Azure Stack Hub de una organización, los usuarios (empleados) pueden suscribirse a los servicios que les ofrece sin pagar.

Los usuarios crean otras suscripciones y obtienen acceso a las suscripciones existentes cuando inician sesión en Azure Stack Hub. Cada suscripción representa una asociación con una única oferta. La oferta (y sus planes y cuotas) asignada a una suscripción no se puede compartir con otras suscripciones. Cada recurso que crea un usuario está asociado a una suscripción.

Como operador de Azure Stack Hub, puede ver información sobre las suscripciones de inquilinos, pero no puede acceder al contenido de estas a menos que un administrador de inquilinos de esa suscripción le agregue explícitamente mediante el control de acceso basado en roles. Esto permite a los inquilinos aplicar la separación de poderes y responsabilidades entre el operador de Azure Stack Hub y los espacios de inquilino. 

La excepción a este caso es una situación en la que el propietario de la suscripción no puede proporcionar al operador acceso a la suscripción, lo que requiere que el administrador asuma la propiedad de la suscripción como se indica en [Cambio del propietario de facturación de las suscripciones de usuario de Azure Stack Hub](azure-stack-change-subscription-owner.md).

### <a name="default-provider-subscription"></a>Suscripción de proveedor predeterminada

La suscripción de proveedor predeterminada se crea automáticamente al implementar el ASDK. Esta suscripción puede usarse para administrar Azure Stack Hub, implementar proveedores de recursos adicionales y crear ofertas y planes para los usuarios. Por motivos de seguridad y concesión de licencias, no debe usarse para ejecutar aplicaciones y cargas de trabajo de cliente. No se puede cambiar la cuota de la suscripción de proveedor predeterminada.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la creación de planes, ofertas y suscripciones, comience con [Creación de un plan](azure-stack-create-plan.md).
