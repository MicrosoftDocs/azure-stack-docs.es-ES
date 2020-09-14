---
title: Plan de implementación de Controladora de red
description: En este tema se explica cómo planear la implementación de Controladora de red mediante Windows Admin Center en un conjunto de máquinas virtuales que ejecutan el sistema operativo de Azure Stack HCI.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 58ae46591e2119bbfc9269440f36e9c37fd0e7b9
ms.sourcegitcommit: 69c859a89941ee554d438d5472308eece6766bdf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2020
ms.locfileid: "89643942"
---
# <a name="plan-to-deploy-the-network-controller"></a>Plan de implementación de Controladora de red

>Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019 

La planeación de la implementación de Controladora de red mediante Windows Admin Center requiere un conjunto de máquinas virtuales que ejecuten el sistema operativo de Azure Stack HCI. Controladora de red es un rol del servidor escalable y de alta disponibilidad que requiere un mínimo de tres máquinas virtuales para proporcionar alta disponibilidad en la red.

   >[!NOTE]
   > Es recomendable implementar Controladora de red en sus propias máquinas virtuales dedicadas.

## <a name="network-controller-requirements-for-azure-stack-hci"></a>Requisitos de Controladora de red para Azure Stack HCI
Se requiere lo siguiente para implementar Controladora de red:
- Un disco duro virtual para el sistema operativo de Azure Stack HCI para crear las máquinas virtuales de Controladora de red.
- Un nombre de dominio y credenciales para unir las máquinas virtuales de Controladora de red a un dominio.
- Una configuración de red física que coincida con una de las dos opciones de topología de esta sección.

    Windows Admin Center crea la configuración en el host de Hyper-V. Sin embargo, la red de administración debe conectarse a los adaptadores físicos del host de acuerdo con una de las dos opciones siguientes:

    **Opción 1**: Un solo conmutador físico conecta la red de administración a un adaptador de administración físico en el host y un tronco en los adaptadores físicos que usa el conmutador virtual:

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="Opción 1 para crear una red física para Controladora de red." lightbox="./media/network-controller/topology-option-1.png":::

    **Opción 2**: Si la red de administración está físicamente separada de las redes de carga de trabajo, se requieren dos conmutadores virtuales:

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Opción 2 para crear una red física para Controladora de red." lightbox="./media/network-controller/topology-option-1.png":::

- Información de la red de administración que utiliza Controladora de red para comunicarse con Windows Admin Center y los hosts de Hyper-V.
- Direccionamiento basado en DHCP o en la red estática para las máquinas virtuales de la controladora de red.
- El nombre de dominio completo de Transferencia de estado representacional (REST) para Controladora de red que usan los clientes de administración para comunicarse con Controladora de red.

   >[!NOTE]
   > Windows Admin Center no admite actualmente la autenticación de Controladora de red para la comunicación con clientes REST ni para la comunicación entre las máquinas virtuales de Controladora de red. Puede usar la autenticación basada en Kerberos si usa PowerShell para implementarla y administrarla.

## <a name="configuration-requirements"></a>Requisitos de configuración
Puede implementar los nodos de clúster de Controladora de red en la misma subred o en subredes diferentes. Si planea implementar los nodos de clúster de Controladora de red en distintas subredes, debe proporcionar el nombre DNS de REST de Controladora de red durante el proceso de implementación.

Para más información, consulte [Configuración del registro DNS dinámico para Controladora de red](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).


## <a name="next-steps"></a>Pasos siguientes
Ahora está listo para implementar Controladora de red en las máquinas virtuales que ejecutan el sistema operativo de Azure Stack HCI.

Para obtener más información, consulte:
- [Creación de un clúster de Azure Stack HCI](../deploy/create-cluster.md)

## <a name="see-also"></a>Consulte también
- [Controladora de red](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [Alta disponibilidad de Controladora de red](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)