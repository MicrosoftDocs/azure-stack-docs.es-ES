---
title: Proceso de planeamiento de capacidad de Azure Stack | Microsoft Docs
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
ms.date: 07/16/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 06/13/2019
ms.openlocfilehash: 02cd98891f825714e63b29502f46e9847548d3a3
ms.sourcegitcommit: b9d520f3b7bc441d43d489e3e32f9b89601051e6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75727401"
---
# <a name="azure-stack-compute"></a>Proceso de Azure Stack

Los [tamaños de VM](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) admitidos en Azure Stack son un subconjunto de los que se admiten en Azure. Azure impone límites de recursos junto con varios vectores para evitar el consumo excesivo de recursos (nivel de servicio y local del servidor). Sin la imposición de algunos límites sobre el consumo del inquilino, las experiencias de este se verán afectadas cuando otros inquilinos consuman recursos en exceso. Para la salida de redes de la máquina virtual, hay extremos de ancho de banda en Azure Stack que coinciden con las limitaciones de Azure. En el caso de los recursos de almacenamiento en Azure Stack, los límites de almacenamiento de IOPS evitan el consumo excesivo básico de recursos por parte de los inquilinos para el acceso de almacenamiento.

>[!IMPORTANT]
>[Capacity Planner de Azure Stack](https://aka.ms/azstackcapacityplanner) no considera ni garantiza el rendimiento de IOPS.

## <a name="vm-placement"></a>Selección de ubicación de VM

El motor de ubicación de Azure Stack coloca las máquinas virtuales de los inquilinos a través de los hosts disponibles.

Azure Stack usa dos consideraciones al ubicar máquinas virtuales. La primera, es la suficiente memoria en el host para ese tipo de máquina virtual. La segunda, si las máquinas virtuales forman parte de un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) o son [conjuntos de escalado de máquinas virtuales](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

Para conseguir la alta disponibilidad de un sistema de producción con varias VM en Azure Stack, las VM se colocan en un conjunto de disponibilidad que las distribuye a varios dominios de error. Un dominio de error en un conjunto de disponibilidad se define como un único nodo en la unidad de escalado. Azure Stack admite un conjunto de disponibilidad con un máximo de tres dominios de error para coherencia con Azure. Las máquinas virtuales colocadas en conjuntos de disponibilidad se aislarán físicamente entre sí al distribuirlas de la manera más uniforme que sea posible en varios dominios de error, es decir, hosts de Azure Stack. Si se produce un error de hardware, las máquinas virtuales del dominio de error que presente el error se reiniciarán en otros dominios de error pero, si es posible, se mantendrán en dominios de error independientes de las otras máquinas virtuales que se encuentran en el mismo conjunto de disponibilidad. Cuando el host vuelva a estar en línea, las VM se volverán a equilibrar para mantener la alta disponibilidad.  

Los conjuntos de escalado de máquinas virtuales usan conjuntos de disponibilidad en el back-end y comprueban que cada instancia de conjunto de escalado de máquinas virtuales se coloque en un dominio de error distinto. Esto significa que usan nodos de infraestructura de Azure Stack independientes. Por ejemplo, en un sistema de Azure Stack de cuatro nodos, puede haber una situación en que un conjunto de escalado de máquinas virtuales de tres instancias genere un error durante la creación debido a la falta de la capacidad de cuatro nodos para colocar tres instancias de conjunto de escalado de máquinas virtuales en tres nodos independientes de Azure Stack. Además, los nodos de Azure Stack se pueden completar a distintos niveles antes de intentar seleccionar su ubicación. 

Azure Stack no asigna más memoria de la necesaria. Sin embargo, se permite una confirmación excesiva del número de núcleos físicos. 

Dado que los algoritmos de selección no contemplan como factor la relación de aprovisionamiento excesivo existente de núcleos virtuales a físicos, cada host puede tener una relación diferente. Como Microsoft, no proporcionamos orientación sobre la relación de núcleos físicos a virtuales debido a la variación en las cargas de trabajo y los requisitos de nivel de servicio. 

## <a name="consideration-for-total-number-of-vms"></a>Consideración para el número total de máquinas virtuales 

Hay una nueva consideración para planear la capacidad de Azure Stack con precisión. Con la actualización 1901 (y cada actualización subsiguiente), ahora hay un límite en el número total de máquinas virtuales que se pueden crear. Este límite está diseñado para ser temporal y evitar la inestabilidad de la solución. El origen del problema de estabilidad en los números mayores de las máquinas virtuales se está abordando, pero aún no se ha determinado un tiempo específico para la corrección. Ahora hay un límite por servidor de 60 máquinas de virtuales con un límite total en la solución de 700. Por ejemplo, el límite de una máquina virtual de Azure Stack de 8 servidores sería 480 (8 * 60). Para una solución de Azure Stack de 12 a 16 servidores, el límite sería 700. Este límite se ha creado teniendo todas las consideraciones de capacidad de proceso en cuenta, como la reserva de resistencia y la relación entre la CPU virtual y la física que le gustaría mantener en el sello al operador. Para más información, consulte la nueva versión del planeador de capacidad. 

En caso de que se alcanzara el límite de escalado de máquinas virtuales, se devolverían como resultado los códigos de error siguientes: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded.

## <a name="considerations-for-deallocation"></a>Consideraciones para la desasignación

Cuando una máquina virtual se encuentra en estado _desasignado_, no se usan recursos de memoria. Esto permite colocar otras máquinas virtuales en el sistema. 

Si la máquina virtual desasignada se vuelve a iniciar, la utilización de memoria o la asignación se tratan como si se colocara una máquina virtual nueva en el sistema y se utiliza la memoria disponible. 

Si no hay memoria disponible, la máquina virtual no se iniciará.

## <a name="azure-stack-memory"></a>Memoria de Azure Stack 

Azure Stack se ha diseñado para mantener en ejecución las VM que se han aprovisionado correctamente. Por ejemplo, si un host no tiene conexión debido a un error de hardware, Azure Stack intentará reiniciar la VM en otro host. El segundo ejemplo es la revisión y actualización del software de Azure Stack. Si es necesario reiniciar un host físico, se intenta mover las VM que se ejecutan en ese host a otro host disponible en la solución.   

Este movimiento o administración de VM solo se puede lograr si hay capacidad de memoria reservada para permitir el reinicio o la migración que se produzca. Una parte de la memoria total del host está reservada y no está disponible para selección de ubicación de VM de inquilino. 

Puede revisar un gráfico circular en el portal de administración que muestra la memoria libre y ocupada en Azure Stack. El siguiente diagrama muestra la capacidad de memoria física en una unidad de escalado de Azure Stack en Azure Stack:

![Capacidad de memoria física](media/azure-stack-capacity-planning/physical-memory-capacity.png)

La memoria usada está formada por varios componentes. Los componentes siguientes consumen la memoria de la sección de uso del gráfico circular.  

 -  Reserva o uso de sistema operativo del host: se trata de la memoria que usa el sistema operativo (SO) en el host, las tablas de página de memoria virtual, los procesos que se ejecutan en el sistema operativo del host y la memoria caché de Espacios de almacenamiento directo. Dado que este valor depende de la memoria que utilizan los diferentes procesos de Hyper-V que se ejecutan en el host, puede fluctuar.
 - Servicios de infraestructura: se trata de las VM de infraestructura que forman Azure Stack. A partir de la versión de lanzamiento 1904 de Azure Stack, esto implica aproximadamente 31 máquinas virtuales que ocupan 242 GB + (4 GB x número de nodos) de memoria. La utilización de la memoria del componente de servicios de infraestructura puede cambiar a medida que trabajamos para que los servicios de infraestructura sean más escalables y resistentes.
 - Reserva de resistencia: Azure Stack reserva una parte de la memoria para permitir la disponibilidad del inquilino durante un error de host único, así como durante la revisión y actualización para permitir la correcta migración en vivo de VM.
 - VM de inquilino: son VM de inquilino que crean los usuarios de Azure Stack. Además de ejecutar VM, las VM que llegan al tejido también consumen memoria. Esto significa que las máquinas virtuales con el estado "Creando" o "Error", o las máquinas virtuales apagadas desde el invitado, consumirán memoria. Sin embargo, las máquinas virtuales que se han desasignado mediante la opción de detención de desasignación del Portal, PowerShell o la CLI no consumirán memoria de Azure Stack.
 - Proveedores de recursos de valor agregado (RPs): máquinas virtuales implementadas para los proveedores de recursos de valor agregado como SQL, MySQL, App Service, etc.


La mejor manera de comprender el consumo de memoria en el portal es usar [Capacity Planner de Azure Stack](https://aka.ms/azstackcapacityplanner) para ver el impacto de diversas cargas de trabajo. El siguiente cálculo es el mismo que usa la herramienta de planeación.

Este cálculo devuelve la memoria disponible total que se puede usar para la selección de ubicación de VM de inquilino. Esta capacidad de memoria es para la totalidad de la unidad de escalado de Azure Stack. 


  Memoria disponible para la selección de ubicación de VM = memoria total del host - reserva de resistencia - memoria usada por las VM en ejecución - sobrecarga de infraestructura de Azure Stack<sup>1</sup>

  Reserva de resistencia = H + R * ((N-1) * H) + V * (N-2)

> Donde:
> - H = tamaño de la memoria de un solo servidor
> - N = tamaño de la unidad de escalado (número de servidores)
> - R = reserva del sistema operativo para la sobrecarga de sistema operativo, que es 0,15 en esta fórmula<sup>2</sup>
> - V = máquina virtual más grande de la unidad de escalado

  <sup>1</sup> Sobrecarga de la infraestructura de Azure Stack = 242 GB + (4 GB x n.º de nodos). Se usan, aproximadamente, 31 VM para hospedar la infraestructura de Azure Stack y, en total, se consumen alrededor de 242 GB + (4 GB x n.º de nodos) de memoria y146 núcleos virtuales. La razón de este número de máquinas virtuales es satisfacer la separación del servicio que se necesita para cumplir los requisitos de seguridad, escalabilidad, servicio y aplicación de revisión. Esta estructura de servicio interno permite la futura introducción de nuevos servicios de infraestructura mientras se desarrollan. 

  <sup>2</sup>Reserva del sistema operativo para la sobrecarga = 15 % (0,15) de la memoria del nodo. El valor de reserva del sistema operativo es un valor estimado y puede variar según la capacidad de memoria física del servidor y la sobrecarga general del sistema operativo.


El valor V, la máquina virtual más grande de la unidad de escalado, se basa dinámicamente en el tamaño más grande de memoria de la máquina virtual del inquilino. Por ejemplo, el valor de la máquina virtual más grande podría ser 7 GB o 112 GB, así como cualquier otro tamaño de memoria de la máquina virtual admitido en la solución Azure Stack. Cambiar la mayor VM del tejido de Azure Stack provocará un aumento en la reserva de resistencia, además del aumento de memoria de la propia VM. 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**P.** : Mi inquilino ha implementado una nueva máquina virtual, ¿cuánto tiempo tardará el gráfico de capacidad del portal de administración en mostrar la capacidad restante?

**R.** : La hoja de capacidad se actualiza cada 15 minutos, por lo tanto, tenga esto en cuenta.

**P.** : El número de máquinas virtuales implementadas en mi instancia de Azure Stack no ha cambiado, pero mi capacidad fluctúa. ¿Por qué?

**R.** : La memoria disponible para la ubicación de las máquinas virtuales tiene múltiples dependencias, una de las cuales es la reserva del sistema operativo del host. Este valor depende de la memoria que utilizan los diferentes procesos de Hyper-V que se ejecutan en el host, que no es un valor constante.

**P.** : ¿En qué estado tienen que estar las máquinas virtuales de los inquilinos para consumir memoria?

**R.** : Además de ejecutar VM, las VM que llegan al tejido también consumen memoria. Esto significa que las máquinas virtuales que se encuentran en estado "Creando", "Con error" o las que se apaguen desde el invitado, en lugar de detenerse la desasignación desde el Portal o PowerShell o la cli, consumirán memoria.

**P.** : Tengo una instancia de Azure Stack de cuatro hosts. Mi inquilino tiene tres máquinas virtuales que consumen 56 GB de RAM (D5_v2) cada una. Una de las máquinas virtuales cambia de tamaño a 112 GB de RAM (D14_v2) y los informes de memoria disponibles en el panel dieron como resultado un aumento de 168 GB de uso en la hoja de capacidad. El cambio de tamaño posterior de las otras dos máquinas virtuales de D5_v2 a D14_v2 ha dado como resultado un aumento de solo 56 GB de RAM cada una. ¿Por qué ocurre esto?

**R.** : La memoria disponible es una función de la reserva de resistencia mantenida por Azure Stack. La reserva de resistencia es una función del mayor tamaño de máquina virtual en la marca de Azure Stack. Al principio, la máquina virtual más grande en la marca tenía 56 GB de memoria. Cuando se cambió de tamaño la máquina virtual, la más grande de la marca pasó a 112 GB de memoria, lo que no solo aumentó la memoria utilizada por la máquina virtual del inquilino, sino que también aumentó la reserva de resistencia. Esto dio como resultado un aumento de 56 GB (de 56 GB a 112 GB de aumento de la memoria de la máquina virtual del inquilino) más un aumento de la memoria de reserva de resistencia de 112 GB. Al cambiar el tamaño de las máquinas virtuales posteriores, se conservó el tamaño mayor de la máquina virtual como la máquina virtual de 112 GB y, por lo tanto, no se produjo un aumento de la reserva de resistencia. El aumento en el consumo de memoria fue solo el aumento de la memoria de la máquina virtual del inquilino (56 GB). 


> [!NOTE]
> Los requisitos de planeamiento de capacidad de red son mínimos, ya que solo se puede configurar el tamaño de la dirección VIP pública. Para obtener información sobre cómo agregar más direcciones IP públicas en Azure Stack, consulte [Add Public IP Addresses](azure-stack-add-ips.md) (Adición de direcciones IP públicas).

## <a name="next-steps"></a>Pasos siguientes
Más información acerca del [almacenamiento de Azure Stack](azure-stack-capacity-planning-storage.md)
