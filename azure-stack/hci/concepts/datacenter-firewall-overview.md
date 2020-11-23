---
title: Introducción al firewall del centro de datos en Azure Stack HCI
description: Use este tema para obtener más información sobre el firewall del centro de datos de Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 34efe18aee4a62481d81a5e9a810a0a71d97b063
ms.sourcegitcommit: 40d3f3f0ac088d1590d1fb64ca05ac1dabf4e00c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874964"
---
# <a name="what-is-datacenter-firewall"></a>¿Qué es un firewall del centro de datos?

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

El firewall del centro de datos es un firewall de redes definidas por software (SDN) multiinquilino, del nivel de red, 5-tuplas (protocolo, números de puerto de origen y de destino, direcciones IP de origen y de destino) y con estado. El firewall del centro de datos protege los flujos de tráfico horizontales de derecha a izquierda y verticales de arriba abajo en el nivel de red de las redes virtuales y redes VLAN tradicionales.

## <a name="how-datacenter-firewall-works"></a>Funcionamiento del firewall del centro de datos

Para habilitar y configurar el firewall del centro de datos, cree listas de control de acceso (ACL) que se apliquen a una subred o una interfaz de red. Las directivas del firewall se aplican en el puerto vSwitch de cada máquina virtual de inquilino. Las directivas se insertan mediante el portal del inquilino y [Controladora de red](network-controller-overview.md) las distribuye a todos los hosts aplicables.

Los administradores de inquilinos pueden instalar y configurar directivas de firewall para ayudar a proteger sus redes contra tráfico no deseado procedente de redes de Internet e intranet.

:::image type="content" source="media/datacenter-firewall/multitenant-firewall-overview.png" alt-text="Firewall del centro de datos en la pila de red" border="false":::

El administrador del proveedor de servicios o el administrador de inquilinos puede administrar las directivas del firewall del centro de datos mediante Controladora de red y las API de Northbound. También puede configurar y administrar las directivas del firewall del centro de datos mediante Windows Admin Center.

## <a name="advantages-for-cloud-service-providers"></a>Ventajas de los proveedores de servicios en la nube

El firewall del centro de datos ofrece las siguientes ventajas para los proveedores de servicios criptográficos (CSP):

- Una solución de firewall basada en software, muy escalable, administrable y de fácil diagnóstico que se puede ofrecer a los inquilinos.

- Libertad para trasladar máquinas virtuales de inquilino a distintos hosts de proceso sin interrumpir las directivas del firewall de inquilino.

    - Se implementa como un firewall del agente de host del puerto vSwitch.

    - Las máquinas virtuales de inquilino obtienen las directivas asignadas a su firewall del agente de host de vSwitch.

    - Las reglas del firewall se configuran en cada puerto vSwitch, independientemente del host real que ejecuta la máquina virtual.

- Ofrece protección a las máquinas virtuales de inquilinos independientemente del sistema operativo invitado del inquilino.

## <a name="advantages-for-tenants"></a>Ventajas para los inquilinos

El firewall del centro de datos ofrece las siguientes ventajas para los inquilinos:

- Posibilidad de definir reglas de firewall para ayudar a proteger cargas de trabajo con conexión a Internet y cargas de trabajo internas en redes.

- Posibilidad de definir reglas de firewall para ayudar a proteger el tráfico entre máquinas virtuales en la misma subred de nivel 2 (L2), así como entre máquinas virtuales en subredes L2 diferentes.

- Posibilidad de definir reglas de firewall para ayudar a proteger y aislar el tráfico de red entre las redes locales de inquilinos y sus redes virtuales en el proveedor de servicios.

- Posibilidad de aplicar directivas de firewall a redes VLAN tradicionales y redes virtuales basadas en superposición

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Uso del firewall del centro de datos para redes definidas por software en Azure Stack HCI](../manage/use-datacenter-firewall.md)
- [SDN en Azure Stack HCI](software-defined-networking.md)