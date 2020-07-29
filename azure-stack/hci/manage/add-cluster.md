---
title: Adición o eliminación de servidores para un clúster de Azure Stack HCI
description: Aprenda agregar o quitar nodos de servidor de un clúster en Azure Stack HCI
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 07/21/2020
ms.openlocfilehash: 9dfdbcab43694146c4190db8ef29905626a4d597
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866651"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Adición o eliminación de servidores para un clúster de Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Puede agregar o quitar servidores fácilmente de un clúster en Azure Stack HCI. Tenga en cuenta que cada nuevo servidor físico debe coincidir al máximo con el resto de los servidores del clúster en lo relativo a tipo de CPU, memoria, número de unidades y tipo y tamaño de estas.

Siempre que agregue o quite un servidor, también debe realizar la validación del clúster posteriormente para asegurarse de que el clúster funciona con normalidad.

## <a name="obtain-oem-hardware"></a>Obtener hardware de OEM

El primer paso es adquirir hardware de HCI nuevo a partir del OEM original. Haga siempre referencia a la documentación proporcionada por el OEM al agregar un nuevo hardware de servidor para su uso en el clúster.

1. Coloque el nuevo servidor físico en el bastidor y cabléelo de forma adecuada.
1. Habilite los puertos de conmutador físico y ajuste las listas de control de acceso e identificadores de VLAN si procede.
1. Configure la dirección IP correcta en el controlador de administración de placa base (BMC) y aplique toda la configuración de BIOS según las instrucciones del OEM.
1. Aplique la base de referencia del firmware actual a todos los componentes mediante las herramientas proporcionadas por el OEM.
1. Ejecute las pruebas de validación de OEM para garantizar la homogeneidad de los servidores de clúster existentes.

## <a name="add-a-server-to-the-cluster"></a>Agregar un servidor al clúster

Una vez que el servidor se haya puesto en marcha correctamente, use Windows Admin Center para unir el servidor al clúster.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Pantalla Agregar servidor" lightbox="media/manage-cluster/add-server.png":::

1. En **Windows Admin Center**, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Servidores**.
1. En **Servidores**, seleccione la pestaña **Inventario**.
1. En la pestaña **Inventario**, seleccione **Agregar**.
1. En **Nombre del servidor**, escriba el nombre de dominio completo del servidor que desea agregar, haga clic en **Agregar** y, a continuación, haga clic en **Agregar** de nuevo en la parte inferior.
1. Compruebe que el servidor se ha agregado correctamente al clúster.

## <a name="remove-a-server-from-the-cluster"></a>Eliminación de un servidor del clúster

Los pasos para quitar un servidor del clúster son parecidos a los pasos para agregarlo.

Tenga en cuenta que cuando se quita un servidor, también se quitan las máquinas virtuales, unidades y cargas de trabajo asociadas al servidor.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Cuadro de diálogo Quitar servidor" lightbox="media/manage-cluster/remove-server.png":::

1. En **Windows Admin Center**, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Servidores**.
1. En **Servidores**, seleccione la pestaña **Inventario**.
1. En la pestaña **Inventario**, seleccione el servidor que desea quitar y, a continuación, seleccione **Quitar**.
1. Para quitar también todas las unidades de servidor del bloque de almacenamiento, seleccione esa casilla.
1. Compruebe que el servidor se ha quitado correctamente del clúster.

Siempre que agregue o quite un nodo de servidor en un clúster, asegúrese de ejecutar una prueba de validación de clúster después.

## <a name="next-steps"></a>Pasos siguientes

- Debe validar el clúster después de agregar o quitar el nodo. Para más información, consulte [Validación del clúster](../deploy/validate.md).