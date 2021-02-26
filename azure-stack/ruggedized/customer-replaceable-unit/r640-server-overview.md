---
title: Introducción al servidor de
description: Obtenga información sobre el servidor de Azure Stack Hub Ruggedized
author: justinha
ms.topic: how-to
ms.date: 01/28/2021
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: ''
ms.openlocfilehash: a922c99121157c03705784dd99b8abf97fbec507
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577376"
---
# <a name="server-overview"></a>Introducción al servidor de

Esta sección es una introducción a los componentes del servidor.

## <a name="chassis-front-view"></a>Vista frontal del chasis

En la siguiente ilustración se muestra el servidor 2U.


![Diagrama que muestra el chasis frontal del servidor.](media/image-60.png)

En la tabla siguiente se describen las características del panel frontal.

| Puertos, paneles y ranuras  | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Panel de control izquierdo        | Contiene el indicador de identificador y estado del sistema, el LED de estado y el indicador de iDRAC Quick Sync 2 (inalámbrico). <br>**Nota:** El indicador de iDRAC Quick Sync 2 solo está disponible en determinadas configuraciones. <br>LED de estado: permite identificar los componentes de hardware que tienen errores. Hay hasta cinco LED de estado y una barra de LED de estado general del sistema (estado del chasis e identificador del sistema). <br>Quick Sync 2 (inalámbrico): indica un sistema habilitado para sincronización rápida. La característica Quick Sync es opcional; permite administrar el sistema mediante dispositivos móviles. Esta característica agrega el inventario de hardware o firmware y diversa información de diagnóstico y error en el nivel de sistema que se puede usar para solucionar problemas del sistema.  |
| Puerto VGA                  | Permite conectar un dispositivo de pantalla al sistema.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Panel de control derecho       | Contiene el botón de encendido, el puerto USB, el micropuerto de iDRAC Direct y el LED de estado de iDRAC Direct.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Vista trasera del chasis

En la siguiente ilustración se muestran los componentes del chasis trasero.


![Diagrama que muestra el chasis trasero del servidor.](media/image-61.png)

En la tabla siguiente se describen las características del panel trasero.


| Puertos, paneles y ranuras       | Icono                      | Descripción                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Unidades de disco duro                   | N/D                       | Sistema operativo y almacenamiento de datos para los nodos de servidor HLH y SU.                                                                                                                                                                                                                        |
| Módulo BOSS                   | N/D                       | Solución de almacenamiento optimizado de arranque (BOSS) que se usa para instalar el sistema operativo o el hipervisor.                                                                                                                                                                                 |
| Unidades de fuente de alimentación (2)        | N/D                       | Aportan alimentación al servidor y se proporcionan en tándem para ofrecer redundancia.                                                                                                                                                                                                 |
| Puertos USB 3.0                 |  :::image type="icon" source="media/image-62.png"::: | Los puertos USB tienen 9 clavijas y son compatibles con 3.0. Estos puertos permiten conectar dispositivos USB al sistema.                                                                                                                                                                     |
| Puerto VGA                      |   :::image type="icon" source="media/image-63.png":::  | Permite conectar un dispositivo de pantalla al sistema.                                                                                                                                                                                                                      |
| Puerto serie                   |   :::image type="icon" source="media/image-64.png":::  | Permite conectar un dispositivo serie al sistema.                                                                                                                                                                                                                       |
| Puerto de iDRAC9 Enterprise        |   :::image type="icon" source="media/image-65.png":::  | Permite acceder de forma remota a iDRAC.                                                                                                                                                                                                                                       |
| Puerto de alimentación de CMA                | N/D                       | El puerto de alimentación del soporte de orientación de cables (CMA) permite conectar el cable indicador de estado al CMA.                                                                                                                                                                     |
| Botón de identificación del sistema  |   :::image type="icon" source="media/image-66.png"::: | El botón de identificación del sistema está disponible en la parte delantera y trasera del servidor. Al presionar el botón, el indicador de identificador y estado del sistema parpadea. También puede usar el botón de identificador del sistema para restablecer el iDRAC y acceder al BIOS mediante el modo de paso a paso.  |

## <a name="right-control-panel"></a>Panel de control derecho

La ilustración siguiente muestra el panel de control derecho.

![Diagrama en el que se muestra el botón de encendido y los puertos USB del panel de control.](media/image-67.png)

En la tabla siguiente se describen los indicadores y controles del panel derecho.


| Elemento  | Indicador o botón  | Icono                      | Descripción                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Botón de encendido         |   :::image type="icon" source="media/image-68.png":::  | Indica si el sistema está encendido o apagado. Presione el botón de encendido para encender o apagar manualmente el sistema.  **Nota:** Presione el botón de encendido para apagar correctamente un sistema operativo compatible con ACPI.  |
| 2     | Puerto USB             | :::image type="icon" source="media/image-70.png":::    | El puerto USB tiene 4 patillas y es compatible con 2.0. Este puerto le permite conectar dispositivos USB al sistema.                                                                                                         |
| 3     | LED de iDRAC Direct     | N/D                       | El indicador LED de iDRAC Direct se enciende para indicar que el puerto de iDRAC Direct está conectado activamente a un dispositivo.                                                                                        |
| 4     | Puerto de iDRAC Direct    |   :::image type="icon" source="media/image-65.png"::: | El puerto de iDRAC Direct es un micropuerto compatible con USB 2.0. Este puerto permite acceder a las características de iDRAC Direct.                                                                                              |

## <a name="inside-the-server"></a>Interior del servidor

En las siguientes ilustraciones se muestran los componentes internos del servidor.

![Diagrama en el que se muestra una vista del lado superior de los componentes internos del servidor HLH R640.](media/image-71.png)


![Diagrama en el que se muestra una vista del lado superior de los componentes internos del servidor SU R640.](media/image-72.png)

## <a name="motherboard"></a>Placa base

En la siguiente figura se muestra el diseño de la placa base.


![Diagrama en el que se muestra la información del servicio y el diseño de la placa base del servidor R640.](media/image-73.png)

## <a name="memory"></a>Memoria

En la siguiente figura se muestra la información de memoria.


![Diagrama en el que se muestra la información de memoria del servidor R640.](media/image-74.png)
