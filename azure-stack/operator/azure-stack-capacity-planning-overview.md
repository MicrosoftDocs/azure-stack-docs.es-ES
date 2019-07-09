---
title: Introducción al planeamiento de capacidad de Azure Stack | Microsoft Docs
description: Obtenga más información sobre el planeamiento de capacidad para las implementaciones de Azure Stack.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 335e92fcdb2eadd8bebfbfd3fb2e3b31edd00734
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131339"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Introducción al planeamiento de capacidad de Azure Stack

Al evaluar una solución de Azure Stack, considere las opciones de configuración de hardware que tienen un impacto directo en la capacidad total de la nube de Azure Stack. 

Por ejemplo, debe elegir opciones sobre la CPU, la densidad de memoria, la configuración de almacenamiento y la escala de solución global o el número de servidores. A diferencia de una solución de virtualización tradicional, la aritmética simple de estos componentes no es aplicable a la hora de determinar la capacidad utilizable. Azure Stack se ha diseñado para hospedar los componentes de infraestructura o administración dentro de la propia solución. Asimismo, parte de la capacidad de la solución está reservada para admitir la resistencia; la actualización del software de la solución de forma que minimice la interrupción de las cargas de trabajo de inquilino. 

> [!IMPORTANT]
> Esta información de planeación de capacidad y [Capacity Planner de Azure Stack](https://aka.ms/azstackcapacityplanner) son un punto de partida para el planeamiento de Azure Stack y las decisiones de configuración. Esta información no está pensada para que actúe como un sustituto de su propia investigación y análisis. Microsoft no ofrece ninguna manifestación o garantía, ni expresa ni implícita, con respecto a la información proporcionada aquí.
 
Se genera una solución de Azure Stack como un clúster hiperconvergido de proceso y almacenamiento. La convergencia permite el uso compartido de la capacidad de hardware en el clúster, lo que se conoce como *unidad de escalado*. En Azure Stack, una unidad de escalado proporciona la disponibilidad y la escalabilidad de los recursos. Una unidad de escalado consta de un conjunto de servidores de Azure Stack, a los que se denomina *hosts*. Todo el software de infraestructura se hospeda dentro de un conjunto de máquinas virtuales (VM) y comparte los mismos servidores físicos que las VM del inquilino. A continuación, las tecnologías de agrupación en clústeres de Windows Server de la unidad de escalado y las instancias individuales de Hyper-V administran todas las VM de Azure Stack. 

La unidad de escalado simplifica la adquisición y administración de Azure Stack. La unidad de escalado también permite el movimiento y la escalabilidad de todos los servicios (inquilino e infraestructura) en Azure Stack. 

En las siguientes secciones, se proporcionan más detalles acerca de cada componente:

- [Proceso de Azure Stack](azure-stack-capacity-planning-compute.md)
- [Almacenamiento de Azure Stack](azure-stack-capacity-planning-storage.md)
- [Capacity Planner de Azure Stack](azure-stack-capacity-planner.md)
