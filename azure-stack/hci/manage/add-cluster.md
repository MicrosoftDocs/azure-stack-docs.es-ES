---
title: Adición o eliminación de servidores para un clúster de Azure Stack HCI
description: Aprenda agregar o quitar nodos de servidor de un clúster en Azure Stack HCI
ms.topic: article
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 4d8e122c8a0b68740ecf34f8140322ee627d03e7
ms.sourcegitcommit: 91f4baa7a770b7a82f1ddccec4dbac14be530d06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2020
ms.locfileid: "83769042"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Adición o eliminación de servidores para un clúster de Azure Stack HCI

> Se aplica a: Windows Server 2019

Puede agregar o quitar servidores fácilmente de un clúster en Azure Stack HCI. Tenga en cuenta que cada nuevo servidor físico debe coincidir al máximo con el resto de los servidores del clúster en lo relativo a tipo de CPU, memoria, número de unidades y tipo y tamaño de estas.

Siempre que agregue o quite un servidor, también debe realizar la validación del clúster posteriormente para asegurarse de que el clúster funciona con normalidad.

## <a name="obtain-server-hardware-from-your-oem"></a>Obtención del hardware de servidor del fabricante de equipos originales (OEM)

El primer paso es adquirir hardware de HCI nuevo a partir del OEM original. Haga siempre referencia a la documentación proporcionada por el OEM al agregar un nuevo hardware de servidor para su uso en el clúster.

1. Coloque el nuevo servidor físico en el bastidor y cabléelo de forma adecuada.
1. Habilite los puertos de conmutador físico y ajuste las listas de control de acceso e identificadores de VLAN si procede.
1. Configure la dirección IP correcta en el controlador de administración de placa base (BMC) y aplique toda la configuración de BIOS según las instrucciones del OEM.
1. Aplique la base de referencia del firmware actual a todos los componentes mediante las herramientas proporcionadas por el OEM.
1. Ejecute las pruebas de validación de OEM para garantizar la homogeneidad de los servidores de clúster existentes.

## <a name="add-the-server-to-the-cluster"></a>Incorporación del servidor al clúster

Una vez que el servidor se haya puesto en marcha correctamente, use Windows Admin Center para unir el servidor al clúster.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Pantalla Agregar servidor":::

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

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Cuadro de diálogo Quitar servidor":::

1. En **Windows Admin Center**, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Servidores**.
1. En **Servidores**, seleccione la pestaña **Inventario**.
1. En la pestaña **Inventario**, seleccione el servidor que desea quitar y, a continuación, seleccione **Quitar**.
1. Para quitar también todas las unidades de servidor del bloque de almacenamiento, seleccione esa casilla.
1. Compruebe que el servidor se ha quitado correctamente del clúster.

## <a name="validate-the-cluster"></a>Validación del clúster

Siempre que agregue o quite un servidor de un clúster, debe validar el clúster. La validación del clúster debe superarse sin errores para que la admita Microsoft.

:::image type="content" source="media//manage-cluster/validate-cluster.png" alt-text="Cuadro de diálogo Validar clúster":::

1. En **Windows Admin Center**, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Herramientas**, seleccione **Servidores**.
1. En **Servidores**, seleccione la pestaña **Inventario**, seleccione **Más** y, para terminar, seleccione **Validar clúster**.
1. Compruebe que todos los pasos de validación se han superado correctamente.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre la validación de clústeres, consulte [Validar hardware para un clúster de conmutación por error](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134244(v=ws.11)).