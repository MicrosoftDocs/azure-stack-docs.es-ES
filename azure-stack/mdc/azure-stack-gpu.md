---
title: VM de GPU en Azure Stack | Microsoft Docs
description: Referencia de computación de GPU en Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2020
ms.author: patricka
ms.reviewer: kivenkat
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 2b941c47b9c9662998b8d55bc6878e935969d1a4
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872541"
---
# <a name="gpu-vms-on-azure-stack"></a>VM de GPU en Azure Stack 

*Se aplica a: Sistemas integrados de Azure Stack* 

En este tema se trata cómo administrar VM de GPU en Azure Stack Hub.


## <a name="partitioned-gpu-vm-size"></a>Tamaño de VM de GPU con particiones 

Las máquinas virtuales de la serie NVv4 usan la tecnología de las GPU [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25). Con la serie NVv4, Azure Stack Hub introduce máquinas virtuales con GPU parciales. Este tamaño se puede usar con aplicaciones de gráficos aceleradas por GPU y escritorios virtuales. Las máquinas virtuales NVv4 actualmente solo admiten el sistema operativo invitado Windows. 

| Size | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | GPU | Memoria de GPU: GiB | Discos de datos máx. | Nº máx. NIC | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="patch-and-update-fru-behavior-of-vms"></a>Revisión y actualización, comportamiento de FRU de las VM 

Las máquinas virtuales de GPU experimentarán tiempo de inactividad durante operaciones tales como aplicación de revisiones y actualización (PnU) y el reemplazo de hardware (FRU) de Azure Stack Hub. En la tabla siguiente se describe el estado de la máquina virtual observado durante estas actividades, así como la acción manual que el usuario puede hacer para que estas máquinas virtuales vuelvan a estar disponibles después de estas operaciones. 

| Operación | PnU: actualización rápida | PnU: actualización completa, actualización de OEM | FRU | 
| --- | --- | --- | --- | 
| Estado de la máquina virtual  | No disponible durante y después de la actualización sin una operación de inicio manual. | No disponible durante la actualización. Disponible después de la actualización con operación manual. | No disponible durante la actualización. Disponible después de la actualización con operación manual.| 
| Operación manual | Si es necesario que la VM esté disponible durante la actualización y hay particiones de GPU disponibles, se puede hacer clic en el botón "Reiniciar" del portal para reiniciar la VM. La VM se debe reiniciar después de la actualización desde el portal con el botón "Reiniciar". | La máquina virtual no puede estar disponible durante la actualización. Después de completar la actualización, la VM debe detenerse y desasignarse con el botón "Detener" e iniciarse de nuevo con el botón "Iniciar". | La VM no puede estar disponible durante la actualización. Una vez finalizada esta operación, la VM debe detenerse y desasignarse con el botón "Detener" y volver a iniciarse con el botón "Iniciar".| 

## <a name="guest-driver-installation"></a>Instalación del controlador de invitado 

[En este documento](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup) se explica la configuración del controlador invitado de AMD en la VM habilitada para la GPU-P NVv4 junto con los pasos para comprobar la instalación del controlador. 

## <a name="next-steps"></a>Pasos siguientes 

[Características de las máquinas virtuales de Azure Stack](azure-stack-vm-considerations.md) 
