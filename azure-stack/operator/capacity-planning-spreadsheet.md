---
title: Hoja de cálculo de planeamiento de capacidad de Azure Stack | Microsoft Docs
description: Obtenga más información sobre la hoja de cálculo de planeamiento de la capacidad de Azure Stack de las implementaciones de Azure Stack.
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 1756499b7736c309b07856bf5edfba45f3c0e11a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618872"
---
# <a name="azure-stack-capacity-planner"></a>Herramienta de planeamiento de capacidad de Azure Stack
La Herramienta de planeamiento de capacidad de Azure Stack es una hoja de cálculo que se usa para planear la capacidad de recursos de Azure Stack. La herramienta de planeamiento de capacidad le permite diseñar varias asignaciones de recursos informáticos y ver cómo estos se ajustarían a través de una selección de ofertas de hardware. A continuación se proporcionan instrucciones detalladas para el uso de Azure Stack Calculator.

## <a name="worksheet-descriptions"></a>Descripciones de la hoja de cálculo
El siguiente es un breve resumen de las hojas de cálculo incluidas en la hoja de cálculo Herramienta de planeamiento de capacidad de Azure Stack que puede descargarse desde [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner):

|Nombre de pestaña|DESCRIPCIÓN|
|-----|-----|
|Declinación de responsabilidades de versión|Breve descripción de los objetivos de la calculadora, el número de versión y la fecha de lanzamiento.|
|Instrucciones|Proporciona instrucciones detalladas para el uso de la Herramienta de planeamiento de capacidad de Azure Stack.|
|DefinedSolutionSKUs|Tabla de varias columnas que contiene hasta cinco de las definiciones de hardware. Las entradas de esta hoja son ejemplos. La intención es que el usuario cambiará los detalles para que coincidan con las configuraciones del sistema que se considera para su uso o compra.|
|DefineByVMFootprint|Busca la SKU de hardware adecuada mediante la creación de una colección de varios tamaños y cantidades de máquinas virtuales.|
|DefineByWorkloadFootprint|Busca la SKU de hardware adecuada mediante la creación de una colección de cargas de trabajo de Azure Stack.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Instrucciones de DefinedSolutionSKUs
Esta hoja de cálculo contiene hasta cinco ejemplos de definición de hardware. Cambie los detalles para que coincidan con las configuraciones del sistema que se considera para su uso o compra.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Selecciones de hardware proporcionadas por los asociados de hardware autorizados
Azure Stack se ofrece como sistema integrado con software instalado por los asociados de soluciones. Estos asociados de soluciones tendrán sus propias versiones autoritativas de herramientas de planeamiento de capacidad de Azure Stack, y esas herramientas deben usarse para finalizar las conversaciones sobre la capacidad de la solución.

### <a name="multiple-ways-to-model-computing-resources"></a>Varias formas de modelar los recursos informáticos
Los bloques de creación básicos usados para el modelado de recursos en el planificador de Azure Stack son los distintos tamaños de máquinas virtuales de Azure Stack (VM). El tamaño de estas máquinas virtuales va desde las más pequeñas, “Basic 0”, hasta la mayor máquina virtual actual, “Standard_Fsv2”. Según sus necesidades, pueden utilizarse varias cantidades de máquinas virtuales distintas para crear asignaciones de recursos informáticos con esta herramienta de dos maneras diferentes.

1. El usuario del planificador selecciona una oferta de hardware específica y ve qué combinaciones de varios recursos se ajustan a ese recurso de hardware. 

2. El usuario del planificador crea una combinación específica de asignaciones de máquinas virtuales y después permite que Azure Resource Calculator muestre qué SKU de hardware disponibles son capaces de admitir esta configuración de máquina virtual.

Esta herramienta proporciona dos métodos para asignar recursos de máquina virtual, ya sea como una única colección de asignaciones de recursos de máquina virtual o como una colección de hasta seis diferentes configuraciones de carga de trabajo. Cada configuración de carga de trabajo puede contener una asignación de recursos de máquinas virtuales disponibles diferente. A continuación se proporciona información detallada para crear y usar cada uno de estos modelos de asignación. Solo deben modificarse los valores contenidos en celdas que no tienen sombreado de fondo o en las listas desplegables de SKU en esta hoja de cálculo. Los cambios realizados en celdas sombreadas pueden interrumpir los cálculos de recursos.


## <a name="definebyvmfootprint-instructions"></a>Instrucciones de DefineByVMFootprint
Para crear un modelo con una sola colección de varios tamaños y cantidades de máquinas virtuales, seleccione la pestaña “DefineByVMFootprint” y siga esta secuencia de pasos.

