---
title: Administración de redes lógicas de inquilinos
description: En este tema se proporcionan instrucciones detalladas sobre cómo usar Windows Admin Center para crear, actualizar y eliminar redes lógicas después de haber implementado Controladora de red.
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/02/2021
ms.openlocfilehash: 5cf88e5befd551eb7789388807c9c1e4df671dc9
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562949"
---
# <a name="manage-tenant-logical-networks"></a>Administración de redes lógicas de inquilinos

>Se aplica a: Azure Stack HCI, versión 20H2, Windows Server 2019 y Windows Server 2016

En este tema se proporcionan instrucciones detalladas sobre cómo usar Windows Admin Center para crear, actualizar y eliminar redes lógicas después de haber implementado Controladora de red. Una red lógica de redes definidas por software (SDN) es una red tradicional basada en VLAN.

Al modelar una red basada en VLAN como una red lógica de SDN, puede aplicar directivas de red a las cargas de trabajo que están asociadas a estas redes. Por ejemplo, puede aplicar listas de control de acceso (ACL) de seguridad a las cargas de trabajo que están asociadas a redes lógicas de SDN. La aplicación de las listas de control de acceso protege las cargas de trabajo basadas en VLAN de ataques externos e internos.

## <a name="create-a-logical-network"></a>Crear red lógica
Siga los pasos que se indican a continuación en Windows Admin Center para crear una red lógica.

:::image type="content" source="./media/tenant-logical-networks/create-logical-network.png" alt-text="Captura de pantalla de la pantalla principal de Windows Admin Center que muestra el cuadro Nombre de redes lógicas." lightbox="./media/tenant-logical-networks/create-logical-network.png":::

1. En la página principal de Windows Admin Center, en **Todas las conexiones**, seleccione el clúster en el que desea crear la red lógica.
1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes lógicas**.
1. En **Redes lógicas**, seleccione la pestaña **Inventario** y, a continuación, seleccione **Nuevo**.
1. En el panel **Redes lógicas**, escriba un nombre para la red lógica.
1. En **Subred lógica**, seleccione **Agregar**.

    :::image type="content" source="./media/tenant-logical-networks/create-logical-network-subnet.png" alt-text="Captura de pantalla de la pantalla principal de Windows Admin Center que muestra el panel Subred lógica." lightbox="./media/tenant-logical-networks/create-logical-network-subnet.png":::

1. En el panel **Subred lógica**, escriba un nombre para la subred y, a continuación, proporcione la siguiente información:
    1. Un **identificador de VLAN** para la red.
    1. Un **prefijo de dirección** en la notación de enrutamiento de interdominios sin clases (CIDR).
    1. La **puerta de enlace predeterminada** de la red.
    1. La dirección del servidor **DNS**, si es necesaria.
    1. Active la casilla **Red lógica pública** si la red lógica va a proporcionar conectividad para clientes externos.
1. En **Grupos de direcciones IP de subredes lógicas**, seleccione **Agregar** y proporcione la siguiente información:
    1. Un **nombre de grupo de direcciones IP** lógico.
    1. Una **dirección IP de inicio**.
    1. Una **dirección IP de fin**. Las direcciones IP de inicio y de fin deben estar dentro del prefijo de dirección proporcionado para la subred.
    1. Seleccione **Agregar**.
1. En la página **Subred lógica**, seleccione **Agregar**.
1. En la página **Redes lógicas**, seleccione **Enviar**.
1. En la lista **Redes lógicas**, compruebe que el estado de la red lógica es **Correcto**.

## <a name="get-a-list-of-logical-networks"></a>Obtención de una lista de redes lógicas
Puede ver fácilmente todas las redes lógicas del clúster.

:::image type="content" source="./media/tenant-logical-networks/list-logical-networks.png" alt-text="Captura de pantalla de la pantalla principal de Windows Admin Center que muestra el panel Inventario de Redes lógicas." lightbox="./media/tenant-logical-networks/list-logical-networks.png":::

1. En la página principal de Windows Admin Center, en **Todas las conexiones**, seleccione el clúster en el que desea ver las redes lógicas.
1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes lógicas**.
1. En la pestaña **Inventario** se enumeran todas las redes lógicas disponibles en el clúster y se proporcionan comandos para administrar redes lógicas individuales. Puede:
    - Ver la lista de redes lógicas.
    - Ver la configuración de las redes lógicas, el estado de cada red lógica y si la virtualización de red está habilitada para cada una de ellas. Si está habilitada la virtualización de red, también puede ver el número de redes virtuales asociadas a cada red lógica.
    - Cambiar la configuración de una red lógica.
    - Eliminar una red lógica.

## <a name="view-logical-network-details"></a>Visualización de los detalles de las redes lógicas
Puede ver información detallada de una red lógica específica en su página dedicada.

:::image type="content" source="./media/tenant-logical-networks/view-logical-network-details.png" alt-text="Captura de pantalla de Windows Admin Center que muestra la vista de detalles de una red lógica." lightbox="./media/tenant-logical-networks/view-logical-network-details.png":::

1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes lógicas**.
1. Seleccione la pestaña **Inventario** y, a continuación, seleccione la red lógica de la que desea conocer los detalles. En la siguiente página, puede:
    - Ver el estado de aprovisionamiento de la red lógica (Correcto, Con errores).
    - Ver si la virtualización de red está habilitada para la red lógica.
    - Ver las subredes de la red lógica.
    - Agregar nuevas subredes, eliminar las existentes y modificar la configuración de una subred de la red lógica.
    - Seleccionar cada subred para ir a su página **Subred**, donde puede agregar, quitar y modificar grupos de direcciones IP de subred lógica.
    - Ver las redes virtuales y las conexiones asociadas a la red lógica.

## <a name="change-a-logical-networks-virtualization-setting"></a>Modificación de la configuración de virtualización de una red lógica
Puede cambiar la configuración de virtualización de una red lógica.

:::image type="content" source="./media/tenant-logical-networks/change-logical-network-setting.png" alt-text="Captura de pantalla de Windows Admin Center que muestra la opción Habilitar virtualización de red de una red lógica." lightbox="./media/tenant-logical-networks/change-logical-network-setting.png":::

1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes lógicas**.
1. Seleccione la pestaña **Inventario**, seleccione la red lógica y, a continuación, seleccione **Configuración**.
1. Si planea implementar redes virtuales sobre la red lógica, en el nombre de la red lógica, active la casilla **Habilitar virtualización de red** y, a continuación, seleccione **Enviar**.

## <a name="delete-a-logical-network"></a>Eliminación de una red lógica
Puede eliminar una red lógica si ya no la necesita.

:::image type="content" source="./media/tenant-logical-networks/delete-logical-network.png" alt-text="Captura de pantalla de Windows Admin Center que muestra el mensaje de confirmación de eliminación para eliminar una red lógica." lightbox="./media/tenant-logical-networks/delete-logical-network.png":::

1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes lógicas**.
1. Seleccione la pestaña **Inventario**, seleccione la red lógica y, a continuación, seleccione **Eliminar**.
1. En la solicitud de confirmación de **Eliminar**, seleccione **Sí**.
1. Junto al cuadro de búsqueda **Redes lógicas**, seleccione **Actualizar** para asegurarse de que la red lógica se ha eliminado.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, vea también:
- [Administración de redes virtuales de inquilinos](tenant-virtual-networks.md)
- [Redes definidas por software (SDN) en Azure Stack HCI](../concepts/software-defined-networking.md)
