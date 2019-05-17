---
title: ¿Qué es Azure Stack? | Microsoft Docs
description: Obtenga información sobre cómo Azure Stack le permite ejecutar servicios de Azure en su centro de datos.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: f8206c658170a16e517e64a328d188c015b9e394
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618069"
---
# <a name="azure-stack-overview"></a>Introducción a Azure Stack

Azure Stack es una extensión de Azure que ofrece una manera de ejecutar aplicaciones en un entorno local y proporcionar servicios de Azure en su centro de datos. Con una plataforma de nube coherente, las organizaciones pueden tomar decisiones de tecnología con confianza basándose en los requisitos empresariales, en lugar de tomar decisiones empresariales basándose en las limitaciones de la tecnología.

## <a name="why-use-azure-stack"></a>¿Por qué usar Azure Stack?

Azure proporciona una plataforma completa en la que los desarrolladores pueden compilar aplicaciones modernas. Sin embargo, algunas aplicaciones basadas en la nube se enfrentan a obstáculos como la latencia, una conectividad intermitente y las normativas. Azure y Azure Stack desbloquean nuevos casos de uso de nube híbrida para aplicaciones de línea de negocios que están orientadas al cliente e internas:

- **Soluciones perimetrales y desconectadas**. Para satisfacer los requisitos de latencia y conectividad, procese los datos en el entorno local en Azure Stack y agréguelos después a Azure para analizarlos, con lógica de aplicaciones común entre ambos. Incluso puede implementar Azure Stack desconectado de Internet sin conectividad a Azure. Piense, por ejemplo, en plantas de producción, cruceros o pozos mineros.

- **Aplicaciones en la nube que cumplen diversas regulaciones**. Desarrolle e implemente aplicaciones en Azure, con total flexibilidad de implementación en el entorno local de Azure Stack para satisfacer requisitos de cumplimiento normativo o de directivas, sin necesidad de realizar cambios de código. Algunos ejemplos de aplicación son la auditoría global, los informes financieros, las operaciones de cambio de divisas, los juegos en línea y los informes de gastos.

- **Modelo de aplicación en la nube en el entorno local**. Use las arquitecturas de servicios, contenedores, sin servidor y microservicio de Azure para actualizar y ampliar las aplicaciones existentes o crear otras nuevas. Utilice procesos de DevOps coherentes en Azure en la nube y Azure Stack en el entorno local para acelerar la modernización de aplicaciones críticas fundamentales.

Azure Stack habilita estos escenarios de uso al proporcionar:

- Una experiencia de entrega integrada para ponerse en marcha rápidamente con sistemas integrados de Azure Stack específicos de nuestros asociados de hardware. Tras la entrega, Azure Stack se integra fácilmente en el centro de datos con supervisión mediante el paquete de administración de System Center Operations Manager o la extensión de Nagios.

- Administración de identidades flexible con Azure Active Directory (Azure AD) para entornos híbridos de Azure y Azure Stack, aprovechando Servicios de federación de Active Directory (AD FS) para implementaciones desconectadas. 

- Un entorno de desarrollo de aplicaciones coherente con Azure para maximizar la productividad de los desarrolladores y habilitar estrategias de DevOps comunes en entornos híbridos.

- Entrega de servicios de Azure desde el entorno local mediante capacidad informática de nube híbrida. Adopte prácticas operativas comunes en Azure y Azure Stack para implementar y utilizar servicios de Azure IaaS y PaaS con las mismas experiencias administrativas y herramientas que Azure. Microsoft traslada a Azure Stack la innovación continua de Azure, incluidos nuevos servicios de Azure, actualizaciones de servicios existentes y otras aplicaciones e imágenes de Azure Marketplace.

## <a name="azure-stack-architecture"></a>Arquitectura de Azure Stack
Los sistemas integrados de Azure Stack se conforman en bastidores de entre 4 y 16 servidores creados por asociados de hardware de confianza y se entregan directamente al centro de datos. Tras la entrega, un proveedor de soluciones trabajará con usted para implementar el sistema integrado y garantizar que la solución de Azure Stack cumple sus requisitos empresariales. Deberá preparar el centro de datos asegurándose de que todo lo relacionado con la alimentación y la refrigeración, la conectividad de borde y el resto de requisitos de integración del centro de datos está en vigor. 

> Para más información acerca de la experiencia de integración del centro de datos de Azure Stack, consulte [Integración del centro de datos de Azure Stack](azure-stack-customer-journey.md).

Azure Stack se basa en hardware estándar del sector y se administra mediante las mismas herramientas que ya usa para administrar las suscripciones de Azure. Como resultado, puede aplicar procesos de DevOps coherentes tanto si está conectado a Azure como si no. 

