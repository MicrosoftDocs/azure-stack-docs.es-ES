---
title: Solución de problemas de Microsoft Azure Stack | Microsoft Docs
description: Solución de problemas de Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 09/04/2019
ms.openlocfilehash: a9d62640b2baabfd3283099656719a880dd0a41b
ms.sourcegitcommit: a8379358f11db1e1097709817d21ded0231503eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2019
ms.locfileid: "70377248"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Solución de problemas de Microsoft Azure Stack

En este documento se proporciona información para solucionar problemas de Azure Stack. 


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

En estas secciones se incluyen vínculos a documentos que abordan las preguntas comunes enviadas a los servicios de soporte técnico al cliente (CSS) de Microsoft.

### <a name="purchase-considerations"></a>Consideraciones de compra

* [Cómo comprar](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Azure Stack overview](azure-stack-overview.md) (Introducción a Azure Stack)

### <a name="azure-stack-development-kit-asdk"></a>Kit de desarrollo de Azure Stack (ASDK)

Para obtener ayuda con el [Kit de desarrollo de Azure Stack](../asdk/asdk-what-is.md), puede ponerse en contacto con los expertos en el [Foro de MSDN de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). El ASDK se ofrece como un entorno de evaluación sin soporte técnico a través de CSS. En el foro de MSDN se hacen referencia los casos de soporte técnicos abiertos para ASDK.

### <a name="updates-and-diagnostics"></a>Actualizaciones y diagnósticos

