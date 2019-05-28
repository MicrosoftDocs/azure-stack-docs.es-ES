---
title: Administración de la capacidad de memoria física para Azure Stack | Microsoft Docs
description: Supervise y administre el espacio de almacenamiento disponible para Azure Stack.
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3aa69f1ea6e16b582a17da867c4895413d0043d2
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782374"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Administración de la capacidad de memoria física para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Para aumentar la capacidad de memoria total disponible para Azure Stack, puede agregar más memoria. En Azure Stack, al servidor físico también se le conoce como un *nodo de unidad de escalado*. Todos los nodos de unidad de escalado que sean miembros de una sola unidad de escalado deben tener la misma cantidad de memoria.

> [!note]  
> Antes de continuar, consulte la documentación del fabricante de hardware para ver si se admite la actualización de una memoria física. El contrato de soporte técnico del proveedor de hardware de OEM puede requerir que el proveedor realice la colocación del bastidor del servidor físico y la actualización de firmware del dispositivo.

En el siguiente diagrama de flujo se muestra el proceso general para agregar memoria a cada nodo de la unidad de escalado.

![Agregar memoria a cada nodo de la unidad de escalado](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Adición de memoria a un nodo existente
Los pasos siguientes proporcionan una información general de alto nivel sobre el proceso de adición de memoria. 

> [!Warning]
> No siga estos pasos sin consultar la documentación proporcionada por su OEM.
> 
> [!Warning]
> Se debe cerrar la unidad de escalado completa porque no se admite una actualización gradual de la memoria.

1. Detenga Azure Stack mediante los pasos descritos en el artículo [Inicio y detención de Azure Stack](azure-stack-start-and-stop.md).
2. Actualice la memoria en cada equipo físico con la documentación del fabricante del hardware.
3. Inicie Azure Stack mediante los pasos descritos en el artículo [Inicio y detención de Azure Stack](azure-stack-start-and-stop.md).

## <a name="next-steps"></a>Pasos siguientes

 - Para aprender a administrar cuentas de almacenamiento en Azure Stack para buscar, recuperar y reclamar capacidad de almacenamiento según las necesidades empresariales, consulte [Administración de cuentas de almacenamiento en Azure Stack](azure-stack-manage-storage-accounts.md).
 - Para aprender cómo el operador en la nube de Azure Stack supervisa y administra la capacidad de almacenamiento de su implementación de Azure Stack, consulte [Administración de la capacidad de almacenamiento para Azure Stack](azure-stack-manage-storage-shares.md). 
