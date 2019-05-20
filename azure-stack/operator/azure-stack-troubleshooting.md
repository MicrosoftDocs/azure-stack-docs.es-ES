---
title: Solución de problemas de Microsoft Azure Stack | Microsoft Docs
description: Solución de problemas de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: c7486cb56dee87b8a894d165fce4c3a0dfaae6d9
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618117"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Solución de problemas de Microsoft Azure Stack

Este documento proporciona información para solucionar problemas comunes de Azure Stack. 

> [!NOTE]
> Como Azure Stack Technical Development Kit (ASDK) se ofrece como un entorno de evaluación, no hay soporte técnico oficial de los Servicios de soporte al cliente de Microsoft. Si experimenta un problema, asegúrese de revisar el [foro MSDN de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para obtener más ayuda e información.  

Las recomendaciones para solucionar los problemas que se describen en esta sección se derivan de varios orígenes y pueden resolver o no el problema en particular. Los ejemplos de código se proporcionan tal cual y no se pueden garantizar los resultados esperados. Esta sección está sujeta a modificaciones y actualizaciones frecuentes cuando se implementan mejoras del producto.

## <a name="deployment"></a>Implementación
### <a name="general-deployment-failure"></a>Error de implementación general
Si experimenta un error durante la instalación, puede reiniciar la implementación en el paso con errores con la opción -rerun del script de implementación.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al final de la implementación de ASDK, la sesión de PowerShell todavía está abierta y no muestra ninguna salida.
Este comportamiento probablemente sea solo el resultado del comportamiento predeterminado de una ventana de comandos de PowerShell, cuando se ha seleccionado. La implementación del kit de desarrollo se ha realizado correctamente, pero el script se ha puesto en pausa al seleccionar la ventana. Puede comprobar que se completó la configuración buscando la palabra "select" en la barra de título de la ventana de comandos.  Presione la tecla ESC para cancelar la selección; después debería mostrarse el mensaje de finalización.

### <a name="deployment-fails-due-to-lack-of-external-access"></a>La implementación produce un error debido a la falta de acceso externo
Cuando se produce un error en la implementación durante las etapas en las que se requiere de acceso externo, se devuelve una excepción similar al ejemplo siguiente:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Si se produce este error, revise la [documentación del tráfico de red de implementación](deployment-networking.md) para asegurarse de que se cumplen todos los requisitos de red mínimos. También está disponible una herramienta de comprobación de redes para partners como parte del Kit de herramientas de partners.

Los errores de implementación que presentan la excepción anterior suelen deberse a problemas para conectarse a recursos de Internet.

Para comprobar que este es el problema, puede realizar los pasos siguientes:

1. Abra PowerShell.
2. Escriba -PSSession para WAS01 o cualquiera de las máquinas virtuales de ERCS.
3. Ejecute el commandlet: Test-NetConnection login.windows.net -port 443

Si se produce un error en este comando, compruebe que el conmutador TOR y otros dispositivos de red están configurados para [permitir el tráfico de red](azure-stack-network.md).

## <a name="virtual-machines"></a>Máquinas virtuales
### <a name="default-image-and-gallery-item"></a>Elemento de la galería e imagen predeterminada
Se debe agregar un elemento de la galería y una imagen de Windows Server antes de implementar máquinas virtuales en Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Después de reiniciar el host de Azure Stack, algunas máquinas virtuales podrían no iniciarse automáticamente.
Después de reiniciar el host, puede observar que los servicios de Azure Stack no están disponibles de inmediato.  Esto se debe a que las [máquinas virtuales de infraestructura](../asdk/asdk-architecture.md#virtual-machine-roles ) de Azure Stack y los proveedores de recursos tardan un tiempo en comprobar la coherencia, pero finalmente se iniciarán de forma automática.

También puede observar que las máquinas virtuales de ese inquilino no se inician de forma automática después de reiniciar el host del Kit de desarrollo de Azure Stack. Es un problema conocido y solo requiere algunos pasos manuales para ponerlas en línea:

1.  En el host del Kit de desarrollo de Azure Stack, inicie el **Administrador de clústeres de conmutación por error** en el menú Inicio.
2.  Seleccione el clúster **Cluster.azurestack.local**.
3.  Seleccione **Roles**.
4.  Las máquinas virtuales del inquilino aparecen con estado *guardado*. Una vez que se ejecutan todas las máquinas virtuales de la infraestructura, haga clic en las del inquilino y seleccione **Iniciar** para reanudar la máquina virtual.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>He eliminado algunas máquinas virtuales, pero sigo viendo los archivos del disco duro virtual en el disco. ¿Es normal este comportamiento?
Sí, este es el comportamiento esperado. Se diseñó así porque:

* Al eliminar una máquina virtual, no se eliminan los discos duros virtuales. Los discos son recursos independientes en el grupo de recursos.
* Cuando se elimina una cuenta de almacenamiento, la eliminación es visible de inmediato a través de Azure Resource Manager, pero los discos que puede contener todavía se conservan en el almacenamiento hasta que se ejecuta la recolección de elementos no utilizados.

Si ve discos duros virtuales "huérfanos", es importante saber si forman parte de la carpeta de una cuenta de almacenamiento que se eliminó. Si la cuenta de almacenamiento no se eliminó, es normal que sigan estando disponibles.

Puede leer más acerca de cómo configurar el umbral de conservación y las recuperaciones a petición en [Administración de cuentas de almacenamiento](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Almacenamiento
### <a name="storage-reclamation"></a>Recuperación de almacenamiento
La funcionalidad reclamada capacidad puede tardar hasta 14 horas en mostrarse en el portal. La recuperación de espacio depende de diversos factores, como el porcentaje de uso de archivos de contenedor internos en el almacén de blobs de bloque. Por lo tanto, en función de cuántos datos se eliminen, no hay ninguna garantía de la cantidad de espacio que se podría recuperar cuando se ejecute el recolector de elementos no utilizados.

