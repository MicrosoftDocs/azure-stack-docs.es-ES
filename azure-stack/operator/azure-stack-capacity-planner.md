---
title: Capacity Planner de Azure Stack | Microsoft Docs
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
ms.openlocfilehash: 98dafb9df568e5e14e5f1890103d0261e07beb98
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131352"
---
# <a name="azure-stack-capacity-planner"></a>Herramienta de planeamiento de capacidad de Azure Stack

Capacity Planner de Azure Stack es una hoja de cálculo que muestra cómo se adaptarían diferentes asignaciones de recursos informáticos a una selección de ofertas de hardware. 

## <a name="worksheet-descriptions"></a>Descripciones de la hoja de cálculo
En la tabla siguiente se describen todas las hojas de cálculo de Capacity Planner de Azure Stack, que se pueden descargar de [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner). 

|Nombre de la hoja de cálculo|DESCRIPCIÓN|
|-----|-----|
|Declinación de responsabilidades de versión|Objetivos de la calculadora, número de versión y fecha de lanzamiento.|
|Instrucciones|Instrucciones detalladas para el planeamiento de capacidad del modelo para una colección de máquinas virtuales (VM).|
|DefinedSolutionSKUs|Tabla con hasta cinco definiciones de hardware. Las entradas son ejemplos. Cambie los detalles para que coincidan con las configuraciones del sistema en cuestión.|
|DefineByVMFootprint|Para encontrar la SKU de hardware adecuada, compare las configuraciones con distintos tamaños y cantidades de VM.|
|DefineByWorkloadFootprint|Busca la SKU de hardware adecuada mediante la creación de una colección de cargas de trabajo de Azure Stack.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Instrucciones de DefinedSolutionSKUs
Esta hoja de cálculo contiene hasta cinco ejemplos de definición de hardware. Cambie los detalles para que coincidan con las configuraciones del sistema en cuestión.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Selecciones de hardware proporcionadas por los asociados de hardware autorizados
Azure Stack se ofrece como sistema integrado con software instalado por los asociados de soluciones. Los asociados de soluciones proporcionan sus propias versiones de herramientas de planeamiento de capacidad de Azure Stack. Use estas herramientas para discusiones finales sobre la capacidad de la solución.

### <a name="multiple-ways-to-model-computing-resources"></a>Varias formas de modelar los recursos informáticos
El modelado de recursos de Capacity Planner de Azure Stack depende de los distintos tamaños de las VM de Azure Stack. El tamaño de las VM va desde el más pequeño, “Basic 0”, hasta el mayor, “Standard_Fsv2”. Puede modelar las asignaciones de recursos informáticos de dos maneras diferentes:

- Seleccione una oferta de hardware específica y considere qué combinaciones de varios recursos encajan. 

- Cree una combinación específica de asignaciones de VM y permita que Azure Resource Calculator muestre qué SKU de hardware disponibles son capaces de admitir esta configuración de VM.

Esta herramienta proporciona dos métodos para asignar recursos de VM, ya sea como una única colección de asignaciones de recursos de VM o como una colección de hasta seis configuraciones diferentes de carga de trabajo. Cada configuración de carga de trabajo puede contener una asignación de recursos de VM disponibles diferente. Las secciones siguientes contienen instrucciones detalladas para crear y usar cada uno de estos modelos de asignación. Solo deben modificarse los valores contenidos en celdas que no tienen sombreado de fondo o en las listas desplegables de SKU en esta hoja de cálculo. Los cambios realizados en celdas sombreadas pueden interrumpir los cálculos de recursos.


## <a name="definebyvmfootprint-instructions"></a>Instrucciones de DefineByVMFootprint
Para crear un modelo con una sola colección de varios tamaños y cantidades de VM, seleccione la pestaña **DefineByVMFootprint** y siga estos pasos:

