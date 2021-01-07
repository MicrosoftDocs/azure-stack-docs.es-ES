---
title: Introducción al planeamiento de capacidad
titleSuffix: Azure Stack Hub
description: Obtenga información sobre el planeamiento de capacidad para las implementaciones de Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 4b3a6b58b6738c4248eaeb5d76b48fa9d1fdb442
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871360"
---
# <a name="capacity-planning-for-azure-stack-hub-overview"></a>Introducción al planeamiento de capacidad de Azure Stack Hub

Al evaluar una solución de Azure Stack Hub, considere las opciones de configuración de hardware que afectan directamente a la capacidad total de la nube de Azure Stack Hub.

Deberá elegir opciones sobre la CPU, la densidad de memoria, la configuración de almacenamiento y la escala de solución global o el número de servidores. Sin embargo, la determinación de la capacidad utilizable será diferente a la de una solución de virtualización tradicional porque ya hay alguna capacidad en uso. Azure Stack Hub se ha creado para hospedar los componentes de infraestructura o administración dentro de la propia solución. Además, parte de la capacidad de la solución está reservada para respaldar la resistencia. La resistencia se define como la actualización del software de la solución de forma que minimice la interrupción de las cargas de trabajo de inquilino.

> [!IMPORTANT]
> Esta información de planeamiento de capacidad y [Capacity Planner de Azure Stack Hub](https://aka.ms/azstackcapacityplanner) son un buen punto de partida para el planeamiento y las decisiones de configuración de Azure Stack Hub. Esta información no está pensada para que actúe como un sustituto de su propia investigación y análisis. Microsoft no ofrece ninguna manifestación o garantía, ni expresa ni implícita, con respecto a la información proporcionada aquí.

## <a name="hyperconvergence-and-the-scale-unit"></a>Hiperconvergencia y unidad de escalado
Se crea una solución de Azure Stack Hub como un clúster hiperconvergido de proceso y almacenamiento. La convergencia permite el uso compartido de la capacidad de hardware en el clúster, lo que se conoce como *unidad de escalado*. En Azure Stack Hub, una unidad de escalado proporciona la disponibilidad y la escalabilidad de los recursos. Una unidad de escalado consta de un conjunto de servidores de Azure Stack Hub, que se conocen como *hosts*. Todo el software de infraestructura se hospeda dentro de un conjunto de máquinas virtuales (VM) y comparte los mismos servidores físicos que las VM del inquilino. A continuación, las tecnologías de agrupación en clústeres de Windows Server de la unidad de escalado y las instancias individuales de Hyper-V administran todas las máquinas virtuales de Azure Stack Hub.

La unidad de escalado simplifica la adquisición y administración de Azure Stack Hub. La unidad de escalado también permite el movimiento y la escalabilidad de todos los servicios (inquilino e infraestructura) en Azure Stack Hub.

En las siguientes secciones, se proporcionan más detalles acerca de cada componente:

- [Proceso de Azure Stack Hub](azure-stack-capacity-planning-compute.md)
- [Almacenamiento de Azure Stack Hub](azure-stack-capacity-planning-storage.md)
- [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md)
