---
title: Conocimientos necesarios para trabajar con el host de ciclo de vida de hardware
description: Obtenga información sobre los conocimientos necesarios para trabajar con el host de ciclo de vida de hardware.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f4dc52cff6f04cdfdafedd845ccf40843687e7a3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392851"
---
# <a name="required-knowledge-for-working-with-the-hardware-lifecycle-host"></a>Conocimientos necesarios para trabajar con el host de ciclo de vida de hardware

Para completar los procedimientos de FRU, debe estar familiarizado con los siguientes conceptos y guías, y disponer de acceso a ellos.

## <a name="hardware-lifecycle-host"></a>Host de ciclo de vida de hardware

El host del ciclo de vida del hardware (HLH) es un servidor de administración físico situado en la parte superior del bastidor de Azure Stack Hub. Para acceder al host, puede conectarse a él mediante uno de estos tres métodos:

* Directo (carro de bloqueos)
* iDRAC (puerto de servicio)
* iDRAC (acceso por IP)

Si está dentro del centro de datos, puede conectarse directamente al servidor HLH mediante los puertos VGA y USB. Por ejemplo, mediante la conexión de un carro de bloqueos.

Si está dentro del centro de datos, conecte el portátil al puerto de servicio de iDRAC 9 mediante un cable micro USB. Para obtener más información, consulte Acceso a la interfaz de iDRAC mediante conexión USB directa.

Colabore con el cliente para conectarse al servidor HLH desde su red de administración y su estación de trabajo de administración a la IP del controlador iDRAC.

> [!NOTE]
> Solo las redes previamente agregadas a las ACL de conmutador pueden conectarse directamente a la interfaz iDRAC del servidor HLH.

## <a name="credentials"></a>Credenciales

Colabore con el cliente para obtener las credenciales para lo siguiente:

* HLH
* administrator
* cuenta iDRAC (opcional)

Una cuenta de Windows con todos los derechos de administrador.

Si no se conecta directamente al servidor mediante un carro de bloqueos, necesitará las credenciales de la cuenta de iDRAC para obtener acceso al KVM virtual.

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Manual de instalación y servicio de EMC PowerEdge R640 de Dell

Para obtener información detallada sobre cómo reemplazar físicamente el hardware pertinente, consulte los procedimientos de reemplazo del servidor FRU PowerEdge R640 en el
[Manual de instalación y servicio de EMC PowerEdge R640](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) de [Dell](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Vaya a la sección [Instalación y extracción de componentes del sistema](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us).

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Manual de servicio de dispositivos Tactical Cloud de Microsoft Azure Stack Hub

El manual de servicio de dispositivos Tactical Cloud de Microsoft Azure Stack Hub contiene instrucciones para quitar el host de ciclo de vida de hardware de los pods tácticos de Tracewell.

## <a name="dell-emc-poweredge-idrac"></a>iDRAC de EMC PowerEdge de Dell

Debe entender cómo usar y navegar por la interfaz web de iDRAC del servidor EMC PowerEdge de Dell.

