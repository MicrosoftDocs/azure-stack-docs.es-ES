---
title: Planeamiento de la capacidad de red de Azure Stack | Microsoft Docs
description: Aprenda a planear la integración de red del centro de datos con los sistemas integrados de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 4894fb7184944095d968d08e2d668912a78119d4
ms.sourcegitcommit: ef7efcde76d1d7875ca1c882afebfd6a27f1c686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2019
ms.locfileid: "72888048"
---
# <a name="network-integration-planning-for-azure-stack"></a>Planeamiento de la capacidad de red de Azure Stack

En este artículo se ofrece información sobre la infraestructura de red de Azure Stack que le ayudará decidir cuál es la mejor forma de integrar Azure Stack en su entorno de red existente. 

> [!NOTE]
> Para resolver nombres DNS externos de Azure Stack (por ejemplo, www\.bing.com), deberá proporcionar servidores DNS a fin de reenviar las solicitudes DNS. Para más información acerca de los requisitos de DNS de Azure Stack, consulte [Integración del centro de datos de Azure Stack: DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Diseño de la red física

La solución Azure Stack requiere una infraestructura física resistente y de alta disponibilidad para admitir sus operaciones y servicios. Los vínculos superiores desde ToR para conmutadores de perímetro se limitan a medios SFP+ o SFP28 y velocidades de 1 GB, 10 GB o 25 GB. Póngase en contacto con su proveedor de hardware del fabricante de equipos originales (OEM) para obtener información sobre la disponibilidad. El siguiente diagrama presenta nuestro diseño recomendado:

![Diseño de red de Azure Stack recomendado](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Redes lógicas

Las redes lógicas representan una abstracción de la infraestructura de red física subyacente. Se utilizan para organizar y simplificar las asignaciones de red para hosts, máquinas virtuales (VM) y servicios. Como parte de la creación de redes lógicas, se crean sitios de red para definir las redes de área local virtual (VLAN), subredes IP y pares de subredes IP/VLAN que se asocian a la red lógica en cada ubicación física.

En la siguiente tabla se muestran las redes lógicas y los intervalos de subred IPv4 asociados para los que se debe planear:

| Red lógica | DESCRIPCIÓN | Size | 
| -------- | ------------- | ------------ | 
| VIP pública | Azure Stack usa un total de 31 direcciones de esta red. Ocho direcciones IP públicas se utilizan para un pequeño conjunto de servicios de Azure Stack, mientras que el resto lo usan las VM del inquilino. Si tiene previsto usar App Service y los proveedores de recursos de SQL, se usan 7 direcciones más. Las 15 direcciones IP restantes están reservadas a los futuros servicios de Azure. | /26 (62 hosts) - /22 (1022 hosts)<br><br>Recomendado = /24 (254 hosts) | 
| Infraestructura del conmutador | Direcciones IP de punto a punto con fines de enrutamiento, interfaces de administración de conmutador dedicado y direcciones de bucle invertido asignadas al conmutador. | /26 | 
| Infraestructura | Se utiliza para que los componentes internos de Azure Stack se comuniquen. | /24 |
| Privada | Se utiliza para la red de almacenamiento y las VIP privadas. | /24 | 
| BMC | Se utiliza para comunicarse con los BMC en los hosts físicos. | /26 | 
| | | |

## <a name="network-infrastructure"></a>Infraestructura de red

La infraestructura de red de Azure Stack consta de varias redes lógicas que están configuradas en los conmutadores. El diagrama siguiente muestra estas redes lógicas y cómo integrarlas con la parte superior del rack (TOR), el controlador de administración de placa base (BMC) y los conmutadores de borde (red de cliente).

![Conexiones del conmutador y diagrama de red lógica](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Red de BMC

Esta red está dedicada a conectar todos los controladores de administración de la placa base (también conocidos como BMC o procesadores de servicio) a la red de administración. Algunos ejemplos son: iDRAC, iLO, iBMC, etc. Solo se usa una cuenta de BMC para comunicarse con cualquier nodo de BMC. Si está presente, el host del ciclo de vida del hardware (HLH) se ubica en esta red y puede proporcionar software específico del OEM para el mantenimiento o la supervisión del hardware.

El HLH también hospeda la máquina virtual de implementación (DVM). La DVM se utiliza durante la implementación de Azure Stack y se quita cuando esta finaliza. La DVM requiere acceso a Internet en escenarios de implementación conectados para probar, validar y obtener acceso a varios componentes. Estos componentes pueden estar dentro y fuera de la red corporativa (por ejemplo, NTP, DNS y Azure). Para obtener más información acerca de los requisitos de conectividad, consulte la [sección de NAT en la integración con el firewall de Azure Stack](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Red privada

Esta red /24 (254 direcciones IP de host) es privada de la región de Azure Stack (no se expande más allá de los dispositivos de conmutación de borde de la región de Azure Stack) y se divide en dos subredes:

- **Red de almacenamiento**. Una red /25 (126 direcciones IP de host) que se utiliza para admitir el uso del tráfico de almacenamiento de espacios directo y el bloque de mensajes del servidor (SMB) y la migración en vivo de VM.
- **Red IP virtual interna**. Una red /25 dedicada únicamente a una red IP virtual de uso interno para el equilibrador de carga de software.

### <a name="azure-stack-infrastructure-network"></a>Red de la infraestructura de Azure Stack
Esta red /24 está dedicada a los componentes internos de Azure Stack para que puedan comunicarse e intercambiar datos entre ellos. Esta subred se puede enrutar fuera de la solución Azure Stack a su centro de datos, no se recomienda usar direcciones IP enrutables públicas o de Internet en esta subred. Esta red se anuncia en el borde, pero la mayoría de sus direcciones IP están protegidas por listas de control de acceso (ACL). Las direcciones IP permitidas para el acceso se encuentran dentro de un pequeño intervalo equivalente de tamaño a una red de /27 y hospedan servicios como el [punto de conexión con privilegios (PEP)](azure-stack-privileged-endpoint.md) y [Azure Stack Backup](azure-stack-backup-reference.md).

### <a name="public-vip-network"></a>Red IP virtual pública

La red IP virtual pública se asigna al controlador de red en Azure Stack. No es una red lógica en el conmutador. El equilibrador de carga de software utiliza el grupo de direcciones y asigna redes /32 a las cargas de trabajo de inquilino. En la tabla de enrutamiento de conmutador, estas direcciones IP /32 se publican como una ruta disponible a través de BGP. Esta red contiene las direcciones IP externas accesibles o públicas. La infraestructura de Azure Stack reserva las primeras 31 direcciones de esta red IP virtual pública, mientras que el resto lo utilizan las máquinas virtuales de inquilino. El tamaño de red en esta subred puede variar desde un mínimo de /26 (64 hosts) hasta un máximo de /22 (1022 hosts). Se recomienda que planee una red /24.

### <a name="switch-infrastructure-network"></a>Red de la infraestructura de conmutadores

Esta red /26 es la subred que contiene las subredes IP /30 (dos IP de host) punto a punto enrutables y los bucles invertidos que son subredes /32 dedicadas a la administración de conmutadores en banda y al ID de router de BGP. Este rango de direcciones IP debe enrutarse fuera de la solución Azure Stack a su centro de datos. Pueden ser direcciones IP privadas o públicas.

### <a name="switch-management-network"></a>Red de administración de conmutadores

Esta red /29 (seis IP de host) está dedicada a conectar los puertos de administración de los conmutadores. Permite el acceso fuera de banda para la implementación, administración y solución de problemas. Se calcula a partir de la red de infraestructura de conmutadores mencionada anteriormente.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre el planeamiento de redes: [conectividad de borde](azure-stack-border-connectivity.md).