* [Uso de las herramientas de diagnóstico en Azure Stack](azure-stack-diagnostics.md)
* [Validación del estado del sistema de Azure Stack](azure-stack-diagnostic-test.md)
* [Ritmo del lanzamiento de las actualizaciones](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Sistemas operativos y tamaños admitidos para máquinas virtuales invitadas

* [Sistemas operativos invitados compatibles con Azure Stack](azure-stack-supported-os.md)
* [Tamaños de máquina virtual admitidos en Azure Stack](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Elementos de Azure Marketplace disponibles para Azure Stack](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Administrar la capacidad

#### <a name="memory"></a>Memoria

Para aumentar la capacidad de memoria total disponible para Azure Stack, puede agregar más memoria. En Azure Stack, al servidor físico también se le conoce como un nodo de unidad de escalado. Todos los nodos de unidad de escalado que sean miembros de una sola unidad de escalado deben tener [la misma cantidad de memoria](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Período de retención

La configuración del período de retención permite a un operador de nube especificar un período de tiempo en días (entre 0 y 9999 días) durante el cual existe la posibilidad de que cualquier cuenta eliminada se pueda recuperar. El período de retención predeterminado se establece en 0 días. Un valor configurado de "0" significa que una cuenta eliminada está inmediatamente fuera de retención y marcada para la recolección periódica de elementos no utilizados.

* [Establecimiento del período de retención](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Seguridad, cumplimiento e identidad  

#### <a name="manage-rbac"></a>Administración de RBAC

En Azure Stack, un usuario puede ser un lector, propietario o colaborador de cada una de las instancias de una suscripción, grupo de recursos o servicio.

* [Administración de RBAC de Azure Stack](azure-stack-manage-permissions.md)

Si los roles integrados para los recursos de Azure no cumplen las necesidades específicas de su organización, puede crear sus propios roles personalizados. En este tutorial, creará un rol personalizado llamado Reader Support Tickets (Lector de incidencias de soporte) con Azure PowerShell.

* [Tutorial: Creación de un rol personalizado para los recursos de Azure con Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Administración de uso y facturación como un CSP

* [Administración de uso y facturación como un CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Creación de una suscripción de CSP o APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Elija el tipo de cuenta de servicios compartidos que utiliza para Azure Stack. Estos son los tipos de suscripciones que se pueden usar para el registro de una instancia de Azure Stack multiinquilino:

* Proveedor de servicios en la nube
* Suscripción de servicios compartidos de asociados


## <a name="troubleshoot-deployment"></a>Solución de problemas de implementación 
### <a name="general-deployment-failure"></a>Error de implementación general
Si experimenta un error durante la instalación, puede reiniciar la implementación en el paso con errores con la opción -rerun del script de implementación.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al final de la implementación de ASDK, la sesión de PowerShell todavía está abierta y no muestra ninguna salida.
Este comportamiento probablemente sea solo el resultado del comportamiento predeterminado de una ventana de comandos de PowerShell, cuando se ha seleccionado. La implementación del kit de desarrollo se ha realizado correctamente, pero el script se ha puesto en pausa al seleccionar la ventana. Puede comprobar que se completó la configuración buscando la palabra "select" en la barra de título de la ventana de comandos. Presione la tecla ESC para cancelar la selección; después debería mostrarse el mensaje de finalización.

### <a name="deployment-fails-due-to-lack-of-external-access"></a>La implementación produce un error debido a la falta de acceso externo
Cuando se produce un error en la implementación durante las etapas en las que se requiere de acceso externo, se devuelve una excepción similar al ejemplo siguiente:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Si se produce este error, revise la [documentación del tráfico de red de implementación](deployment-networking.md) para asegurarse de que se cumplen todos los requisitos de red mínimos. También está disponible una herramienta de comprobación de redes para partners como parte del Kit de herramientas de partners.

Los errores de implementación que presenta la excepción anterior suelen deberse a problemas para conectarse a recursos de Internet.

Para comprobar que este es el problema, puede realizar los pasos siguientes:

1. Abra PowerShell.
2. Escriba -PSSession para WAS01 o cualquiera de las máquinas virtuales de ERCS.
3. Ejecute el commandlet: Test-NetConnection login.windows.net -port 443

Si se produce un error en este comando, compruebe que el conmutador TOR y otros dispositivos de red están configurados para [permitir el tráfico de red](azure-stack-network.md).

## <a name="troubleshoot-virtual-machines"></a>Solución de problemas de máquinas virtuales
### <a name="default-image-and-gallery-item"></a>Elemento de la galería e imagen predeterminada
Se debe agregar un elemento de la galería y una imagen de Windows Server antes de implementar máquinas virtuales en Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Después de reiniciar el host de Azure Stack, algunas máquinas virtuales podrían no iniciarse automáticamente.
Después de reiniciar el host, puede observar que los servicios de Azure Stack no están disponibles de inmediato.  Esto se debe a que las [máquinas virtuales de infraestructura](../asdk/asdk-architecture.md#virtual-machine-roles ) de Azure Stack y los proveedores de recursos tardan un tiempo en comprobar la coherencia, pero finalmente se iniciarán de forma automática.

También puede observar que las máquinas virtuales de ese inquilino no se inician de forma automática después de reiniciar el host del Kit de desarrollo de Azure Stack. Es un problema conocido y solo requiere algunos pasos manuales para ponerlas en línea:

1.  En el host del Kit de desarrollo de Azure Stack, inicie el **Administrador de clústeres de conmutación por error** en el menú Inicio.
2.  Seleccione el clúster **Cluster.azurestack.local**.
3.  Seleccione **Roles**.
4.  Las máquinas virtuales del inquilino aparecen con estado *guardado*. Una vez que se ejecutan todas las máquinas virtuales de la infraestructura, haga clic en las del inquilino y seleccione **Iniciar** para reanudarlas.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>He eliminado algunas máquinas virtuales, pero sigo viendo los archivos del disco duro virtual en el disco. ¿Es normal este comportamiento?
Sí, este es el comportamiento esperado. Se diseñó así porque:

* Al eliminar una máquina virtual, no se eliminan los discos duros virtuales. Los discos son recursos independientes en el grupo de recursos.
* Cuando se elimina una cuenta de almacenamiento, la eliminación es visible de inmediato a través de Azure Resource Manager, pero los discos que puede contener todavía se conservan en el almacenamiento hasta que se ejecuta la recolección de elementos no utilizados.

Si ve discos duros virtuales "huérfanos", es importante saber si forman parte de la carpeta de una cuenta de almacenamiento que se eliminó. Si la cuenta de almacenamiento no se eliminó, es normal que sigan estando disponibles.

Puede leer más acerca de cómo configurar el umbral de conservación y las recuperaciones a petición en [Administración de cuentas de almacenamiento](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Solución de problemas de almacenamiento
### <a name="storage-reclamation"></a>Recuperación de almacenamiento
La funcionalidad reclamada capacidad puede tardar hasta 14 horas en mostrarse en el portal. La recuperación de espacio depende de diversos factores, como el porcentaje de uso de archivos de contenedor internos en el almacén de blobs de bloque. Por lo tanto, en función de cuántos datos se eliminen, no hay ninguna garantía de la cantidad de espacio que se podría recuperar cuando se ejecute el recolector de elementos no utilizados.

