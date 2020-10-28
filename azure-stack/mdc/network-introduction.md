---
title: Introducción a las redes del Centro de datos modular (MDC).
description: Obtenga información sobre las redes del dispositivo MDC.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/31/2019
ms.lastreviewed: 12/31/2019
ms.openlocfilehash: 511ea66e0f70041ffc237463e33fccdbf390360d
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183473"
---
# <a name="modular-data-center-mdc-network-introduction"></a>Introducción a la red del Centro de datos modular (MDC)

## <a name="package-content"></a>Contenido de paquete

La solución se suministra con el equipo de red adicional que se indica a continuación. Este equipo se usa para conectar el contenedor a la red.

Compruebe la óptica del conmutador que coincide con su entorno.

| Cantidad | Tipo | Modelo |
|-----|---------------|-------|
| 12 | 12x QSFP-40G | "SR" |
| 12 | 12x SFP+ 10 GB | "SR" |
| 12 | 12x SFP 1G | "SR" |
| 12 | 12x QSFP-40G | "LR" |
| 12 | 12x SFP+ 10 GB | "LR" |
| 12 | 12x SFP 1G | "LR" |

## <a name="network-design-overview"></a>Introducción al diseño de redes

### <a name="physical-network-design"></a>Diseño de la redes física

La solución MDC requiere una infraestructura física resistente y de alta disponibilidad para admitir sus operaciones y servicios. Los vínculos superiores de los conmutadores ToR a los perimetrales se limitan a medios SFP+ o SFP28 y a velocidades de 1 GB, 10 GB o 40 GB.

El siguiente diagrama presenta nuestro diseño recomendado para MDC:

![Diseño recomendado de la red física](media/network-introduction/physical-network-design.png)

### <a name="logical-network-design"></a>Diseño de la red lógica

El diseño de una red lógica representa una abstracción de la infraestructura de red física. Se utilizan para organizar y simplificar las asignaciones de red para hosts, máquinas virtuales (VM) y servicios. Como parte de la creación de la red lógica, se crean sitios de red para definir lo siguiente:
- Redes de área local virtuales (VLAN)
- Subredes IP
- Pares de subred IP/VLAN

Todas estas están asociadas a la red lógica en cada ubicación física.

En la siguiente tabla se muestran las redes lógicas y los intervalos de subred IPv4 asociados para los que se debe planear:

| **Red lógica** | **Descripción** | **Tamaño** |
|---------------------|-----------------|----------|
| Dirección IP virtual (VIP) pública | MDC usa un total de 31 direcciones de esta red. Ocho direcciones IP públicas se utilizan para un pequeño conjunto de servicios de MDC, mientras que el resto lo usan las VM del inquilino. Si tiene previsto usar App Service y los proveedores de recursos de SQL, se usan 7 direcciones más. Las 15 direcciones IP restantes están reservadas a los futuros servicios de Azure. | /26 (62 hosts) - /22 (1022 hosts) <br><Br>Recomendado = /24 (254 hosts) |
| Infraestructura del conmutador | Direcciones IP de punto a punto con fines de enrutamiento, interfaces de administración de conmutador dedicado y direcciones de bucle invertido asignadas al conmutador. | /26 |
| Infraestructura | Se utiliza para que los componentes internos de MDC se comuniquen. | /24 |
| Privados | Se usa para la red de almacenamiento, las direcciones IP virtuales privadas, los contenedores de infraestructura y otras funciones internas. | /20 |
| Controlador de administración de placa base (BMC) | Se usa para comunicarse con los controladores de administración de placa base en los hosts físicos. | /26 |
| Isilon | Se usa para comunicarse con el almacenamiento de Isilon. | 1x TOR /25 1x BMC /25 (administración) |

### <a name="network-infrastructure"></a>Infraestructura de red

La infraestructura de red de MDC consta de varias redes lógicas que están configuradas en los conmutadores. El diagrama siguiente muestra estas redes lógicas y cómo integrarlas con la parte superior del rack (TOR), el controlador de administración de placa base y los conmutadores de borde (red de cliente).

Diagrama de la red lógica de MDC:

![Diseño de la red lógica](media/network-introduction/logical-network-design.png)

#### <a name="bmc-network"></a>Red de BMC

Esta red está dedicada a conectar todos los controladores de administración de la placa base (también conocidos como BMC o procesadores de servicio) a la red de administración. Algunos ejemplos son: iDRAC, iLO, iBMC, etc. Solo se usa una cuenta de BMC para comunicarse con cualquier nodo de BMC. Si está presente, el host del ciclo de vida del hardware (HLH) se ubica en esta red y puede proporcionar software específico del OEM para el mantenimiento o la supervisión del hardware.

