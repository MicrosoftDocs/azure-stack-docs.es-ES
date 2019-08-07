---
title: Configuración de VPN Gateway para Azure Stack | Microsoft Docs
description: Obtenga información y configure las instancias de VPN Gateway para Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 53a423ebc8e9f503934bfd3df2f4962a7b584059
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658583"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack"></a>Configuración de VPN Gateway para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Una puerta de enlace de VPN es un tipo de puerta de enlace de red virtual que envía tráfico cifrado entre una red virtual de Azure Stack y una puerta de enlace de VPN remota. La puerta de enlace de VPN remota puede estar en Azure, un dispositivo del centro de datos o un dispositivo de otro sitio. Si hay conectividad de red entre los dos puntos de conexión, puede establecer una conexión VPN de sitio a sitio (S2S) segura entre las dos redes.

Una conexión de puerta de enlace de VPN se basa en la configuración de varios recursos, cada uno de los cuales contiene valores configurables. En este artículo se describen los recursos y la configuración relacionados con VPN Gateway para una red virtual que se crea en el modelo de implementación de Resource Manager. Puede encontrar las descripciones y los diagramas de topología de cada solución de conexión en el artículo [About VPN gateway for Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md) (Acerca VPN Gateway para Azure Stack).

## <a name="vpn-gateway-settings"></a>Configuración de VPN Gateway

### <a name="gateway-types"></a>Tipos de puerta de enlace

Cada red virtual de Azure Stack es compatible con una puerta de enlace de red virtual individual, que debe ser del tipo **Vpn**.  Esta compatibilidad difiere de la de Azure, que admite tipos adicionales.

Al crear una puerta de enlace de red virtual, debe asegurarse de que el tipo de puerta de enlace es el correcto para su configuración. Una puerta de enlace de VPN requiere la marca `-GatewayType Vpn`, por ejemplo:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>SKU de puerta de enlace

Al crear una puerta de enlace de red virtual, debe especificar la SKU de la puerta de enlace que desea usar. Seleccione las SKU que cumplan sus requisitos en función de los tipos de cargas de trabajo, rendimientos, características y Acuerdos de Nivel de Servicio.

Azure Stack ofrece las SKU de VPN Gateway que aparecen en la siguiente tabla.

| | Rendimiento de VPN Gateway |Túneles IPsec máximos de VPN Gateway |
|-------|-------|-------|
|**SKU básica**  | 100 Mbps  | 20    |
|**SKU estándar**   | 100 Mbps  | 20 |
|**SKU de alto rendimiento** | 200 Mbps | 10 |

### <a name="resizing-gateway-skus"></a>Cambio de tamaño de las SKU de puerta de enlace

Azure Stack no admite un cambio de tamaño de las SKU entre las SKU heredadas admitidas.

De igual modo, Azure Stack no admite un cambio de tamaño de una SKU heredada compatible (**Básico**, **Estándar** y **Alto rendimiento**) a una SKU más reciente compatible con Azure (**VpnGw1**, **VpnGw2** y **VpnGw3**).

### <a name="configure-the-gateway-sku"></a>Configuración de la SKU de puerta de enlace

#### <a name="azure-stack-portal"></a>Portal de Azure Stack

Si usa el portal de Azure Stack para crear una puerta de enlace de red virtual de Resource Manager, puede seleccionar la SKU de la puerta de enlace mediante la lista desplegable. Las opciones corresponden con el tipo de puerta de enlace y tipo de VPN que seleccione.

#### <a name="powershell"></a>PowerShell

En el siguiente ejemplo de PowerShell se especifica `-GatewaySku` como **VpnGw1**:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Tipos de conexión

En el modelo de implementación de Resource Manager, cada configuración requiere un tipo de conexión de puerta de enlace de red virtual específico. Los valores de PowerShell de Resource Manager para `-ConnectionType` son:

