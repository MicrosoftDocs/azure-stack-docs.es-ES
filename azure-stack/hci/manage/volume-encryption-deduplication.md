---
title: Habilitación del cifrado de volumen, desduplicación y compresión en Azure Stack HCI
description: En este tema se explica cómo usar el cifrado de volumen, la desduplicación y la compresión en Azure Stack HCI mediante Windows Admin Center.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 09/03/2020
ms.openlocfilehash: af5eb68af12e831f310e96ba27a348e8c615d4fe
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2020
ms.locfileid: "89449023"
---
# <a name="enable-volume-encryption-deduplication-and-compression-in-azure-stack-hci"></a>Habilitación del cifrado de volumen, desduplicación y compresión en Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se explica cómo habilitar el cifrado con BitLocker en volúmenes de Azure Stack HCI mediante Windows Admin Center. También se explica cómo habilitar la desduplicación y la compresión en los volúmenes. Para aprender a crear volúmenes, consulte [Creación de volúmenes](create-volumes.md).

## <a name="turn-on-bitlocker-to-protect-volumes"></a>Activación de BitLocker para proteger volúmenes
Para activar BitLocker en Windows Admin Center, haga lo siguiente:

1. Conéctese a un clúster de Espacios de almacenamiento directo y luego, en el panel **Herramientas** , seleccione **Volúmenes**.
1. En la página **Volúmenes**, seleccione la pestaña **Inventario**, y luego, en **Características opcionales**, active el conmutador **Encryption (BitLocker)** (Cifrado [BitLocker]).

    :::image type="content" source="media/volume-encryption-deduplication/bitlocker-toggle-switch.png" alt-text="Conmutador de alternancia para habilitar BitLocker":::

1. En la ventana emergente **Encryption (BitLocker)** (Cifrado [BitLocker]), seleccione **Iniciar** y, a continuación, en la página **Turn on Encryption** (Activar cifrado), proporcione sus credenciales para completar el flujo de trabajo.

>[!NOTE]
   > Si la ventana emergente **Install BitLocker feature first** (Instalar primero la característica de BitLocker), siga las instrucciones para instalar la característica en cada servidor del clúster y, a continuación, reinicie los servidores.

## <a name="turn-on-deduplication-and-compression"></a>Activación de la desduplicación y la compresión
La desduplicación y la compresión se administran por volumen. La desduplicación y la compresión utilizan un modelo de procesamiento posterior, lo que significa que no verá cuánto ahorra hasta que se ejecute. Cuando lo hace, funcionará en todos los archivos, incluso en los que ya estaban allí.

Para activar la desduplicación y la compresión en un volumen de Windows Admin Center:

1. Conéctese a un clúster de Espacios de almacenamiento directo y luego, en el panel **Herramientas** , seleccione **Volúmenes**.
1. En la página **Volumes** (Volúmenes), seleccione la pestaña **Inventory** (Inventario).
1. En la lista de volúmenes, seleccione el nombre del volumen que quiere administrar.
1. En la página de detalles del volumen, active el conmutador **Deduplication and compression** (Desduplicación y compresión).
1. En el panel **Enable deduplication** (Habilitar desduplicación), seleccione el modo de desduplicación.

    En lugar de configuraciones complicadas, Windows Admin Center permite elegir perfiles ya preparados para diferentes cargas de trabajo. Si no está seguro, use la configuración predeterminada.

1. Seleccione **Enable deduplication** (Habilitar desduplicación).

Vea un vídeo rápido sobre cómo activar la desduplicación y la compresión. El vídeo no muestra el cifrado.

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

La habilitación del cifrado de volumen tiene un pequeño impacto en el rendimiento del volumen, normalmente por debajo del 10 %, pero el impacto varía en función del hardware y las cargas de trabajo. La desduplicación y la compresión de datos también afectan al rendimiento; para más información, consulte [Determinar qué cargas de trabajo son aptas para la desduplicación de datos](/windows-server/storage/data-deduplication/install-enable#enable-dedup-candidate-workloads).

<!---Add info on greyed out ReFS option? --->

¡Y ya está! Repita tantas veces como sea necesario para proteger los datos de los volúmenes.

## <a name="next-steps"></a>Pasos siguientes
Para temas relacionados y otras tareas de administración del almacenamiento, vea también:

- [Introducción a Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Planeamiento de volúmenes](../concepts/plan-volumes.md)
- [Ampliar volúmenes](extend-volumes.md)
- [Eliminar volúmenes](delete-volumes.md)