1. En la esquina superior derecha de esta hoja de cálculo, use los controles de cuadro de lista desplegable proporcionados para seleccionar el número inicial de servidores (entre 4 y 16) que quiere instalar en cada sistema de hardware (SKU). Se puede modificar este número de servidores en cualquier momento durante el proceso de modelado para ver cómo el cambio afecta a los recursos disponibles generales para su modelo de asignación de recursos.
2. Si desea modelar varias asignaciones de recursos de máquina virtual con una configuración de hardware específica, busque el cuadro de lista desplegable azul directamente debajo de la etiqueta “SKU actual” en la esquina superior derecha de la página. Despliegue este cuadro de lista y seleccione la SKU de hardware deseada.
3. Ahora tiene todo listo para empezar a agregar máquinas virtuales de tamaños distintos al modelo. Para incluir un tipo determinado de máquina virtual, escriba un valor de cantidad en el cuadro de contorno azul a la izquierda de la entrada de esa máquina virtual.

   > [!NOTE]
   > El almacenamiento total de la máquina virtual hace referencia a la capacidad total del disco de datos de esta (número de discos admitidos * capacidad máxima de un solo disco [1 TB]). En función de los indicadores de configuración hemos rellenado la tabla de configuraciones de almacenamiento disponibles de manera que pueda elegir el nivel de recursos de almacenamiento para cada máquina virtual de Azure Stack. Sin embargo, es importante tener en cuenta que puede agregar elementos a la tabla de configuraciones de almacenamiento disponibles o cambiarla según sea necesario.<br><br>Cada máquina virtual empieza con un almacenamiento temporal que se asignó inicialmente. Para reflejar el aprovisionamiento fino de almacenamiento temporal se puede cambiar el número local-temp a cualquier valor en la lista desplegable del menú, incluida la cantidad de almacenamiento temporal máxima permitida.

4. A medida que agregue máquinas virtuales, verá cómo cambian los gráficos que muestran los recursos disponibles de la SKU. Esto le permite ver los efectos de agregar varios tamaños y las cantidades de máquinas virtuales durante el proceso de modelado. Otra forma de ver el efecto de los cambios es mirar los números “Consumido” y “Todavía disponible” que aparecen directamente debajo de la lista de máquinas virtuales disponibles. Estos números reflejan los valores estimados según la SKU de hardware seleccionada actualmente.
5. Una vez haya creado el conjunto de máquinas virtuales, puede encontrar la SKU de hardware recomendada haciendo clic en el botón de “SKU recomendada” que se encuentra en la esquina superior derecha de la página, directamente debajo de la etiqueta “SKU actual”. Con este botón puede modificar las configuraciones de máquina virtual y después ver qué hardware es compatible con cada configuración.


## <a name="definebyworkloadfootprint-instructions"></a>Instrucciones DefineByWorkloadFootprint
Para crear un modelo con una colección de cargas de trabajo de Azure Stack, seleccione la pestaña “DefineByWorkloadFootprint” y siga esta secuencia de pasos. Las cargas de trabajo de Azure Stack se crean mediante los recursos de máquina virtual disponibles.   

> [!TIP]
> Para cambiar el tamaño de almacenamiento proporcionado en una máquina virtual de Azure Stack, consulte la nota en el paso tres de la sección anterior.

1. En la esquina superior derecha de esta página, use los controles de cuadro de lista desplegable proporcionados para seleccionar el número inicial de servidores (entre 4 y 16) que quiere instalar en cada sistema de hardware (SKU).
2. Si desea modelar varias asignaciones de recursos de máquina virtual con una configuración de hardware específica, busque el cuadro de lista desplegable azul directamente debajo de la etiqueta “SKU actual” en la esquina superior derecha de la página. Despliegue este cuadro de lista y seleccione la SKU de hardware deseada.
3. Seleccione el tamaño de almacenamiento adecuado para cada una de las máquinas virtuales de Azure Stack deseadas en la página DefineByVMFootprint como se describió anteriormente en el paso tres de las instrucciones de DefineByVMFootprint. El tamaño de almacenamiento por máquina virtual se define en la hoja DefineByVMFootprint.
4. A partir de la parte superior izquierda de la página DefineByWorkloadFootprint cree configuraciones para hasta seis tipos diferentes de cargas de trabajo especificando la cantidad de cada tipo de máquina virtual dentro de esa carga de trabajo. Esto se hace colocando valores numéricos en la columna directamente debajo del nombre de esa carga de trabajo. Los nombres de la carga de trabajo pueden modificarse para reflejar el tipo de carga de trabajo que será compatible con esta configuración determinada.
5. Puede incluir una determinada cantidad de cada tipo de carga de trabajo especificando un valor en la parte inferior de la columna directamente debajo de la etiqueta “Cantidad”.
6. Una vez que se han creado las cantidades y los tipos de cargas de trabajo, hacer clic en el botón “SKU sugerido” que se encuentra en la esquina superior derecha de la página, directamente debajo de la etiqueta “SKU actual”, provocará que se muestre la SKU más pequeña con recursos suficientes para admitir esta configuración de cargas de trabajo.
7. Se puede lograr más modelado modificando el número de servidores seleccionados para una SKU de hardware o cambiando las cantidades o asignaciones de VM dentro de la configuración de carga de trabajo. Los gráficos asociados mostrarán información inmediata que indica cómo los cambios afectan al consumo de recursos global.
8. Cuando esté conforme con los cambios, haga clic de nuevo en el botón “SKU recomendada” para mostrar la SKU sugerida para la nueva configuración.


## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre las [Consideraciones de integración del centro de datos para sistemas integrados de Azure Stack](azure-stack-datacenter-integration.md)