* **IPsec**

   En el siguiente ejemplo de PowerShell, se crea una conexión S2S que requiere el tipo de conexión IPsec:

   ```powershell
   New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

### <a name="vpn-types"></a>Tipos de VPN

Al crear la puerta de enlace de red virtual para una configuración de puerta de enlace de VPN, debe especificar un tipo de VPN. El tipo de VPN que elija dependerá de la topología de conexión que desee crear. Un tipo de VPN también puede depender del hardware que se esté usando. Las configuraciones de S2S requieren un dispositivo VPN. Algunos dispositivos VPN solo serán compatibles con un determinado tipo de VPN.

> [!IMPORTANT]  
> En la actualidad, Azure Stack solo admite el tipo de VPN basado en ruta. Si el dispositivo solo admite VPN basadas en directiva, no se admiten conexiones a dichos dispositivos desde Azure Stack.  
>
> Además, Azure Stack no admite el uso de selectores de tráfico basados en directivas para puertas de enlace basadas en rutas en este momento, ya que las configuraciones de directivas personalizadas de IPSec/IKE no se admiten.

* **PolicyBased**: las VPN basadas en directivas cifran y dirigen los paquetes a través de túneles de IPsec basados en las directivas de IPsec configuradas con las combinaciones de prefijos de dirección entre su red local y la red virtual de Azure Stack. La directiva (o el selector de tráfico) suele ser una lista de acceso en la configuración del dispositivo VPN.

  >[!NOTE]
  >**PolicyBased** es compatible con Azure, pero no con Azure Stack.

* **RouteBased**: las VPN basadas en rutas utilizan rutas que se configuran en la dirección IP de reenvío o en la tabla de enrutamiento para dirigir los paquetes a sus correspondientes interfaces de túnel. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o el selector de tráfico para las VPN **RouteBased** se configuran como una conectividad universal (también se pueden usar caracteres comodín). De forma predeterminada, no se puede cambiar. El valor de un tipo de VPN **basada en ruta** es **RouteBased**.

En el siguiente ejemplo de PowerShell se especifica `-VpnType` como **RouteBased**. Al crear una puerta de enlace, debe asegurarse de que `-VpnType` es correcto para su configuración.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Requisitos de la puerta de enlace

La tabla siguiente enumera los requisitos de las puertas de enlace VPN.

| |VPN Gateway básica basada en directivas | VPN Gateway básica basada en rutas | VPN Gateway estándar basada en rutas | VPN Gateway de alto rendimiento basada en rutas|
|--|--|--|--|--|
| **Conectividad de sitio a sitio (conectividad S2S)** | No compatible | Configuración VPN basada en rutas | Configuración VPN basada en rutas | Configuración VPN basada en rutas |
| **Método de autenticación**  | No compatible | Clave precompartida para conectividad S2S  | Clave precompartida para conectividad S2S  | Clave precompartida para conectividad S2S  |
| **Número máximo de conexiones S2S**  | No compatible | 20 | 20| 10|
|**Compatibilidad con enrutamiento activo (BGP)** | No compatible | No compatible | Compatible | Compatible |

### <a name="gateway-subnet"></a>Subred de puerta de enlace

Antes de crear una puerta de enlace de VPN, debe crear una subred de puerta de enlace. La subred de puerta de enlace contiene las direcciones IP que usan los servicios y las máquinas virtuales de la puerta de enlace de red virtual. Al crear la puerta de enlace de red virtual, las máquinas virtuales de puerta de enlace se implementan en la subred de puerta de enlace, y se configuran con las opciones de puerta de enlace de VPN necesarias. No implemente nada más (por ejemplo, máquinas virtuales adicionales) en la subred de puerta de enlace.

>[!IMPORTANT]
>Para que la subred de puerta de enlace funcione correctamente, su nombre tiene que ser **GatewaySubnet** . Azure Stack usa este nombre para identificar la subred en la que se van a implementar las VM de la puerta de enlace de red y los servicios.

Al crear la subred de puerta de enlace, especifique el número de direcciones IP que contiene la subred. Las direcciones IP de la subred de puerta de enlace se asignan a las máquinas virtuales y los servicios de puerta de enlace. Algunas configuraciones requieren más direcciones IP que otras. Consulte los requisitos de configuración que desea crear y compruebe que la subred de puerta de enlace que desea crear los cumple.

Además, debe asegurarse de que la subred de puerta de enlace tenga suficientes direcciones IP para controlar futuras configuraciones adicionales. Por lo tanto, aunque es posible crear una puerta de enlace tan pequeña como /29, se recomienda que sea de /28 o mayor (/28, /27, /26, etc.). De este modo, si agrega funcionalidad en el futuro, no tendrá que desconectar la puerta de enlace y luego eliminar y volver a crear la subred de puerta de enlace para que admita más direcciones IP.

En el ejemplo de PowerShell de Resource Manager siguiente, se muestra una subred de puerta de enlace con el nombre **GatewaySubnet**. Puede ver que la notación CIDR especifica /27, que permite suficientes direcciones IP para la mayoría de las configuraciones que existen.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Cuando trabaje con subredes de la puerta de enlace, evite asociar un grupo de seguridad de red (NSG) a la subred de la puerta de enlace. La asociación de un grupo de seguridad de red a esta subred puede causar que la puerta de enlace de VPN deje de funcionar como cabría esperar. Para más información acerca de los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](/azure/virtual-network/virtual-networks-nsg)

### <a name="local-network-gateways"></a>Puertas de enlace de red local

Al crear una configuración de puerta de enlace de VPN en Azure, la puerta de enlace de red local a menudo representa la ubicación local. En Azure Stack representa cualquier dispositivo VPN remoto que se encuentra fuera de Azure Stack. Este podría ser un dispositivo VPN de su centro de datos (o un centro de datos remoto) o VPN Gateway en Azure.

Asigne un nombre a la puerta de enlace de red local, así como la dirección IP pública del dispositivo VPN y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure examina los prefijos de dirección de destino para el tráfico de red, consulta la configuración que especificó para la puerta de enlace de red local y enruta los paquetes según corresponda.

En el ejemplo siguiente de PowerShell, se crea una nueva puerta de enlace de red local:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

A veces es necesario modificar la configuración de la puerta de enlace de red local; por ejemplo, al agregar o modificar el intervalo de direcciones, o si cambia la dirección IP del dispositivo VPN. Para obtener más información, consulte [Modificación de la configuración de la puerta de enlace de red local mediante PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parámetros de IPsec/IKE

Cuando se configura una conexión VPN en Azure Stack, es preciso configurar la conexión en ambos extremos. Si va a configurar una conexión VPN entre Azure Stack y un dispositivo de hardware, como un conmutador o un enrutador que actúa como VPN Gateway, dicho dispositivo puede pedirle más valores.

A diferencia de Azure, que admite varias ofertas como iniciador y respondedor, Azure Stack admite solo una de forma predeterminada.  Si tiene que usar distintas configuraciones IPSec/IKE para trabajar con el dispositivo VPN, hay más opciones de parámetros disponibles para configurar la conexión manualmente.  Para obtener más información, consulte [Configuración de la directiva IPsec/IKE para conexiones VPN de sitio a sitio](azure-stack-vpn-s2s.md).

### <a name="ike-phase-1-main-mode-parameters"></a>Parámetros de la fase 1 de IKE (Modo principal)

| Propiedad              | Valor|
|-|-|
| Versión de IKE           | IKEv2 |
|Grupo Diffie-Hellman   | Grupo 2 (1024 bits) |
| Método de autenticación | Clave previamente compartida |
|Algoritmos de cifrado y hash | AES256, SHA256 |
|Vigencia de SA (tiempo)     | 28.800 segundos|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parámetros de la fase 2 de IKE (modo rápido)

| Propiedad| Valor|
|-|-|
|Versión de IKE |IKEv2 |
|Cifrados y algoritmos hash (cifrado)     | GCMAES256|
|Cifrados y algoritmos hash (autenticación) | GCMAES256|
|Vigencia de SA (tiempo)  | 27 000 segundos  |
|Vigencia de SA (Kilobytes) | 33 553 408     |
|Confidencialidad directa perfecta (PFS) |Ninguno (consulte la Nota 1) |
|Detección de cuellos del mismo nivel | Compatible|  

* *Nota 1*:  Antes de la versión 1807, Azure Stack usaba un valor de PFS2048 para la confidencialidad directa perfecta (PFS).

## <a name="next-steps"></a>Pasos siguientes

* [Conexión mediante ExpressRoute](../operator/azure-stack-connect-expressroute.md)