La arquitectura de Azure Stack le permite proporcionar servicios de Azure en el límite de ubicaciones remotas o con conectividad intermitente, desconectado de Internet. Puede crear soluciones híbridas que procesen datos localmente en Azure Stack y, a continuación, agregarlos a Azure para un análisis y procesamiento adicionales. Por último, dado que Azure Stack se instala de forma local, puede cumplir requisitos específicos normativos o de directivas con la flexibilidad de implementar la aplicación de nube en el entorno local sin necesidad de cambiar el código. 

## <a name="deployment-options"></a>Opciones de implementación

### <a name="production-or-evaluation-environments"></a>Entornos de producción o evaluación
Azure Stack se ofrece con dos opciones de implementación para satisfacer sus necesidades: sistemas integrados de Azure Stack para su uso en producción y el Kit de desarrollo de Azure Stack (ASDK) para evaluar Azure Stack:

- **Sistemas integrados de Azure Stack**. Los sistemas integrados de Azure Stack se ofrecen a través de una asociación de Microsoft y sus asociados de hardware, lo que crea una solución que ofrece innovación dirigida a la nube y simplicidad en la administración de la computación. Dado que Azure Stack se ofrece como un sistema en el que el software y el hardware están perfectamente integrados, tendrá la flexibilidad y el control que necesita, así como la capacidad de innovar desde la nube. El tamaño de los sistemas integrados de Azure Stack va de los 4 a los 16 nodos y tiene un soporte técnico conjunto por parte de un asociado de hardware y Microsoft. Use los sistemas integrados de Azure Stack para crear nuevos escenarios e implementar nuevas soluciones para las cargas de trabajo de producción.

- **Kit de desarrollo de Azure Stack**. El [Kit de desarrollo de Azure Stack (ASDK)](../asdk/asdk-what-is.md) es una implementación gratuita en un solo nodo de Azure Stack que puede usar para evaluar y conocer Azure Stack. También lo puede usar como entorno de desarrollo para compilar aplicaciones con API y herramientas coherentes con Azure. Sin embargo, el Kit de desarrollo de Azure Stack no está pensado para usarse como un entorno de producción y tiene las siguientes limitaciones en comparación con la implementación completa de producción de sistemas integrados:

    - El Kit de desarrollo de Azure Stack solo puede asociarse a un proveedor de identidades de Azure Active Directory (Azure AD) o de Servicios de federación de Active Directory (AD FS).
    - Como los componentes de Azure Stack se implementan en un único equipo host, hay un número limitado de recursos físicos para los recursos de los inquilinos. Esta configuración no está pensada para la evaluación del rendimiento ni para realizar un escalado.
    - Los escenarios de red están limitados porque hay un solo host y por los requisitos de implementación de NIC.

### <a name="connection-models"></a>Modelos de conexión
Puede implementar Azure Stack, ya sea **con conexión** a Internet (y a Azure) o **sin conexión**. Esto define las opciones que están disponibles tanto para el almacén de identidades (Azure Active Directory o Servicios de federación de Active Directory) como para el modelo de facturación (facturación basada en pago por uso o facturación por capacidad).

> Para más información, consulte las consideraciones para modelos de implementación [con conexión](azure-stack-connected-deployment.md) y [sin conexión](azure-stack-disconnected-deployment.md). 

### <a name="identity-provider"></a>Proveedor de identidades 
Azure Stack usa Azure Active Directory (Azure AD) o los Servicios de federación de Active Directory (AD FS) como proveedor de identidades para establecer identidades de Azure Stack. 

> [!IMPORTANT]
> Este es un punto clave en las decisiones. La elección entre Azure AD o AD FS como proveedor de identidades es una decisión puntual que debe realizar en el momento de la implementación. Para cambiarlo posteriormente es preciso volver a implementar todo el sistema.

Azure AD es el proveedor de identidades multiinquilino basado en la nube de Microsoft. La mayoría de los escenarios híbridos con implementaciones con conexión a Internet usan Azure AD como almacén de identidades. No obstante, puede elegir usar los Servicios de federación de Active Directory (AD FS) para las implementaciones desconectadas de Azure Stack. Los proveedores de recursos de Azure Stack y otras aplicaciones funcionan con AD FS de manera muy similar a como lo hacen con Azure AD. Azure Stack incluye su propia instancia de Active Directory, así como una instancia de Graph API de Active Directory. 

