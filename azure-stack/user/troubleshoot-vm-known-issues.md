---
title: Solución de problemas conocidos de máquinas virtuales en Azure Stack Hub
description: Aprenda a solucionar problemas conocidos de máquinas virtuales en Azure Stack Hub
author: mattbriggs
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 07/09/2020
ms.openlocfilehash: a33c732309d2b74ef341f248a7e89dcbd35a4221
ms.sourcegitcommit: 77f53d8f4188feea7dd2197650ee860104b1e2aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501048"
---
# <a name="known-issues-vms-on-azure-stack-hub"></a>Problemas conocidos: Máquinas virtuales en Azure Stack Hub

En este artículo puede encontrar ayuda para solucionar problemas conocidos del proveedor de recursos de proceso de Azure Stack Hub cuando trabaje con máquinas virtuales y conjuntos de escalado.

## <a name="portal-doesnt-show-correct-vm-name"></a>El portal no muestra el nombre correcto de la máquina virtual
- **Aplicable a**  
    este problema afecta a todas las versiones.  
- **Causa**  
    Al ver los detalles de una máquina virtual en la hoja de información general, el nombre del equipo se muestra como (no disponible). Por su propio diseño, la pantalla es para las máquinas virtuales creadas a partir de discos o instantáneas de disco especializados.  
- **Corrección**  
    En el portal, seleccione **Configuración** > **Propiedades**.
- **Repetición**  
    Comunes  

## <a name="nvv4-vm-size-on-portal"></a>Tamaño de máquina virtual NVv4 en el portal
- **Aplicable a**  
    Este problema se aplica a Azure Stack Hub versión 2002 y posteriores.  
- **Causa**  
    Durante la creación de una máquina virtual, verá el tamaño de máquina virtual: NV4as_v4. Los clientes que tienen el hardware necesario para la versión preliminar de la GPU de Azure Stack Hub basada en el procesador AMD MI25 pueden realizar una implementación correcta de la máquina virtual. Todos los demás clientes recibirán un error de implementación con este tamaño de máquina virtual.  
- **Corrección**  
    Ninguno.  
- **Repetición**  
    Comunes  

## <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM
- **Aplicable a**  
    este problema se aplica a todas las versiones admitidas.  
- **Causa**  
    al crear una nueva máquina virtual (VM), puede aparecer el siguiente error: No se pudo iniciar la máquina virtual "nombreDeLaMáquinaVirtual". Error: No se pudo actualizar la configuración de salida de serie de la máquina virtual "vm-name"). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.  
- **Corrección**  
    Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- **Repetición**  
    Comunes  

## <a name="vm-diagnostics-storage-account-not-found"></a>No se ha encontrado la cuenta de almacenamiento de diagnóstico de la máquina virtual
- **Aplicable a**  
    este problema se aplica a todas las versiones admitidas.  
- **Causa**  
    Al intentar iniciar una máquina virtual detenida desasignada, puede aparecer el siguiente error: No se encontró la cuenta de almacenamiento de los diagnósticos de la máquina virtual "diagnosticstorageaccount". Asegúrese de que la cuenta de almacenamiento no se ha eliminado. El error se produce si intenta iniciar una máquina virtual con los diagnósticos de arranque habilitados, pero se ha eliminado la cuenta de almacenamiento de diagnósticos de arranque a la que se hace referencia.  
- **Corrección**  
    Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.  
- **Repeticiones** habituales  

## <a name="consumed-compute-quota"></a>Cuota de proceso consumida
- **Aplicable a**  
    este problema se aplica a todas las versiones admitidas.  
- **Causa**   
    Al crear una nueva máquina virtual, es posible que reciba un error como, por ejemplo, Esta suscripción tiene una capacidad de vCPU regionales totales en esta ubicación. y está usando las 50 vCPU regionales totales disponibles. Esto indica que se ha alcanzado la cuota de núcleos totales que tiene disponibles.  
- **Corrección**  
    pida a su operador un plan complementario con una cuota adicional. El cambio de la cuota del plan actual no funcionará ni reflejará la cuota incrementada.
- **Repetición**  
    poco frecuente  

## <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

-  **Aplicable a**  
    este problema se aplica a todas las versiones admitidas.  
- **Causa**  
    Creación de errores durante la revisión y actualización de entornos de Azure Stack Hub de cuatro nodos. La creación de máquinas virtuales en un conjunto de disponibilidad de tres dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error FabricVmPlacementErrorUnsupportedFaultDomainSize durante el proceso de actualización en un entorno de Azure Stack Hub de cuatro nodos.  
- **Corrección**  
    Se pueden crear máquinas virtuales únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una implementación de Azure Stack Hub de cuatro nodos.  
- **Repetición**  
    poco frecuente  

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de las [características de la máquina virtual de Azure Stack Hub](azure-stack-vm-considerations.md).
