---
title: Tamaños de máquina virtual admitidos en Azure Stack Hub
description: Referencia para los tamaños de máquinas virtuales admitidos en Azure Stack Hub.
author: mattbriggs
ms.topic: reference
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 03/23/2020
ms.openlocfilehash: 01299f2ddd04e66145d781785097c74e5d129e48
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873765"
---
# <a name="vm-sizes-supported-in-azure-stack-hub"></a>Tamaños de máquina virtual admitidos en Azure Stack Hub

En este artículo se incluyen los tamaños de máquinas virtuales que están disponibles en Azure Stack Hub. Puede usar este artículo como ayuda para seleccionar una máquina virtual que admita su solución de Azure Stack Hub.

IOPS (operaciones de entrada y salida por segundo) de disco en Azure Stack Hub es una función del tamaño de máquina virtual, en lugar del tipo de disco. Esto significa que para una VM de la serie Standard_Fs, independientemente de si elige SSD o HDD para el tipo de disco, el límite de IOPS de un único disco de datos adicional es de solo 2300 IOPS. Los límites de IOPS impuestos (máximo posible) sirven para evitar vecinos ruidosos. IOPS no es una garantía de que obtendrá un tamaño específico de la máquina virtual.

La vCPU de la máquina virtual depende del número de núcleos por nodo. Por ejemplo, los sistemas con núcleos o procesadores lógicos inferiores a 64 no admitirán el tamaño de máquina virtual Standard_F64s_v2.

## <a name="vm-general-purpose"></a>Máquina virtual de uso general

Los tamaños de máquina virtual de uso general proporcionan una relación equilibrada entre la CPU y la memoria. Estas máquinas se usan para desarrollo y pruebas, bases de datos pequeñas o medianas, y servidores web de tráfico bajo o medio. Cada disco de datos tiene 2300 IOPS en los tamaños prémium de máquina virtual, excepto en la serie A básica. En el caso de la serie A básica, el tamaño del disco de datos es de 500 IOPS.

### <a name="basic-a"></a>A básico

> [!NOTE]
> Se han retirado los tamaños de máquina virtual *A básico* para la [creación de conjuntos de escalado de máquinas virtuales](../operator/azure-stack-compute-add-scalesets.md) (VMSS) a través del portal. Para crear un VMSS con este tamaño, use PowerShell o una plantilla.

