---
title: Configuración de conexiones VPN de sitio a sitio de IPsec/IKE en Azure Stack Hub
description: Obtenga información acerca de la directiva de IPsec o IKE para conexiones de red virtual a red virtual o VPN de sitio a sitio en Azure Stack Hub y configúrela.
author: sethmanheim
ms.custom: contperfq4
ms.topic: article
ms.date: 05/21/2020
ms.author: sethm
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: fdc1f71e5d4c5afa8b3989b69795d150cf96de67
ms.sourcegitcommit: d69eacbf48c06309b00d17c82ebe0ce2bc6552df
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780677"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Configuración de la directiva IPsec/IKE para conexiones VPN de sitio a sitio

En este artículo se describen los pasos para configurar una directiva de IPsec o IKE para conexiones VPN de sitio a sitio (S2S) en Azure Stack Hub.

>[!NOTE]
> Debe ejecutar la compilación **1809** de Azure Stack Hub, o posterior, para poder usar esta función.  Si actualmente ejecuta una versión anterior a 1809, actualice el sistema Azure Stack Hub a la versión más reciente antes de seguir los pasos descritos en este artículo.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Parámetros de la directiva de IPsec e IKE para puertas de enlace de VPN

El estándar del protocolo IPsec e IKE admite una gran variedad de algoritmos criptográficos en diversas combinaciones. Para ver qué parámetros se admiten en Azure Stack Hub, consulte [Parámetros de IPsec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), lo que puede ayudarle a cumplir los requisitos de cumplimiento o seguridad.

En este artículo se proporcionan instrucciones para crear y configurar una directiva de IPsec o IKE y aplicarla a una conexión nueva o existente.

## <a name="considerations"></a>Consideraciones

Tenga en cuenta las siguientes consideraciones importantes al utilizar estas directivas:

- La directiva de IPsec o IKE solo funciona en los SKU de puerta de enlace *Estándar* y *HighPerformance* (basadas en rutas).

- Solo se puede especificar una combinación de directivas para una conexión dada.

- Es preciso especificar todos los algoritmos y parámetros de IKE (modo principal) e IPsec (modo rápido). No se permite la especificación de una directiva parcial.

- Consulte las especificaciones del proveedor de dispositivos VPN para asegurarse de que los dispositivos VPN locales admiten la directiva. No se pueden establecer conexiones de sitio a sitio si las directivas son incompatibles.

### <a name="prerequisites"></a>Prerrequisitos

Asegúrese de que cumple los siguientes requisitos previos antes de empezar:

- Suscripción a Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Los cmdlets de PowerShell de Azure Resource Manager. Consulte [Instalación de PowerShell para Azure Stack Hub](../operator/azure-stack-powershell-install.md) para más información sobre la instalación de los cmdlets de PowerShell.

## <a name="part-1---create-and-set-ipsecike-policy"></a>Parte 1: Creación y establecimiento de una directiva de IPsec o IKE

En esta sección se describen los pasos necesarios para crear y actualizar la directiva de IPsec o IKE en una conexión VPN de sitio a sitio:

1. Cree una red virtual y una puerta de enlace de VPN.

2. Cree una puerta de enlace de red local para la conexión entre locales.

3. Cree una directiva de IPsec o IKE con los algoritmos y parámetros seleccionados.

4. Cree una conexión de IPSec con la directiva de IPsec o IKE.

5. Agregue, actualice o quite una directiva de IPsec o IKE para una conexión existente.

Las instrucciones incluidas en este artículo le ayudan a instalar y configurar directivas de IPsec o IKE como se muestra en la siguiente figura:

![Instalación y configuración de directivas de IPsec o IKE](media/azure-stack-vpn-s2s/site-to-site.svg)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Parte 2: algoritmos criptográficos y niveles de clave admitidos

En la tabla siguiente se enumeran los algoritmos criptográficos y los niveles de clave admitidos que pueden configurar los clientes de Azure Stack Hub:

