---
title: Introducción al servidor EMC PowerEdge Tactical R640
description: Más información sobre el servidor EMC PowerEdge Tactical R640
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d1f0e141c4ca4966aab9735064287121718dee87
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97393125"
---
# <a name="dell-emc-poweredge-tactical-r640-server-overview"></a>Introducción al servidor EMC PowerEdge Tactical R640 de Dell

Esta sección ofrece información general sobre los componentes del servidor EMC PowerEdge Tactical R640 de Dell.

## <a name="chassis-front-view"></a>Vista frontal del chasis

En la siguiente figura se muestra el servidor 2U PowerEdge Tactical R640.

Figura 3. Vista frontal del chasis de los servidores HLH y SU PowerEdge Tactical R640

![](media/image-60.png)

En la tabla siguiente se describen las características del panel frontal del servidor PowerEdge Tactical R640.

Tabla 1. Características del panel frontal del servidor PowerEdge Tactical R640

| Puertos, paneles y ranuras  | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Panel de control izquierdo        | Contiene el indicador de identificador y estado del sistema, el LED de estado y el indicador de iDRAC Quick Sync 2 (inalámbrico). <br>**Nota:** El indicador de iDRAC Quick Sync 2 solo está disponible en determinadas configuraciones. <br>LED de estado: permite identificar los componentes de hardware que tienen errores. Hay hasta cinco LED de estado y una barra de LED de estado general del sistema (estado del chasis e identificador del sistema). Para obtener más información, consulte la tabla Indicadores LED de estado de PowerEdge. <br>Quick Sync 2 (inalámbrico): indica un sistema habilitado para sincronización rápida. La característica Quick Sync es opcional; permite administrar el sistema mediante dispositivos móviles. Esta característica agrega el inventario de hardware o firmware y diversa información de diagnóstico y error en el nivel de sistema que se puede usar para solucionar problemas del sistema.  |
| Puerto VGA                  | Permite conectar un dispositivo de pantalla al sistema.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Panel de control derecho       | Contiene el botón de encendido, el puerto USB, el micropuerto de iDRAC Direct y el LED de estado de iDRAC Direct.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Vista trasera del chasis

En la siguiente figura se muestran los componentes traseros del chasis del servidor PowerEdge Tactical R640.

Figura 4. Vista trasera del chasis del servidor HLH PowerEdge Tactical R640

![](media/image-61.png)

En la tabla siguiente se describen las características del panel trasero del servidor PowerEdge Tactical R640.

Tabla 2. Características del panel trasero del servidor PowerEdge Tactical R640

| Puertos, paneles y ranuras       | Icono                      | Descripción                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Unidades de disco duro                   | N/D                       | Sistema operativo y almacenamiento de datos para los nodos de servidor HLH y SU.                                                                                                                                                                                                                        |
| Módulo BOSS                   | N/D                       | Solución de almacenamiento optimizado de arranque (BOSS) que se usa para instalar el sistema operativo o el hipervisor.                                                                                                                                                                                 |
| Unidades de fuente de alimentación (2)        | N/D                       | Aportan alimentación al servidor y se proporcionan en tándem para ofrecer redundancia.                                                                                                                                                                                                 |
| Puertos USB 3.0                 |  ![](media/image-62.png)   | Los puertos USB tienen 9 clavijas y son compatibles con 3.0. Estos puertos permiten conectar dispositivos USB al sistema.                                                                                                                                                                     |
| Puerto VGA                      |   ![](media/image-63.png)  | Permite conectar un dispositivo de pantalla al sistema.                                                                                                                                                                                                                      |
| Puerto serie                   |   ![](media/image-64.png)  | Permite conectar un dispositivo serie al sistema.                                                                                                                                                                                                                       |
| Puerto de iDRAC9 Enterprise        |   ![](media/image-65.png)  | Permite acceder de forma remota a iDRAC.                                                                                                                                                                                                                                       |
| Puerto de alimentación de CMA                | N/D                       | El puerto de alimentación del soporte de orientación de cables (CMA) permite conectar el cable indicador de estado al CMA.                                                                                                                                                                     |
| Botón de identificación del sistema  |   ![](media/image-66.png) | El botón de identificación del sistema está disponible en la parte delantera y trasera del servidor. Al presionar el botón, el indicador de identificador y estado del sistema parpadea. También puede usar el botón de identificador del sistema para restablecer el iDRAC y acceder al BIOS mediante el modo de paso a paso.  |

## <a name="right-control-panel"></a>Panel de control derecho

En la siguiente figura se muestra el panel de control derecho del servidor PowerEdge Tactical R640.

Figura 5. Panel de control derecho del servidor PowerEdge Tactical R640

![](media/image-67.png)

En la tabla siguiente se describen los indicadores y controles del panel derecho.

Tabla 3. Indicadores y controles del panel de control derecho del servidor PowerEdge Tactical R640

| Elemento  | Indicador o botón  | Icono                      | Descripción                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Botón de encendido         |   ![](media/image-68.png)  | Indica si el sistema está encendido o apagado. Presione el botón de encendido para encender o apagar manualmente el sistema.  **Nota:** Presione el botón de encendido para apagar correctamente un sistema operativo compatible con ACPI.  |
| 2     | Puerto USB             | ![](media/image-70.png)    | El puerto USB tiene 4 patillas y es compatible con 2.0. Este puerto le permite conectar dispositivos USB al sistema.                                                                                                         |
| 3     | LED de iDRAC Direct     | N/D                       | El indicador LED de iDRAC Direct se enciende para indicar que el puerto de iDRAC Direct está conectado activamente a un dispositivo.                                                                                        |
| 4     | Puerto de iDRAC Direct    |   ![](media/image-65.png) | El puerto de iDRAC Direct es un micropuerto compatible con USB 2.0. Este puerto permite acceder a las características de iDRAC Direct.                                                                                              |

## <a name="inside-the-poweredge-tactical-r640"></a>Interior del servidor PowerEdge Tactical R640

En la siguiente figura se muestran los componentes internos del servidor PowerEdge Tactical R640.

Figura 6. Interior del servidor HLH PowerEdge Tactical R640

![](media/image-71.png)

Ilustración 7. Interior del servidor SU PowerEdge Tactical R640

![](media/image-72.png)

## <a name="motherboard"></a>Placa base

En la siguiente figura se muestra el diseño de la placa base.

Figura 8. Diseño de la placa base del servidor PowerEdge Tactical R640

![](media/image-73.png)

## <a name="memory"></a>Memoria

En la siguiente figura se muestra la información de memoria del servidor PowerEdge Tactical R640.

Figura 9. Información de memoria del servidor PowerEdge Tactical R640

![](media/image-74.png)