El HLH también hospeda la máquina virtual de implementación (DVM). La DVM se utiliza durante la implementación de MDC y se quita cuando esta finaliza. La DVM requiere acceso a Internet en escenarios de implementación conectados para probar, validar y obtener acceso a varios componentes. Estos componentes pueden estar dentro y fuera de la red corporativa (por ejemplo, NTP, DNS y Azure). Para obtener más información acerca de los requisitos de conectividad, consulte la sección de NAT en la integración con el firewall de MDC.

#### <a name="private-network"></a>Red privada

La red /20 (4096 IP de host) es privada para la región de MDC. No se expande más allá de los dispositivos de conmutación de borde de la región de MDC. Esta red se divide en varias subredes, por ejemplo:

- **Red de almacenamiento** . Una red /25 (128 direcciones IP) que se utiliza para admitir el uso del tráfico de almacenamiento de espacios directo y el bloque de mensajes del servidor (SMB), y la migración en vivo de máquinas virtuales.
- Red IP virtual interna: red /25 dedicada a las IP virtuales solo internas para el equilibrador de carga de software.
- **Red de contenedor** : red /23 (512 IP) dedicada al tráfico solo interno entre contenedores que ejecutan servicios de infraestructura.

El tamaño de la red privada modificado es /20 (4096 IP) del espacio de direcciones IP privadas. Esta red es privada para el sistema de MDC. No se enruta más allá de los dispositivos de conmutación de borde del sistema de MDC y se puede reutilizar en varios de estos sistemas. Aunque la red es privada para MDC, no debe superponerse a otras redes del centro de datos. Para obtener instrucciones sobre el espacio de direcciones IP privadas, se recomienda seguir las RFC 1918.

El espacio de direcciones IP privadas /20 se divide en varias redes, que permitirán la ejecución de la infraestructura del sistema de MDC en contenedores en versiones futuras. Consulte las notas de la versión 1910 para obtener más información. Este nuevo espacio de direcciones IP privadas contribuye a realizar esfuerzos continuos para reducir el espacio de direcciones IP enrutables necesario antes de la implementación.

#### <a name="mdc-infrastructure-network"></a>Red de la infraestructura de MDC

Esta red /24 está dedicada a los componentes internos de MDC para que puedan comunicarse e intercambiar datos entre ellos. Esta subred se puede enrutar externamente de la solución MDC al centro de datos. **No** se recomienda utilizar direcciones IP enrutables públicas o de Internet en esta subred. Esta red se anuncia en el borde, pero la mayoría de sus direcciones IP están protegidas por listas de control de acceso (ACL). Las direcciones IP permitidas para el acceso están dentro de un intervalo reducido, con un tamaño equivalente al de una red /27. Las direcciones IP hospedan servicios, como el punto de conexión con privilegios (PEP) y la copia de seguridad de MDC.

#### <a name="public-vip-network"></a>Red IP virtual pública

La red IP virtual pública se asigna a la controladora de red en MDC. No es una red lógica en el conmutador. El equilibrador de carga de software utiliza el grupo de direcciones y asigna redes /32 a las cargas de trabajo de inquilino. En la tabla de enrutamiento de conmutadores, estas direcciones IP /32 se publican como una ruta disponible a través del Protocolo de puerta de enlace de borde (BGP). Esta red contiene direcciones públicas a las que se puede acceder externamente. La infraestructura de MDC reserva las primeras 31 direcciones de esta red IP virtual pública, mientras que el resto lo utilizan las VM del inquilino. El tamaño de red en esta subred puede variar desde un mínimo de /26 (64 hosts) hasta un máximo de /22 (1022 hosts). Se recomienda que planee una red /24.

#### <a name="switch-infrastructure-network"></a>Red de la infraestructura de conmutadores

La red /26 es la subred que contiene las subredes IP enrutables de punto a punto /30 (dos IP de host) y los bucles invertidos. Se trata de subredes dedicadas /32 para la administración de conmutadores en la banda y el identificador del enrutador BGP. Este rango de direcciones IP debe enrutarse fuera de la solución MDC a su centro de datos. Las direcciones IP pueden ser privadas o públicas.

#### <a name="switch-management-network"></a>Red de administración de conmutadores

Esta red /29 (seis IP de host) está dedicada a conectar los puertos de administración de los conmutadores. Permite el acceso fuera de banda para la implementación, la administración y la solución de problemas. Se calcula a partir de la red de infraestructura de conmutadores mencionada anteriormente.

#### <a name="isilon-network"></a>Red Isilon

Hay dos redes /25, una que reside en el conmutador TOR y otra que se usa en el conmutador BMC para la administración.

## <a name="dns-design-overview"></a>Introducción al diseño de DNS

Para acceder a los puntos de conexión de MDC ( *portal* ,  *adminportal* ,  *management* y *adminmanagement* ) desde fuera de MDC, debe integrar los servicios de DNS de MDC con los servidores DNS que hospedan las zonas DNS que quiere usar en MDC.

### <a name="mdc-dns-namespace"></a>Espacio de nombres DNS del Centro de datos modular

