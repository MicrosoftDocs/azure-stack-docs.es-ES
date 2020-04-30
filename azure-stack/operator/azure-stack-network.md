---
title: Planeamiento de la integración de red en Azure Stack Hub
description: Aprenda a planear la integración de red del centro de datos con los sistemas integrados de Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: f447f4969e9cb9bcb4d56ea5961473e1028e44f3
ms.sourcegitcommit: 7b8e067cb449e67ca9c2935580684d78840ad495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106914"
---
# <a name="network-integration-planning-for-azure-stack"></a>Planeamiento de la capacidad de red de Azure Stack

En este artículo se ofrece información sobre la infraestructura de red de Azure Stack que le ayudará decidir cuál es la mejor forma de integrar Azure Stack en su entorno de red existente. 

> [!NOTE]
> Para resolver nombres DNS externos de Azure Stack (por ejemplo, www\.bing.com), deberá proporcionar servidores DNS a fin de reenviar las solicitudes DNS. Para más información acerca de los requisitos de DNS de Azure Stack, consulte [Integración del centro de datos de Azure Stack: DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Diseño de la red física

La solución Azure Stack requiere una infraestructura física resistente y de alta disponibilidad para admitir sus operaciones y servicios. Para integrar Azure Stack en la red, se necesitan vínculos superiores de los conmutadores de la parte superior del bastidor (ToR) al conmutador o enrutador más cercano, que en esta documentación se denomina borde. Los ToR se pueden vincular a un único borde o a un par de ellos. La herramienta de automatización preconfigura el ToR, espera un mínimo de una conexión entre el ToR y el borde al usar el enrutamiento de BGP y un mínimo de dos conexiones (una por cada ToR) entre los ToR y el borde cuando se usa el enrutamiento estático, con un máximo de cuatro conexiones en ambas opciones de enrutamiento. Estas conexiones se limitan a los soportes físicos SFP+ o SFP28 y a una velocidad de 1 GB, 10 GB o 25 GB. Póngase en contacto con su proveedor de hardware del fabricante de equipos originales (OEM) para obtener información sobre la disponibilidad. El siguiente diagrama presenta el diseño recomendado:

![Diseño de red de Azure Stack recomendado](media/azure-stack-network/physical-network.svg)


## <a name="logical-networks"></a>Redes lógicas

Las redes lógicas representan una abstracción de la infraestructura de red física subyacente. Se utilizan para organizar y simplificar las asignaciones de red para hosts, máquinas virtuales (VM) y servicios. Como parte de la creación de redes lógicas, se crean sitios de red para definir las redes de área local virtual (VLAN), subredes IP y pares de subredes IP/VLAN que se asocian a la red lógica en cada ubicación física.

En la siguiente tabla se muestran las redes lógicas y los intervalos de subred IPv4 asociados para los que se debe planear:

| Red lógica | Descripción | Size | 
| -------- | ------------- | ------------ | 
| VIP pública | Azure Stack usa un total de 31 direcciones de esta red. Ocho direcciones IP públicas se utilizan para un pequeño conjunto de servicios de Azure Stack, mientras que el resto lo usan las VM del inquilino. Si tiene previsto usar App Service y los proveedores de recursos de SQL, se usan 7 direcciones más. Las 15 direcciones IP restantes están reservadas a los futuros servicios de Azure. | /26 (62 hosts) - /22 (1022 hosts)<br><br>Recomendado = /24 (254 hosts) | 
| Infraestructura del conmutador | Direcciones IP de punto a punto con fines de enrutamiento, interfaces de administración de conmutador dedicado y direcciones de bucle invertido asignadas al conmutador. | /26 | 
| Infraestructura | Se utiliza para que los componentes internos de Azure Stack se comuniquen. | /24 |
| Privada | Se usa para la red de almacenamiento, direcciones IP virtuales privadas, contenedores de infraestructura y otras funciones internas. A partir de la versión 1910, el tamaño de esta subred cambia a /20. Para más información, consulte la sección [Red privada](#private-network) de este artículo. | /20 | 
| BMC | Se utiliza para comunicarse con los BMC en los hosts físicos. | /26 | 
| | | |

> [!NOTE]
> Cuando el sistema se actualice a la versión 1910, una alerta en el portal recordará al operador que ejecute el nuevo cmdlet PEP **Set-AzsPrivateNetwork** para agregar un nuevo espacio de direcciones IP privadas /20. Consulte las [notas de la versión 1910](release-notes.md) para obtener instrucciones sobre cómo ejecutar el cmdlet. Para más información e instrucciones sobre cómo seleccionar el espacio de direcciones IP privadas /20, consulte la sección [Red privada](#private-network) de este artículo.

## <a name="network-infrastructure"></a>Infraestructura de red

La infraestructura de red de Azure Stack consta de varias redes lógicas que están configuradas en los conmutadores. El diagrama siguiente muestra estas redes lógicas y cómo integrarlas con la parte superior del rack (TOR), el controlador de administración de placa base (BMC) y los conmutadores de borde (red de cliente).

![Conexiones del conmutador y diagrama de red lógica](media/azure-stack-network/networkdiagram.svg)

### <a name="bmc-network"></a>Red de BMC

Esta red está dedicada a conectar todos los controladores de administración de la placa base (también conocidos como BMC o procesadores de servicio) a la red de administración. Algunos ejemplos son: iDRAC, iLO, iBMC, etc. Solo se usa una cuenta de BMC para comunicarse con cualquier nodo de BMC. Si está presente, el host del ciclo de vida del hardware (HLH) se ubica en esta red y puede proporcionar software específico del OEM para el mantenimiento o la supervisión del hardware.

El HLH también hospeda la máquina virtual de implementación (DVM). La DVM se utiliza durante la implementación de Azure Stack y se quita cuando esta finaliza. La DVM requiere acceso a Internet en escenarios de implementación conectados para probar, validar y obtener acceso a varios componentes. Estos componentes pueden estar dentro y fuera de la red corporativa (por ejemplo, NTP, DNS y Azure). Para obtener más información acerca de los requisitos de conectividad, consulte la [sección de NAT en la integración con el firewall de Azure Stack](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Red privada

Esta red /20 (4096 direcciones IP) es privada para la región de Azure Stack (no se enruta más allá de los dispositivos de conmutación de borde del sistema de Azure Stack) y se divide en varias subredes. Estos son algunos ejemplos:

- **Red de almacenamiento**. Una red /25 (128 direcciones IP) que se utiliza para admitir el uso del tráfico de almacenamiento de espacios directo y el bloque de mensajes del servidor (SMB), y la migración en vivo de máquinas virtuales.
- **Red IP virtual interna**. Una red /25 dedicada únicamente a una red IP virtual de uso interno para el equilibrador de carga de software.
- **Red de contenedor**: Una red /23 (512 IP) dedicada al tráfico solo interno entre contenedores que ejecutan servicios de infraestructura.

A partir de la versión 1910, el sistema de Azure Stack Hub **requiere** un espacio de direcciones IP internas privadas adicional /20. Esta red será privada para el sistema de Azure Stack (no se enruta más allá de los dispositivos de conmutación de borde del sistema de Azure Stack) y se puede reutilizar en varios sistemas de Azure Stack dentro del centro de recursos. Aunque la red es privada para Azure Stack, no debe superponerse a otras redes del centro de recursos. El espacio IP privado /20 se divide en varias redes que permiten ejecutar la infraestructura de Azure Stack Hub en contenedores (como se mencionó anteriormente en las [notas de la versión 1905](release-notes.md?view=azs-1905)). Además, este nuevo espacio de direcciones IP privadas contribuye a realizar unos esfuerzos continuos para reducir el espacio de direcciones IP enrutables necesario antes de la implementación. Al ejecutar la infraestructura de Azure Stack Hub en contenedores se pretende optimizar el uso y mejorar el rendimiento. Además, el espacio IP privado /20 también se usa para permitir esfuerzos continuos que reducirán el espacio IP enrutable necesario antes de la implementación. Para obtener instrucciones sobre el espacio de direcciones IP privadas, se recomienda seguir [RFC 1918](https://tools.ietf.org/html/rfc1918).

En el caso de los sistemas implementados antes de 1910, esta subred /20 será una red adicional que se debe especificar en los sistemas tras actualizarse a 1910. Se deberá proporcionar la red adicional al sistema mediante el cmdlet PEP **Set-AzsPrivateNetwork**.

> [!NOTE]
> Tenga en cuenta que la entrada /20 constituirá un requisito previo en la próxima actualización de Azure Stack Hub posterior a la 1910. Cuando se publique la siguiente actualización de Azure Stack Hub después de la versión 1910 e intente instalarla, se producirá un error en el proceso si no se ha completado la entrada /20 tal como se describe a continuación en los pasos de corrección. Se mostrará una alerta en el portal de administración hasta que se hayan completado los pasos de corrección indicados. Consulte el artículo [Integración de la red del centro de datos](azure-stack-network.md#private-network) para comprender cómo se consumirá este nuevo espacio privado.

**Pasos de corrección**: Para llevar a cabo la corrección, siga las instrucciones para [abrir una sesión de PEP](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Prepare un [intervalo de direcciones IP internas privadas](azure-stack-network.md#logical-networks) de tamaño /20 y ejecute el siguiente cmdlet (solo disponible a partir de la versión 1910) en la sesión PEP; para ello, use el siguiente ejemplo: `Set-AzsPrivateNetwork -UserSubnet 10.87.0.0/20`. Si la operación se realiza correctamente, recibirá el mensaje **Azs Internal Network range added to the config** (Intervalo de red interno de Azs agregado a la configuración). Además, la alerta se cerrará en el portal de administración. El sistema Azure Stack Hub ya se puede actualizar a la versión siguiente.

### <a name="azure-stack-infrastructure-network"></a>Red de la infraestructura de Azure Stack
Esta red /24 está dedicada a los componentes internos de Azure Stack para que puedan comunicarse e intercambiar datos entre ellos. Esta subred se puede enrutar fuera de la solución Azure Stack a su centro de datos, no se recomienda usar direcciones IP enrutables públicas o de Internet en esta subred. Esta red se anuncia en el borde, pero la mayoría de sus direcciones IP están protegidas por listas de control de acceso (ACL). Las direcciones IP permitidas para el acceso se encuentran dentro de un pequeño intervalo equivalente de tamaño a una red de /27 y hospedan servicios como el [punto de conexión con privilegios (PEP)](azure-stack-privileged-endpoint.md) y [Azure Stack Backup](azure-stack-backup-reference.md).

### <a name="public-vip-network"></a>Red IP virtual pública

La red IP virtual pública se asigna al controlador de red en Azure Stack. No es una red lógica en el conmutador. El equilibrador de carga de software utiliza el grupo de direcciones y asigna redes /32 a las cargas de trabajo de inquilino. En la tabla de enrutamiento de conmutador, estas direcciones IP /32 se publican como una ruta disponible a través de BGP. Esta red contiene las direcciones IP externas accesibles o públicas. La infraestructura de Azure Stack reserva las primeras 31 direcciones de esta red IP virtual pública, mientras que el resto lo utilizan las máquinas virtuales de inquilino. El tamaño de red en esta subred puede variar desde un mínimo de /26 (64 hosts) hasta un máximo de /22 (1022 hosts). Se recomienda que planee una red /24.

### <a name="switch-infrastructure-network"></a>Red de la infraestructura de conmutadores

Esta red /26 es la subred que contiene las subredes IP /30 (dos IP de host) punto a punto enrutables y los bucles invertidos que son subredes /32 dedicadas a la administración de conmutadores en banda y al ID de router de BGP. Este rango de direcciones IP debe enrutarse fuera de la solución Azure Stack a su centro de datos. Pueden ser direcciones IP privadas o públicas.

### <a name="switch-management-network"></a>Red de administración de conmutadores

Esta red /29 (seis IP de host) está dedicada a conectar los puertos de administración de los conmutadores. Permite el acceso fuera de banda para la implementación, administración y solución de problemas. Se calcula a partir de la red de infraestructura de conmutadores mencionada anteriormente.

## <a name="permitted-networks"></a>Redes permitidas

A partir de la versión 1910, la hoja de cálculo de implementación tendrá este nuevo campo que permitirá al operador cambiar algunas listas de control de acceso (ACL) para proporcionar acceso a las interfaces de administración de dispositivos de red y al host de ciclo de vida de hardware (HLH) desde un intervalo de red del centro de datos de confianza. Con el cambio de la lista de control de acceso, el operador puede permitir que sus máquinas virtuales de jumpbox de administración de un intervalo de red específico obtengan acceso a la interfaz de administración de conmutadores, al sistema operativo HLH y al BMC HLH. El operador puede proporcionar una o varias subredes a esta lista; si se deja en blanco, se denegará el acceso de forma predeterminada. Esta nueva funcionalidad reemplaza la necesidad de una intervención manual posterior a la implementación, tal como se solía describir en [Modificar la configuración específica de la configuración del conmutador de Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-customer-defined#access-control-list-updates).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre el planeamiento de redes: [conectividad de borde](azure-stack-border-connectivity.md).
