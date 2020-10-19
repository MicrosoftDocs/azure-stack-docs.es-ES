---
title: Introducción sobre el reflector de rutas del Protocolo de puerta de enlace de borde (BGP) en Azure Stack HCI
description: Use este tema para obtener información sobre el reflector de rutas de BGP para redes definidas por software (SDN) en Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: ca130c60e8cf08484001f606c4d0f84d786ca16b
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858989"
---
# <a name="what-is-route-reflector"></a>¿Qué es el reflector de rutas?

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

El reflector de rutas del Protocolo de puerta de enlace de borde (BGP), que se incluye con la [puerta de enlace de Servicio de acceso remoto (RAS)](gateway-overview.md), proporciona una alternativa a la topología de malla completa de BGP necesaria para la sincronización de rutas entre los enrutadores. Un reflector de rutas en una implementación de redes definidas por software es una entidad lógica que se encuentra en el plano de control entre las puertas de enlace de RAS y [Controladora de red](network-controller-overview.md). No obstante, no participa en el enrutamiento del plano de datos.

## <a name="how-route-reflector-works"></a>Funcionamiento del reflector de rutas

Con la sincronización de malla completa, todos los enrutadores de BGP deben conectarse con todos los demás enrutadores de la topología de enrutamiento. Sin embargo, cuando se usa el reflector de rutas, este es el único enrutador que se conecta con todos los demás enrutadores, denominados clientes del reflector de rutas de BGP, lo que simplifica la sincronización de rutas y reduce el tráfico de red. El reflector de rutas aprende todas las rutas, calcula las mejores rutas y redistribuye las mejores rutas a sus clientes de BGP.

Puede configurar los túneles de acceso remoto de un inquilino individual para que finalicen en más de una máquina virtual de puerta de enlace de RAS. Esto proporciona mayor flexibilidad a los proveedores de servicios en la nube en situaciones en las que una máquina virtual de puerta de enlace de RAS no puede cumplir todos los requisitos de ancho de banda de las conexiones de inquilino.

Sin embargo, esta funcionalidad presenta la complejidad adicional de la administración de rutas y la sincronización efectiva de estas entre los sitios remotos de inquilinos y sus recursos virtuales del centro de datos en la nube. El suministro de inquilinos con conexiones a varias puertas de enlace de RAS también introduce una complejidad adicional en la configuración en el lado de la empresa, donde cada sitio de inquilino tendrá vecinos de enrutamiento independientes.

Un reflector de rutas de BGP en el plano de control aborda estos problemas y hace que la implementación del tejido interno de CSP sea transparente para los inquilinos empresariales.

- Cuando se agrega un nuevo inquilino al centro de datos, Controladora de red configura automáticamente la primera puerta de enlace de RAS del inquilino como reflector de rutas.

- Cada inquilino tiene un reflector de rutas correspondiente y reside en una de las máquinas virtuales de puerta de enlace de RAS que están asociadas a ese inquilino.

- Un reflector de rutas de un inquilino actúa como reflector de rutas de todas las máquinas virtuales de puerta de enlace de RAS asociadas al inquilino. Las puertas de enlace de inquilinos que no son reflector de rutas de puerta de enlace de RAS son los clientes del reflector de rutas. El reflector de rutas realiza la sincronización de rutas entre todos sus clientes de forma que se pueda producir el enrutamiento de la ruta de acceso de datos real.

- Un reflector de rutas no proporciona servicios para la puerta de enlace de RAS en la que está configurada.

- Un reflector de rutas actualiza Controladora de red con las rutas empresariales que se corresponden con los sitios empresariales del inquilino. Esto permite que Controladora de red configure las directivas de virtualización de red de Hyper-V necesarias en la red virtual de inquilino para acceder a la ruta de acceso completa a los datos.

- Si los clientes de la empresa usan el enrutamiento BGP en el espacio de direcciones del cliente, el reflector de rutas de la puerta de enlace de RAS será el único vecino BGP externo (eBGP) para todos los sitios del inquilino correspondiente. Esto es así independientemente de los puntos de terminación de túnel del inquilino empresarial. En otras palabras, independientemente de la máquina virtual de puerta de enlace de RAS del centro de datos del CSP que termine el túnel de VPN de sitio a sitio para un sitio de inquilino, el eBGP del mismo nivel para todos los sitios de inquilino será el reflector de rutas.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Introducción a la puerta de enlace de RAS](gateway-overview.md)
- [Arquitectura de implementación de la puerta de enlace de RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Solicitud de comentarios del Grupo de trabajo de ingeniería de Internet (IETF) tema RFC 4456 sobre el reflector de rutas del BGP: una alternativa al protocolo BGP interno de malla completa](https://tools.ietf.org/html/rfc4456)