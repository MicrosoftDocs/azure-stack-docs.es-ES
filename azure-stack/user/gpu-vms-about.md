---
title: Máquina virtual de la unidad de procesamiento gráfico en Azure Stack Hub
description: Referencia de computación de GPU en Azure Stack Hub.
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: reference
ms.date: 10/20/2020
ms.reviewer: kivenkat
ms.lastreviewed: 07/07/2020
ms.openlocfilehash: 9289b1c2ae3119a03898e2d9c361bde4976a16f7
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546929"
---
# <a name="graphics-processing-unit-gpu-virtual-machine-vm-on-azure-stack-hub"></a>Máquina virtual (VM) de la unidad de procesamiento gráfico (GPU) en Azure Stack Hub

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo, se describe cuáles son los modelos de unidad de procesamiento gráfico (GPU) que se admiten en un sistema de varios nodos de Azure Stack Hub. También se pueden encontrar instrucciones sobre la instalación de los controladores que se usan con las GPU. La compatibilidad con GPU en Azure Stack Hub permite soluciones de inteligencia artificial, entrenamiento, inferencia y visualización de datos. La GPU AMD Radeon Instinct MI25 puede usarse para respaldar aplicaciones con un uso intensivo de gráficos, como AutoCAD de Autodesk.

Se puede elegir entre tres modelos de GPU en el período de versión preliminar pública. Están disponibles en las GPU de NVIDIA V100, NVIDIA T4 y AMD MI25. Estas GPU físicas se alinean con los siguientes tipos de máquina virtual (VM) de la serie N de Azure de la siguiente manera:
- [NCv3](/azure/virtual-machines/ncv3-series)
- [NVv4 (AMD Mi25)](/azure/virtual-machines/nvv4-series)
- [NCasT4_v3](/azure/virtual-machines/nct4-v3-series)

> [!IMPORTANT]  
> La compatibilidad con GPU de Azure Stack Hub está actualmente en versión preliminar pública. Para participar en la versión preliminar, rellene el formulario en [aka.ms/azurestackhubgpupreview](https://aka.ms/azurestackhubgpupreview).
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ncv3"></a>NCv3

Las máquinas virtuales de la serie NCv3 disponen de la tecnología de las GPU NVIDIA Tesla V100. Los clientes pueden aprovechar estas GPU actualizadas para cargas de trabajo de HPC tradicionales, como la creación de modelos de embalses, la secuenciación de ADN, el análisis de proteínas, la realización de simulaciones Monte Carlo y otras. 

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | GPU | Memoria de GPU: GiB | Discos de datos máx. | Nº máx. NIC |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 8 |

## <a name="nvv4"></a>NVv4

Las máquinas virtuales de la serie NVv4 usan la tecnología de las GPU [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-MI25). Con la serie NVv4, Azure Stack Hub introduce máquinas virtuales con GPU parciales. Este tamaño se puede usar con aplicaciones de gráficos aceleradas por GPU y escritorios virtuales. Las máquinas virtuales NVv4 actualmente solo admiten el sistema operativo invitado Windows. 

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | GPU | Memoria de GPU: GiB | Discos de datos máx. | Nº máx. NIC | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="ncast4_v3"></a>NCasT4_v3

| Size | vCPU | Memoria: GiB | GPU | Memoria de GPU: GiB | Discos de datos máx. | Nº máx. NIC | 
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 | 1 | 16 | 8 | 4 | 
| Standard_NC8as_T4_v3 |8 |56 | 1 | 16 | 16 | 8 | 
| Standard_NC16as_T4_v3 |16 |112 | 1 | 16 | 32 | 8 | 
| Standard_NC64as_T4_v3 |64 |448 | 4 | 64 | 32 | 8 |

## <a name="patch-and-update-fru-behavior-of-vms"></a>Revisión y actualización, comportamiento de FRU de máquinas virtuales 

Las máquinas virtuales de GPU experimentarán tiempo de inactividad durante operaciones tales como aplicación de revisiones y actualización (PnU) y el reemplazo de hardware (FRU) de Azure Stack Hub. En la tabla siguiente se describe el estado de la máquina virtual observado durante estas actividades, así como la acción manual que el usuario puede hacer para que estas máquinas virtuales vuelvan a estar disponibles después de estas operaciones. 

| Operación | PnU: actualización rápida | PnU: actualización completa, actualización de OEM | FRU | 
| --- | --- | --- | --- | 
| Estado de la máquina virtual  | No disponible durante y después de la actualización sin una operación de inicio manual. | No disponible durante la actualización. Disponible después de la actualización con operación manual. | No disponible durante la actualización. Disponible después de la actualización con operación manual.| 
| Operación manual | Si es necesario que la máquina virtual esté disponible durante la actualización, si hay particiones de GPU disponibles, la máquina virtual se puede reiniciar desde el portal haciendo clic en el botón **Restart** (Reiniciar). Reinicie la máquina virtual después de la actualización en el portal con el botón **Restart** (Reiniciar). | La máquina virtual no puede estar disponible durante la actualización. Después de completar la actualización, la máquina virtual debe detenerse y desasignarse con el botón **Stop** (Detener) e iniciarse de nuevo con el botón "Start" (Iniciar). | La máquina virtual no puede estar disponible durante la actualización. Finalizada esta, la máquina virtual debe detenerse y desasignarse con el botón **Stop** (Detener) y volver a iniciarse con el botón **Start** (Iniciar).| 

## <a name="guest-driver-installation"></a>Instalación del controlador de invitado

### <a name="amd-mi25"></a>AMD MI25

En el artículo [Instalación de controladores de GPU de AMD en máquinas virtuales de la serie N con Windows](/azure/virtual-machines/windows/n-series-amd-driver-setup) se proporcionan instrucciones sobre cómo instalar el controlador para AMD Radeon Instinct MI25 en la máquina virtual habilitada para la GPU-P NVv4 junto con los pasos para comprobar la instalación del controlador. Esta extensión solo funciona en modo conectado.

### <a name="nvidia"></a>NVIDIA

Los controladores de NVIDIA deben instalarse dentro de la máquina virtual para cargas de trabajo de CUDA o GRID mediante la GPU.

#### <a name="use-case-graphicsvisualization"></a>Caso de uso: gráficos/visualización

Este escenario requiere el uso de controladores de GRID. Los controladores de GRID se pueden descargar a través del centro de aplicaciones de NVIDIA siempre que se tengan las licencias necesarias. Los controladores de GRID también requieren un servidor de licencias de GRID con las licencias de GRID adecuadas antes de usarlos en la máquina virtual. Esta documentación se puede usar para aprender a configurar el servidor de licencias.

#### <a name="use-case-computecuda"></a>Caso de uso: proceso/CUDA

Los controladores de NVIDIA CUDA y GRID deben instalarse manualmente en la máquina virtual. Los controladores de Tesla CUDA pueden obtenerse en el [sitio web de descarga](https://www.nvidia.com/Download/index.aspx) de NVIDIA. Los controladores de CUDA no necesitan un servidor de licencias.

## <a name="next-steps"></a>Pasos siguientes

- [Instalación de controladores de GPU de NVIDIA en máquinas virtuales de la serie N con Linux](/azure/virtual-machines/linux/n-series-driver-setup)
- [Características de las máquinas virtuales de Azure Stack](azure-stack-vm-considerations.md)