> Puede obtener más información acerca de las consideraciones de identidad de Azure Stack en [Información general sobre identidades en Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>¿Cómo se administra Azure Stack?
Después de implementar Azure Stack en una implementación de sistemas integrados o en una instalación de ASDK, los principales métodos para interactuar con Azure Stack son el portal de administración, el portal de usuarios y PowerShell. Cada uno de los portales de Azure Stack cuenta con el respaldo de instancias independientes de Azure Resource Manager. Un **operador de Azure Stack** usa el portal de administración para administrar Azure Stack y para hacer cosas como crear ofertas para inquilinos y mantener y supervisar el estado del sistema integrado. El portal de usuarios (conocido también como "portal de inquilinos") proporciona una experiencia de autoservicio para el consumo de recursos de nube, como máquinas virtuales, cuentas de almacenamiento y Web Apps. 

> Para más información acerca de cómo administrar Azure Stack mediante el portal de administración, consulte el [inicio rápido del portal de administración de Azure Stack](azure-stack-manage-portals.md).

Como operador de Azure Stack, puede ofrecer una amplia variedad de servicios y aplicaciones, como [máquinas virtuales](azure-stack-tutorial-tenant-vm.md), [aplicaciones web](azure-stack-app-service-overview.md), [SQL Server](azure-stack-tutorial-sql.md) de alta disponibilidad y bases de datos de [MySQL Server](azure-stack-tutorial-mysql.md). También puede usar [plantillas de Azure Resource Manager de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) para implementar SharePoint, Exchange y mucho más. 

Mediante el portal de administración, puede [configurar Azure Stack para prestar servicios](azure-stack-plan-offer-quota-overview.md) a inquilinos mediante planes, ofertas, cuotas y suscripciones. Los usuarios inquilinos pueden suscribirse a varias ofertas. Las ofertas pueden tener uno o varios planes, y los planes pueden tener uno o varios servicios. Los operadores también administran la capacidad y responden a las alertas. 

Si Azure Stack está configurado, un **usuario de Azure Stack** (también llamado inquilino) consume los servicios que el operador ofrece. Los inquilinos pueden aprovisionar, supervisar y administrar los servicios a los que se han suscrito, como Web Apps, Storage y Virtual Machines.

> Para más información acerca de cómo administrar Azure Stack, como dónde usar qué cuentas, las responsabilidades típicas del operador, qué decir a los usuarios y cómo obtener ayuda, revise los [aspectos básicos de administración de Azure Stack](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Proveedores de recursos 
Los proveedores de recursos son servicios web que forman la base de todos los servicios IaaS y PaaS de Azure Stack. Azure Resource Manager utiliza distintos proveedores de recursos para proporcionar acceso a los servicios. Cada uno de estos proveedores de recursos le ayuda a configurar y controlar sus respectivos recursos. Los administradores de servicios también pueden agregar nuevos proveedores de recursos personalizados. 

### <a name="foundational-resource-providers"></a>Proveedores de recursos fundamentales 
Hay tres proveedores de recursos de IaaS fundamentales: Proceso, red y almacenamiento:

- **Proceso**. El proveedor de recursos de proceso permite a los inquilinos de Azure Stack crear sus propias máquinas virtuales. El proveedor de recursos de proceso incluye la capacidad de crear tanto máquinas virtuales como extensiones de máquina virtual. El servicio de extensión de Máquina virtual ayuda a proporcionar capacidades de IaaS a las máquinas virtuales Windows y Linux.  Por ejemplo, puede usar el proveedor de recursos de proceso para aprovisionar una máquina virtual Linux y ejecutar scripts de Bash durante la implementación para configurar la máquina virtual.
- **Proveedor de recursos de red**. El proveedor de recursos de red ofrece una serie de características de Redes definidas por software (SDN) y Virtualización de función de red (NFV) a la nube privada. El proveedor de recursos de red puede usarse para crear recursos, como equilibradores de carga de software, direcciones IP públicas, grupos de seguridad de red y redes virtuales.
- **Proveedor de recursos de almacenamiento**. El proveedor de recursos de almacenamiento ofrece cuatro servicios de almacenamiento coherentes con Azure: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [cola](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabla](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)y administración de cuentas de KeyVault, que proporciona administración y auditoría de secretos, como contraseñas y certificados. También ofrece un servicio de administración del almacenamiento en la nube para facilitar la administración del proveedor de los servicios de almacenamiento coherentes con Azure. Azure Storage proporciona la flexibilidad necesaria para almacenar y recuperar grandes cantidades de datos no estructurados, como documentos y archivos multimedia con Blobs de Azure y datos estructurados basados en NoSQL con Tablas de Azure. 

### <a name="optional-resource-providers"></a>Proveedores de recursos opcionales
Hay tres proveedores de recursos de PaaS opcionales que puede implementar y usar con Azure Stack: Proveedores de recursos de App Service, SQL Server y MySQL Server:

- **App Service**. [Azure App Service en Azure Stack](azure-stack-app-service-overview.md) es una oferta de plataforma como servicio (PaaS) de Microsoft Azure disponible en Azure Stack. El servicio habilita a sus clientes internos o externos para crear aplicaciones de Azure Functions, API y web para cualquier plataforma o dispositivo. 
- **SQL Server**. Use el [proveedor de recursos de SQL Server](azure-stack-sql-resource-provider.md) para ofrecer las bases de datos de SQL como un servicio de Azure Stack. Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear bases de datos para aplicaciones nativas en la nube, sitios web que usan SQL y otras cargas de trabajo que usan SQL.
- **MySQL Server**. Use el [proveedor de recursos de MySQL Server](azure-stack-mysql-resource-provider-deploy.md) para ofrecer las bases de datos de MySQL como un servicio de Azure Stack. El proveedor de recursos MySQL se ejecuta como un servicio en una máquina virtual (VM) Server Core de Windows Server 2016.

## <a name="providing-high-availability"></a>Provisión de alta disponibilidad
Para conseguir la alta disponibilidad de un sistema de producción con varias VM en Azure, las VM se colocan en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) que las distribuye a varios dominios de error y dominios de actualización. En la escala más pequeña de Azure Stack, un dominio de error en un conjunto de disponibilidad se define como un único nodo en la unidad de escalado.  

Si bien la infraestructura de Azure Stack ya es resistente ante errores, la tecnología subyacente (clústeres de conmutación por error) de todos modos tiene cierto tiempo de inactividad de las máquinas virtuales en un servidor físico si se produce un error de hardware. Azure Stack admite un conjunto de disponibilidad con un máximo de tres dominios de error para coherencia con Azure.

- **Dominios de error**. Las máquinas virtuales colocadas en conjuntos de disponibilidad se aislarán físicamente entre sí al distribuirlas de la manera más uniforme que sea posible en varios dominios de error (nodos de Azure Stack). Si se produce un error de hardware, las máquinas virtuales del dominio de error que presente el error se reiniciarán en otros dominios de error pero, si es posible, se mantendrán en dominios de error independientes de las otras máquinas virtuales que se encuentran en el mismo conjunto de disponibilidad. Cuando el hardware vuelva a estar en línea, las máquinas virtuales se volverán a equilibrar para mantener la alta disponibilidad. 
 
- **Dominios de actualización**. Los dominios de actualización son otro concepto de Azure que proporciona alta disponibilidad en los conjuntos de disponibilidad. Un dominio de actualización es un grupo lógico de hardware adyacente que puede someterse a mantenimiento al mismo tiempo. Las máquinas virtuales que se encuentran en el mismo dominio de actualización se reiniciarán en conjunto durante el mantenimiento planeado. Cuando los inquilinos crean máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Azure las distribuye de manera automática entre estos dominios de actualización. En Azure Stack, las máquinas virtuales se migran en vivo entre los otros hosts en línea del clúster antes de que se actualice su host subyacente. Como no hay tiempo de inactividad para el inquilino durante una actualización del host, la característica de dominio de actualización de Azure Stack solo existe para compatibilidad de plantilla con Azure. 

## <a name="role-based-access-control"></a>Control de acceso basado en rol
El control de acceso basado en rol (RBAC) se puede usar para conceder acceso al sistema a usuarios, grupos y servicios autorizados asignándoles roles a nivel de suscripción, grupo de recursos o recurso individual. Cada rol define el nivel de acceso que un usuario, grupo o servicio tiene sobre los recursos de Microsoft Azure Stack.

RBAC de Azure Stack cuenta con tres roles básicos que se aplican a todos los tipos de recurso: propietario, colaborador y lector. El propietario tiene acceso completo a todos los recursos y cuenta con el derecho a delegar acceso a otros. El colaborador puede crear y administrar todos los tipos de recursos de Azure, pero no puede conceder acceso a otros. El lector solo puede ver los recursos existentes. El resto de los roles RBAC permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite crear y administrar máquinas virtuales, pero no permite administrar la red virtual ni la subred a las que se conecta la máquina virtual.

> Para más información, consulte [Administración del control de acceso basado en rol](azure-stack-manage-permissions.md). 

## <a name="reporting-usage-data"></a>Creación de informes de datos de uso
Microsoft Azure Stack recopila y agrega los datos de uso de todos los proveedores de recursos y los transmite a Azure para su procesamiento por parte de Comercio de Azure. Los datos de uso recopilados en Azure Stack pueden verse a través de una API de REST. Hay una API de inquilino coherente con Azure coherente, así como API de proveedor y de proveedor delegado para obtener datos de uso de todas las suscripciones del inquilino. Estos datos se pueden utilizar para realizar la integración con una herramienta o servicio externos con fines de facturación o contracargo. Una vez que Comercio de Azure ha procesado el uso, puede verse en el portal de facturación de Azure.

> Obtenga más información sobre la [creación de informes de datos de uso de Azure Stack en Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Pasos siguientes

[Comparación de Azure Stack y Azure global](compare-azure-azure-stack.md)

[Conceptos básicos de administración](azure-stack-manage-basics.md)

[Inicio rápido: Uso del portal de administración de Azure Stack](azure-stack-manage-portals.md)