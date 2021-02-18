---
title: Administración de redes virtuales de inquilinos
description: En este tema se proporcionan instrucciones detalladas sobre cómo usar Windows Admin Center para crear, actualizar y eliminar redes virtuales de Virtualización de red de Hyper-V (HNV) después de haber implementado redes definidas por software.
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: fb90a8f9df28ed8fd67330c42ae1e3d2f3aa445c
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562939"
---
# <a name="manage-tenant-virtual-networks"></a>Administración de redes virtuales de inquilinos

>Se aplica a: Azure Stack HCI, versión 20H2, Windows Server 2019 y Windows Server 2016

En este tema se proporcionan instrucciones detalladas sobre cómo usar Windows Admin Center para crear, actualizar y eliminar redes virtuales de Virtualización de red de Hyper-V (HNV) después de haber implementado redes definidas por software.

HNV le ayuda a aislar las redes de inquilinos para que cada red de inquilinos sea una entidad independiente. Las entidades no tienen ninguna posibilidad de conexión cruzada, a menos que configure cargas de trabajo de acceso público o emparejamiento entre redes virtuales.

## <a name="create-a-virtual-network"></a>Creación de una red virtual
Siga los pasos que se indican a continuación en Windows Admin Center para crear una red virtual.

:::image type="content" source="./media/tenant-virtual-networks/create-virtual-network.png" alt-text="Captura de pantalla de la pantalla principal de Windows Admin Center que muestra el panel en el que se crea un nombre de red virtual y un prefijo de dirección." lightbox="./media/tenant-virtual-networks/create-virtual-network.png":::

1. En la pantalla principal de Windows Admin Center, en **Todas las conexiones**, seleccione el clúster en el que desea crear la red virtual.
1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes virtuales**.
1. En **Redes virtuales**, seleccione la pestaña **Inventario** y, a continuación, seleccione **Nueva**.
1. En el panel **Redes virtuales**, escriba un nombre para la red virtual.
1. En **Prefijos de direcciones** seleccione **Agregar** y, a continuación, escriba un prefijo de dirección con la notación de enrutamiento de interdominios sin clases (CIDR). Opcionalmente, puede agregar más prefijos de dirección.
1. En **Subredes**, seleccione **Agregar**, escriba un nombre para la subred y, a continuación, proporcione un prefijo de dirección en la notación de CIDR.

   >[!NOTE]
   > El prefijo de dirección de la subred debe estar dentro del intervalo de prefijos de direcciones que haya definido en **Prefijos de direcciones** de la red virtual.

1. Seleccione **Enviar** o bien, agregue más subredes y, a continuación, seleccione **Enviar**.
1. En la lista **Redes virtuales**, compruebe que el estado de la red virtual es **Correcto**.

## <a name="get-a-list-of-virtual-networks"></a>Obtención de una lista de redes virtuales
Puede ver fácilmente todas las redes virtuales del clúster.

:::image type="content" source="./media/tenant-virtual-networks/list-virtual-networks.png" alt-text="Captura de pantalla de Windows Admin Center que muestra la lista de redes virtuales." lightbox="./media/tenant-virtual-networks/list-virtual-networks.png":::

1. En la página principal de Windows Admin Center, en **Todas las conexiones**, seleccione el clúster en el que desea ver las redes virtuales.
1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes virtuales**.
1. En la pestaña **Inventario** se enumeran todas las redes virtuales disponibles en el clúster y se proporcionan comandos para administrar redes virtuales individuales. Puede:
    - Ver una lista de redes virtuales.
    - Ver la configuración de la red virtual, su estado y el número de máquinas virtuales conectadas a cada red virtual.
    - Cambiar la configuración de una red virtual.
    - Eliminar una red virtual.

## <a name="view-virtual-network-details"></a>Visualización de los detalles de una red virtual
Puede ver información detallada de una red virtual específica en su página dedicada.

:::image type="content" source="./media/tenant-virtual-networks/view-virtual-network-details.png" alt-text="Captura de pantalla de Windows Admin Center que muestra la vista de detalles de una red virtual." lightbox="./media/tenant-virtual-networks/view-virtual-network-details.png":::

1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes virtuales**.
1. Seleccione la pestaña **Inventario** y, a continuación, seleccione la red virtual de la que desea conocer los detalles. En la siguiente página, puede:
    - Ver el **estado de aprovisionamiento** de la red virtual (Correcto, Con errores).
    - Ver el **estado de configuración** de la red virtual (Correcto, Error, Advertencia, Desconocido).
    - Ver la **red lógica** subyacente de la red virtual.
    - Ver el **espacio de direcciones** de la red virtual.
    - Agregar nuevas subredes, eliminar las existentes y modificar la configuración de una subred de la red virtual.
    - Ver las **conexiones de máquina virtual** a la red virtual.

## <a name="change-virtual-network-settings"></a>Modificación de la configuración de la red virtual
Puede actualizar los prefijos de dirección de redes virtuales, administrar el emparejamiento de estas y configurar un enrutador de Protocolo de puerta de enlace de borde (BGP) y nodos del mismo nivel para la red virtual.

:::image type="content" source="./media/tenant-virtual-networks/change-virtual-network-settings.png" alt-text="Captura de pantalla de Windows Admin Center que muestra la vista de configuración de una red virtual." lightbox="./media/tenant-virtual-networks/change-virtual-network-settings.png":::

1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes virtuales**.
1. Seleccione la pestaña **Inventario**, seleccione una red virtual y, a continuación, seleccione **Configuración**.
1. En la pestaña **General**, puede:
    - Quitar los prefijos de dirección existentes o agregar otros nuevos.
    - Configurar el emparejamiento con otra red virtual.
    - Agregar un enrutador BGP a la red virtual. Para ello, debe proporcionar el nombre del enrutador BGP y el número del sistema autónomo (ASN).
    - Agregar uno o varios nodos del mismo nivel para el enrutador BGP. Para ello, debe proporcionar los nombres de todos los nodos del mismo nivel del BGP y el número ASN de cada uno de ellos.

## <a name="delete-a-virtual-network"></a>Eliminar una red virtual
Puede eliminar una red virtual si ya no la necesita.

:::image type="content" source="./media/tenant-virtual-networks/delete-virtual-network.png" alt-text="Captura de pantalla de Windows Admin Center que muestra el mensaje de confirmación de Eliminar red virtual." lightbox="./media/tenant-virtual-networks/delete-virtual-network.png":::

1. En **Herramientas**, desplácese hacia abajo hasta el área **Redes** y seleccione **Redes virtuales**.
1. Seleccione la pestaña **Inventario**, seleccione una red virtual y, a continuación, seleccione **Eliminar**.
1. En el mensaje de confirmación de **Eliminar red virtual**, seleccione **Eliminar**.
1. Junto al cuadro de búsqueda Redes virtuales, seleccione **Actualizar** para asegurarse de que la red virtual se ha eliminado.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, vea también:
- [Redes definidas por software (SDN) en Azure Stack HCI](../concepts/software-defined-networking.md)
