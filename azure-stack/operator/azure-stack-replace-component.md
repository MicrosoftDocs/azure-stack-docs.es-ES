---
title: Sustitución de un componente de hardware en un nodo de unidad de escalado de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo reemplazar un componente de hardware en un sistema integrado de Azure Stack.
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
ms.date: 07/18/2019
ms.author: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 4cb8da451743bc6a8e15c57aacf28f0aa83258c9
ms.sourcegitcommit: 4f3e161e7632c8a6e3d41946b09f22b5bdb08d36
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413149"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Sustitución de un componente de hardware en un nodo de unidad de escalado de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el proceso general para reemplazar los componentes de hardware que se pueden intercambiar directamente. Los verdaderos pasos para reemplazar varían en función del fabricante del hardware OEM. Consulte la documentación de Field Replaceable Unit (FRU) del proveedor para obtener los pasos detallados específicos para el sistema integrado de Azure Stack.

> [!CAUTION]  
> La nivelación del firmware es fundamental para el éxito de la operación que se describe en este artículo. La omisión de este paso puede dar lugar a inestabilidad del sistema, disminución del rendimiento, subprocesos de seguridad o evitar que la automatización de Azure Stack implemente el sistema operativo. Vea siempre la documentación del asociado de hardware cuando reemplace este para asegurarse de que el firmware aplicado coincide con la versión de OEM que se muestra en el [portal de administración de Azure Stack](azure-stack-updates.md).

| Asociado de hardware | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Todo | [Guía de operaciones de Cisco Integrated System for Microsoft Azure Stack](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Notas de la versión de Cisco Integrated System for Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Todo | [Cloud for Microsoft Azure Stack 14G (cuenta e inicio de sesión necesarios)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud for Microsoft Azure Stack 13G (cuenta e inicio de sesión necesarios)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPÓN | [Departamento de soporte técnico de servicio administrado de Fujitsu (cuenta e inicio de sesión necesarios)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Soporte técnico de TI de Fujitsu para productos y sistemas](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport (cuenta e inicio de sesión necesarios)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Todo | [HPE ProLiant for Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Todo | [ThinkAgile SXM Best Recipes](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Paquete de firmware/OEM](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[Documentación de terra Azure Stack (incluye FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

Los componentes que no se pueden intercambiar directamente incluyen:

- CPU*
- Memoria*
- Controlador de administración de placa base (BMC)/tarjeta de vídeo
- Controlador de disco/adaptador de bus host (HBA)/backplane
- Adaptador de red (NIC)
- Disco del sistema operativo*
- Unidades de datos (unidades que no admiten intercambio directo, por ejemplo, tarjeta complementaria PCI-e)*

*Es posible que estos componentes admitan el intercambio directo, pero esto puede variar en función de la implementación del proveedor. Consulte la documentación de FRU del distribuidor de OEM para ver los pasos detallados.

En el diagrama de flujo siguiente se muestra el proceso general de FRU para reemplazar un componente de hardware que no admite el intercambio directo.

![Diagrama de flujo que muestra el flujo de reemplazo de componente](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Es posible que esta acción no sea necesaria en función de la condición física del hardware.

** El fabricante del hardware OEM podría o no realizar el reemplazo de componentes y actualizar el firmware en función del contrato de soporte técnico.

## <a name="review-alert-information"></a>Revisión de la información de las alertas

El sistema de mantenimiento y supervisión de Azure Stack rastrea el estado de los adaptadores de red y las unidades de datos que Espacios de almacenamiento directo controla. No rastrea otros componentes de hardware. Para todos los demás componentes de hardware, se generan alertas en la solución de supervisión de hardware específica del proveedor que se ejecuta en el host de ciclo de vida de hardware.  

## <a name="component-replacement-process"></a>Proceso de reemplazo de los componentes

Los pasos siguientes proporcionan una información general de alto nivel del proceso de reemplazo de componentes. No siga estos pasos sin consultar la documentación de FRU proporcionada por su OEM.

1. Use la acción Apagado para apagar correctamente el nodo de unidad de escalado. Es posible que esta acción no sea necesaria en función de la condición física del hardware.

2. En el improbable caso de que la acción de apagado devuelva un error, use la acción [Purga](azure-stack-node-actions.md#drain) para poner el nodo de unidad de escalado en modo de mantenimiento. Es posible que esta acción no sea necesaria en función de la condición física del hardware.

   > [!NOTE]  
   > En cualquier caso, solo se puede deshabilitar y apagar un nodo a la vez sin que se interrumpan los Espacios de almacenamiento directo (S2D).

3. Una vez que el nodo de la unidad de escalado está en el modo de mantenimiento, use la acción de [apagado](azure-stack-node-actions.md#scale-unit-node-actions). Es posible que esta acción no sea necesaria en función de la condición física del hardware.

   > [!NOTE]  
   > En el caso improbable de que la acción de apagado no funcione, use en su lugar la interfaz web del controlador de administración de placa base (BMC).

4. Reemplace el componente de hardware dañado. Si el fabricante del hardware OEM realiza el reemplazo de componente podría variar en función del contrato de soporte técnico.  
5. Actualice el firmware. Siga el proceso de actualización del firmware específico del proveedor con el host de ciclo de vida de hardware para asegurarse de que el componente de hardware de reemplazo tiene aplicado el nivel de firmware aprobado. Si el fabricante del hardware OEM realiza este paso podría variar en función del contrato de soporte técnico.  
6. Use la acción de [reparación](azure-stack-node-actions.md#scale-unit-node-actions) para devolver el nodo de la unidad de escalado a la unidad de escalado.
7. Use el punto de conexión con privilegios para [comprobar el estado de la reparación del disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). Con las nuevas unidades de datos, un trabajo de reparación del almacenamiento completo puede tardar varias horas, según la carga del sistema y el espacio consumido.
8. Una vez finalizada la acción de reparación, confirme que todas las alertas activas se cerraron automáticamente.

## <a name="next-steps"></a>Pasos siguientes

- Para información sobre cómo reemplazar un disco físico intercambiable directamente, consulte [Reemplazar un disco físico en Azure Stack](azure-stack-replace-disk.md).
- Para información sobre cómo reemplazar un nodo físico, consulte el artículo sobre cómo [reemplazar un nodo de unidad de escalado](azure-stack-replace-node.md).
