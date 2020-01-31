---
title: Solucionar problemas del ASDK
description: Obtenga información sobre cómo solucionar problemas del Kit de desarrollo de Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d02b500fce65464ecc27e8fbbd6edbbf7c6882b3
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873318"
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

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>No se permite el parámetro osProfile de error de validación de plantillas

Si recibe un mensaje de error durante la validación de la plantilla que indica que el parámetro "osProfile" no está permitido, asegúrese de que está usando las versiones correctas de las API para estos componentes:

- [Proceso](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Network](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

Para copiar un disco duro virtual de Azure a Azure Stack, use [AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy). Trabaje con su proveedor para resolver los problemas con la propia imagen. Para más información sobre los requisitos de WALinuxAgent para Azure Stack, consulte [Agente Linux de Azure](../operator/azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>La implementación produce un error debido a la falta de acceso externo
Cuando se produce un error en la implementación durante las etapas en las que se requiere de acceso externo, se devuelve una excepción similar al ejemplo siguiente:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Si se produce este error, asegúrese de que la [documentación del tráfico de red de implementación](../operator/deployment-networking.md) cumple todos los requisitos de red mínimos. También está disponible una herramienta de comprobación de redes para partners como parte del Kit de herramientas de partners.

Otros errores de implementación suelen deberse a problemas para conectarse a recursos de Internet.

Para comprobar la conectividad a los recursos de Internet, puede realizar los pasos siguientes:

1. Abra PowerShell.
2. Escriba -PSSession para WAS01 o cualquiera de las máquinas virtuales de ERCS.
3. Ejecute el siguiente cmdlet: 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Si se produce un error en este comando, compruebe que el conmutador TOR y otros dispositivos de red están configurados para [permitir el tráfico de red](../operator/azure-stack-network.md).


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
