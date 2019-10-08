---
title: Solucionar problemas con el ADSK | Microsoft Docs
description: Obtenga información sobre cómo solucionar problemas del Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ab43d94c2e65032e5e525ec000e38cacb01b2980
ms.sourcegitcommit: 1bae55e754d7be75e03af7a4db3ec43fd7ff3e9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319100"
---
# <a name="troubleshoot-the-asdk"></a>Solucionar problemas del ASDK
En este artículo se proporciona información sobre cómo solucionar problemas comunes para el Kit de desarrollo de Azure Stack (ASDK). Para obtener ayuda con los sistemas integrados de Azure Stack, consulte [Solución de problemas de Microsoft Azure Stack](../operator/azure-stack-troubleshooting.md). 

Como ASDK es un entorno de evaluación, los servicios de soporte técnico al cliente (CSS) de Microsoft no proporcionan dicho soporte. Si tiene alguna incidencia que no esté documentada, puede obtener ayuda de expertos en el [foro de MSDN de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 


## <a name="deployment"></a>Implementación
### <a name="deployment-failure"></a>Error de implementación
Si experimenta un error durante la instalación, puede reiniciar la implementación en el paso con errores con la opción -rerun del script de implementación. Por ejemplo:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al final de la implementación, la sesión de PowerShell todavía está abierta y no muestra ninguna salida.
Este comportamiento probablemente sea solo el resultado del comportamiento predeterminado de cuando se selecciona una ventana Comandos de PowerShell. La implementación del ASDK se ha realizado correctamente, pero el script se ha puesto en pausa al seleccionar la ventana. Puede comprobar que se completó la configuración buscando la palabra "select" en la barra de título de la ventana de comandos. Presione la tecla ESC para cancelar la selección; después debería mostrarse el mensaje de finalización.

## <a name="virtual-machines"></a>Máquinas virtuales
### <a name="default-image-and-gallery-item"></a>Elemento de la galería e imagen predeterminada
Se debe agregar un elemento de la galería y una imagen de Windows Server antes de implementar máquinas virtuales en Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>Después de reiniciar el host de Azure Stack, algunas máquinas virtuales no se inician automáticamente.
Después de reiniciar el host, puede observar que los servicios de Azure Stack no están disponibles de inmediato. Esto se debe a que tanto las [máquinas virtuales de infraestructura](asdk-architecture.md#virtual-machine-roles) de Azure Stack y como los RP tardan un tiempo en comprobar la coherencia, pero finalmente se iniciarán automáticamente.

También puede observar que las VM de ese inquilino no se inician de forma automática después de reiniciar el host del ASDK. Puede conectarlas con algunos pasos manuales:

1.  En el host del ASDK, inicie el **Administrador de clústeres de conmutación por error** en el menú Inicio.
2.  Seleccione el clúster **Cluster.azurestack.local**.
3.  Seleccione **Roles**.
4.  Las máquinas virtuales del inquilino aparecen con estado *guardado*. Una vez que se ejecutan todas las máquinas virtuales de la infraestructura, haga clic en las del inquilino y seleccione **Iniciar** para reanudar la máquina virtual.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>He eliminado algunas máquinas virtuales, pero sigo viendo los archivos del disco duro virtual en el disco. 
Este comportamiento es así por diseño:

* Al eliminar una VM, no se eliminan los discos duros virtuales. Los discos son recursos independientes en el grupo de recursos.
* Cuando se elimina una cuenta de almacenamiento, la eliminación es visible de inmediato a través de Azure Resource Manager, pero los discos que puede contener todavía se conservan en el almacenamiento hasta que se ejecuta la recolección de elementos no utilizados.

Si ve discos duros virtuales "huérfanos", es importante saber si forman parte de la carpeta de una cuenta de almacenamiento que se eliminó. Si la cuenta de almacenamiento no se eliminó, es normal que los discos duros virtuales permanezcan.

Puede leer más acerca de cómo configurar el umbral de conservación y las recuperaciones a petición en [Administración de cuentas de almacenamiento](../operator/azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Recuperación de almacenamiento
Puede que la capacidad recuperada tarde hasta 14 horas en mostrarse en el portal. La recuperación de espacio depende de diversos factores, como el porcentaje de uso de archivos de contenedor internos en el almacén de blobs de bloque. Por lo tanto, en función de los datos que se eliminen, no hay ninguna garantía de la cantidad de espacio que se podría recuperar cuando se ejecute el recolector de elementos no utilizados.

## <a name="next-steps"></a>Pasos siguientes
[Visite el foro de soporte técnico de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
