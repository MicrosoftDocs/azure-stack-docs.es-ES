---
title: Solución de problemas de Azure Stack Hub
titleSuffix: Azure Stack
description: Aprenda a solucionar problemas de Azure Stack Hub, como problemas con las máquinas virtuales, el almacenamiento y App Service.
author: PatAltimore
ms.topic: article
ms.date: 12/10/2020
ms.author: v-myoung
ms.reviewer: prchint
ms.lastreviewed: 12/10/2020
ms.openlocfilehash: 583c0e933e823b1ac0fcf11fd378e81515656099
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343662"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Solución de problemas de Azure Stack Hub

Este documento contiene información acerca de cómo solucionar problemas en los entornos integrados de Azure Stack Hub. Para obtener ayuda con el Kit de desarrollo de Azure Stack Hub, consulte [Solución de problemas de ASDK](../asdk/asdk-troubleshooting.md) o pida ayuda a los expertos en el [Foro de MSDN de Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

En estas secciones se incluyen vínculos a documentos que abordan las preguntas comunes enviadas a Soporte técnico de Microsoft.

### <a name="purchase-considerations"></a>Consideraciones de compra

* [Cómo comprar](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Introducción a Azure Stack Hub](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Actualizaciones y diagnósticos

* [Uso de las herramientas de diagnóstico en Azure Stack Hub](./azure-stack-diagnostic-log-collection-overview.md)
* [Validación del estado del sistema en Azure Stack Hub](azure-stack-diagnostic-test.md)
* [Ritmo del lanzamiento de las actualizaciones](azure-stack-servicing-policy.md#update-package-release-cadence)
* [Comprobación y solución de problemas de estado de nodo](azure-stack-node-actions.md)

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

* [Tutorial: Creación de un rol personalizado para los recursos de Azure con Azure PowerShell](/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Administración de uso y facturación como un CSP

* [Administración de uso y facturación como un CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Creación de una suscripción de CSP o APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Elija el tipo de cuenta de servicios compartidos que utiliza con Azure Stack Hub. Estos son los tipos de suscripciones que se pueden usar para el registro de una instancia de multiinquilino de Azure Stack Hub:

* Proveedor de soluciones en la nube
* Suscripción de servicios compartidos de asociados

### <a name="get-scale-unit-metrics"></a>Obtención de métricas de unidad de escalado

Puede usar PowerShell para obtener información sobre el uso de la marca sin ayuda de Soporte técnico de Microsoft. Para obtener el uso de la marca:

1. Cree una sesión PEP.
2. Ejecute `test-azurestack`.
3. Salga de la sesión PEP.
4. Ejecute `get-azurestacklog -filterbyrole seedring` mediante una llamada a Invoke-Command.
5. Extraiga el archivo seedring.zip. Puede obtener el informe de validación de la carpeta ERCS donde ejecutó `test-azurestack`.

Para más información, consulte [Diagnósticos de Azure Stack Hub](azure-stack-get-azurestacklog.md).

## <a name="troubleshoot-virtual-machines-vms"></a>Solución de problemas de máquinas virtuales

### <a name="reset-linux-vm-password"></a>Restablecimiento de la contraseña de la máquina virtual Linux

Si olvida la contraseña de una máquina virtual Linux y la opción **Restablecer la contraseña** no funciona debido a problemas con la extensión VMAccess, puede realizar un restablecimiento siguiendo estos pasos:

1. Elija una máquina virtual Linux para usarla como máquina virtual de recuperación.

1. Inicie sesión en el portal de usuario:
   1. Anote el tamaño de la máquina virtual, la NIC, la dirección IP pública, el grupo de seguridad de red y los discos de datos.
   1. Detenga la máquina virtual afectada.
   1. Elimine la máquina virtual afectada.
   1. Conecte el disco de la máquina virtual afectada como un disco de datos en la máquina virtual de recuperación (puede tardar un par de minutos para que el disco esté disponible).

1. Inicie sesión en la máquina virtual de recuperación y ejecute el siguiente comando:

   ```
   sudo su –
   mkdir /tempmount
   fdisk -l
   mount /dev/sdc2 /tempmount /*adjust /dev/sdc2 as necessary*/
   chroot /tempmount/
   passwd root /*substitute root with the user whose password you want to reset*/
   rm -f /.autorelabel /*Remove the .autorelabel file to prevent a time consuming SELinux relabel of the disk*/
   exit /*to exit the chroot environment*/
   umount /tempmount
   ```

1. Inicie sesión en el portal de usuario:

   1. Desconecte el disco de la máquina virtual de recuperación.
   1. Vuelva a crear la máquina virtual a partir del disco.
   1. Asegúrese de transferir la dirección IP pública de la máquina virtual anterior, conecte los discos de datos, etc.


También puede tomar una instantánea del disco original y crear un nuevo disco a partir de ella en lugar de realizar los cambios directamente en el disco original. Para más información, consulte los temas siguientes:

- [Restablecimiento de contraseñas](/azure/virtual-machines/troubleshooting/reset-password)
- [Creación de un disco a partir de una instantánea](/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-portal-linux#create-a-disk-from-the-snapshot)
- [Cambio y restablecimiento de la contraseña raíz](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-terminal_menu_editing_during_boot#sec-Changing_and_Resetting_the_Root_Password)


### <a name="license-activation-fails-for-windows-server-2012-r2-during-provisioning"></a>No se puede activar la licencia para Windows Server 2012 R2 durante el aprovisionamiento

En este caso, Windows no se activará y verá una marca de agua en la esquina inferior derecha de la pantalla. Los registros de WaSetup.xml que se encuentran en C:\Windows\Panther contienen el siguiente evento:

```xml
<Event time="2019-05-16T21:32:58.660Z" category="ERROR" source="Unattend">
    <UnhandledError>
        <Message>InstrumentProcedure: Failed to execute 'Call ConfigureLicensing()'. Will raise error to caller</Message>
        <Number>-2147221500</Number>
        <Description>Could not find the VOLUME_KMSCLIENT product</Description>
        <Source>Licensing.wsf</Source>
    </UnhandledError>
</Event>
```


Para activar la licencia, copie la clave de activación automática de máquina virtual para la SKU que desea activar.

|Edición|Clave de activación automática de máquina virtual|
|-|-|
|Centro de datos|Y4TGP-NPTV9-HTC2H-7MGQ3-DV4TW|
|Estándar|DBGBW-NPF86-BJVTX-K3WKJ-MTB6V|
|Essentials|K2XGM-NMBT3-2R6Q8-WF2FK-P36R2|

En la máquina virtual, ejecute el siguiente comando:

```powershell
slmgr /ipk <AVMA_key>
```

Para obtener detalles completos, consulte [Activación de máquinas virtuales](/windows-server/get-started-19/vm-activation-19).

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

Si usa un sistema integrado en un escenario sin conexión, es conveniente que utilice una entidad de certificación (CA) empresarial. Exporte el certificado raíz en un formato de base 64 y, a continuación, impórtelo en el Explorador de Azure Storage. No olvide quitar la barra diagonal final (`/`) del punto de conexión de Resource Manager. Para más información, consulte [Prepárese para conectarse a Azure Stack Hub](../user/azure-stack-storage-connect-se.md).

## <a name="troubleshoot-app-service"></a>Solución de problemas de App Service

### <a name="create-aadidentityappps1-script-fails"></a>Error al ejecutar el script Create-AADIdentityApp.ps1

Si se produce un error en el script Create-AADIdentityApp.ps1 necesario para App Service, no olvide incluir el parámetro `-AzureStackAdminCredential` obligatorio al ejecutar el script. Para más información, consulte [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app).

## <a name="troubleshoot-azure-stack-hub-updates"></a>Solución de problemas de las actualizaciones de Azure Stack Hub

El proceso de revisión y actualización de Azure Stack Hub está diseñado para permitir a los operadores aplicar paquetes de actualización de una manera coherente y simplificada. Aunque es poco frecuente, pueden producirse problemas durante la revisión y actualización. Se recomiendan los siguientes pasos en caso de que se produzca un problema durante el proceso de revisión y actualización:

0. **Requisitos previos**: Asegúrese de que ha seguido la [lista de comprobación de la actividad de actualización](release-notes-checklist.md) y de que ha [habilitado la recopilación proactiva de registros](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively).

1. Siga los pasos de corrección de la alerta de error que se crea cuando se produce un error en la actualización.

2. Si no ha podido resolver el problema, cree una [incidencia de soporte técnico de Azure Stack Hub](./azure-stack-help-and-support-overview.md?view=azs-2002). Asegúrese de que ha [recopilado los registros](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now) del intervalo de tiempo en el que se produjo el problema.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Problemas habituales de revisión y actualización de Azure Stack Hub

*Se aplica a: Sistemas integrados de Azure Stack Hub*

### <a name="preparationfailed"></a>PreparationFailed

**Aplicable a**: este problema se aplica a todas las versiones admitidas.

**Causa**: Al intentar instalar la actualización de Azure Stack Hub, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a `PreparationFailed`. En el caso de los sistemas conectados a Internet, esto suele indicar que el paquete de actualización no se puede descargar correctamente debido a una conexión de Internet débil. 

**Corrección**: Para solucionar este problema, vuelva a hacer clic en **Instalar ahora**. Si el problema persiste, recomendamos cargar manualmente la actualización siguiendo la [sección de instalación de actualizaciones](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Repetición**: Comunes

### <a name="warnings-and-errors-reported-while-update-is-in-progress"></a>Advertencias y errores detectados mientras la actualización está en curso

**Aplicable a**: este problema se aplica a todas las versiones admitidas.

**Causa**: Cuando una actualización de Azure Stack Hub está en el estado **En curso**, pueden aparecer advertencias y errores en el portal. Los componentes pueden agotar el tiempo esperando por otros componentes durante la actualización, lo que genera un error. Azure Stack Hub cuenta con un mecanismo para reintentar o corregir algunas de las tareas debido a errores intermitentes.

**Corrección**: Mientras la actualización de Azure Stack Hub se encuentre en el estado **En curso**, se pueden omitir las advertencias y los errores que se indican en el portal.

**Repetición**: Comunes

::: moniker range="azs-2002"
### <a name="2002-update-failed"></a>Error en la actualización 2002

**Aplicable a**: este problema solo se aplica a la versión 2002.

**Causa**: al intentar instalar la actualización 2002, es posible que se produzca un error de actualización y que se proporcione el siguiente mensaje: `The private network parameter is missing from cloud parameters. Please use set-azsprivatenetwork cmdlet to set private networkTrace`.

**Corrección**: [configure una red interna privada](./azure-stack-network.md?view=azs-2002#private-network).
::: moniker-end