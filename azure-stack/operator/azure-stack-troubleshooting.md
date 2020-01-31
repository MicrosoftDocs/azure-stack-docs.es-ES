---
title: Solución de problemas de Azure Stack Hub
titleSuffix: Azure Stack
description: Aprenda a solucionar problemas de Azure Stack Hub, como problemas con las máquinas virtuales, el almacenamiento y App Service.
author: justinha
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: bacffec0b3bda76eab27097c2d090df371d51fcf
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880848"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Solución de problemas de Azure Stack Hub

Este documento contiene información acerca de cómo solucionar problemas en los entornos integrados de Azure Stack Hub. Para obtener ayuda con el Kit de desarrollo de Azure Stack Hub, consulte [Solución de problemas de ASDK](../asdk/asdk-troubleshooting.md) o pida ayuda a los expertos en el [Foro de MSDN de Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

En estas secciones se incluyen vínculos a documentos que abordan las preguntas comunes enviadas a los servicios de soporte técnico al cliente (CSS) de Microsoft.

### <a name="purchase-considerations"></a>Consideraciones de compra

* [Cómo comprar](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Introducción a Azure Stack Hub](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Actualizaciones y diagnósticos

* [Uso de las herramientas de diagnóstico en Azure Stack Hub](azure-stack-diagnostics.md)
* [Validación del estado del sistema en Azure Stack Hub](azure-stack-diagnostic-test.md)
* [Ritmo del lanzamiento de las actualizaciones](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Sistemas operativos y tamaños admitidos para máquinas virtuales invitadas

* [Sistemas operativos invitados compatibles con Azure Stack Hub](azure-stack-supported-os.md)
* [Tamaños de máquina virtual admitidos en Azure Stack Hub](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Elementos de Azure Marketplace disponibles para Azure Stack Hub](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Administrar la capacidad

#### <a name="memory"></a>Memoria

Si desea aumentar la capacidad de memoria total disponible en Azure Stack Hub, puede agregar más memoria. En Azure Stack Hub, el servidor físico se conoce también como "nodo de unidad de escalado". Todos los nodos de unidad de escalado que sean miembros de una sola unidad de escalado deben tener [la misma cantidad de memoria](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Período de retención

La configuración del período de retención permite al operador de nube especificar un período de tiempo en días (entre 0 y 9999 días) durante el cual existe la posibilidad de que una cuenta eliminada pueda recuperarse. El período de retención predeterminado se establece en **0** días. Un valor configurado de **0** significa que una cuenta eliminada está inmediatamente sin retención y marcada para la recolección periódica de elementos no utilizados.

* [Establecimiento del período de retención](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Seguridad, cumplimiento e identidad  

#### <a name="manage-rbac"></a>Administración de RBAC

En Azure Stack Hub, un usuario puede ser un lector, propietario o colaborador de cada una de las instancias de una suscripción, grupo de recursos o servicio.

* [Administración de RBAC de Azure Stack Hub](azure-stack-manage-permissions.md)

Si los roles integrados para los recursos de Azure no cumplen las necesidades específicas de su organización, puede crear sus propios roles personalizados. En este tutorial, creará un rol personalizado llamado Reader Support Tickets (Lector de incidencias de soporte) con Azure PowerShell.

* [Tutorial: Creación de un rol personalizado para los recursos de Azure con Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Administración de uso y facturación como un CSP

* [Administración de uso y facturación como un CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Creación de una suscripción de CSP o APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Elija el tipo de cuenta de servicios compartidos que utiliza con Azure Stack Hub. Estos son los tipos de suscripciones que se pueden usar para el registro de una instancia de multiinquilino de Azure Stack Hub:

* Proveedor de soluciones en la nube
* Suscripción de servicios compartidos de asociados

### <a name="get-scale-unit-metrics"></a>Obtención de métricas de unidad de escalado

Puede usar PowerShell para obtener información sobre el uso de la marca sin ayuda de CSS. Para obtener el uso de la marca:

1. Cree una sesión PEP.
2. Ejecute `test-azurestack`.
3. Salga de la sesión PEP.
4. Ejecute `get-azurestacklog -filterbyrole seedring` mediante una llamada a Invoke-Command.
5. Extraiga el archivo seedring.zip. Puede obtener el informe de validación de la carpeta ERCS donde ejecutó `test-azurestack`.

Para más información, consulte [Diagnósticos de Azure Stack Hub](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

## <a name="troubleshoot-virtual-machines-vms"></a>Solución de problemas de máquinas virtuales

### <a name="default-image-and-gallery-item"></a>Elemento de la galería e imagen predeterminada

Antes de implementar máquinas virtuales en Azure Stack Hub, es necesario agregar un elemento de la galería y una imagen de Windows Server.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>He eliminado algunas máquinas virtuales, pero sigo viendo los archivos del disco duro virtual en el disco.

Este comportamiento es así por diseño:

* Al eliminar una VM, no se eliminan los discos duros virtuales. Los discos son recursos independientes en el grupo de recursos.
* Cuando se elimina una cuenta de almacenamiento, la eliminación puede verse de inmediato en Azure Resource Manager. Sin embargo, los discos que puede contener se conservan en el almacenamiento hasta que se ejecuta la recolección de elementos no utilizados.

Si ve discos duros virtuales "huérfanos", es importante saber si forman parte de la carpeta de una cuenta de almacenamiento que se eliminó. Si la cuenta de almacenamiento no se eliminó, es normal que los discos duros virtuales sigan allí.

Puede leer más acerca de cómo configurar el umbral de conservación y las recuperaciones a petición en [Administración de cuentas de almacenamiento](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Solución de problemas de almacenamiento

### <a name="storage-reclamation"></a>Recuperación de almacenamiento

La funcionalidad reclamada capacidad puede tardar hasta 14 horas en mostrarse en el portal. La recuperación de espacio depende de diversos factores, como el porcentaje de uso de archivos de contenedor internos en el almacén de blobs en bloques. Por lo tanto, en función de los datos que se eliminen, no hay ninguna garantía de la cantidad de espacio que se podría recuperar cuando se ejecute el recolector de elementos no utilizados.

### <a name="azure-storage-explorer-not-working-with-azure-stack-hub"></a>El Explorador de Azure Storage no funciona con Azure Stack Hub

Si usa un sistema integrado en un escenario sin conexión, es conveniente que utilice una entidad de certificación (CA) empresarial. Exporte el certificado raíz en un formato de base 64 y, a continuación, impórtelo en el Explorador de Azure Storage. No olvide quitar la barra diagonal final (`/`) del punto de conexión de Resource Manager. Para más información, consulte [Prepárese para conectarse a Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se#prepare-for-connecting-to-azure-stack).

## <a name="troubleshooting-app-service"></a>Solución de problemas de App Service

### <a name="create-aadidentityappps1-script-fails"></a>Error al ejecutar el script Create-AADIdentityApp.ps1

Si se produce un error en el script Create-AADIdentityApp.ps1 necesario para App Service, no olvide incluir el parámetro `-AzureStackAdminCredential` obligatorio al ejecutar el script. Para más información, consulte [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).
