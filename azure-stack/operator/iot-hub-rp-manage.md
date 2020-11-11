---
title: Administración de IoT Hub en Azure Stack Hub
description: Experiencia y alertas de administración de IoT Hub
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: ca410a3ec5b66a3ab3243ae06f82f4cb736af836
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050412"
---
# <a name="how-to-manage-iot-hub-on-azure-stack-hub"></a>Administración de IoT Hub en Azure Stack Hub

La experiencia de administración de IoT Hub le permite visualizar y administrar el estado general, las alertas y la capacidad.

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

## <a name="iot-hub-management-dashboard"></a>Panel de administración de IoT Hub

Para acceder al panel de administración de IoT Hub:

1. Inicie sesión en el portal de administración de Azure Stack Hub, seleccione la vista **Panel** a la izquierda y, a continuación, seleccione el proveedor de recursos de **IoT Hub** :

   [![panel del operador](media\iot-hub-rp-manage\dashboard.png)](media\iot-hub-rp-manage-capacity\dashboard.png#lightbox)

2. En el panel de IoT Hub se proporciona una vista de resumen, que muestra las alertas actuales, las cuotas creadas en la unidad de escalado y el número total de clústeres de IoT Hub de la suscripción: 

   [![Panel de IoT Hub: información general](media\iot-hub-rp-manage\dashboard-rp-iot-hub-overview.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-overview.png#lightbox)

## <a name="alerts"></a>Alertas

El proveedor de recursos de IoT Hub admite las siguientes alertas:

|Category|Alerta|Tipo|Descripción|
|-|-|-|-|
|Rendimiento|El uso de la CPU de IoT Hub requiere atención.|Advertencia|El promedio de % de uso de CPU del proveedor de recursos de IoT Hub en las últimas seis horas es superior al 75 %.|
|Rendimiento|El uso de memoria de IoT Hub requiere atención.|Advertencia|El uso de memoria restante del proveedor de recursos de IoT Hub durante las 6 últimas horas es inferior a 1024 MB.|
|Rendimiento|Espacio en disco insuficiente para el proveedor de recursos de IoT Hub.|Advertencia|El espacio en disco restante es inferior al 10 %.|
|Resource|Error al crear o actualizar el recurso de IoT Hub.|Advertencia|El número de errores de creación o actualización del proveedor de recursos de IoT Hub es como mínimo 1 en 15 minutos.|
|Servicio|Los errores de registro del proveedor de recursos de IoT Hub requieren atención.|Advertencia|El número de errores de registro del proveedor de recursos de IoT Hub por instancia de rol es superior a 3 en 15 minutos.|
|Servicio|IoTHub-SU-InternalError|Advertencia|El número de errores internos de la unidad de almacenamiento de IoT Hub y de tiempo de espera es como mínimo 5 en los últimos 30 minutos.|
|Servicio|Se ha producido un error en la copia de seguridad del plano de datos de IoT Hub.|Advertencia|Se han producido errores en la copia de seguridad del plano de datos de IoT Hub en los últimos 15 minutos. Los datos del dispositivo no se protegerán.|
|Servicio|Se han producido errores en copias de seguridad consecutivas del plano de datos de IoT Hub.|Advertencia|Se han producido errores en copias de seguridad consecutivas del plano de datos de IoT Hub en los últimos 15 minutos. Los datos del dispositivo no se protegerán.|
|Servicio|Se han producido errores en copias de seguridad del plano de datos de IoT Hub.|Advertencia|No se ha realizado una copia de seguridad correcta de IoT Hub en las últimas 3 horas. Es posible que los datos del dispositivo no se protejan.|
|Servicio|Error en la restauración del plano de datos de IoT Hub.|Advertencia|Error en la restauración de la información del dispositivo de IoT Hub. IotHubPerformanceMetrics ha superado el umbral durante los últimos 15 minutos.|
|Servicio|Se ha producido un error de puerta de enlace de IoT Hub.|Advertencia|Se ha producido un error de puerta de enlace de IoT Hub. La funcionalidad de telemetría del dispositivo puede verse afectada.|
|Servicio|Se ha producido un error de contenedor de IoT Hub.|Advertencia|Se ha producido un error de contenedor de IoT Hub. Esto puede dar lugar a un error de autenticación del dispositivo. |
|Servicio|Se ha producido un error de contenedor en la administración de dispositivos de IoT Hub.|Advertencia|Se ha producido un error de contenedor en la administración de dispositivos de IoT Hub. Las funcionalidades de dispositivos gemelos, de método directo, se pueden degradar.|

Para supervisar alertas y cuotas:

1. Seleccione **Alertas** para ver el historial de alertas: 
 
   [![Panel de IoT Hub: alertas](media\iot-hub-rp-manage\dashboard-rp-iot-hub-alerts.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-alerts.png#lightbox)  

2. Seleccione **Cuotas** para ver la lista de cuotas en vigor:  

   > [!NOTE]
   > Durante la versión preliminar, se deshabilita la característica **Create** y se proporciona una cuota predeterminada ilimitada. La característica **Create** se habilitará para disponibilidad general.

   [![Panel de IoT Hub: cuotas](media\iot-hub-rp-manage\dashboard-rp-iot-hub-quotas.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-quotas.png#lightbox) 

## <a name="capacity-management"></a>Administración de capacidades

Como operador, puede administrar y controlar su instancia de Azure Stack Hub de la misma manera que cualquier operador de la nube. Asegúrese de que el software se instala correctamente, se configura correctamente y de forma segura, y funciona con alta disponibilidad de forma coherente y eficaz. Deberá aplicar los principios de administración de capacidad en el funcionamiento de la instancia de Azure Stack Hub y del proveedor de recursos de IoT Hub.

### <a name="azure-stack-hub-capacity-management"></a>Administración de la capacidad de Azure Stack Hub

Para determinar la capacidad necesaria para IoT Hub, deberá realizar una estimación de la carga de trabajo, principalmente el número de dispositivos y el rendimiento de los mensajes. Como ayuda para la planeación, hemos realizado las siguientes pruebas en un entorno de cuatro nodos de Azure Stack Hub como referencia:

| Escenario | Máquinas virtuales | Núcleos virtuales | Dispositivos por centro de conectividad | [Edición del centro de conectividad](https://azure.microsoft.com/pricing/details/iot-hub) | Concentradores | Unidades por centro de conectividad | Número total de dispositivos | Unidades totales del centro de conectividad | Millones de mensajes por día |
|----------|---------------|------------------|-----------------------|-------------------|-|-|-|-|-|
|Predeterminado|5|20|300 000|S2|4|200|1 200 000|800|4,800|
|12 máquinas virtuales|12|48|500.000|S2|4|200|2 000 000|800|4,800|
|18 máquinas virtuales|18|72|400.000|S3|4|10|1 600 000|40|12,000|

Consulte la información sobre la [herramienta de planeamiento de capacidad de Azure Stack Hub](azure-stack-capacity-planner.md) para conocer más detalles.

### <a name="iot-hub-capacity-management"></a>Administración de la capacidad de IoT Hub

Como Azure Stack Hub se implementa en un centro de datos local con recursos limitados, todos los servicios que se ejecutan en Azure Stack Hub comparten y compiten por el mismo grupo de recursos. Los operadores necesitan planear y administrar la capacidad en función de las necesidades de la empresa. El proveedor de recursos de IoT Hub ofrece a los operadores la posibilidad de administrar los requisitos de capacidad del servicio.

IoT Hub tiene un único tipo de máquina virtual. Como parte de la implementación de IoT Hub, el sistema aprovisiona un conjunto de estas máquinas virtuales en Azure Stack Hub. Las máquinas virtuales pueden admitir un determinado número de dispositivos y de rendimiento de los mensajes. La configuración predeterminada debe cumplir con la mayoría de los requisitos. No obstante, si necesita más dispositivos o un mayor rendimiento de los mensajes, puede aumentar la capacidad mediante el portal de administración, la CLI o PowerShell. 

Para supervisar y cambiar la configuración de la capacidad:

1. Seleccione la vista **Capacidad** de la izquierda. Verá el estado de la capacidad, incluido el número de máquinas virtuales aprovisionadas en un clúster de IoT Hub y el uso de recursos. El número de nodos que aparece es el número actual de nodos asignados a IoT Hub. 

2. Para aumentar la capacidad, seleccione el nombre del clúster de IoT Hub, cambie el número de nodos y, a continuación, seleccione **Update Scale** (Actualizar escala). Puede aumentar la capacidad agregando tantas máquinas virtuales como permitan los recursos disponibles.

   > [!IMPORTANT]
   > Solo se admite el escalado horizontal de clústeres de IoT Hub (de menor a mayor) en la versión preliminar. La reducción horizontal (de mayor a menor) se admitirá en la versión de disponibilidad general de IoT Hub.

   [![Panel de IoT Hub: capacidad](media\iot-hub-rp-manage\dashboard-rp-iot-hub-capacity.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-capacity.png#lightbox)


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre:

Las funcionalidades de supervisión de Azure Stack Hub, incluidas las alertas, consulte [Supervisión de estado y alertas](azure-stack-monitor-health.md).

La colección de registros de Azure Stack Hub, consulte [Introducción a la recopilación de registros de diagnóstico de Azure Stack Hub](azure-stack-diagnostic-log-collection-overview.md).

