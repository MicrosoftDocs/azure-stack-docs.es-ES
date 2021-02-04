---
title: Consumo de datos de supervisión de Azure Stack Hub
description: Obtenga más información sobre las opciones para consumir datos de supervisión de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: b4398423fa50c0c9ff90cd9d9c73a48760791a8a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247632"
---
# <a name="consume-monitoring-data-from-azure-stack-hub"></a>Consumo de datos de supervisión de Azure Stack Hub

Con la canalización de Azure Monitor, los datos de supervisión se encuentran en una única ubicación, al igual que ocurre con Azure Monitor en Azure global. Sin embargo, no todos los datos de supervisión de Azure global están disponibles en Azure Stack Hub. En este artículo, se proporciona un resumen de las distintas formas de utilizar los datos de supervisión en Azure Stack Hub.
 
## <a name="options-for-data-consumption"></a>Opciones para el consumo de datos

| Tipo de datos | Category | Servicios admitidos | Métodos de acceso |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Métricas en el nivel de plataforma de Azure Monitor | Métricas | [Métricas de Azure Stack Hub compatibles con Azure Monitor](azure-stack-metrics-supported.md) | API DE REST |
| Cálculo de métricas del sistema operativo invitado (por ejemplo, el recuento de rendimiento) | Métricas | Máquinas virtuales Windows y Linux | Tabla o blob de almacenamiento:<br>Azure Diagnostics para Windows o Linux <br>Centro de eventos:<br>Windows Azure Diagnostics |
| Métricas de almacenamiento | Métricas | Azure Storage | Tabla de almacenamiento:<br>Storage Analytics |
| Registro de actividades | Eventos | Todos los servicios de Azure | API REST:<br>Azure Monitor Event API |
| Cálculo de los registros del sistema operativo invitado (por ejemplo, IIS, ETW, syslogs) | Eventos | Máquinas virtuales Windows y Linux | Tabla o blob de almacenamiento:<br>Azure Diagnostics para Windows o Linux <br>Centro de eventos:<br>Windows Azure Diagnostics |
| Registros de almacenamiento | Eventos | Azure Storage | Tabla de almacenamiento:<br>Storage Analytics |

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre [Azure Monitor en Azure Stack Hub](azure-stack-metrics-azure-data.md).