Se le solicitará que proporcione información importante relacionada con DNS cuando implemente el Centro de datos modular.

| **Campo** | **Descripción** | **Ejemplo** |
|-----------|-----------------|-------------|
| Region | Ubicación geográfica de la implementación del Centro de datos modular. | *este* |
| Nombre de dominio externo | Nombre de la zona que quiere usar para la implementación de MDC. | *cloud.fabrikam.com* |
| Nombre de dominio interno | Nombre de la zona interna que se usa para los servicios de infraestructura en MDC. Está integrado en el servicio de directorio y es privado (no es accesible desde fuera de la implementación de MDC). | *azurestack.local* |
| Reenviadores de DNS | Servidores DNS que se usan para reenviar consultas de DNS, registros y zonas DNS que se hospedan fuera de MDC, ya sea en la intranet corporativa o en la red pública de Internet. Puede editar el valor del reenviador DNS con el cmdlet  **Set-AzSDnsForwarder** después de la implementación. | |
| Prefijo de nomenclatura (opcional) | Prefijo de nomenclatura que quiere que tengan los nombres de máquina de instancia de rol de infraestructura de MDC. Si no se proporciona, el valor predeterminado es  *azs* . | *azs* |

El nombre de dominio completo (FQDN) de su implementación y puntos de conexión de MDC es la combinación de los parámetros de región y de nombre de dominio externo. Con los valores de los ejemplos de la tabla anterior, el FQDN para esta implementación de MDC sería el siguiente: *east.cloud.fabrikam.com* .

De esta forma, los ejemplos de algunos de los puntos de conexión de esta implementación tendrían un aspecto parecido a las direcciones URL siguientes:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

Para usar este espacio de nombres DNS de ejemplo para una implementación de MDC, se deben cumplir las condiciones siguientes:

- La zona fabrikam.com se registra con un registrador de dominios, un servidor DNS corporativo interno o ambos. El registro depende de los requisitos de resolución de nombres.

- El dominio secundario cloud.fabrikam.com existe en la zona fabrikam.com.

- Se puede acceder a los servidores DNS que hospedan las zonas fabrikam.com y cloud.fabrikam.com desde la implementación de MDC.

Para resolver los nombres DNS para los puntos de conexión y las instancias de MDC desde fuera de MDC, debe integrar los servidores DNS. Incluidos los servidores que hospedan la zona DNS externa para MDC, con los servidores DNS que hospedan la zona principal que quiere usar.

#### <a name="dns-name-labels"></a>Etiquetas de nombre DNS

MDC admite la adición de una etiqueta de nombre DNS a una dirección IP pública para permitir la resolución de nombres de las direcciones IP públicas. Las etiquetas DNS constituyen una manera práctica de acceder a las aplicaciones y los servicios hospedados en MDC por el nombre. La etiqueta de nombre DNS usa un espacio de nombres ligeramente diferente al de los puntos de conexión de la infraestructura. Siguiendo el ejemplo de espacio de nombres anterior, el espacio de nombres para las etiquetas de nombre DNS sería: *\*.east.cloudapp.cloud.fabrikam.com* . 

Si un inquilino especifica **Myapp** en el campo de la etiqueta de nombre DNS de un recurso de dirección IP pública, crea un registro A para myapp en la zona **east.cloudapp.cloud.fabrikam.com** en el servidor DNS externo de MDC. El nombre de dominio completo resultante sería: *myapp.east.cloudapp.cloud.fabrikam.com* . 

Si desea aprovechar esta funcionalidad y usar este espacio de nombres, debe integrar los servidores DNS. Se incluyen los servidores que hospedan la zona DNS externa para MDC y también los servidores DNS que hospedan la zona principal que quiere usar. Este espacio de nombres es distinto del espacio de nombres utilizado para los puntos de conexión de servicio de MDC, por lo que debe crear una delegación adicional o un regla de reenvío condicional.

Para obtener más información acerca del funcionamiento de la etiqueta de nombre DNS, consulte la sección sobre el uso de DNS en MDC.

### <a name="resolution-and-delegation"></a>Resolución y delegación

Existen dos tipos de servidores DNS:

- Un servidor DNS autoritativo hospeda zonas DNS. Solo responde a las consultas DNS de los registros de dichas zonas.

- Un servidor DNS recursivo no hospeda zonas DNS. Responde a todas las consultas DNS, para lo que realiza una llamada a los servidores DNS autoritativos a fin de recopilar los datos que necesita.

MDC incluye servidores DNS autoritativos y recursivos. Los servidores recursivos se usan para resolver todos los nombres, salvo para la zona privada interna y la zona DNS pública externa para esa implementación de MDC.

### <a name="resolving-external-dns-names-from-mdc"></a>Resolver nombres DNS externos desde MDC

