---
title: Consumo de datos de supervisión de Azure Stack | Microsoft Docs
description: Obtenga más información sobre las opciones para consumir datos de supervisión de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 20e4f71480aa377e56115c499f96492e768010c3
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955735"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Consumo de datos de supervisión de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Con la canalización de Azure Monitor, los datos de supervisión se encuentran en una única ubicación, al igual que ocurre con Azure Monitor en Azure global. Sin embargo, no todos los datos de supervisión de Azure global están disponibles en Azure Stack. En este artículo, se proporciona un resumen de las distintas formas de utilizar los datos de supervisión en Azure Stack.
 
## <a name="options-for-data-consumption"></a>Opciones para el consumo de datos

| Tipo de datos | Category | Servicios admitidos | Métodos de acceso |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Métricas en el nivel de plataforma de Azure Monitor | Métricas | [Métricas de Azure Stack compatibles con Azure Monitor](azure-stack-metrics-supported.md) | API DE REST |
| Cálculo de métricas del sistema operativo invitado (por ejemplo, el recuento de rendimiento) | Métricas | Máquinas virtuales Windows y Linux | Tabla o blob de almacenamiento:<br>Azure Diagnostics para Windows o Linux <br>Centro de eventos:<br>Windows Azure Diagnostics |
| Métricas de almacenamiento | Métricas | Azure Storage | Tabla de almacenamiento:<br>Storage Analytics |
| Registro de actividades | Eventos | Todos los servicios de Azure | API REST:<br>Azure Monitor Event API |
| Cálculo de los registros del sistema operativo invitado (por ejemplo, IIS, ETW, syslogs) | Eventos | Máquinas virtuales Windows y Linux | Tabla o blob de almacenamiento:<br>Azure Diagnostics para Windows o Linux <br>Centro de eventos:<br>Windows Azure Diagnostics |
| Registros de almacenamiento | Eventos | Azure Storage | Tabla de almacenamiento:<br>Storage Analytics |

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre [Azure Monitor en Azure Stack](azure-stack-metrics-azure-data.md).
