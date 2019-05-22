---
title: Planeamiento de capacidad de Azure Stack | Microsoft Docs
description: Obtenga más información sobre el planeamiento de capacidad para las implementaciones de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 1a5bde916779961f9cc4c099b57324b8ddbd3bda
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984496"
---
# <a name="azure-stack-capacity-planning"></a>Planeamiento de capacidad de Azure Stack
Al evaluar una solución de Azure Stack, existen opciones de configuración de hardware que tienen un impacto directo en la capacidad total de la nube de Azure Stack. Estas son las opciones clásicas de CPU, densidad de memoria, configuración de almacenamiento y escala de solución global o número de servidores. A diferencia de una solución de virtualización tradicional, la aritmética simple de estos componentes no es aplicable a la hora de determinar la capacidad utilizable. El primer motivo es que Azure Stack se ha diseñado para hospedar los componentes de infraestructura o administración dentro de la propia solución. El segundo motivo es que parte de la capacidad de la solución está reservada en apoyo de la resistencia; la actualización del software de la solución de forma que minimice la interrupción de las cargas de trabajo de inquilino.

> [!IMPORTANT]
> La información del planeamiento de capacidad que se ha proporcionado, así como la hoja de cálculo que lo acompaña, pretenden ser únicamente una guía que le ayudará a tomar decisiones de configuración y planeamiento de Azure Stack. No están pensados para que actúen como un sustituto de su propia investigación y análisis. 

## <a name="compute-and-storage-capacity-planning"></a>Planeamiento de la capacidad de almacenamiento y proceso
Se genera una solución de Azure Stack como un clúster hiperconvergido de proceso, red y almacenamiento. Esto permite un uso eficaz o un uso compartido de toda la capacidad de hardware del clúster, a lo que se hace referencia como unidad de escalado para Azure Stack, de forma que proporciona disponibilidad y escalabilidad. Todo el software de infraestructura se hospeda dentro de un conjunto de máquinas virtuales (VM) y comparte los mismos servidores físicos que las máquinas virtuales del inquilino. Las tecnologías de agrupación en clústeres de Windows Server de la unidad de escalado y las instancias individuales de Hyper-V administran todas las máquinas virtuales. Este enfoque simplifica la adquisición y administración de una solución de Azure Stack y permite el movimiento y la escalabilidad de todos los servicios (inquilino e infraestructura) en la totalidad de la unidad de escalado.

El único recurso físico que no se aprovisiona en exceso en una solución de Azure Stack es la memoria del servidor. Los otros recursos, los núcleos de CPU, el ancho de banda de red y la capacidad de almacenamiento se aprovisionarán en exceso para usar de la mejor forma posible los recursos disponibles. Al calcular la capacidad disponible para una solución, la memoria del servidor físico es el principal colaborador. El uso de otros recursos consiste en comprender la proporción del aprovisionamiento en exceso que es posible y qué se aceptará para la carga de trabajo prevista.

Aproximadamente, se usan 30 máquinas virtuales para hospedar la infraestructura de Azure Stack y, en total, se consumen alrededor de 230 GB de memoria y 140 núcleos virtuales. La razón de este número de máquinas virtuales es satisfacer la separación del servicio que se necesita para cumplir los requisitos de seguridad, escalabilidad, servicio y aplicación de revisión. Esta estructura de servicio interno permite la futura introducción de nuevos servicios de infraestructura mientras se desarrollan.

Para admitir la actualización automatizada de todos los componentes de software de la infraestructura o del servidor físico, o bien para las revisiones y actualizaciones, las selecciones de ubicación de la máquina virtual del usuario y de la infraestructura no consumirán todos los recursos de memoria de la unidad de escalado. Se dejará sin asignar una cantidad de la memoria total de todos los servidores de una unidad de escalado en apoyo de los requisitos de resistencia de la solución. Por ejemplo, cuando se actualiza la imagen de Windows Server del servidor físico, las máquinas virtuales hospedadas en el servidor se mueven a otro lugar de la unidad de escalado mientras se actualizan las imágenes de Windows Server del servidor. Una vez completada la actualización, el servidor se reinicia y vuelve a las cargas de trabajo del servicio. El objetivo de la revisión y de la actualización de una solución de Azure Stack es evitar la necesidad de detener las máquinas virtuales hospedadas. Tratando de cumplir dicho objetivo, se deja sin asignar el mínimo indispensable de la capacidad de memoria de un servidor para permitir el movimiento de máquinas virtuales dentro de la unidad de escalado. Esta selección de ubicación y movimiento se aplica a las máquinas virtuales de la infraestructura y a las máquinas virtuales creadas en nombre del usuario o inquilino de la solución de Azure Stack. El resultado final de la implementación es tal que la cantidad de memoria reservada para admitir el movimiento necesario de la máquina virtual puede ser mucho más que la capacidad de un único servidor debido a que los desafíos de selección de ubicación de las máquinas virtuales tienen diferentes requisitos de memoria. Una sobrecarga adicional en la categoría de uso de memoria es la de la propia instancia de Windows Server. La instancia del sistema operativo base de cada servidor usará memoria para el sistema operativo y sus tablas de la página virtual, junto con la memoria utilizada por Hyper-V en la administración de cada una de las máquinas virtuales hospedadas.

La capacidad de la máquina virtual viene determinada por la memoria disponible. Las relaciones de núcleos virtuales y núcleos físicos ejemplifican cómo la densidad de la máquina virtual cambiará la capacidad de CPU disponible, a menos que la solución se construya con un mayor número de núcleos físicos (se elige otra CPU). Lo mismo puede decirse de la capacidad de almacenamiento y de la capacidad de memoria caché de almacenamiento.

Los ejemplos anteriores de densidad de la máquina virtual tienen una finalidad meramente explicativa. La complejidad aumenta en los cálculos de soporte técnico. Es necesario contactar con Microsoft o con un asociado de soluciones para entender mejor las opciones de planeamiento de capacidad y la capacidad disponible resultante.

En el resto de esta sección se describen los requisitos de implementación de Azure Stack de proceso y almacenamiento. Utilice esta información para entender de forma básica cuáles son los componentes necesarios y sus valores mínimos de configuración. También se proporciona información adicional para describir cómo se configura la solución con respecto a la capacidad disponible y los posibles límites del sistema con respecto a la capacidad de rendimiento y la capacidad del inquilino.

> [!NOTE]
> Los requisitos de planeamiento de capacidad de red son mínimos, ya que solo se puede configurar el tamaño de la dirección VIP pública. Para obtener información sobre cómo agregar más direcciones IP públicas en Azure Stack, consulte [Add Public IP Addresses](azure-stack-add-ips.md) (Adición de direcciones IP públicas).


## <a name="next-steps"></a>Pasos siguientes
[Planeamiento de la capacidad de proceso](capacity-planning-compute.md)