| IPsec/IKEv2                                          | Opciones                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Cifrado IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Integridad de IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Grupo DH                                             | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2, DHGroup1                 |
| Cifrado IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, No |
| Integridad de IPsec                                      | GCMASE256, GCMAES192, GCMAES128                                          |
| Grupo PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, PFSMM, Ningún                  |
| Vigencia de SA QM                                       | (Opcional: Se usan los valores predeterminados si no se especifica ningún valor)<br />                         Segundos (entero; mín. 300/predeterminado 27 000 segundos)<br />                         KBytes (entero; mín. 1024/predeterminado 102 400 000 KBytes) |
| Selector de tráfico                                     | En Azure Stack Hub no se admiten selectores de tráfico basados en directivas.         |

- La configuración de su dispositivo VPN local debe coincidir o contener los siguientes algoritmos y parámetros que se especifican en la directiva de IPsec o IKE de Azure:

  - Algoritmo de cifrado de IKE (modo principal/fase 1)
  - Algoritmo de integridad de IKE (modo principal/fase 1).
  - Grupo DH (modo principal/fase 1).
  - Algoritmo de cifrado de IPsec (modo rápido/fase 2).
  - Algoritmo de integridad de IPsec (modo rápido/fase 2).
  - Grupo PFS (modo rápido/fase 2).
  - Las vigencias de SA solo son especificaciones locales y no es preciso que coincidan.

- Si se usa GCMAES para el algoritmo de cifrado IPsec, se debe seleccionar el mismo algoritmo GCMAES y longitud de clave para la integridad de IPsec; por ejemplo, el uso de GCMAES128 para ambos.

- En la tabla anterior:

  - IKEv2 corresponde al modo principal o fase 1.
  - IPsec corresponde al modo rápido o fase 2.
  - El Grupo DH especifica el grupo Diffie-Hellmen utilizado en el modo principal o fase 1.
  - El grupo PFS especifica el grupo Diffie-Hellmen utilizado en el modo rápido o fase 2.

- La vigencia de SA del modo principal de IKEv2 se fija en 28 800 segundos en las puertas de enlace de VPN de Azure Stack Hub.

En la tabla siguiente se muestran los grupos Diffie-Hellman admitidos en la directiva personalizada:

| Grupo Diffie-Hellman | DHGroup   | PFSGroup      | Longitud de la clave    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | MODP de 768 bits  |
| 2                    | DHGroup2  | PFS2          | MODP de 1024 bits |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | MODP de 2048 bits |
| 19                   | ECP256    | ECP256        | ECP de 256 bits   |
| 20                   | ECP384    | ECP384        | ECP de 384 bits   |
| 24                   | DHGroup24 | PFS24         | MODP de 2048 bits |

