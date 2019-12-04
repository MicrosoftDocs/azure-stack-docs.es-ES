---
title: Reemplazar un nodo de la unidad de escalado en un sistema integrado de Azure Stack
titleSuffix: Azure Stack
description: Obtenga información acerca de cómo reemplazar un nodo físico de la unidad de escalado en un sistema integrado de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 255802151183cc2b832aaa64f2110b9c9052a0ba
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465463"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Reemplazar un nodo de la unidad de escalado en un sistema integrado de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el proceso general para reemplazar un equipo físico (también denominado un nodo de la unidad de escalado) en un sistema integrado de Azure Stack. Los pasos para reemplazar el nodo de la unidad de escalado actual varían en función del proveedor de hardware del fabricante de equipos originales (OEM). Consulte la documentación de Field Replaceable Unit (FRU) del proveedor para obtener pasos detallados específicos para su sistema.

> [!CAUTION]  
> La nivelación del firmware es fundamental para el éxito de la operación que se describe en este artículo. La omisión de este paso puede dar lugar a inestabilidad del sistema, disminución del rendimiento, subprocesos de seguridad o impedir que la automatización de Azure Stack implemente el sistema operativo. Vea siempre la documentación del asociado de hardware cuando reemplace hardware para asegurarse de que el firmware aplicado coincide con la versión de OEM que se muestra en el [portal de administración de Azure Stack](azure-stack-updates.md). Para obtener más información y vínculos a la documentación del asociado, vea [Sustitución de un componente de hardware](azure-stack-replace-component.md).

En el siguiente diagrama de flujo se muestra el proceso general de FRU para reemplazar un nodo completo de la unidad de escalado.

![Diagrama de flujo del proceso de reemplazo del nodo](media/azure-stack-replace-node/replacenodeflow.png)

*Es posible que esta acción no sea necesaria en función de la condición física del hardware.

> [!Note]  
> Si se produce un error en la operación de apagado, se recomienda usar la operación de purga seguida de la operación de detención. Para obtener más información, vea [Acciones de los nodos de unidad de escalado en Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-node-actions).

## <a name="review-alert-information"></a>Revisión de la información de las alertas

Si un nodo de la unidad de escalado no está funcionando, recibirá las alertas críticas siguientes:

- Node not connected to network controller (Nodo no conectado al controlador de red)
- Node inaccessible for virtual machine placement (Nodo inaccesible para la colocación de la máquina virtual)
- Scale unit node is offline (Nodo de la unidad de escalado desconectado)

![Lista de alertas para cuando la unidad de escalado no funciona](media/azure-stack-replace-node/nodedownalerts.png)

Si abre la alerta **Scale unit node is offline** (Nodo de la unidad de escalado desconectado), en la descripción de la misma podrá consultar el nodo de la unidad de escalado que no es accesible. También puede recibir alertas adicionales en la solución de supervisión específica del OEM que se ejecuta en el host de ciclo de vida del hardware.

![Detalles de la alerta de nodo sin conexión](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Proceso de reemplazo del nodo de la unidad de escalado

Los pasos siguientes se proporcionan como una descripción general del proceso de reemplazo del nodo de la unidad de escalado. Consulte la documentación de FRU del proveedor de hardware OEM para obtener pasos detallados y específicos para su sistema. No siga estos pasos sin consultar la documentación proporcionada por su OEM.

1. Use la acción **Apagado** para apagar correctamente el nodo de unidad de escalado. Es posible que esta acción no sea necesaria en función de la condición física del hardware.

2. En el improbable caso de que la acción de apagado devuelva un error, use la acción [Purga](azure-stack-node-actions.md#drain) para poner el nodo de unidad de escalado en modo de mantenimiento. Es posible que esta acción no sea necesaria en función de la condición física del hardware.

   > [!NOTE]  
   > En cualquier caso, solo se puede deshabilitar y apagar un nodo a la vez sin que se interrumpan los Espacios de almacenamiento directo (S2D).

3. Una vez que el nodo de la unidad de escalado está en el modo de mantenimiento, use la acción de [Detenido](azure-stack-node-actions.md#stop). Es posible que esta acción no sea necesaria en función de la condición física del hardware.

   > [!NOTE]  
   > En el caso poco probable de que la acción de apagado no funcione, utilice en su lugar la interfaz web del controlador de administración de placa base (BMC).

4. Reemplace el equipo físico. Normalmente, este reemplazo lo realiza el proveedor de hardware de OEM.
5. Use la acción [Reparar](azure-stack-node-actions.md#repair) para agregar el nuevo equipo físico a la unidad de escalado.
6. Use el punto de conexión privilegiado para [comprobar el estado de la reparación del disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). Con las nuevas unidades de datos, un trabajo de reparación del almacenamiento completo puede tardar varias horas, según la carga del sistema y el espacio consumido.
7. Una vez finalizada la acción de reparación, confirme que todas las alertas activas se cerraron automáticamente.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo reemplazar un disco físico mientras el sistema está encendido, consulte [Reemplazar un disco](azure-stack-replace-disk.md). 
- Para obtener información acerca de cómo reemplazar un componente de hardware que requiere apagar el sistema, consulte [Sustitución de un componente de hardware](azure-stack-replace-component.md).
