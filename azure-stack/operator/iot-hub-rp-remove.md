---
title: Eliminación de IoT Hub en Azure Stack Hub
description: Instrucciones sobre la desinstalación de IoT Hub en Azure Stack Hub
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 4095336f6a18441512e8c1052051d6e1608d904a
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050401"
---
# <a name="how-to-remove-iot-hub-on-azure-stack-hub"></a>Eliminación de IoT Hub en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

En este artículo se proporcionan instrucciones sobre cómo eliminar el proveedor de recursos de IoT Hub en Azure Stack Hub. Este proceso tarda unos 37 minutos.

> [!WARNING]
> Una vez desinstalado IoT Hub, **_se eliminarán todos los dispositivos y datos_**. Esta operación **NO** se puede recuperar.

## <a name="uninstalling-iot-hub"></a>Desinstalación de IoT Hub

1) Vaya a **Administración de Marketplace**. IoT Hub estará en la lista y se marcará como instalado. Haga clic en **IoT Hub**.

    [![Lista de proveedores de recursos](../operator/media/iot-hub-rp-remove/uninstall1.png)](../operator/media/iot-hub-rp-remove/uninstall1.png#lightbox)

2) Haga clic en **Desinstalar** en **IoT Hub** , proporcione el nombre del proveedor de recursos **microsoft.iothub** y, a continuación, haga clic en el botón **Desinstalar**.

    [![Desinstalar IoT Hub y confirmar](../operator/media/iot-hub-rp-remove/uninstall2.png)](../operator/media/iot-hub-rp-remove/uninstall2.png#lightbox)

3) Espere a que se complete la desinstalación. Antes de intentar instalar IoT Hub de nuevo, espere al menos 10 minutos.

>[!IMPORTANT]
>Las dependencias (por ejemplo, Event Hubs) **NO** se desinstalarán. Si desea desinstalar o quitar todas las dependencias de Marketplace, tendrá que hacerlo por separado.

## <a name="next-steps"></a>Pasos siguientes

Para volver a instalar, consulte [Instalación del proveedor de recursos de IoT Hub en Azure Stack conectado](iot-hub-rp-install.md).
