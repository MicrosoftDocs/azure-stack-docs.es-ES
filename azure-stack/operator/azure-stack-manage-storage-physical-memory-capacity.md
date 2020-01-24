---
title: Administración de la capacidad de memoria física en Azure Stack Hub | Microsoft Docs
description: Aprenda a supervisar y administrar la memoria física y la capacidad en Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 24fdcfc4b28a1c5fb9ebb2d7facb774ec176de05
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535151"
---
# <a name="manage-physical-memory-capacity-in-azure-stack-hub"></a>Administración de la capacidad de memoria física en Azure Stack Hub

Para aumentar la capacidad de memoria total disponible en Azure Stack Hub, puede agregar más memoria. En Azure Stack Hub, el servidor físico se conoce también como *nodo de unidad de escalado*. Todos los nodos de unidad de escalado que sean miembros de una sola unidad de escalado deben tener la misma cantidad de memoria.

> [!note]  
> Antes de continuar, consulte la documentación del fabricante de hardware para ver si se admite la actualización de una memoria física. El contrato de soporte técnico del proveedor de hardware de OEM puede requerir que el proveedor realice la colocación del bastidor del servidor físico y la actualización de firmware del dispositivo.

En el siguiente diagrama de flujo se muestra el proceso general para agregar memoria a cada nodo de la unidad de escalado.

![Proceso para agregar memoria a cada nodo de la unidad de escalado](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Adición de memoria a un nodo existente
Los pasos siguientes proporcionan información general de alto nivel sobre el proceso de adición de memoria.

> [!Warning]
> No siga estos pasos sin consultar la documentación proporcionada por su OEM.
> 
> [!Warning]
> Se debe cerrar la unidad de escalado completa porque no se admite una actualización gradual de la memoria.

1. Detenga Azure Stack Hub mediante los pasos descritos en el artículo [Inicio y detención de Azure Stack Hub](azure-stack-start-and-stop.md).
2. Actualice la memoria en cada equipo físico con la documentación del fabricante del hardware.
3. Inicie Azure Stack Hub mediante los pasos descritos en el artículo [Inicio y detención de Azure Stack Hub](azure-stack-start-and-stop.md).

## <a name="next-steps"></a>Pasos siguientes

 - Para obtener información sobre cómo administrar cuentas de almacenamiento en Azure Stack Hub, consulte [Administración de cuentas de almacenamiento de Azure Stack Hub](azure-stack-manage-storage-accounts.md).
 - Para obtener información sobre cómo supervisar y administrar la capacidad de almacenamiento de la implementación de Azure Stack Hub, consulte [Administración de la capacidad de almacenamiento para Azure Stack Hub](azure-stack-manage-storage-shares.md).
