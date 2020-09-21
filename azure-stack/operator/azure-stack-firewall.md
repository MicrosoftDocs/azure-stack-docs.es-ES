---
title: Integración de un firewall con un sistema de Azure Stack Hub
description: Más información sobre la integración del firewall de Azure Stack Hub para sistemas integrados de Azure Stack Hub
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 666d205fb99ac309196bd4b84032ef8c7877ab8a
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572874"
---
# <a name="azure-stack-hub-firewall-integration"></a>Integración del firewall de Azure Stack Hub
Se recomienda que use un dispositivo de firewall para ayudar a proteger Azure Stack Hub. Los firewalls pueden ayudarle a defenderse contra ataques por denegación de servicio distribuido (DDOS), a detectar intrusos y a inspeccionar el contenido que reciba. Sin embargo, también pueden convertirse en un cuello de botella en el rendimiento de los servicios de Azure Storage como blobs, tablas y colas.

 Si se usa un modo de implementación desconectada, debe publicar el punto de conexión de AD FS. Para obtener más información, consulte el [artículo sobre la identidad de integración del centro de datos](azure-stack-integrate-identity.md).

Los puntos de conexión de Azure Resource Manager (administrador), el portal de administración y el almacén de claves (administrador) no requieren necesariamente publicación externa. Por ejemplo, como proveedor de servicios, podría interesarle limitar la superficie expuesta a ataques y administrar Azure Stack Hub solo desde la red y no desde Internet.

Para organizaciones empresariales, la red externa puede ser la red corporativa existente. En este caso, debe publicar esos puntos de conexión para trabajar con Azure Stack Hub desde la red corporativa.

### <a name="network-address-translation"></a>Traducción de direcciones de red
La traducción de direcciones de red (NAT) es el método recomendado para permitir que la máquina virtual de implementación (DVM) obtenga acceso a los recursos externos y a Internet durante la implementación, así como a las máquinas virtuales de la consola de recuperación de emergencia (ERCS) o al punto de conexión con privilegios (PEP) durante el registro y la solución de problemas.

NAT también puede ser una alternativa a las direcciones IP públicas de la red externa o VIP públicas. Sin embargo, no se recomienda, ya que limita la experiencia del usuario inquilino y aumenta la complejidad. Una opción sería una NAT con una relación de uno a uno que siga requiriendo una dirección IP pública por cada IP de usuario del grupo. Otra opción es una NAT con una relación de varios a uno que requiera una regla NAT por cada VIP de usuario de todos los puertos que ese usuario podría usar.

Algunas de las desventajas del uso de NAT para una VIP pública son:
- NAT agrega sobrecarga al administrar las reglas de firewall, ya que los usuarios controlan sus propios puntos de conexión y sus propias reglas de publicación en la pila de redes definidas por software (SDN). Los usuarios deben ponerse en contacto con el operador de Azure Stack Hub para que se publiquen sus VIP y para actualizar la lista de puertos.
- Aunque el uso de NAT limita la experiencia del usuario, ofrece un control total al operador de las solicitudes de publicación.
- En el caso de escenarios de nube híbrida con Azure, debe tener en cuenta que Azure no admite la configuración de un túnel VPN a un punto de conexión con NAT.

### <a name="ssl-interception"></a>Interceptación de SSL
En la actualidad, se recomienda deshabilitar cualquier interceptación de SSL (por ejemplo, la descarga de descifrado) en todo el tráfico de Azure Stack Hub. En caso de que se admita en actualizaciones futuras, se proporcionarán instrucciones acerca de cómo habilitar la interceptación de SSL en Azure Stack Hub.

## <a name="edge-firewall-scenario"></a>Escenario de firewall perimetral
En una implementación perimetral, Azure Stack Hub se implementa directamente detrás del firewall o el enrutador perimetral. En estos casos, se admite que el firewall se encuentre por encima del borde (Escenario 1) donde se admiten configuraciones de firewall activa/activa y activa/pasiva, o que actúe como dispositivo de borde (Escenario 2) donde solo se admite la configuración de firewall activa/activa basada en varias rutas de acceso de igual costo (ECMP) con enrutamiento estático o BGP para la conmutación por error.

Las direcciones IP enrutables públicas se especifican para el grupo de VIP públicas de la red externa en el momento de la implementación. En un escenario perimetral, no se recomienda utilizar direcciones IP enrutables públicas en ninguna otra red por motivos de seguridad. Este escenario permite que un usuario disfrute de la experiencia completa en la nube autocontrolada como si se tratara de una nube pública como Azure.  

![Ejemplo de firewall perimetral de Azure Stack Hub](./media/azure-stack-firewall/firewallScenarios.svg)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Escenario de firewall de red perimetral o de intranet empresarial
En una implementación perimetral o de intranet empresarial, Azure Stack Hub se implementa en un firewall de varias zonas o entre el firewall perimetral y el firewall de red corporativo interno. A continuación, se distribuye el tráfico entre la red perimetral (o DMZ) segura y las zonas no seguras como se describe a continuación:

- **Zona segura**: se trata de una red interna que usa direcciones IP enrutables corporativas o internas. La red segura se puede dividir, puede tener acceso de salida a Internet a través de la NAT del firewall y, normalmente, se puede acceder a ella desde cualquier punto del centro de datos a través de la red interna. Todas las redes de Azure Stack Hub deben residir en la zona segura, excepto el grupo de VIP públicas de la red externa.
- **Zona perimetral**. La red perimetral es el lugar en que se suelen implementar las aplicaciones externas o accesibles desde Internet, como los servidores web. Se suelen supervisar a través de un firewall para evitar ataques como DDoS e intrusión (acceso por parte de piratas informáticos) y permitir el tráfico entrante especificado de Internet. Solo debe residir en la zona DMZ el grupo de VIP públicas de redes externas de Azure Stack Hub.
- **Zona no segura**. Se trata de la red externa: Internet. **No** se recomienda implementar Azure Stack Hub en la zona no segura.

![Ejemplo de red perimetral de Azure Stack Hub](./media/azure-stack-firewall/perimeter-network-scenario.svg)

## <a name="learn-more"></a>Más información
Más información sobre los [puertos y protocolos que utilizan los puntos de conexión de Azure Stack Hub](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Pasos siguientes
[Requisitos de PKI de Azure Stack Hub](azure-stack-pki-certs.md)

