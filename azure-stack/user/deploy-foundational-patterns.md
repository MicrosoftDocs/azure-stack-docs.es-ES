---
title: Implementación de patrones fundamentales en Azure Stack Hub
description: Aprenda a implementar patrones fundamentales con Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 5609d9195a909a20a54917555a7309fe0901d7fa
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525479"
---
# <a name="deploy-foundational-patterns-overview"></a>Introducción a la implementación de patrones fundamentales


Cada uno de estos patrones contiene instrucciones, plantillas de Azure Resource Manager y tutoriales. Puede usar estos patrones junto con aplicaciones de terceros para crear ofertas que todavía no se admiten en Azure Stack. Por ejemplo, los operadores están acostumbrados a tratar con las dificultades que implica la configuración de una red privada virtual (VPN) en una única instancia de Azure Stack Hub, pero no lo están con respecto a la creación de una VPN que abarque dos o más entornos. Los operadores pueden experimentar problemas al intentar crear un equilibrador de carga delante de una instancia de Azure Stack Hub para administrar cargas de trabajo. Con la siguiente guía, puede acelerar el tiempo de implementación para lanzar sus cargas de trabajo listas para producción.

## <a name="networking"></a>Redes

Use los patrones de red para encontrar instrucciones sobre cómo crear el emparejamiento de red virtual con Azure Stack Hub. El emparejamiento de red virtual le permite conectar dos redes virtuales de forma que aparezcan como una sola red. La conectividad de sitio a sitio entre redes virtuales se realiza mediante el servicio de enrutamiento y acceso remoto (RRAS). RRAS permite que las máquinas virtuales Windows funcionen como enrutadores. Con estos scripts, puede implementar dos redes virtuales entre los grupos de recursos de un grupo de recursos de Azure Stack Hub, entre suscripciones y entre dos instancias de Azure Stack Hub. Puede implementar los scripts en Azure Stack Hub y en Azure global. 

Cada artículo trata aspectos comunes, como: 
- Escala
- Ancho de banda
- Seguridad
- Continuidad empresarial

|  Emparejamiento de redes virtuales de Azure  |  VPN  |  Equilibrador de carga  |
| --- | --- | --- |
| ![Emparejamiento de red virtual con máquinas virtuales](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Emparejamiento de red virtual con máquinas virtuales](azure-stack-network-howto-vnet-peering.md) | ![Configuración de VPN como local](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Configuración de VPN como local](azure-stack-network-howto-vnet-to-onprem.md) | ![Equilibrador de carga F5](media/deploy-foundational-patterns/icon-networking-62-load-balancers.svg)<br>[Equilibrador de carga F5](network-howto-f5.md) |
| ![Emparejamiento de red virtual con FortiGate](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Emparejamiento de red virtual con FortiGate](azure-stack-network-howto-vnet-to-vnet.md) | ![Red privada virtual](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Conexión de red virtual a red virtual](azure-stack-network-howto-vnet-to-vnet-stacks.md) |  |
|  | ![Creación de un túnel VPN (GRE)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Creación de un túnel VPN (GRE)](network-howto-vpn-tunnel-gre.md) | |
|  | ![Configuración de una VPN de sitio a sitio múltiple](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Configuración de una VPN de sitio a sitio múltiple](network-howto-vpn-tunnel.md) | |
|  | ![Creación de un túnel VPN (IPSEC)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Creación de un túnel VPN (IPSEC)](network-howto-vpn-tunnel-ipsec.md)| |


## <a name="storage"></a>Storage

Use los patrones de almacenamiento para aumentar las opciones de almacenamiento con Azure Stack Hub. El almacenamiento de Azure Stack Hub es finito. Conéctese a los recursos del centro de datos existente. Encuentre instrucciones para crear una máquina virtual Windows en Azure Stack hub para conectarse a un destino iSCSI externo. Puede aprender a habilitar algunas características clave, como E/S de múltiples rutas, para optimizar el rendimiento y la conectividad entre la máquina virtual y el almacenamiento externo.

| Almacenamiento iSCSI | Ampliación del almacenamiento |
| --- | --- |
| ![Conexión al almacenamiento iSCSI](media/deploy-foundational-patterns/icon-storage-87-storage-accounts-classic.svg)<br>[Conexión al almacenamiento iSCSI](azure-stack-network-howto-iscsi-storage.md) | ![Ampliación del centro de datos](media/deploy-foundational-patterns/icon-storage-88-recovery-services-vaults.svg)<br>[Ampliación del centro de datos](azure-stack-network-howto-extend-datacenter.md) |

## <a name="backup"></a>Copia de seguridad

Puede usar los patrones de copia de seguridad y recuperación ante desastres para copiar todos los recursos de una suscripción en Azure o en otra instancia de Azure Stack Hub. Estos patrones examinan el uso de CommVault Live-Sync para replicar la información almacenada en el interior de las máquinas virtuales en otro entorno. Puede encontrar scripts para crear una cuenta de almacenamiento y una cuenta de almacenamiento de copia de seguridad para enviar los datos. Con el módulo replicador de suscripciones de Azure puede orquestar la replicación de recursos y puede personalizar el procesador para controlar diversos recursos. 



|  Copia de seguridad  |  Copiar  |
| --- | --- |
| ![Copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault](media/deploy-foundational-patterns/icon-storage-100-import-export-jobs.svg)<br>[Copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault](azure-stack-network-howto-backup-commvault.md) | ![Copia de recursos de suscripción](media/deploy-foundational-patterns/icon-storage-94-data-box.svg)<br>[Copia de recursos de suscripción](azure-stack-network-howto-backup-replicator.md) |
|  ![Copia de seguridad de las cuentas de almacenamiento en Azure Stack Hub](media/deploy-foundational-patterns/icon-storage-93-storage-sync-services.svg)<br>[Copia de seguridad de las cuentas de almacenamiento en Azure Stack Hub](azure-stack-network-howto-backup-storage.md)  | |

## <a name="github-samples"></a>Ejemplos de GitHub

Puede encontrar las plantillas en el repositorio de GitHub [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).

## <a name="next-steps"></a>Pasos siguientes

[Documentación sobre soluciones y patrones híbridos de Azure](/hybrid/app-solutions)
