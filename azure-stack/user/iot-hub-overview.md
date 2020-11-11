---
title: Introducción a IoT Hub en Azure Stack Hub
description: Obtenga más información sobre el proveedor de recursos de IoT Hub en Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 098168947a28214a3955a1961c80a2c06825126b
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064725"
---
# <a name="overview-of-iot-hub-on-azure-stack-hub"></a>Introducción a IoT Hub en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

IoT Hub en Azure Stack Hub le permite crear soluciones de IoT híbridas. IoT Hub es un servicio administrado que actúa como centro de conectividad de mensajes para comunicaciones bidireccionales entre la aplicación de IoT y los dispositivos que administra. Puede usar IoT Hub en Azure Stack Hub para compilar soluciones de IoT con comunicaciones confiables y seguras entre los dispositivos IoT y un back-end de solución en el entorno local.

## <a name="features"></a>Características

| Característica | IoT Hub en Azure | IoT Hub en Azure Stack Hub (versión preliminar) (S2/S3) |
|-|-|-|
|Telemetría del dispositivo a la nube| ✔ | ✔ |
|Mensajería de la nube a un dispositivo| ✔ | ✔ |
|Identidad por dispositivo| ✔ | ✔ |
|Enrutamiento de mensajes <sub>1</sub><sub>, 4</sub>| ✔ | ✔ |
|Protocolos HTTP, AMQP y MQTT| ✔ | ✔ |
|Servicios multiinquilino| ✔ | ✔ |
|Supervisión y diagnóstico| ✔ | ✔ |
|Mensajería de la nube a un dispositivo| ✔ | ✔ |
|Administración de dispositivos, dispositivo gemelo, módulo gemelo| ✔ | ✔ |
|Notificaciones gemelas, eventos del ciclo de vida del dispositivo| ✔ | ✔ |
|Implementación superpuesta de Edge| ✔ | próximamente |
|Portal de administración <sub>2</sub>| ✘ | ✔ |
|Rotación de secretos <sub>2</sub>| ✘ | ✔ |
|Administración de la capacidad <sub>2</sub>| ✘ | ✔ |
|Copia de seguridad y restauración <sub>3</sub>| ✘ | ✘ |
|DeviceConnected, DeviceDisconnected, ASC <sub>4</sub>| ✔ | ✘ |
|Configuración del módulo del dispositivo| ✔ | próximamente |
|Streaming de dispositivos, IoT Plug and Play, trabajos, carga de archivos <sub>5</sub>| ✔ | ✘ |
|Supervisión del estado de conexión del dispositivo con Event Grid <sub>4</sub>| ✔ | ✘ |
|Conmutación por error <sub>6</sub>| ✔ | ✘ |

<sub>1</sub> Limitado a los puntos de conexión integrados, Event Hubs y Storage. Service Bus no está disponible en Azure Stack Hub.  
<sub>2</sub> Para que los operadores administren IoT Hub en ASH.  
<sub>3</sub> La copia de seguridad está disponible en la versión preliminar. La restauración se admitirá en la fase de disponibilidad general.  
<sub>4</sub> Depende de otros servicios que no están disponibles en Azure Stack Hub.  
<sub>5</sub> En la hoja de ruta para llevar a Azure Stack Hub.  
<sub>6</sub> No aplicable en Azure Stack Hub.  

## <a name="api-available-for-iot-hub-on-azure-stack-hub"></a>API disponible para IoT Hub en Azure Stack Hub

|API existentes|IoT Hub en Azure Stack Hub|
|-|-|
|Apply Configuration On Device| ✔ |
| Configuration Create | ✔ |
| Configuration Delete | ✔ |
| Configuration Read | ✔ |
|Configuration Read Many| ✔ |
|Configuration Service Apply|  ✔ |
|Configuration Update|  ✔ |
|Device Direct Invoke Method|  ✔ |
|GetDeviceAndModuleInScope|  ✔ |
|GetDevicesAndModulesInScope| ✔ |
|Unregister Device| ✔ |
|Get Devices| ✔ |
|Update Module Twin| ✔ |
|D2C Get Twin| ✔ |
|Importa los dispositivos| ✔ |
|Get Twin| ✔ |
|Unregister Module| ✔ |
|Actualizar dispositivo| ✔ |
|Update Module| ✔ |
|Query Devices| ✔ |
|Exporta los dispositivos| ✔ |
|Back up and Restore – ADM| ✔ |
|Replace Twin| ✔ |
|Back up and Restore – DSS| ✔ |
|D2C Notify DesiredProperties| ✔ |
|D2C Patch ReportedProperties| ✔ |
|Get Module Twin| ✔ |
|Module D2C Get Twin| ✔ |
|Obtener módulo| ✔ |
|Module D2C Notify DesiredProperties| ✔ |
|Module D2C Patch ReportedProperties| ✔ |
|Module Direct Invoke Method| ✔ |
|Update Twin| ✔ |
|Bulk Device Operations| ✔ |
|Device to Cloud Telemetry| ✔ |
|Register Device| ✔ |
|Register Module| ✔ |
|Replace Module Twin| ✔ |
|GenericAuthentication| ✔ |
|Get Device| ✔ |
|Partition Move/Role Change| ✔ |

## <a name="differences-between-iot-hub-on-azure-cloud-and-iot-hub-on-azure-stack"></a>Diferencias entre IoT Hub en la nube de Azure y IoT Hub en Azure Stack

| Aspecto | IoT Hub en la nube | IoT Hub en Stack |
|-|-|-|
| Consumo de mensajes | https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin |De forma predeterminada, los mensajes se enrutan al punto de conexión orientado al servicio integrado ( /messages/events), que es compatible con Event Hubs. En la nube de Azure, puede acceder a los mensajes desde el punto de conexión proporcionando la cadena de conexión de IoT Hub o una cadena de conexión de Event Hubs. Sin embargo, en Azure Stack Hub, solo se admite la cadena de conexión de Event Hubs. |

## <a name="next-steps"></a>Pasos siguientes

Si IoT Hub no está disponible en su suscripción, colabore con el administrador para [instalar IoT Hub en el proveedor de recursos de Azure Stack Hub](../operator/iot-hub-rp-overview.md).

Para más información sobre cómo usar IoT Hub, consulte la [documentación de Azure IoT Hub](/azure/iot-hub/).