Para resolver nombres DNS para puntos de conexión fuera de MDC (por ejemplo, www.bing.com), deberá proporcionar servidores DNS para que MDC reenvíe las solicitudes DNS para las que MDC no es autoritativo. Se requieren los servidores DNS a los que MDC reenvía las solicitudes en la hoja de cálculo de implementación (en el campo Reenviador DNS). Proporcione al menos dos servidores en este campo por razones de tolerancia a errores. Sin estos valores, se produce un error de implementación de MDC. Puede editar los valores del reenviador DNS con el cmdlet  **Set-AzSDnsForwarder** después de la implementación.

## <a name="firewall-design-overview"></a>Introducción al diseño del firewall

Se recomienda que use un dispositivo de firewall para ayudar a proteger MDC. Los firewalls pueden ayudarle a defenderse contra ataques por denegación de servicio distribuido (DDOS), a detectar intrusos y a inspeccionar el contenido que reciba. Sin embargo, también pueden convertirse en un cuello de botella en el rendimiento de los servicios de Azure Storage como blobs, tablas y colas.

Si se usa un modo de implementación desconectada, debe publicar el punto de conexión de AD FS. Para obtener más información, consulte el artículo sobre la identidad de integración del centro de datos.

Los puntos de conexión de Azure Resource Manager (administrador), el portal de administración y el almacén de claves (administrador) no requieren necesariamente publicación externa. Por ejemplo, como proveedor de servicios, podría limitar la superficie expuesta a ataques únicamente mediante la administración de MDC desde la red y no desde Internet.

Para organizaciones empresariales, la red externa puede ser la red corporativa existente. En este caso, debe publicar los puntos de conexión para trabajar con MDC desde la red corporativa.

### <a name="network-address-translation"></a>Traducción de direcciones de red

La traducción de direcciones de red (NAT) es el método recomendado para permitir que la máquina virtual de implementación (DVM) acceda a los recursos externos durante la implementación. También para las VM de la consola de recuperación de emergencia (ERCS) o el punto de conexión con privilegios (PEP) durante el registro y la solución de problemas.

NAT también puede ser una alternativa a las direcciones IP públicas de la red externa o VIP públicas. Sin embargo, no se recomienda, ya que limita la experiencia del usuario inquilino y aumenta la complejidad. Una opción sería una NAT con una relación de uno a uno que siga requiriendo una dirección IP pública por cada IP de usuario del grupo. Otra opción es una NAT con una relación de varios a uno que requiera una regla NAT por cada VIP de usuario de todos los puertos que ese usuario podría usar.

Algunas de las desventajas del uso de NAT para una VIP pública son:

- Sobrecarga al administrar las reglas de firewall, ya que los usuarios controlan sus propios puntos de conexión y sus propias reglas de publicación en la pila de redes definidas por software (SDN). Los usuarios deben ponerse en contacto con el operador de MDC para que se publiquen sus VIP y para actualizar la lista de puertos. 
- Aunque el uso de NAT limita la experiencia del usuario, ofrece un control total al operador de las solicitudes de publicación.
- En el caso de escenarios de nube híbrida con Azure, debe tener en cuenta que Azure no admite la configuración de un túnel VPN a un punto de conexión con NAT.

### <a name="ssl-interception"></a>Interceptación de SSL

En la actualidad, se recomienda deshabilitar cualquier interceptación de SSL (por ejemplo, la descarga de descifrado) en todo el tráfico de MDC. En caso de que se admita en actualizaciones futuras, se proporcionarán instrucciones acerca de cómo habilitar la interceptación de SSL en MDC.

### <a name="edge-deployment-firewall-scenario"></a>Escenario de firewall de implementación perimetral

En una implementación perimetral, MDC se implementa directamente detrás del firewall o el enrutador perimetral. En estos casos, se admite que el firewall esté encima del borde (escenario 1), donde admite configuraciones de firewall activo/activo y activo/pasivo. También puede actuar como el dispositivo de borde (escenario 2), donde solo es compatible con la configuración de firewall activo/activo. El escenario 2 se basa en una ruta de acceso múltiple de igual costo (ECMP) con enrutamiento BGP o estático para la conmutación por error.

