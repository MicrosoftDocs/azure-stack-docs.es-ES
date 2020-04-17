---
title: Reemplazar unidades con errores en Azure Stack HCI
description: Cómo reemplazar unidades con errores en Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: e0aaed5d444a0d7b617ecd2ccd350a9812be8a2c
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "79025975"
---
# <a name="replace-failed-drives-on-azure-stack-hci"></a>Reemplazar unidades con errores en Azure Stack HCI

Azure Stack HCI funciona con unidades SATA, SAS o NVMe de conexión directa, cada una de ellas conectada directamente a un solo servidor. Si se produce un error en una unidad, tendrá que acceder al hardware del servidor físico para reemplazarla.

## <a name="find-the-alert"></a>Buscar la alerta
Cuando se produce un error en una unidad, aparece una alerta en la parte superior izquierda, en el área **Alerts** (Alerts) del panel **Windows Admin Center**. También puede seleccionar **Drives** (Unidades) en el panel de navegación del lado izquierdo, o hacer clic en el vínculo **VIEW DRIVES >** (VER UNIDADES) en el icono de la esquina inferior derecha para examinar las unidades y ver su estado. En la pestaña **View** (Ver), la cuadrícula permite la ordenación, agrupación y búsqueda por palabras clave.

1. En el panel, haga clic en la alerta para ver los detalles, como la ubicación física de la unidad.
1. Para más información, haga clic en el acceso directo **Go to drive** (vaya a la unidad) para ir a la página de detalles de la unidad **Drive**.
1. Si el hardware lo admite, puede hacer clic en **Turn light on/off** (Activar o desactivar la luz) para controlar la luz del indicador de la unidad.
   Espacios de almacenamiento directo retira y evacua automáticamente las unidades con errores. Cuando esto sucede, el estado de la unidad es Retired (Retirada) y su barra de capacidad de almacenamiento está vacía.
1. Quite la unidad con errores e inserte la unidad de reemplazo.

## <a name="wait-for-the-alert-to-clear"></a>Esperar a que se borre la alerta
En **Drives > Inventory** (Unidades > Inventario) aparecerá la nueva unidad. Con el tiempo, la alerta se borrará, los volúmenes volverán a un estado correcto y el almacenamiento se reequilibrará en la nueva unidad. No se requiere ninguna acción del usuario.

## <a name="next-steps"></a>Pasos siguientes
-  Para más información sobre cómo controlar el estado del almacenamiento en distintos niveles, incluido el nivel de unidad, consulte [Estados operativos y de mantenimiento](/windows-server/storage/storage-spaces/storage-spaces-states).
