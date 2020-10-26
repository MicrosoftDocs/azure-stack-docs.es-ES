---
title: Introducción al Centro de datos modular
description: El Centro de datos modular es un centro de datos portátil y fácil de implementar, adecuado para atender operaciones de combate a gran escala en puestos de mando temporales y fijos.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 01/13/2020
ms.reviewer: prchint
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: d2803f1968be7a4a3e64f65f3d86a2dbb41aaf1c
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183543"
---
# <a name="modular-data-center-overview"></a>Introducción al Centro de datos modular 

El Centro de datos modular se basa en Azure Stack Hub. El Centro de datos modular es un centro de datos portátil y fácil de implementar, adecuado para atender operaciones de combate a gran escala en puestos de mando temporales y fijos.

Azure Stack Hub es una solución escalable horizontal y verticalmente que proporciona a entornos perimetrales funcionalidades de nube híbrida nativa y multiinquilina para servicios IaaS y PaaS, y que admite diversos escenarios modulares para puestos de mando temporales y fijos, y fuerzas expedicionarias. Azure Stack Hub es un dispositivo de hardware y software integrado que está disponible comercialmente con distintas capacidades basadas en el escalado mediante unidades de escalado de nodos, y que está disponible con extensiones, entre las que se incluyen configuraciones habilitadas para unidades de procesamiento gráfico (GPU) de uso general y almacenamiento externo ampliable.

## <a name="how-you-can-use-the-mdc"></a>Uso del Centro de datos modular

Azure Stack ofrece a los escenarios modulares perimetrales cuatro principios básicos que son coherentes con las funcionalidades de Azure, y que incluso las amplían. 

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Desarrollo y entrega de aplicaciones con un modelo DevOps común que incluye la simetría de API con Azure

La coherencia entre Azure y Azure Stack significa que las soluciones para frente se desarrollan una vez, se implementan para que admitan distintos casos de uso y se protegen y mantienen mediante un conjunto común de herramientas, como Azure Key Vault para la administración de claves y Azure Monitor para la supervisión y administración de recursos. Azure Stack puede funcionar con datos, aplicaciones y herramientas para DevOps locales, y con operaciones seguras como, por ejemplo, la administración de claves.

### <a name="deliver-azure-services-on-premises"></a>Entrega de servicios de Azure locales

Azure Stack se ejecuta en entornos de comunicaciones poco favorables (con problemas, congestionados o denegados), así como en entornos potentes de comunicaciones, y no depende de la conectividad a Azure para ejecutar aplicaciones críticas y permitir las operaciones locales. 

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Uso de la experiencia de entrega de software y hardware integrada

El perímetro modular requiere una gama de funcionalidades que proporcionan no solo servicios de proceso y almacenamiento básicos, sino también funcionalidades avanzadas de aprendizaje automático, inteligencia artificial y análisis. La capacidad para conectarse a su nube segura desde el perímetro modular cuando sea posible, o a la inversa, de operar de forma independiente de ella en entornos difíciles, es fundamental para proporcionar al frente de batalla acceso a los datos necesarios para tomar decisiones.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Mantenimiento del centro de datos seguro y disponible con las operaciones de seguridad en la nube híbrida

El diseño nativo en la nube de Azure Stack elimina las complejidades operativas de los entornos de virtualización tradicionales, ya que permite a los administradores elegir cuándo revisar y coordinar toda la operación a través del proceso de instalación y el tejido de administración de Azure Stack creado de forma nativa en el sistema.

La administración asistida remota de Microsoft o una oferta administrada por este para dispositivos de Azure Stack se puede habilitar proporcionando a los usuarios el acceso adecuado mediante RBAC para realizar acciones con el portal de administración, el punto de conexión de administración con privilegios o la interfaz de la línea de comandos. Esto permite que Microsoft realice todas las revisiones y demás actividades administrativas y de supervisión. Azure Stack se puede actualizar fácilmente mediante el portal de administración seguro o los comandos de la interfaz de la línea de comandos (CLI), los cuales aplican las actualizaciones de seguridad y de características de IaaS y PaaS para mantener la paridad comercial con Azure en redes locales o remotas, si procede. 

## <a name="benefits-of-using-the-mdc"></a>Ventajas del uso del Centro de datos modular

El Centro de datos modular admite un entorno coherente con Azure en entornos de comunicaciones desfavorables:
 - Centros de datos estáticos, modulares, que se pueden implementar rápidamente con Azure Cloud Services para potenciar grandes aplicaciones de análisis en centros de operaciones modulares.
 - Las ofertas de perímetro modular de Azure mantienen la coherencia entre la nube y el perímetro mediante un enfoque singular que admite recursos primitivos de IaaS como máquinas virtuales, almacenamiento y redes virtuales.
 - Azure Active Directory y compatibilidad con el control de acceso basado en rol
 - Interfaces administrativas comunes
 - Simetría de API y soporte técnico para las herramientas de DevOps de Microsoft, de terceros y de código abierto
 - Administración y supervisión mediante Azure Log Analytics y Azure Security Center
 - Traslade la agilidad de la informática en la nube a sus entornos local y perimetral una nube híbrida.<br>Puede:
     - Reutilizar el código y ejecutar aplicaciones nativas en la nube de forma coherente entre Azure y los entornos locales.
     - Ejecutar cargas de trabajo virtualizadas tradicionales con conexiones opcionales a los servicios de Azure.
     - Transferir datos a la nube o mantenerlos en su centro de datos soberano para mantener el cumplimiento.
     - Ejecutar cargas de trabajo virtualizadas o en contenedores, de aprendizaje automático y aceleradas mediante hardware, y todo ello con la inteligencia perimetral.

## <a name="next-steps"></a>Pasos siguientes

[Planeamiento de la capacidad de Azure Stack Hub](../operator/azure-stack-capacity-planning-overview.md)
