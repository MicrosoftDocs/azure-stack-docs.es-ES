---
title: Plan de implementación de Controladora de red
description: En este tema se explica cómo planear la implementación de Controladora de red mediante Windows Admin Center en un conjunto de máquinas virtuales.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: bfea9216cefdc64c7749f8b49d5ecc3a422e5130
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510795"
---
# <a name="plan-to-deploy-network-controller"></a>Plan de implementación de Controladora de red

>Se aplica a: Azure Stack HCI, versión 20H2, Windows Server 2019 y Windows Server 2016

La planeación de la implementación de Controladora de red mediante Windows Admin Center requiere un conjunto de máquinas virtuales que ejecuten el sistema operativo de Azure Stack HCI o Windows Server. Controladora de red es un rol del servidor escalable y de alta disponibilidad que requiere un mínimo de tres máquinas virtuales para proporcionar alta disponibilidad en la red.

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

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Opción 2 para crear una red física para Controladora de red." lightbox="./media/network-controller/topology-option-2.png":::

    **Opción 3**: La red de administración está físicamente separada de las redes de carga de trabajo. Esta opción usa dos conmutadores virtuales, uno para el proceso y otro para el almacenamiento:

    :::image type="content" source="./media/network-controller/topology-option-3.png" alt-text="Opción 3 para crear una red física para Controladora de red." lightbox="./media/network-controller/topology-option-3.png":::

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
Ahora está listo para implementar Controladora de red en las máquinas virtuales.

## <a name="see-also"></a>Vea también
- [Creación de un clúster de Azure Stack HCI](../deploy/create-cluster.md)
- [Implementación de una infraestructura de SDN mediante SDN Express](../manage/sdn-express.md)
- [Introducción a la controladora de red](network-controller-overview.md)
- [Alta disponibilidad de Controladora de red](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)