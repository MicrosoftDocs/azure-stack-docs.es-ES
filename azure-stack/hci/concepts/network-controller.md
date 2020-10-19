---
title: Plan de implementación de Controladora de red
description: En este tema se explica cómo planear la implementación de Controladora de red mediante Windows Admin Center en un conjunto de máquinas virtuales que ejecutan el sistema operativo de Azure Stack HCI.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 10/7/2020
ms.openlocfilehash: ec9ddb62dc876fbd4b99ebc2c8e2a3af4a54e8a7
ms.sourcegitcommit: 9a91dbdaa556725f51bcf3d8e79a4ed2dd5a209f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2020
ms.locfileid: "91847697"
---
# <a name="plan-to-deploy-network-controller"></a>Plan de implementación de Controladora de red

>Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

La planeación de la implementación de Controladora de red mediante Windows Admin Center requiere un conjunto de máquinas virtuales que ejecuten el sistema operativo de Azure Stack HCI. Controladora de red es un rol del servidor escalable y de alta disponibilidad que requiere un mínimo de tres máquinas virtuales para proporcionar alta disponibilidad en la red.

   >[!NOTE]
   > Es recomendable implementar Controladora de red en sus propias máquinas virtuales dedicadas.

## <a name="network-controller-requirements"></a>Requisitos de Controladora de red
Se requiere lo siguiente para implementar Controladora de red:
- Un disco duro virtual para el sistema operativo de Azure Stack HCI para crear las máquinas virtuales de Controladora de red.
- Un nombre de dominio y credenciales para unir las máquinas virtuales de Controladora de red a un dominio.
- Al menos un conmutador virtual que configure mediante el asistente para la creación de clústeres de Windows Admin Center.
- Una configuración de red física que coincida con una de las opciones de topología de esta sección.

    Windows Admin Center crea la configuración en el host de Hyper-V. Sin embargo, la red de administración debe conectarse a los adaptadores físicos del host de acuerdo con una de las tres opciones siguientes:

    **Opción 1**: La red de administración está físicamente separada de las redes de carga de trabajo. Esta opción usa un solo conmutador virtual para el proceso y el almacenamiento:

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="Opción 1 para crear una red física para Controladora de red." lightbox="./media/network-controller/topology-option-1.png":::

    **Opción 2**: La red de administración está físicamente separada de las redes de carga de trabajo. Esta opción usa un único conmutador virtual solo para el proceso:

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Opción 1 para crear una red física para Controladora de red." lightbox="./media/network-controller/topology-option-2.png":::

    **Opción 3**: La red de administración está físicamente separada de las redes de carga de trabajo. Esta opción usa dos conmutadores virtuales, uno para el proceso y otro para el almacenamiento:

    :::image type="content" source="./media/network-controller/topology-option-3.png" alt-text="Opción 1 para crear una red física para Controladora de red." lightbox="./media/network-controller/topology-option-3.png":::

- También puede agrupar los adaptadores físicos de administración para que usen el mismo conmutador de administración. En este caso, se recomienda seguir usando una de las opciones de esta sección.
- Información de la red de administración que utiliza Controladora de red para comunicarse con Windows Admin Center y los hosts de Hyper-V.
- Direccionamiento basado en DHCP o en la red estática para las máquinas virtuales de la controladora de red.
- El nombre de dominio completo de Transferencia de estado representacional (REST) para Controladora de red que usan los clientes de administración para comunicarse con Controladora de red.

   >[!NOTE]
   > Windows Admin Center no admite actualmente la autenticación de Controladora de red para la comunicación con clientes REST ni para la comunicación entre las máquinas virtuales de Controladora de red. Puede usar la autenticación basada en Kerberos si usa PowerShell para implementarla y administrarla.

## <a name="configuration-requirements"></a>Requisitos de configuración
Puede implementar los nodos de clúster de Controladora de red en la misma subred o en subredes diferentes. Si planea implementar los nodos de clúster de Controladora de red en distintas subredes, debe proporcionar el nombre DNS de REST de Controladora de red durante el proceso de implementación.

Para más información, consulte [Configuración del registro DNS dinámico para Controladora de red](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Pasos siguientes
Ahora está listo para implementar Controladora de red en las máquinas virtuales que ejecutan el sistema operativo.

Para obtener más información, consulte:
- [Creación de un clúster de Azure Stack HCI](../deploy/create-cluster.md)
- [Implementación de controladora de red con Windows PowerShell](../deploy/network-controller-powershell.md)

## <a name="see-also"></a>Consulte también
- [Controladora de red](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [Alta disponibilidad de Controladora de red](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)