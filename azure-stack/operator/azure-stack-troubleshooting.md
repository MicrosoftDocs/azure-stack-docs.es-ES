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
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: b3540727b1868c700e43e2865848a71635e8003d
ms.sourcegitcommit: 534117888d9b7d6d363ebe906a10dcf0acf8b685
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173119"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Solución de problemas de Microsoft Azure Stack

En este documento se proporciona información para solucionar problemas de los entornos integrados de Azure Stack. Para obtener ayuda con el Kit de desarrollo de Azure Stack, consulte [Solución de problemas de ASDK](../asdk/asdk-troubleshooting.md) o pida ayuda a los expertos en el [Foro de MSDN de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

En estas secciones se incluyen vínculos a documentos que abordan las preguntas comunes enviadas a los servicios de soporte técnico al cliente (CSS) de Microsoft.

### <a name="purchase-considerations"></a>Consideraciones de compra

* [Cómo comprar](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Azure Stack overview](azure-stack-overview.md) (Introducción a Azure Stack)

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

La configuración del período de retención permite a un operador de nube especificar un período de tiempo en días (entre 0 y 9999 días) durante el cual existe la posibilidad de que cualquier cuenta eliminada se pueda recuperar. El período de retención predeterminado se establece en **0** días. Un valor configurado de **0** significa que una cuenta eliminada está inmediatamente sin retención y marcada para la recolección periódica de elementos no utilizados.

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

* Proveedor de soluciones en la nube
* Suscripción de servicios compartidos de asociados

## <a name="get-scale-unit-metrics"></a>Obtención de métricas de unidad de escalado

Puede usar PowerShell para obtener información sobre el uso de la marca sin ayuda de CSS. Para obtener el uso de la marca: 

1. Cree una sesión PEP.
2. Ejecute test-azurestack.
3. Salga de la sesión PEP.
4. Ejecute get-azurestacklog -filterbyrole seedring mediante una llamada a invoke-command
5. Extraiga el archivo seedring.zip y podrá obtener el informe de validación de la carpeta ERCS donde ejecutó test-azurestack.

Para más información, consulte [Diagnósticos de Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#to-run-get-azurestacklog-on-azure-stack-integrated-systems).

## <a name="troubleshoot-deployment"></a>Solución de problemas de implementación 
### <a name="general-deployment-failure"></a>Error de implementación general
Si experimenta un error durante la instalación, puede reiniciar la implementación en el paso con errores con la opción -rerun del script de implementación.  

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>No se permite el parámetro osProfile de error de validación de plantillas

Si recibe un mensaje de error durante la validación de la plantilla que indica que el parámetro "osProfile" no está permitido, asegúrese de que está usando las versiones correctas de las API para estos componentes:

- [Proceso](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Red](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

Para copiar un disco duro virtual de Azure a Azure Stack, use [AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy). Trabaje con su proveedor para resolver los problemas con la propia imagen. Para más información sobre los requisitos de WALinuxAgent para Azure Stack, consulte [Agente Linux de Azure](azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>La implementación produce un error debido a la falta de acceso externo
Cuando se produce un error en la implementación durante las etapas en las que se requiere de acceso externo, se devuelve una excepción similar al ejemplo siguiente:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Si se produce este error, asegúrese de que la [documentación del tráfico de red de implementación](deployment-networking.md) cumple todos los requisitos de red mínimos. También está disponible una herramienta de comprobación de redes para partners como parte del Kit de herramientas de partners.

Otros errores de implementación suelen deberse a problemas para conectarse a recursos de Internet.

Para comprobar la conectividad a los recursos de Internet, puede realizar los pasos siguientes:

1. Abra PowerShell.
2. Escriba -PSSession para WAS01 o cualquiera de las máquinas virtuales de ERCS.
3. Ejecute el siguiente cmdlet: 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Si se produce un error en este comando, compruebe que el conmutador TOR y otros dispositivos de red están configurados para [permitir el tráfico de red](azure-stack-network.md).

## <a name="troubleshoot-virtual-machines"></a>Solución de problemas de máquinas virtuales
### <a name="default-image-and-gallery-item"></a>Elemento de la galería e imagen predeterminada
Se debe agregar un elemento de la galería y una imagen de Windows Server antes de implementar máquinas virtuales en Azure Stack.


### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk"></a>He eliminado algunas máquinas virtuales, pero sigo viendo los archivos del disco duro virtual en el disco.
Este comportamiento es así por diseño:

* Al eliminar una máquina virtual, no se eliminan los discos duros virtuales. Los discos son recursos independientes en el grupo de recursos.
* Cuando se elimina una cuenta de almacenamiento, la eliminación es visible de inmediato a través de Azure Resource Manager, pero los discos que puede contener todavía se conservan en el almacenamiento hasta que se ejecuta la recolección de elementos no utilizados.

Si ve discos duros virtuales "huérfanos", es importante saber si forman parte de la carpeta de una cuenta de almacenamiento que se eliminó. Si la cuenta de almacenamiento no se eliminó, es normal que sigan estando disponibles.

Puede leer más acerca de cómo configurar el umbral de conservación y las recuperaciones a petición en [Administración de cuentas de almacenamiento](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Solución de problemas de almacenamiento
### <a name="storage-reclamation"></a>Recuperación de almacenamiento
La funcionalidad reclamada capacidad puede tardar hasta 14 horas en mostrarse en el portal. La recuperación de espacio depende de diversos factores, como el porcentaje de uso de archivos de contenedor internos en el almacén de blobs de bloque. Por lo tanto, en función de cuántos datos se eliminen, no hay ninguna garantía de la cantidad de espacio que se podría recuperar cuando se ejecute el recolector de elementos no utilizados.

## <a name="troubleshooting-app-service"></a>Solución de problemas de App Service
### <a name="create-aadidentityappps1-script-fails"></a>Error al ejecutar el script Create-AADIdentityApp.ps1

Si se produce un error en el script Create-AADIdentityApp.ps1 necesario para App Service, asegúrese de que incluye el parámetro -AzureStackAdminCredential necesario al ejecutar el script. Para más información, consulte [Requisitos previos para implementar App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).

