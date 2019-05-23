---
title: Solución de problemas de Microsoft Azure Stack | Microsoft Docs
description: Información acerca de la solución de problemas del Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 70985ce475ecfe956646941c6b61d70176639eac
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617495"
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Solución de problemas del Kit de desarrollo de Microsoft Azure Stack (ASDK)
En este artículo se proporciona información para solucionar problemas comunes de ASDK. Si tiene algún problema que no esté documentado, asegúrese de consultar el [foro MSDN de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para más información y ayuda.  

> [!IMPORTANT]
> Como el ASDK es un entorno de evaluación, no se ofrece soporte técnico oficial a través de los servicios de soporte técnico al cliente (CSS) de Microsoft.

Las recomendaciones para solucionar los problemas que se describen en esta sección se derivan de varios orígenes y pueden resolver o no el problema en particular. Los códigos de ejemplos se proporcionan tal cual y no se pueden garantizar los resultados esperados. Esta sección está sujeta a modificaciones y actualizaciones frecuentes cuando se implementan mejoras del producto.

## <a name="deployment"></a>Implementación
### <a name="deployment-failure"></a>Error de implementación
Si experimenta un error durante la instalación, puede reiniciar la implementación en el paso con errores con la opción -rerun del script de implementación como se indica en el ejemplo siguiente:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al final de la implementación, la sesión de PowerShell todavía está abierta y no muestra ninguna salida.
Este comportamiento probablemente sea solo el resultado del comportamiento predeterminado de una ventana de comandos de PowerShell, cuando se ha seleccionado. La implementación del kit de desarrollo se ha realizado correctamente, pero el script se ha puesto en pausa al seleccionar la ventana. Puede comprobar que se completó la configuración buscando la palabra "select" en la barra de título de la ventana de comandos. Presione la tecla ESC para cancelar la selección; después debería mostrarse el mensaje de finalización.

## <a name="virtual-machines"></a>Máquinas virtuales
### <a name="default-image-and-gallery-item"></a>Elemento de la galería e imagen predeterminada
Se debe agregar un elemento de la galería y una imagen de Windows Server antes de implementar máquinas virtuales en Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Después de reiniciar el host de Azure Stack, algunas máquinas virtuales podrían no iniciarse automáticamente.
Después de reiniciar el host, puede observar que los servicios de Azure Stack no están disponibles de inmediato. Esto se debe a que tanto las [máquinas virtuales de infraestructura](asdk-architecture.md#virtual-machine-roles) de Azure Stack y como los RP tardan un tiempo en comprobar la coherencia, pero finalmente se iniciarán automáticamente.

También puede observar que las máquinas virtuales de ese inquilino no se inician de forma automática después de reiniciar el host del Kit de desarrollo de Azure Stack. Es un problema conocido y solo requiere algunos pasos manuales para ponerlas en línea:

1.  En el host del Kit de desarrollo de Azure Stack, inicie el **Administrador de clústeres de conmutación por error** en el menú Inicio.
2.  Seleccione el clúster **Cluster.azurestack.local**.
3.  Seleccione **Roles**.
4.  Las máquinas virtuales del inquilino aparecen con estado *guardado*. Una vez que se ejecutan todas las máquinas virtuales de la infraestructura, haga clic en las del inquilino y seleccione **Iniciar** para reanudar la máquina virtual.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>He eliminado algunas máquinas virtuales, pero sigo viendo los archivos del disco duro virtual en el disco. ¿Es normal este comportamiento?
Sí, es normal. Se diseñó así porque:

* Al eliminar una máquina virtual, no se eliminan los discos duros virtuales. Los discos son recursos independientes en el grupo de recursos.
* Cuando se elimina una cuenta de almacenamiento, la eliminación es visible de inmediato a través de Azure Resource Manager, pero los discos que puede contener todavía se conservan en el almacenamiento hasta que se ejecuta la recolección de elementos no utilizados.

Si ve discos duros virtuales "huérfanos", es importante saber si forman parte de la carpeta de una cuenta de almacenamiento que se eliminó. Si la cuenta de almacenamiento no se eliminó, es normal que sigan estando disponibles.

Puede leer más acerca de cómo configurar el umbral de conservación y las recuperaciones a petición en [Administración de cuentas de almacenamiento](../operator/azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Almacenamiento
### <a name="storage-reclamation"></a>Recuperación de almacenamiento
La funcionalidad reclamada capacidad puede tardar hasta 14 horas en mostrarse en el portal. La recuperación de espacio depende de diversos factores, como el porcentaje de uso de archivos de contenedor internos en el almacén de blobs de bloque. Por lo tanto, en función de cuántos datos se eliminen, no hay ninguna garantía de la cantidad de espacio que se podría recuperar cuando se ejecute el recolector de elementos no utilizados.

## <a name="next-steps"></a>Pasos siguientes
[Visite el foro de soporte técnico de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