Para más información, consulte [RFC3526](https://tools.ietf.org/html/rfc3526) y [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Parte 3: crear una nueva conexión VPN de sitio a sitio con una directiva de IPsec o IKE

En esta sección se describen los pasos necesarios para crear una conexión VPN de sitio a sitio con una directiva de IPsec o IKE. Los pasos siguientes permiten crear la conexión, como se muestra en la figura siguiente:

![directiva de sitio a sitio](media/azure-stack-vpn-s2s/site-to-site.svg)

Consulte [Creación de una conexión VPN de sitio a sitio](/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell) para obtener instrucciones detalladas sobre cómo crear una conexión VPN de sitio a sitio.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Paso 1: crear la red virtual, la puerta de enlace de VPN y la puerta de enlace de red local

#### <a name="1-declare-variables"></a>1. Declaración de variables

Para este ejercicio, empiece por declarar las siguientes variables. Asegúrese de reemplazar los marcadores de posición por sus propios valores cuando realice la configuración para el entorno de producción:

```powershell
$Sub1 = "<YourSubscriptionName>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Conexión a su suscripción y creación de un nuevo grupo de recursos

Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del Administrador de recursos. Para más información, consulte [Conexión a Azure Stack Hub con PowerShell como usuario](azure-stack-powershell-configure-user.md).

Abra la consola de PowerShell y conéctese a su cuenta. Por ejemplo:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Creación de la red virtual, la puerta de enlace de VPN y la puerta de enlace de red local

En el ejemplo siguiente se crea la red virtual **TestVNet1** con tres subredes y la puerta de enlace VPN. Al reemplazar valores, es importante que asigne específicamente el nombre **GatewaySubnet** a la subred de la puerta de enlace. Si usa otro, se produce un error al crear la puerta de enlace.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 `
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 `
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix `
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Paso 2: Creación de una conexión VPN de sitio a sitio con una directiva de IPsec o IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Cree una directiva IPsec/IKE.

Este script de ejemplo crea una directiva de IPsec o IKE con los algoritmos y parámetros siguientes:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, no, vigencia de SA de 14 400 segundos y 102 400 000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Si usa GCMAES para IPsec, debe usar el mismo algoritmo GCMAES y longitud de clave para la integridad y el cifrado IPsec.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Creación de la conexión VPN de sitio a sitio con la directiva de IPsec o IKE

Cree una conexión VPN de sitio a sitio y aplique la directiva de IPsec o IKE creada anteriormente:

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Una vez que se haya especificado una directiva de IPsec o IKE en una conexión, la puerta de enlace de VPN de Azure solo enviará o aceptará la propuesta de IPsec o IKE con los algoritmos criptográficos y los niveles de clave especificados en esa conexión en particular. Asegúrese de que el dispositivo VPN local para la conexión usa o acepta la combinación de directivas exacta. En caso contrario, no se podrá establecer el túnel VPN de sitio a sitio.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Parte 4: actualizar una directiva de IPsec o IKE para una conexión

En la sección anterior se ha descrito cómo administrar la directiva de IPsec o IKE para una conexión de sitio a sitio existente. Esta sección explica cómo se realizan las siguientes operaciones en una conexión:

- Mostrar la directiva de IPsec o IKE de una conexión
- Agregar o actualizar la directiva de IPsec o IKE para una conexión
- Eliminar la directiva de IPsec o IKE de una conexión

> [!NOTE]
> La directiva de IPsec o IKE solo se admite en las puertas de enlace de VPN basadas en rutas *Standard* y *HighPerformance*. No funciona en el SKU de puerta de enlace *Básico*.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrar la directiva de IPsec o IKE de una conexión

En el ejemplo siguiente se muestra cómo obtener la directiva de IPsec o IKE configurada en una conexión. Los scripts son una continuación de los ejercicios anteriores:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

El último comando muestra la directiva de IPsec o IKE actual configurada en la conexión, si existe. La siguiente es una salida de ejemplo para la conexión:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Si no hay ninguna directiva de IPsec o IKE configurada, el comando `$connection6.policy` obtiene un valor devuelto vacío. Esto no significa que IPsec o IKE no esté configurado en la conexión, sino que no hay ninguna directiva de IPsec o IKE personalizada. La conexión real usa la directiva predeterminada que se negocia entre el dispositivo VPN local y Azure VPN Gateway.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Agregar o actualizar una directiva de IPsec o IKE para una conexión

Los pasos para agregar una nueva directiva o actualizar una directiva existente en una conexión son los mismos: crear una directiva y, después, aplicar la nueva directiva a la conexión:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Puede obtener la conexión de nuevo para comprobar si la directiva está actualizada:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Debería ver la salida de la última línea tal como se muestra en el ejemplo siguiente:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Eliminar una directiva de IPsec o IKE de una conexión

Una vez que se quite la directiva personalizada de una conexión, Azure VPN Gateway vuelve a la [propuesta predeterminada de IPsec o IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters) y vuelve a negociar con el dispositivo VPN local.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = "AzS123"
$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Puede usar el mismo script para comprobar si la directiva se ha quitado de la conexión.

## <a name="next-steps"></a>Pasos siguientes

- [Valores de la configuración de una puerta de enlace VPN para Azure Stack Hub](azure-stack-vpn-gateway-settings.md)