1. En la esquina superior derecha de esta hoja de cálculo, use los controles de cuadro de lista desplegable proporcionados para seleccionar el número inicial de servidores (entre 4 y 16) que quiere instalar en cada sistema de hardware (SKU). Este número de servidores se puede modificar en cualquier momento durante el proceso de modelado para ver cómo afecta el cambio a los recursos generales disponibles para su modelo de asignación de recursos.
2. Si desea modelar varias asignaciones de recursos de VM con una configuración de hardware específica, busque el cuadro de lista desplegable azul directamente debajo de la etiqueta **SKU actual** en la esquina superior derecha de la página. Despliegue este cuadro de lista y seleccione la SKU de hardware deseada.
3. Ahora todo está listo para empezar a agregar VM de tamaños distintos al modelo. Para incluir un tipo determinado de máquina virtual, escriba un valor de cantidad en el cuadro de contorno azul a la izquierda de la entrada de esa máquina virtual.

   > [!NOTE]
   > El almacenamiento total de la VM hace referencia a la capacidad total del disco de datos de esta (número de discos admitidos multiplicado por la capacidad máxima de un solo disco [1 TB]). En función de los indicadores de configuración hemos rellenado la tabla de configuraciones de almacenamiento disponibles, de manera que pueda elegir el nivel de recursos de almacenamiento para cada VM de Azure Stack. Sin embargo, es importante tener en cuenta que puede agregar elementos a la tabla de configuraciones de almacenamiento disponibles o cambiarla según sea necesario.<br><br>Cada máquina virtual empieza con un almacenamiento temporal que se asignó inicialmente. Para reflejar el aprovisionamiento fino de almacenamiento temporal se puede cambiar el número temporal local por cualquier valor del menú desplegable, incluida la cantidad de almacenamiento temporal máxima permitida.

4. A medida que agregue VM, verá cómo cambian los gráficos que muestran los recursos disponibles de la SKU. Esto le permite ver los efectos de agregar varios tamaños y las cantidades de máquinas virtuales durante el proceso de modelado. Otra forma de ver el efecto de los cambios es mirar los números **Consumido** y **Todavía disponible** que aparecen directamente debajo de la lista de VM disponibles. Estos números reflejan los valores estimados según la SKU de hardware seleccionada actualmente.
5. Una vez haya creado el conjunto de VM, puede encontrar la SKU de hardware recomendada haciendo clic en el botón de **SKU recomendada** que se encuentra en la esquina superior derecha de la página, directamente debajo de la etiqueta **SKU actual**. Con este botón puede modificar las configuraciones de máquina virtual y después ver qué hardware es compatible con cada configuración.


## <a name="definebyworkloadfootprint-instructions"></a>Instrucciones DefineByWorkloadFootprint
Para crear un modelo con una colección de cargas de trabajo de Azure Stack, seleccione la pestaña **DefineByWorkloadFootprint** y siga esta secuencia de pasos. Las cargas de trabajo de Azure Stack se crean mediante los recursos de VM disponibles.   

> [!TIP]
> Para cambiar el tamaño de almacenamiento proporcionado en una máquina virtual de Azure Stack, consulte la nota en el paso tres de la sección anterior.

1. En la esquina superior derecha de esta hoja de cálculo, use los controles de cuadro de lista desplegable proporcionados para seleccionar el número inicial de servidores (entre 4 y 16) que quiere instalar en cada sistema de hardware (SKU).
2. Si desea modelar varias asignaciones de recursos de VM con una configuración de hardware específica, busque el cuadro de lista desplegable azul directamente debajo de la etiqueta **SKU actual** en la esquina superior derecha de la página. Despliegue este cuadro de lista y seleccione la SKU de hardware deseada.
3. Seleccione el tamaño de almacenamiento adecuado para cada una de las VM de Azure Stack deseadas en la página **DefineByVMFootprint**, como se describe en el tercer paso de la sección anterior. El tamaño de almacenamiento por máquina virtual se define en la hoja DefineByVMFootprint.
4. Empezando en la parte superior izquierda de la página **DefineByWorkloadFootprint**, cree configuraciones para hasta seis tipos diferentes de cargas de trabajo. Especifique la cantidad de cada tipo de VM incluido en esa carga de trabajo. Para ello, coloque valores numéricos en la columna directamente debajo del nombre de la carga de trabajo. Los nombres de la carga de trabajo pueden modificarse para reflejar el tipo de carga de trabajo que será compatible con esta configuración determinada.
5. Para incluir una determinada cantidad de cada tipo de carga de trabajo, puede especificar un valor en la parte inferior de la columna directamente debajo de la etiqueta **Cantidad**.
6. Después de crear las cantidades y los tipos de cargas de trabajo, seleccione **SKU recomendada** en la esquina superior derecha de la página, justo debajo de la etiqueta **SKU actual**. Se muestra la SKU más pequeña con recursos suficientes para admitir esta configuración general de cargas de trabajo.
7. Para lograr más modelado, puede modificar el número de servidores seleccionados para una SKU de hardware o cambiar las cantidades o asignaciones de VM en las configuraciones de las cargas de trabajo. Los gráficos asociados muestran comentarios inmediatos que indican cómo afectan los cambios al consumo de recursos en general.
8. Cuando esté conforme con los cambios, haga clic de nuevo en **SKU recomendada** para mostrar la SKU sugerida para la nueva configuración. También puede seleccionar la SKU que quiera en el menú desplegable.

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre las [Consideraciones de integración del centro de datos para sistemas integrados de Azure Stack](azure-stack-datacenter-integration.md).
