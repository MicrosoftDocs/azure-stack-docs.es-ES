---
title: Solución de problemas de conexiones VPN de sitio a sitio en Azure Stack Hub
description: Pasos para la solución de problemas que puede realizar después de configurar una conexión VPN de sitio a sitio entre una red local y una red virtual de Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 11/22/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: a39cbd5a845e79114393710c8ec77d37f30a68a0
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243517"
---
# <a name="troubleshoot-site-to-site-vpn-connections"></a>Solución de problemas de conexiones VPN de sitio a sitio

En este paso se describen los pasos que se pueden dar para solucionar los distintos problemas a que puede deberse que después de configurar una conexión VPN de sitio a sitio (S2S) entre una red local y una red virtual de Azure Stack Hub, la conexión de repente deje de funcionar y no se pueda volver a conectar.

Si su problema con Azure Stack Hub no se trata en este artículo, puede visitar el [foro de Azure Stack Hub en MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

También puede enviar una solicitud de soporte técnico de Azure. Consulte la sección sobre el [soporte técnico de Azure Stack Hub](../operator/azure-stack-manage-basics.md#where-to-get-support).

> [!NOTE]
> Solo se puede crear una conexión VPN de sitio a sitio entre dos implementaciones de Azure Stack Hub. Esto se debe a una limitación en la plataforma, que no permitirá más de una conexión VPN a la misma dirección IP. Dado que Azure Stack Hub aprovecha la puerta de enlace multiinquilino que usa una sola dirección IP pública para todas las puertas de enlace de la VPN del sistema de Azure Stack Hub, no puede haber más de una conexión VPN entre dos sistemas de Azure Stack Hub. Esta limitación se aplica también a la existencia de más de una conexión VPN de sitio a sitio a cualquier puerta de enlace de VPN que use una única dirección IP. Azure Stack Hub no permitirá que se cree más de un recurso de puerta de enlace de red local con la misma dirección IP.

## <a name="initial-troubleshooting-steps"></a>Pasos iniciales para la solución de problemas

Los parámetros predeterminados de Azure Stack Hub para IPsec/IKEV2 han cambiado [a partir de la compilación 1910](../user/azure-stack-vpn-gateway-settings.md#ike-phase-1-main-mode-parameters). Para más información sobre la versión de la compilación, póngase en contacto con un operador de Azure Stack Hub.

> [!IMPORTANT]
> Si se usa un túnel S2S, los paquetes se encapsulan con encabezados adicionales, lo que aumenta el tamaño global del paquete. En estos escenarios, debe fijar TCP **MSS** en **1350**. Si los dispositivos VPN no admiten la fijación de MSS, puede establecer la opción MTU en la interfaz de túnel en **1400** bytes. Para más información, consulte [Optimización del rendimiento de TCP/IP de la red virtual](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

- Confirme que la configuración de VPN se basa en rutas (IKEv2). Azure Stack Hub no admite configuraciones basadas en directivas (IKEv1).

- Compruebe si está usando una [versión del sistema operativo y dispositivo VPN validada](/azure/vpn-gateway/vpn-gateway-about-vpn-devices#devicetable). Si el dispositivo no es un dispositivo VPN validado, tendrá que ponerse en contacto con el fabricante del dispositivo para ver si hay algún problema de compatibilidad.

- Compruebe que no haya intervalos IP superpuestos entre la red virtual de Azure Stack Hub y la red local. Esto puede producir problemas de conectividad. 

- Compruebe las direcciones IP de VPN del mismo nivel:

  - La definición de IP en el objeto **Puerta de enlace de red local** de Azure Stack Hub debe coincidir con la dirección IP del dispositivo local.

  - La definición de la IP de la puerta de enlace de Azure Stack Hub establecida en el dispositivo local debe coincidir con la dirección IP de la puerta de enlace de Azure Stack Hub.

## <a name="status-not-connected---intermittent-disconnects"></a>Estado "No conectado": desconexiones intermitentes

### <a name="az-modules"></a>[Modules de Az](#tab/az)

- Compare la clave compartida del dispositivo VPN local y la de la VPN de la red virtual de AzSH para asegurarse de que coinciden. Para ver la clave compartida de la conexión de la VPN de AzSH, use uno de los siguientes métodos:

  - **Portal de inquilino de Azure Stack Hub**: Vaya a la conexión de sitio a sitio de la puerta de enlace VPN que ha creado. En la sección **Settings** (Configuración), seleccione **Shared key** (Clave compartida).

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="Conexión VPN":::

  - **Azure PowerShell**: Use el siguiente comando de PowerShell:

```powershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm)

- Compare la clave compartida del dispositivo VPN local y la de la VPN de la red virtual de AzSH para asegurarse de que coinciden. Para ver la clave compartida de la conexión de la VPN de AzSH, use uno de los siguientes métodos:

  - **Portal de inquilino de Azure Stack Hub**: Vaya a la conexión de sitio a sitio de la puerta de enlace VPN que ha creado. En la sección **Settings** (Configuración), seleccione **Shared key** (Clave compartida).

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="Conexión VPN":::

  - **Azure PowerShell**: Use el siguiente comando de PowerShell:

```powershell
Get-AzurerRMVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
```

---

## <a name="status-connected---traffic-not-flowing"></a>Estado "Conectado": el tráfico no fluye

- Busque y quite el enrutamiento definido por el usuario (UDR) y los grupos de seguridad de red (NSG) de la subred de puerta de enlace y, después, pruebe el resultado. Si se resuelve el problema, valide la configuración de NSG o UDR aplicada.

   Una ruta definida por el usuario en la subred de puerta de enlace puede restringir un tipo de tráfico y permitir otro. Esto hace que parezca que la conexión VPN no es de confianza para cierto tráfico, pero que lo es para otro tipo.

- Compruebe la dirección de la interfaz externa del dispositivo VPN local. 

  - Si la dirección IP accesible desde Internet del dispositivo VPN está incluida en la definición de **Red local** en Azure Stack Hub, es posible que sufra desconexiones esporádicas.

  - La interfaz externa del dispositivo debe estar directamente en Internet. No debería haber ninguna traducción de direcciones de red ni firewall entre Internet y el dispositivo.

  - Para configurar la agrupación en clústeres del firewall para que tenga una IP virtual, debe interrumpir el clúster y exponer el dispositivo VPN directamente a una interfaz pública con la que la puerta de enlace pueda interactuar.

- Compruebe que las subredes coinciden exactamente.

  - Compruebe que los espacios de direcciones de red virtual coinciden exactamente entre la red virtual de Azure Stack Hub y las definiciones locales.

  - Compruebe que las subredes coinciden exactamente entre la **puerta de enlace de la red local** y las definiciones locales para la red local.

## <a name="create-a-support-ticket"></a>Creación de una incidencia de soporte técnico

Si ninguno de los pasos anteriores soluciona el problema, cree una [incidencia de soporte técnico](../operator/azure-stack-manage-basics.md#where-to-get-support) y use la [herramienta de recopilación de registros a petición](../operator/diagnostic-log-collection.md) para proporcionar registros.