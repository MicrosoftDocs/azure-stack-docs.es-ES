---
title: Problemas conocidos de Azure Stack Hub Ruggedized
description: Obtenga información sobre los problemas conocidos de las versiones de Azure Stack Hub Ruggedized.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: 4b75024a1ff747d39f4f2194d0d86c3882475dee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941313"
---
# <a name="known-issues-in-azure-stack-hub-ruggedized"></a>Problemas conocidos de Azure Stack Hub Ruggedized 

En este artículo se enumeran los problemas conocidos de Azure Stack Hub Ruggedized. La lista se actualiza conforme se identifican nuevos problemas.

## <a name="update"></a>Actualizar

Para ver los problemas conocidos con las actualizaciones de Azure Stack Hub, consulte [Solución de problemas de actualizaciones en Azure Stack Hub](../operator/azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Error de la actualización al instalar el paquete Microsoft.AzureStack.Compute.Installer en la máquina virtual de la entidad de certificación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Durante la actualización, un proceso adopta un bloqueo en el contenido nuevo que debe copiarse en la máquina virtual de la entidad de certificación. Si se produce un error en la actualización, se termina el bloqueo.
- Corrección: Reanude la actualización.
- Repetición: poco frecuente

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Comunes

## <a name="networking"></a>Redes

### <a name="network-security-groups"></a>Grupos de seguridad de red

#### <a name="denyalloutbound-rule-cannot-be-created"></a>No se puede crear la regla DenyAllOutbound

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede crear una regla **DenyAllOutbound** explícita para Internet en un grupo de seguridad de red durante la creación de una máquina virtual, ya que esto impedirá las comunicaciones necesarias para que se complete la implementación de la máquina virtual. También se denegarán las dos direcciones IP esenciales necesarias para implementar VM: IP DHCP: 169.254.169.254 e IP DNS: 168.63.129.16.

- Corrección: Permita el tráfico saliente a Internet durante la creación de la máquina virtual y modifique el grupo de seguridad de red para bloquear el tráfico necesario una vez completada la creación de la máquina virtual.
- Repetición: Comunes

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de documentación de la página de introducción a la Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack Hub. Use los vínculos siguientes para consultar la documentación de Azure Stack Hub:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](../operator/azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer dirige el tráfico a una máquina virtual de back-end en escenarios específicos

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: Al habilitar la **afinidad de sesión** en un equilibrador de carga, el hash de tupla 2 emplea la dirección IP física en lugar de las IP privadas asignadas a las máquinas virtuales. En escenarios en los que el tráfico dirigido al equilibrador de carga llega a través de una VPN, o si todas las máquinas virtuales cliente (IP de origen) residen en el mismo nodo y la afinidad de sesión está habilitada, todo el tráfico se dirige a una máquina virtual de back-end.
- Repetición: Comunes
