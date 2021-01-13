---
title: Notas de la versión del OEM de Azure Stack Hub Ruggedized
description: Notas de la versión del OEM de Azure Stack Hub Ruggedized
author: sethmanheim
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: danlewi
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: a7765eb44017b8d6521930de24794b630ae22344
ms.sourcegitcommit: c5d46662492887b70a599a60f3c3d27e3460a742
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97965467"
---
# <a name="azure-stack-hub-ruggedized-2008-oem-release-notes"></a>Notas de la versión del OEM de Azure Stack Hub Ruggedized 2008

Este artículo contiene información sobre la versión de Azure Stack Hub Ruggedized.

## <a name="overview"></a>Información general

En este documento se describe el contenido de las actualizaciones de origen del firmware y los controladores de Azure Stack Hub Ruggedized. Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack Hub. A continuación se muestran los vínculos de descarga:

* https://aka.ms/azshwpackage
* https://aka.ms/azshwmanifest

## <a name="baseline-and-document-history"></a>Base de referencia e historial de documentos

| Release | Date       | Descripción de los cambios         |
|---------|------------|--------------------------------|
| 2008    | 13/10/2020 | Actualizaciones del paquete del OEM 2.2.2010.5 |

## <a name="firmware"></a>Firmware

### <a name="bios"></a>Bios

| Versión de lanzamiento | Firmware version | Cambios |
|-----------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 2.8.2            | Se ha corregido un problema del sector observado en las versiones de BIOS de la 2.6.4 a la 2.8.1, por el que el sistema podía restablecerse durante el encendido en el momento en que aparecía la configuración de memoria en la pantalla de arranque. El problema es aplicable a las configuraciones de memoria DDR4 y NVDIMM-N.<br><br>Mejora para hacer frente a las vulnerabilidades de seguridad (vulnerabilidades y exposiciones comunes CVE) como CVE-2020-0545, CVE-2020-0548 y CVE-2020-0549. |   |   |
| 2005            | 2.7.7            |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |

### <a name="idrac"></a>IDRAC

| Versión de lanzamiento | Firmware version | Cambios |
|-----------------|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 4.22.0.0         | 167411: Se ha corregido un problema por el que se obligaba a forzar el reinicio del sistema al actualizar el firmware a través de la API de Redfish.<br><br>155376: Se ha corregido un problema que hacía que iDRAC se reiniciara al recopilar registros de SupportAssist.<br><br>162778: Se ha corregido una condición de memoria insuficiente en iDRAC debido al servicio de consola virtual. |   |   |
| 2005            | 4.10.10.10       |                                                                                                                                                                                                                                                                                  |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                  |   |   |

### <a name="nic-azure-stack-nodes"></a>Nodos de Azure Stack NIC

| Versión de lanzamiento    | Firmware version    | Cambios                                                                                                          |
|--------------------|---------------------|------------------------------------------------------------------------------------------------------------------|
|     2008           |     14.27.60.08     | Se ha corregido una aserción grave de firmware que hacía que IRISC dejara de responder debido a que init_hca esperaba la liberación del bloqueo del flujo de temporizadores. |
|     2005           |     14.26.60.00     |                                                                                                                  |

### <a name="nic-hlh"></a>NIC-HLH

| Versión de lanzamiento    | Firmware version    | Cambios                                                                                                                                                                   |
|--------------------|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008           |     19.5.12         | Se ha resuelto un problema en los adaptadores Intel(R) Ethernet X710 de 10 Gb por el que los indicadores LED del dispositivo se quedan encendidos permanentemente después de parpadear cuando el LED del dispositivo está establecido en el modo de intermitencia en la configuración del dispositivo F2. |
|     2005           |     19.0.12         |                                                                                                                                                                           |

### <a name="hba-azure-stack-node-capacity-drives"></a>Unidades de capacidad de los nodos de HBA-Azure Stack

| Versión de lanzamiento    | Firmware version    | Cambios    |
|--------------------|---------------------|------------|
|     2008           |     16.17.01.00     |            |
|     2005           |     16.17.00.05     |            |

### <a name="hba-hlh-capacity-drives"></a>Unidades de capacidad HBA-HLH

|     Versión de lanzamiento |     Firmware version |     Cambios                                                                                           |
|---------------------|----------------------|-------------------------------------------------------------------------------------------------------|
| 2005, 2008          | 25.5.7.0005          | Se ha corregido un problema por el que un controlador podía dejar de responder en el arranque debido a una configuración incompleta de la memoria no volátil. |

### <a name="hba---boot-drives"></a>HBA: unidades de arranque

| Versión de lanzamiento | Firmware version | Cambios                                                                                   |
|-----------------|------------------|-------------------------------------------------------------------------------------------|
| 2005, 2008      | 2.5.13.3024      | Se ha corregido la discrepancia de PPID para las unidades M.2 del controlador BOSS en la página del inventario de hardware de iDRAC |

### <a name="cpld"></a>CPLD

| Versión de lanzamiento | Firmware version | Cambios                                                                                                                                                                                                |
|-----------------|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008        |     9.0.6        | Soluciona un posible problema por el que IDRAC podría dejar de responder durante una actualización del firmware.<br> Se ha agregado el filtrado de ruido de la señal para evitar que se notifiquen errores falsos.<br> Se ha modificado la asignación de memoria del host para evitar la posible deshabilitación del puerto USB frontal. |
|     2005        |     1.0.6        |                                                                                                                                                                                                        |

### <a name="drive-fw"></a>FW de unidad

| Versión de lanzamiento | Firmware version | Cambios                                |
|-----------------|------------------|----------------------------------------|
| 2008            | S402             | Habilita las actualizaciones de firmware en línea futuras |
| 2005            | S401             |                                        |

## <a name="drivers"></a>Controladores

### <a name="nic"></a>NIC

| Versión de lanzamiento | Firmware version | Cambios                                |
|-----------------|------------------|----------------------------------------|
| 2008            | 2.40.22511.0    |  |
| 2005            | 2.30.21713.0 |                                        |

### <a name="hba---capacity-drives"></a>HBA: unidades de capacidad

| Versión de lanzamiento | Firmware version | Cambios |
|-----------------|------------------|---------|
|  2005, 2008   |  2.51.25.02  |         |

### <a name="hba---boot-drives"></a>HBA: unidades de arranque

| Versión de lanzamiento | Firmware version | Cambios |
|-----------------|------------------|---------|
|  2005, 2008   |  1.2.0.1051 |         |

### <a name="intel-chipset"></a>Conjunto de chips de Intel

| Versión de lanzamiento | Firmware version | Cambios |
|-----------------|------------------|---------|
|  2005, 2008   | 10.1.18243.8188 |         |
