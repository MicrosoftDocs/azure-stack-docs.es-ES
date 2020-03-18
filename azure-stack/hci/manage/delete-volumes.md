---
title: Eliminación de volúmenes en Azure Stack HCI
description: Cómo eliminar volúmenes en Azure Stack HCI mediante Windows Admin Center.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: ab99ed7a49fe2bf003245f139451895a85c4edbf
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025981"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Eliminación de volúmenes en Azure Stack HCI

> Se aplica a: Windows Server 2019

En este tema se proporcionan instrucciones para eliminar volúmenes en un clúster de [Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) mediante Windows Admin Center.

Vea un vídeo rápido sobre cómo eliminar un volumen.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Uso de Windows Admin Center para eliminar un volumen

1. En Windows Admin Center, conéctese a un clúster de Espacios de almacenamiento directo y seleccione **Volumes** (Volúmenes) en el panel **Tools** (Herramientas).
2. En la página **Volumes** (Volúmenes), seleccione la pestaña **Inventory** (Inventario) y, a continuación, seleccione el volumen que quiere eliminar.
3. En la parte superior de la página de detalles del volumen, seleccione **Delete** (Eliminar).
4. En el cuadro de diálogo de confirmación, active la casilla para confirmar que desea eliminar el volumen y seleccione **Delete** (Eliminar).

## <a name="next-steps"></a>Pasos siguientes

Para obtener instrucciones detalladas sobre otras tareas de administración del almacenamiento esenciales, consulte también:

- [Planeamiento de volúmenes en Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/plan-volumes)
- [Creación de volúmenes en Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/create-volumes)
- [Extensión de volúmenes en Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/resize-volumes)