---
title: Planeamiento de la capacidad de almacenamiento de Azure Stack Hub
description: Obtenga más información sobre el planeamiento de la capacidad de almacenamiento para las implementaciones de Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 69248d5a81cf3d1017f221a57549d75b205f0fc3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871266"
---
# <a name="azure-stack-hub-storage-capacity-planning"></a>Planeamiento de la capacidad de almacenamiento de Azure Stack Hub

En las secciones siguientes se proporciona información acerca del planeamiento de la capacidad de almacenamiento de Azure Stack Hub para ayudarle con el planeamiento según las necesidades de almacenamiento de la solución.

## <a name="uses-and-organization-of-storage-capacity"></a>Usos y organización de la capacidad de almacenamiento

La configuración hiperconvergida de Azure Stack Hub permite el uso compartido de dispositivos de almacenamiento físico. Existen tres divisiones principales del almacenamiento disponible que se pueden compartir: infraestructura, almacenamiento temporal de las máquinas virtuales de inquilino y almacenamiento de respaldo de los blobs, tablas y colas de los servicios de almacenamiento compatible con Azure (ACS).

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>Niveles de capacidad y memoria caché de Espacios de almacenamiento directo

La capacidad de almacenamiento se puede utilizar para el sistema operativo, el registro local, el volcado y otro tipo de necesidades de almacenamiento de infraestructura temporal. Esta capacidad de almacenamiento local es independiente (dispositivos y capacidad) de los dispositivos de almacenamiento que se encuentran bajo la administración de la configuración de Espacios de almacenamiento directo. El resto de dispositivos de almacenamiento se coloca en un único grupo de capacidad de almacenamiento, independientemente del número de servidores de la unidad de escalado.

Estos dispositivos son de dos tipos: memoria caché y capacidad. Espacios de almacenamiento directo usa estos dispositivos para el almacenamiento en caché con reescritura y lectura. Las capacidades de estos dispositivos de memoria caché, mientras estén en uso, no se dedican a la capacidad "visible" y con formato de los discos virtuales con formato. Por el contrario, Espacios de almacenamiento directo usa dispositivos de capacidad para este propósito, y proporciona así la "ubicación principal" de los datos administrados.

La infraestructura de Azure Stack Hub asigna y administra directamente toda la capacidad de almacenamiento. El operador no tiene que tomar decisiones sobre la configuración, la asignación o la expansión de la capacidad. Azure Stack Hub automatiza estas decisiones de diseño para adaptarse a los requisitos de la solución, ya sea durante la instalación e implementación iniciales, o durante la expansión de la capacidad. Como parte del diseño, Azure Stack Hub tiene en cuenta, entre otros, los detalles acerca de la resistencia y la capacidad reservada para las recopilaciones.

Los operadores pueden elegir entre una configuración de almacenamiento *completamente de flash* o *híbrido*:

![Diagrama de planeamiento de la capacidad de almacenamiento de Azure](media/azure-stack-capacity-planning/storage.png)

En la configuración completamente de flash, la configuración puede ser de uno o dos niveles. Si la configuración es de un nivel, todos los dispositivos de capacidad son del mismo tipo (por ejemplo, *NVMe* o *SATA SSD* o *SAS SSD*) y no se usan dispositivos de memoria caché. En una configuración completamente de flash de dos niveles, lo habitual son dispositivos de caché NVMe y, luego, dispositivos de capacidad SATA o SAS SSD.

En una configuración híbrida de dos niveles, la caché es una elección entre NVMe o SATA/SAS SSD y la capacidad es la unidad de disco duro.

A continuación, se incluye un breve resumen de la configuración de almacenamiento de Azure Stack Hub y Espacios de almacenamiento directo:
- Un grupo de Espacios de almacenamiento directo por unidad de escalado (todos los dispositivos de almacenamiento se configuran dentro de un único grupo).
- Los discos virtuales se crean como un reflejo de tres copias para optimizar el rendimiento y la resistencia.
- Cada disco virtual tiene el formato de un sistema de archivos ReFS.
- La capacidad del disco virtual se calcula y asigna de forma que una cantidad de la capacidad de datos del dispositivo de capacidad se deje sin asignar en el grupo. Esto equivale a una unidad de capacidad por servidor.
- Cada sistema de archivos ReFS tendrá BitLocker habilitado para el cifrado de datos en reposo. 

Los discos virtuales se crean automáticamente y sus capacidades se indican a continuación:

|Nombre|Cálculo de capacidad|Descripción|
|-----|-----|-----|
|Dispositivo local o de arranque|340 GB<sup>1</sup> como mínimo|Almacenamiento individual del servidor para imágenes del sistema operativo y VM de infraestructura "local".|
|Infraestructura|3,5 TB|Uso de toda la infraestructura de Azure Stack Hub.|
|VmTemp|Ver a continuación<sup>2</sup>|Las máquinas virtuales de inquilino tienen un disco temporal conectado y los datos se almacenan en estos discos virtuales.|
|ACS|Ver a continuación<sup>3</sup>|Capacidad de almacenamiento compatible con Azure para el servicio de blobs, tablas y colas.|

<sup>1</sup> Capacidad mínima de almacenamiento necesaria del asociado de soluciones de Azure Stack Hub.

<sup>2</sup> El tamaño del disco virtual usado para los discos temporales de la máquina virtual del inquilino se calcula como una relación de la memoria física del servidor. El disco temporal es una proporción de la memoria física asignada a la máquina virtual. La asignación efectuada para el almacenamiento del "disco temporal" en Azure Stack Hub captura la mayoría de los casos de uso, pero puede que no se satisfagan todas las necesidades de almacenamiento del disco temporal. La proporción es una solución intermedia para disponer de almacenamiento temporal sin consumir la mayor parte de la capacidad de almacenamiento de la solución solo para la capacidad del disco temporal. Se crea un disco de almacenamiento temporal por servidor en la unidad de escalado. La capacidad de almacenamiento temporal no superará el límite del 10 % de la capacidad de almacenamiento total disponible en el grupo de almacenamiento de la unidad de escalado. El cálculo se realiza de forma parecida al siguiente ejemplo:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> Los discos virtuales creados para el uso con ACS son una división simple de la capacidad restante. Como se indicó anteriormente, todos los discos virtuales son un reflejo a tres bandas y el valor de una unidad de capacidad de la capacidad de cada servidor está sin asignar. Los diversos discos virtuales enumerados anteriormente se asignan en primer lugar y, a continuación, se usa la capacidad restante para los discos virtuales de ACS.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información acerca de [Capacity Planner de Azure Stack Hub](azure-stack-capacity-planner.md).
