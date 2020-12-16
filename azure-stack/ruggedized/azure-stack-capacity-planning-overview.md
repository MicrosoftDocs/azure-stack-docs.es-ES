---
title: Introducción al planeamiento de capacidad de Azure Stack Hub | Microsoft Docs
description: Obtenga información sobre el planeamiento de capacidad para las implementaciones de Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 7ccaee0f134f2aa2a56f0fe14bab8fba72a84726
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941386"
---
# <a name="overview-of-azure-stack-hub-capacity-planning"></a>Introducción al planeamiento de la capacidad de Azure Stack Hub

Al evaluar una solución de Azure Stack Hub, considere las opciones de configuración de hardware que afectan directamente a la capacidad total de la nube de Azure Stack Hub. 

Por ejemplo, debe elegir opciones sobre la CPU, la densidad de memoria, la configuración de almacenamiento y la escala de solución global o el número de servidores. A diferencia de una solución de virtualización tradicional, la aritmética simple de estos componentes no es aplicable a la hora de determinar la capacidad utilizable. Azure Stack Hub se ha creado para hospedar los componentes de infraestructura o administración dentro de la propia solución. Asimismo, parte de la capacidad de la solución está reservada para admitir la resistencia; la actualización del software de la solución de forma que minimice la interrupción de las cargas de trabajo de inquilino. 

> [!IMPORTANT]
> Esta información de planeamiento de capacidad y [Capacity Planner de Azure Stack Hub](https://aka.ms/azstackcapacityplanner) son un buen punto de partida para el planeamiento y las decisiones de configuración de Azure Stack Hub. Esta información no está pensada para que actúe como un sustituto de su propia investigación y análisis. Microsoft no ofrece ninguna manifestación o garantía, ni expresa ni implícita, con respecto a la información proporcionada aquí.
 
Se crea una solución de Azure Stack Hub como un clúster hiperconvergido de proceso y almacenamiento. La convergencia permite el uso compartido de la capacidad de hardware en el clúster, lo que se conoce como *unidad de escalado*. En Azure Stack Hub, una unidad de escalado proporciona la disponibilidad y la escalabilidad de los recursos. Una unidad de escalado consta de un conjunto de servidores de Azure Stack Hub, que se conocen como *hosts*. Todo el software de infraestructura se hospeda dentro de un conjunto de máquinas virtuales (VM) y comparte los mismos servidores físicos que las VM del inquilino. A continuación, las tecnologías de agrupación en clústeres de Windows Server de la unidad de escalado y las instancias individuales de Hyper-V administran todas las máquinas virtuales de Azure Stack Hub. 

La unidad de escalado simplifica la adquisición y administración de Azure Stack Hub. La unidad de escalado también permite el movimiento y la escalabilidad de todos los servicios (inquilino e infraestructura) en Azure Stack Hub. 

En las siguientes secciones, se proporcionan más detalles acerca de cada componente:

- [Proceso de Azure Stack Hub](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Almacenamiento de Azure Stack Hub](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Azure Stack Hub Capacity Planner](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)

## <a name="azure-stack-hub-ruggedized-high-and-low-models"></a>Modelos más y menos potentes de Azure Stack Hub Ruggedized

En la tabla siguiente se enumeran las especificaciones de los modelos más y menos potente de Azure Stack Hub Ruggedized.

| Componente               | Especificación |
|-------------------------|---------------|
| CPU                     |Alta: 284 núcleos de vCPU<br>Baja: 184 núcleos de vCPU  |
| Memoria                  |Alta: 1,037 GB<br>Baja: 547 GB                |
| Storage                 |Alta: 34,2 TB<br>Baja: 15,4 TB                |
| Acceleration            |N/D                                          |
| Tamaño/peso             |Proceso: 31,5" de largo x 23,8" de ancho x 15,33" de alto<br>Peso 120 libras (54,43 kg). (cantidad: 2)<br>Redes: 31,5" de largo x 23,8" de ancho x 17,12" de alto<br>Peso 145 libras (65,77 kg). (cantidad: 1)              |
| Configuraciones opcionales |Precalentador<br>Configuración más y menos potente     |

## <a name="next-steps"></a>Pasos siguientes

[Proceso de Azure Stack Hub](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
[Almacenamiento de Azure Stack Hub](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
[Herramienta de planeamiento de capacidad de Azure Stack Hub](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
