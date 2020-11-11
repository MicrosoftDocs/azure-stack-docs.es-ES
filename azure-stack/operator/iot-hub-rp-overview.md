---
title: Introducción a IoT Hub en Azure Stack Hub
description: Más información acerca del proveedor de recursos de IoT Hub en Azure Stack Hub y diferencias con la versión hospedada de IoT Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: adf6635fbdcf62398218a8e6ad0ce061900e0059
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050400"
---
# <a name="iot-hub-on-azure-stack-hub-overview"></a>Introducción a IoT Hub en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

IoT Hub en Azure Stack Hub le permite crear soluciones de IoT híbridas. IoT Hub es un servicio administrado que actúa como centro de conectividad de mensajes para comunicaciones bidireccionales entre la aplicación de IoT y los dispositivos que administra. Puede usar IoT Hub en Azure Stack Hub para compilar soluciones de IoT con comunicaciones confiables y seguras entre los dispositivos IoT y un back-end de solución en el entorno local. 

## <a name="differences-between-azure-iot-hub-and-iot-hub-on-azure-stack-hub"></a>Diferencias entre Azure IoT Hub e IoT Hub en Azure Stack Hub

| Característica | Azure IoT Hub | IoT Hub en Azure Stack Hub |
|-|-|-|
| Escala | https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling | Se admiten instancias de IoT Hub S2 y S3|
| Actualización del servicio | Automático | Manual |

## <a name="iot-hub-throttling"></a>Limitación de IoT Hub

La limitación y las cuotas de IoT Hub en las SKU S2 y S3 de Azure Stack Hub son las mismas que para IoT Hub en Azure. Consulte [Cuotas y limitaciones de Azure IoT Hub](/azure/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling) para obtener más detalles.

## <a name="next-steps"></a>Pasos siguientes

Para preparar la instalación de IoT Hub en Azure Stack Hub, revise el documento [Requisitos previos](iot-hub-rp-prerequisites.md).

Para más información acerca de la diferencia entre una instancia de Azure Stack Hub conectada y una sin conexión, revise [Modelo de conexión de Azure Stack](azure-stack-connection-models.md).