|Tamaño - Tamaño\nombre |vCPU     |Memoria | Tamaño máximo del disco temporal | Rendimiento de discos del SO máx.: (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Rendimiento de discos de datos máx. (IOPS) | Nº máx. NIC |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**A0\Basic_A0**  |1    |768 MB   | 20 GB   |300  | 300  |1 / 1x300  |2   |
|**A1\Basic_A1**  |1    |1,75 GB  | 40 GB   |300  | 300  |2 / 2x300  |2   |
|**A2\Basic_A2**  |2    |3,5 GB   | 60 GB   |300  | 300  |4 / 4x300  |2   |
|**A3\Basic_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8 / 8x300  |2   |
|**A4\Basic_A4**  |8    |14 GB    | 240 GB  |300  | 300  |16 / 16X300 |2   |

### <a name="standard-a"></a>Estándar A 
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1 |0,768 |20  |500 |500 |1x500  |2 |
|**Standard_A1** |1 |1,75  |70  |500 |500 |2 x 500  |2 |
|**Standard_A2** |2 |3,5   |135 |500 |500 |4x500  |2 |
|**Standard_A3** |4 |7     |285 |500 |500 |8x500  |2 |
|**Standard_A4** |8 |14    |605 |500 |500 |16x500 |4 |
|**Standard_A5** |2 |14    |135 |500 |500 |4x500  |2 |
|**Standard_A6** |4 |28    |285 |500 |500 |8x500  |2 |
|**Standard_A7** |8 |56    |605 |500 |500 |16x500 |4 |

### <a name="av2-series"></a>Serie Av2
*Requiere la versión 1804 de Azure Stack Hub o versiones posteriores*

|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standard_A1_v2**  |1   |2   |10   |500 |1000  |2 / 2x500   |2 |
|**Standard_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4x500   |2 |
|**Standard_A4_v2**  |4   |8   |40   |500 |4000  |8 / 8x500   |4 |
|**Standard_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16x500 |8 |
|**Standard_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4x500   |2 |
|**Standard_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8x500   |4 |
|**Standard_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16x500 |8 |

### <a name="d-series"></a>Serie D
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|----------------|----|----|-----|----|------|------------|---------|
|**Standard_D1** |1   |3,5 |50   |500 |3000  |4 / 4x500   |2 |
|**Standard_D2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |


### <a name="ds-series"></a>Serie DS
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3,5 |7    |1000 |4000  |4 / 4x2300   |2 |
|**Standard_DS2** |2   |7   |14   |1000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3** |4   |14  |28   |1000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4** |8   |28  |56   |1000 |32000 |32 / 32x2300 |8 |

### <a name="dv2-series"></a>Serie Dv2
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4x500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>DSv2-series
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1000 |4000  |4 / 4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 |


## <a name="compute-optimized"></a>Proceso optimizado
### <a name="f-series"></a>Serie F
*Requiere la versión 1804 de Azure Stack Hub o versiones posteriores*

|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|-----------------|----|----|-----|----|------|------------|---------|
|**Standard_F1**  |1   |2   |16   |500 |3000  |4 / 4x500   |2 |
|**Standard_F2**  |2   |4   |32   |500 |6000  |8 / 8x500   |2 |
|**Standard_F4**  |4   |8   |64   |500 |12000 |16 / 16x500 |4 |
|**Standard_F8**  |8   |16  |128  |500 |24000 |32 / 32x500 |8 |
|**Standard_F16** |16  |32  |256  |500 |48000 |64 / 64x500 |8 |


### <a name="fs-series"></a>Serie Fs
*Requiere la versión 1804 de Azure Stack Hub o versiones posteriores*  

|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standard_F1s**  |1   |2   |4   |1000 |4000  |4 / 4x2300   |2 |
|**Standard_F2s**  |2   |4   |8   |1000 |8000  |8 / 8x2300   |2 |
|**Standard_F4s**  |4   |8   |16  |1000 |16000 |16 / 16x2300 |4 |
|**Standard_F8s**  |8   |16  |32  |1000 |32000 |32 / 32x2300 |8 |
|**Standard_F16s** |16  |32  |64  |1000 |64000 |64 / 64x2300 |8 |


### <a name="fsv2-series"></a>Serie Fsv2
*Requiere la versión 1804 de Azure Stack Hub o versiones posteriores* 

|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_F2s_v2**  |2   |4   |16   |1000 |4000   |4 / 4x2300    |2 |
|**Standard_F4s_v2**  |4   |8   |32   |1000 |8000   |8 / 8x2300    |2 |
|**Standard_F8s_v2**  |8   |16  |64   |1000 |16000  |16 / 16x2300  |4 |
|**Standard_F16s_v2** |16  |32  |128  |1000 |32000  |32 / 32x2300  |4 |
|**Standard_F32s_v2** |32  |64  |256  |1000 |64000  |32 / 32x2300  |8 |
|**Standard_F64s_v2** |64  |128 |512  |1000 |128000 |32 / 32x2300  |8 |


## <a name="memory-optimized"></a>Memoria optimizada

Los tamaños de las máquinas virtuales optimizadas para memoria ofrecen una relación alta entre memoria y CPU que es excelente para servidores de bases de datos relacionales, memorias caché de medianas a grandes y análisis en memoria.

### <a name="d-series"></a><a name="mo-d"></a>Serie D
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|------------------|---|----|----|--------|------|------------|---------|
|**Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8x500   |2 |
|**Standard_D12**  |4  |28  |200 |500     |12000 |16 / 16x500 |4 |
|**Standard_D13**  |8  |56  |400 |500     |24000 |32 / 32x500 |8 |
|**Standard_D14**  |16 |112 |800 |500     |48000 |64 / 64x500 |8 |

### <a name="ds-series"></a><a name="mo-ds"></a>Serie DS
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1000    |8000  |8 / 8x2300   |2 |
|**Standard_DS12**  |4  |28  |56  |1000    |12000 |16 / 16x2300 |4 |
|**Standard_DS13**  |8  |56  |112 |1000    |32000 |32 / 32x2300 |8 |
|**Standard_DS14**  |16 |112 |224 |1000    |64000 |64 / 64x2300 |8 |

### <a name="dv2-series"></a><a name="mo-dv2"></a>Serie Dv2
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="dsv2-series"></a><a name="mo-dsv2"></a>DSv2-series
|Size     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. NIC |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4 / 4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8 / 8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16 / 16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32 / 32x2300  |8 |


## <a name="next-steps"></a>Pasos siguientes

[Características de las máquinas virtuales de Azure Stack Hub](azure-stack-vm-considerations.md)