Las direcciones IP enrutables públicas se especifican para el grupo de VIP públicas de la red externa en el momento de la implementación. Por motivos de seguridad, las direcciones IP enrutables públicas **no** se recomiendan en ninguna otra red en un escenario perimetral. Este escenario permite que un usuario disfrute de la experiencia completa en la nube autocontrolada como si se tratara de una nube pública como Azure.

 ![Escenario de firewall perimetral de MDC](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Escenario de firewall de red perimetral o de intranet empresarial

En una implementación perimetral o de intranet empresarial, MDC se implementa en un firewall de varias zonas o entre el firewall perimetral y el firewall de red corporativo interno. A continuación, se distribuye el tráfico entre la red perimetral (o DMZ) segura y las zonas no seguras como se describe a continuación:

- **Zona segura** : red interna que usa direcciones IP enrutables corporativas o internas. La red segura se puede dividir. Puede tener acceso de salida a Internet a través de la especificación NAT del firewall. Normalmente es accesible desde dentro del centro de datos a través de la red interna. Todas las redes de MDC deben residir en la zona segura, excepto el grupo de VIP públicas de la red externa.
- **Zona perimetral** : la red perimetral es el lugar en que se suelen implementar las aplicaciones externas o accesibles desde Internet, como los servidores web. Se suele supervisar a través de un firewall para evitar ataques como DDoS e intrusión (acceso por parte de piratas informáticos) y permitir el tráfico entrante especificado de Internet. Solo debe residir en la zona DMZ el grupo de VIP públicas de redes externas de MDC. 
- **Zona no segura** : red externa, Internet. **No** se recomienda implementar MDC en la zona no segura.

![Escenario de firewall de red perimetral](media/network-introduction/perimeter-network-firewall-scenario-50.png) 

## <a name="vpn-design-overview"></a>Introducción al diseño de VPN

Aunque la VPN es un concepto de usuario, hay algunos aspectos importantes que el propietario y el operador de un solución deben conocer.

Antes de poder enviar tráfico de red entre una red virtual de Azure y un sitio local, es preciso crear una puerta de enlace de red virtual para la red virtual.

Una puerta de enlace VPN es un tipo de puerta de enlace de red virtual que envía tráfico cifrado a través de una conexión pública. Puede usar las puertas de enlace de VPN para enviar tráfico de forma segura entre una red virtual en MDC y una red virtual en Azure. También puede enviar tráfico de forma segura entre una red virtual y otra red conectada a un dispositivo VPN.

Al crear una puerta de enlace de red virtual, debe especificar el tipo de puerta de enlace que desea crear. MDC admite un tipo de puerta de enlace de red virtual: Vpn.

Cada red virtual solo puede tener dos puertas de enlace de red virtual, pero solo una de cada tipo. Según la configuración que elija, puede crear varias conexiones a una única instancia de VPN Gateway. Un ejemplo de esto es una configuración de conexión multisitio.

Antes de crear y configurar las puertas de enlace de VPN para MDC, revise las consideraciones de redes de MDC. Aprenderá cómo las configuraciones de MDC difieren de Azure.

En Azure, el rendimiento de ancho de banda de la SKU de la puerta de enlace de VPN que elija debe dividirse entre todas las conexiones que están conectadas a la puerta de enlace. Sin embargo, en MDC, el valor de ancho de banda de la SKU de la puerta de enlace de VPN se aplica a cada recurso de conexión que está conectado a la puerta de enlace. Por ejemplo: 

- En Azure, la SKU de la puerta de enlace de VPN básica puede alojar aproximadamente 100 Mbps de rendimiento agregado. Si crea dos conexiones a esa puerta de enlace de VPN y una usa 50 Mbps de ancho de banda, la otra dispone de otros 50 Mbps.

- En MDC, a cada conexión a la SKU de la puerta de enlace de VPN básica se le asignan 100 Mbps de rendimiento.

### <a name="vpn-types"></a>Tipos de VPN

Al crear la puerta de enlace de red virtual para una configuración de puerta de enlace de VPN, debe especificar un tipo de VPN. El tipo de VPN que elija dependerá de la topología de conexión que desee crear. Un tipo de VPN también puede depender del hardware que se esté usando. Las configuraciones de S2S requieren un dispositivo VPN. Algunos dispositivos VPN solo serán compatibles con un determinado tipo de VPN.

>[!IMPORTANT]
> En la actualidad, MDC solo admite el tipo de VPN basado en ruta. Si el dispositivo solo admite VPN basadas en directiva, no se admiten conexiones a dichos dispositivos desde MDC. Además, MDC no admite el uso de selectores de tráfico basados en directivas para puertas de enlace basadas en rutas en este momento, ya que las configuraciones de directivas personalizadas de IPSec/IKE no se admiten todavía. 

- **PolicyBased** : las VPN basadas en directivas cifran y dirigen los paquetes a través de túneles IPsec, en función de las directivas IPsec. Las directivas se configuran con las combinaciones de prefijos de dirección entre la red local y la red virtual de MDC. La directiva (o el selector de tráfico) suele ser una lista de acceso en la configuración del dispositivo VPN. **PolicyBased** es compatible con Azure, pero no con MDC. 
- **RouteBased** : las VPN basadas en enrutamiento usan rutas configuradas en la tabla de enrutamiento o reenvío de IP. Las rutas dirigen los paquetes a sus interfaces de túnel correspondientes. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o el selector de tráfico para las VPN **RouteBased** se configuran como una conectividad universal (también se pueden usar caracteres comodín). De forma predeterminada, no se puede cambiar. El valor de un tipo de VPN **RouteBased** es **VPN** .

### <a name="configuring-a-vpn-gateway"></a>Configuración de una puerta de enlace de VPN

Una conexión de puerta de enlace de VPN se basa en varios recursos con una configuración específica. La mayoría de los recursos puede configurarse de manera independiente, pero en algunos casos deben estar configurados según un orden determinado.

#### <a name="settings"></a>Configuración

La configuración que ha elegido para cada recurso es fundamental para crear una conexión correcta.

Este artículo le ayudará a conocer los siguientes elementos:

- Tipos de puerta de enlace, tipos de VPN y tipos de conexión.
- Subredes de puerta de enlace, puertas de enlace de red locales y otras configuraciones de recursos que quiera considerar.

#### <a name="connection-topology-diagrams"></a>Diagramas de topologías de conexión

Hay distintas configuraciones disponibles para las conexiones de puerta de enlace de VPN. Es preciso determinar qué configuración es la que mejor se adapta a sus necesidades. En las secciones siguientes, puede ver diagramas de topología e información sobre las conexiones de puerta de enlace de VPN siguientes:

- Modelo de implementación disponible
- Herramientas de configuración disponibles
- Vínculos que llevan directamente a un artículo, si lo hay

Los diagramas y las descripciones de las siguientes secciones pueden ayudarle a seleccionar la topología de conexión que mejor se ajuste a sus requisitos. Los diagramas muestran las principales topologías de referencia, pero también se pueden crear configuraciones más complejas con los diagramas como guía.

#### <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Sitio a sitio y multisitio (túnel VPN de IPsec/IKE)

##### <a name="site-to-site"></a>De sitio a sitio

Una conexión de puerta de enlace de VPN de sitio a sitio (S2S) es una conexión a través de un túnel VPN IPsec/IKE (IKEv2). Este tipo de conexión requiere un dispositivo VPN que esté ubicado en el entorno local y tenga asignada una dirección IP pública. Este dispositivo no puede estar detrás de un NAT. Se pueden usar conexiones S2S para las configuraciones híbridas y entre locales.

##### <a name="multi-site"></a>Multisitio

Una conexión multisitio es una variación de la conexión de sitio a sitio. Puede crear más de una conexión VPN desde la puerta de enlace de red virtual, normalmente conectándose a varios sitios locales. Cuando trabaje con varias conexiones, debe usar un tipo de VPN basada en ruta (conocida como puerta de enlace dinámica al trabajar con redes virtuales clásicas). Como cada red virtual solo puede tener una puerta de enlace de red virtual, todas las conexiones a través de la puerta de enlace comparten el ancho de banda disponible.

### <a name="gateway-skus"></a>SKU de puerta de enlace

Al crear una puerta de enlace de red virtual para MDC, hay que especificar la SKU de la puerta de enlace que desea usar. Se admiten las siguientes SKU de VPN Gateway:

- Básico
- Estándar
- Alto rendimiento

La selección de una SKU de puerta de enlace superior asigna más CPU y ancho de banda de red a la puerta de enlace. Como consecuencia, la puerta de enlace puede admitir un mayor rendimiento de red a la red virtual.

MDC no admite la SKU de puerta de enlace de ultraalto rendimiento, que se utiliza exclusivamente con ExpressRoute. Tenga en cuenta la siguiente información cuando seleccione la SKU:

- MDC no admite puertas de enlace basadas en directivas.
- BGP no es compatible con la SKU básica.
- Las configuraciones de la coexistencia de ExpressRoute-VPN Gateway no se admiten en MDC.

#### <a name="gateway-availability"></a>Disponibilidad de la puerta de enlace

Los escenarios de alta disponibilidad solo se pueden configurar en la SKU de conexión Puerta de enlace de alto rendimiento. A diferencia de Azure, que proporciona disponibilidad a través de las configuraciones activa/activa y activa/pasiva, MDC solo admite la configuración activa/pasiva.

#### <a name="failover"></a>Conmutación por error

Hay tres VM de infraestructura de puerta de enlace multiinquilino en MDC. Dos de estas VM están en modo activo y la tercera está en modo redundante. Las VM activas permite la creación de conexiones VPN en ellas y la VM redundante solo acepta conexiones VPN en el caso de una conmutación por error. Si una VM de puerta de enlace activa no está disponible, la conexión VPN conmuta por error a la VM redundante tras un breve período (unos segundos) de pérdida de conexión.

### <a name="estimated-aggregate-throughput-by-sku"></a>Rendimiento agregado estimado por SKU

En la tabla siguiente se muestran los tipos de puerta de enlace y el rendimiento agregado estimado por SKU de puerta de enlace:

| | **Rendimiento de VPN Gateway (1)** | **Túneles IPsec máx. de VPN Gateway (2)** |
|-|--------------------------------|---------------------------------------|
| SKU básica (3) | 100 Mbps | 20 |
| SKU Estándar | 100 Mbps | 20 |
| SKU de alto rendimiento | 200 Mbps | 10 |

Notas de la tabla

(1) - El rendimiento de la VPN no está garantizado para las conexiones entre locales a través de Internet. Es el valor máximo posible del rendimiento.  
(2) - El número de túneles máximo es el total por cada implementación de MDC para todas las suscripciones.  
(3) - El enrutamiento de BGP no es compatible con la SKU básica.

>[!IMPORTANT]
> Solo se puede crear una conexión VPN de sitio a sitio entre dos implementaciones de MDC. Esto se debe a una limitación en la plataforma, que no permitirá más de una conexión VPN a la misma dirección IP. Dado que MDC aprovecha la puerta de enlace multiinquilino que usa una sola dirección IP pública para todas las puertas de enlace de la VPN del sistema de MDC, no puede haber más de una conexión VPN entre dos sistemas de MDC. 
>
> Esta limitación se aplica también a la existencia de más de una conexión VPN de sitio a sitio a cualquier puerta de enlace de VPN que use una única dirección IP. MDC no permitirá que se cree más de un recurso de puerta de enlace de red local con la misma dirección IP.

### <a name="ipsecike-parameters"></a>Parámetros de IPsec/IKE

Cuando se configura una conexión VPN en MDC, es preciso configurar la conexión en ambos extremos. Si está configurando una conexión VPN entre MDC y un dispositivo de hardware, ese dispositivo podría solicitarle configuración adicional. Por ejemplo, un conmutador o un enrutador que actúe como puerta de enlace de VPN.

A diferencia de Azure, que admite varias ofertas como iniciador y respondedor, MDC admite solo una de forma predeterminada. Si tiene que usar distintas configuraciones IPSec/IKE para trabajar con el dispositivo VPN, hay más opciones de parámetros disponibles para configurar la conexión manualmente.

#### <a name="ike-phase-1-main-mode-parameters"></a>Parámetros de la fase 1 de IKE (Modo principal)

| **Propiedad** | **Valor** |
|--------------|-----------|
| Versión de IKE | IKEv2 |
| Grupo Diffie-Hellman | ECP384 |
| Método de autenticación | Clave previamente compartida |
| Algoritmos de cifrado y hash | AES256, SHA384 |
| Vigencia de SA (tiempo) | 28.800 segundos |

#### <a name="ike-phase-2-quick-mode-parameters"></a>Parámetros de la fase 2 de IKE (modo rápido)

| **Propiedad** | **Valor** |
|--------------|-----------|
| Versión de IKE | IKEv2 |
| Cifrados y algoritmos hash (cifrado) | GCMAES256 |
| Cifrados y algoritmos hash (autenticación) | GCMAES256 |
| Vigencia de SA (tiempo) | 27 000 segundos |
| Vigencia de SA (Kilobytes) | 33 553 408 |
| Confidencialidad directa perfecta (PFS) | ECP384 |
| Detección de cuellos del mismo nivel | Compatible |

### <a name="configure-custom-ipsecike-connection-policies"></a>Configuración de directivas de conexión IPsec/IKE personalizadas

El estándar del protocolo IPsec e IKE admite una gran variedad de algoritmos criptográficos en diversas combinaciones. Para ver qué parámetros se admiten en MDC, consulte para satisfacer los requisitos de cumplimiento o seguridad, consulte Parámetros de IPsec/IKE.

En este artículo se proporcionan instrucciones para crear y configurar una directiva de IPsec o IKE y aplicarla a una conexión nueva o existente.

#### <a name="considerations"></a>Consideraciones

Tenga en cuenta las siguientes consideraciones importantes al utilizar estas directivas:

- La directiva de IPsec o IKE solo funciona en los SKU de puerta de enlace Estándar y HighPerformance (basadas en rutas).
- Solo se puede especificar una combinación de directivas para una conexión dada.
- Es preciso especificar todos los algoritmos y parámetros de IKE (modo principal) e IPsec (modo rápido). No se permite la especificación de una directiva parcial.
- Consulte las especificaciones del proveedor de dispositivos VPN para asegurarse de que los dispositivos VPN locales admiten la directiva. No se pueden establecer conexiones de sitio a sitio si las directivas son incompatibles.

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>Flujo de trabajo para crear y establecer una directiva de IPsec o IKE

En esta sección se describe el flujo de trabajo necesario para crear y actualizar la directiva de IPsec o IKE en una conexión VPN de sitio a sitio:

1. Cree una red virtual y una puerta de enlace de VPN.
2. Cree una puerta de enlace de red local para la conexión entre locales.
3. Cree una directiva de IPsec o IKE con los algoritmos y parámetros seleccionados.
4. Cree una conexión de IPSec con la directiva de IPsec o IKE.
5. Agregue, actualice o quite una directiva de IPsec o IKE para una conexión existente.

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>Algoritmos criptográficos y niveles de clave admitidos

En la tabla siguiente se enumeran los algoritmos criptográficos y los niveles de las claves admitidos que pueden configurar los clientes de MDC:

| **IPsec o IKEv2** | **Opciones** |
|-----------------|-------------|
| Cifrado IKEv2 | AES256, AES192, AES128, DES3, DES |
| Integridad de IKEv2 | SHA384, SHA256, SHA1, MD5 |
| Grupo DH | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, No |
| Cifrado IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, No |
| Integridad de IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupo PFS | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, No |
| Vigencia de SA QM | (Opcional: Se usan los valores predeterminados si no se especifica ningún valor) |
| | Segundos (entero; mín. 300/predeterminado 27 000 segundos) |
| | KBytes (entero; mín. 1024/predeterminado 102 400 000 KBytes) |
| Selector de tráfico | En MDC no se admiten selectores de tráfico basados en directivas. |

La configuración de su dispositivo VPN local debe coincidir o contener los siguientes algoritmos y parámetros que se especifican en la directiva de IPsec o IKE de Azure:

 - Algoritmo de cifrado de IKE (modo principal/fase 1)
 - Algoritmo de integridad de IKE (modo principal/fase 1)
 - Grupo DH (modo principal/fase 1)
 - Algoritmo de cifrado de IPsec (modo rápido/fase 2)
 - Algoritmo de integridad de IPsec (modo rápido/fase 2)
 - Grupo PFS (modo rápido/fase 2)
 - Las vigencias de SA solo son especificaciones locales y no es preciso que coincidan.

Si se GCMAES para el algoritmo de cifrado IPsec, se debe seleccionar el mismo algoritmo GCMAES y longitud de clave para la integridad de IPsec; por ejemplo, el uso de GCMAES128 para ambos.

En la tabla anterior:

 - IKEv2 corresponde al modo principal o fase 1.
 - IPsec corresponde al modo rápido o fase 2.
 - El Grupo DH especifica el grupo Diffie-Hellmen utilizado en el modo principal o fase 1.
 - El grupo PFS especifica el grupo Diffie-Hellmen utilizado en el modo rápido o fase 2.
- La vigencia de SA del modo principal de IKEv2 se fija en 28 800 segundos en las puertas de enlace de VPN de MDC.

En la tabla siguiente se muestran los grupos Diffie-Hellman admitidos en la directiva personalizada:

| **Grupo Diffie-Hellman** | **Grupo DH** | **Grupo PFS** | **Longitud de clave** |
|--------------------------|-------------|--------------|----------------|
| 1 | DHGroup1 | PFS1 | MODP de 768 bits |
| 2 | DHGroup2 | PFS2 | MODP de 1024 bits |
| 14 | DHGroup14 | PFS2048 | MODP de 2048 bits |
| | DHGroup2048 | | |
| 19 | ECP256 | ECP256 | ECP de 256 bits |
| 20 | ECP384 | ECP384 | ECP de 384 bits |
| 24 | DHGroup24 | PFS24 | MODP de 2048 bits |

### <a name="connect-mdc-to-azure-using-azure-expressroute"></a>Conexión de MDC a Azure mediante Azure ExpressRoute

#### <a name="overview-assumptions-and-prerequisites"></a>Información general, suposiciones y requisitos previos

Azure ExpressRoute permite extender las redes locales a la nube de Microsoft. Use una conexión privada proporcionada por un proveedor de conectividad. ExpressRoute no es una conexión VPN a través de la red pública de Internet.

Para más información sobre Azure ExpressRoute, consulte Información general sobre ExpressRoute.

#### <a name="assumptions"></a>Supuestos

En este artículo se da por hecho que:

- Tiene conocimientos prácticos de Azure.
- Tiene conocimientos básicos de MDC.
- Tiene conocimientos básicos de redes.

#### <a name="prerequisites"></a>Prerrequisitos

Para conectar MDC y Azure mediante ExpressRoute, debe cumplir los siguientes requisitos:

- Un circuito ExpressRoute aprovisionado a través de un proveedor de conectividad.
- Una suscripción a Azure para crear un circuito ExpressRoute y VNet en Azure.
- Un enrutador que debe:
  - Admitir conexiones VPN de sitio a sitio entre su interfaz LAN y la puerta de enlace multiinquilino de Azure Stack.
  - Admitir la creación de varios VRF (reenvío y enrutamiento virtual) si hay más de un inquilino en la implementación de MDC.

- Un enrutador que tenga:
  - Un puerto WAN conectado al circuito ExpressRoute.
  - Un puerto LAN conectado a la puerta de enlace multiinquilino del Centro de datos modular.

#### <a name="expressroute-network-architecture"></a>Arquitectura de red de ExpressRoute

En la figura siguiente se muestran los entornos de MDC y Azure después de finalizar la configuración de ExpressRoute mediante los ejemplos de este artículo:

 ![Arquitectura de red de ExpressRoute](media/network-introduction/expressroute-network-architecture-60.png) 

