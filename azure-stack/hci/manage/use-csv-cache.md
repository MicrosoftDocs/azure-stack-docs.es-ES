---
title: Uso de la caché de lectura en memoria de Volumen compartido de clúster con Azure Stack HCI
description: En este tema se describe cómo usar la memoria del sistema para mejorar el rendimiento.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/04/2020
ms.openlocfilehash: 84d5292c3f812402027b310954a021752276a799
ms.sourcegitcommit: 01dcda15d88c8d44b4918e2f599daca462a8e3d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2020
ms.locfileid: "89493808"
---
# <a name="use-the-csv-in-memory-read-cache-with-azure-stack-hci"></a>Uso de la caché de lectura en memoria de Volumen compartido de clúster con Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se describe cómo usar la memoria del sistema para mejorar el rendimiento de Azure Stack HCI.

Azure Stack HCI es compatible con la caché de lectura en memoria de Volumen compartido de clúster (CSV). El uso de la memoria del sistema para las lecturas de caché puede mejorar el rendimiento de aplicaciones como Hyper-V, que usa E/S sin almacenamiento en búfer para acceder a los archivos VHD o VHDX. (Las operaciones de E/S sin almacenamiento en búfer se dan en cualquier operación que el administrador de la caché de Windows no almacena en caché).

Dado que la caché en memoria está en un servidor local, mejora la ubicación de los datos: las lecturas recientes se almacenan en la caché en memoria en el mismo host en el que se ejecuta la máquina virtual, lo que reduce la frecuencia con la que las lecturas pasan a través de la red. Esto produce una menor latencia y un mejor rendimiento de almacenamiento.

Tenga en cuenta que la caché de lectura en memoria de Volumen compartido de clúster es diferente de la [caché de bloque de almacenamiento](../concepts/cache.md) de Azure Stack HCI.

## <a name="planning-considerations"></a>Consideraciones de planeación

La caché de lectura en memoria es más eficaz con cargas de trabajo de lectura intensiva como, por ejemplo, la infraestructura de escritorio virtual. Por el contrario, si la carga de trabajo requiere una gran cantidad de escritura, la caché puede suponer más sobrecarga que beneficios y debe deshabilitarse.

Puede usar hasta el 80 % de la memoria física total para la caché de lectura en memoria de Volumen compartido de clúster. No olvide dejar suficiente memoria para las máquinas virtuales.

  > [!NOTE]
  > Ciertas herramientas de microrealización de pruebas comparativas, como DISKSPD y [VM Fleet](https://github.com/Microsoft/diskspd/tree/master/Frameworks/VMFleet) pueden obtener peores resultados con la caché de lectura en memoria de Volumen compartido de clúster habilitada que sin ella. De forma predeterminada, VM Fleet crea un VHDX de 10 GiB por máquina virtual, lo que supone aproximadamente un total de 1 TiB para 100 máquinas virtuales y, a continuación, realiza lecturas y escrituras *aleatorias de forma uniforme*. A diferencia de las cargas de trabajo reales, las lecturas no siguen ningún patrón predecible o repetitivo, por lo que la caché en memoria no es efectiva y solo produce sobrecarga.

## <a name="configuring-the-in-memory-read-cache"></a>Configuración de la caché de lectura en memoria

La caché de lectura en memoria de Volumen compartido de clúster está disponible en Azure Stack HCI, Windows Server 2019 y Windows Server 2016 con la misma funcionalidad. En Azure Stack HCI y Windows Server 2019, está activada de forma predeterminada con 1 gibibyte (GiB) asignado. En Windows Server 2016, está desactivada de forma predeterminada.

| Versión del SO          | Tamaño predeterminado de caché de Volumen compartido de clúster |
|---------------------|------------------------|
| Azure Stack HCI     | 1 GiB                  |
| Windows Server 2019 | 1 GiB                  |
| Windows Server 2016 | 0 (deshabilitado)           |

Para ver cuánta memoria está asignada con PowerShell, ejecute:

```PowerShell
(Get-Cluster).BlockCacheSize
```

El valor devuelto está en mebibytes (MiB) por servidor. Por ejemplo, `1024` representa 1 GiB.

Para cambiar la cantidad de memoria que se asigna, modifique este valor mediante PowerShell. Por ejemplo, para asignar 2 GiB por servidor, ejecute:

```PowerShell
(Get-Cluster).BlockCacheSize = 2048
```

Para que los cambios surtan efecto inmediatamente, pause y reanude a continuación los volúmenes de CSV o muévalos entre servidores. Por ejemplo, use este fragmento de PowerShell para trasladar cada volumen compartido de clúster a otro nodo de servidor y viceversa:

```PowerShell
Get-ClusterSharedVolume | ForEach {
    $Owner = $_.OwnerNode
    $_ | Move-ClusterSharedVolume
    $_ | Move-ClusterSharedVolume -Node $Owner
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Descripción de la caché de un bloque de almacenamiento](../concepts/cache.md)
- [Uso de volúmenes compartidos de clúster en un clúster de conmutación por error](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional)
