---
title: Características de las máquinas virtuales de Azure Stack Hub
description: Obtenga información acerca de las diferentes características y consideraciones que deben tenerse en cuenta a la hora de trabajar con máquinas virtuales en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 726c632964e0580a5d55219f161e2bc8d6e679de
ms.sourcegitcommit: ad6bbb611ac671b295568d3f00a193b783470c68
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2020
ms.locfileid: "87397505"
---
# <a name="azure-stack-hub-vm-features"></a>Características de las máquinas virtuales de Azure Stack Hub

Las máquinas virtuales de Azure Stack Hub proporcionan recursos informáticos escalables y a petición. Para poder implementar máquinas virtuales, debe aprender las diferencias que existen entre las distintas características de las máquinas virtuales disponibles en Azure Stack Hub y en Microsoft Azure. Este artículo describe estas diferencias e identifica las principales consideraciones para planificar implementaciones de máquina virtual. Para más información acerca de las diferencias de alto nivel entre Azure Stack Hub y Azure, consulte el artículo [Consideraciones clave](azure-stack-considerations.md).

## <a name="vm-differences"></a>Diferencias entre máquinas virtuales

| Característica | Azure (global) | Azure Stack Hub |
| --- | --- | --- |
| Imágenes de máquinas virtuales | Azure Marketplace tiene imágenes que puede usar para crear una máquina virtual. Consulte la página de [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) para ver la lista de imágenes que están disponibles en Azure Marketplace. | De forma predeterminada, no hay ninguna imagen disponible en Marketplace de Azure Stack Hub. El administrador de la nube de Azure Stack Hub debe publicar o descargar imágenes en el Marketplace de Azure Stack Hub para que los usuarios puedan usarlas. |
| Generación de VHD | Las máquinas virtuales de generación 2 admiten características clave que no se admiten en las máquinas virtuales de generación 1. Estas características incluyen una memoria mayor, Intel Software Guard Extensions (SGX Intel) y memoria persistente virtualizada (vPMEM). Las máquinas virtuales de generación 2 que se ejecutan en el entorno local también tienen algunas características que aún no se admiten en Azure. Para más información, consulte [Compatibilidad para máquinas virtuales de generación 2 en Azure](/azure/virtual-machines/windows/generation-2).  | Azure Stack Hub solo admite máquinas virtuales de la generación 1. Puede convertir una máquina virtual de generación 1 de VHDX al formato de archivo VHD y de un disco de expansión dinámica a otro de tamaño fijo. No puede cambiar la generación de una máquina virtual. Para más información, consulte [Compatibilidad para máquinas virtuales de generación 2 en Azure](/azure/virtual-machines/windows/generation-2). |
| Tamaños de máquina virtual | Azure admite una amplia variedad de tamaños de máquinas virtuales. Para aprender más acerca de las opciones y los tamaños disponibles, consulte los[tamaños de máquinas virtuales de Azure](/azure/virtual-machines/sizes). | Azure Stack Hub admite un subconjunto de los tamaños de máquina virtual que están disponibles en Azure. Para ver la lista de tamaños admitidos, consulte la sección [Tamaños de máquina virtual](#vm-sizes) de este artículo. |
| Cuotas de máquinas virtuales | Los [límites de cuota](/azure/azure-resource-manager/management/azure-subscription-service-limits#managing-limits) los establece Microsoft. | El administrador de la nube de Azure Stack Hub debe asignar cuotas antes de ofrecer máquinas virtuales a los usuarios. |
| Extensiones de máquina virtual |Azure admite una amplia variedad de extensiones de máquinas virtuales. Para aprender acerca de las extensiones disponibles, consulte el artículo [Extensiones y características de las máquinas virtuales](/azure/virtual-machines/windows/extensions-features).| Azure Stack Hub admite un subconjunto de extensiones que están disponibles en Azure y cada una de ellas tiene una versión específica. El administrador de la nube de Azure Stack Hub puede elegir las extensiones que están a disposición de sus usuarios. Para ver la lista de extensiones admitidas, consulte la sección [Extensiones de máquina virtual](#vm-extensions) de este artículo. |
| Red de máquinas virtuales | Las direcciones IP públicas asignadas a una máquina virtual del inquilino son accesibles a través de Internet.<br><br><br>Las máquinas virtuales de Azure tienen un nombre DNS fijo. | Las direcciones IP públicas asignadas a una máquina virtual del inquilino son accesibles solo desde el entorno del Kit de desarrollo de Azure Stack. Los usuarios deben tener acceso al Kit de desarrollo de Azure Stack a través de un [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) o una [red privada virtual](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) para conectarse a una máquina virtual que se crea en Azure Stack Hub.<br><br>Las máquinas virtuales creadas en una instancia específica de Azure Stack Hub tienen un nombre DNS basado en el valor que configura el administrador de la nube. |
| Almacenamiento de máquina virtual | Admite [discos administrados](/azure/virtual-machines/windows/managed-disks-overview). | Los discos administrados se admiten en Azure Stack Hub en la versión 1808 y posteriores. |
| Rendimiento de los discos de la máquina virtual | Depende del tamaño y tipo de disco. | Depende del tamaño de la máquina virtual a la que están conectados los discos. Para más información, consulte el artículo [Tamaños de máquina virtual admitidos en Azure Stack Hub](azure-stack-vm-sizes.md).
| Versiones de API | Azure tiene siempre las últimas versiones de API para todas las características de la máquina virtual. | Azure Stack Hub admite servicios específicos de Azure y las versiones de las API específicas de estos servicios. Para ver la lista de versiones de API compatibles, consulte la sección [versiones de API](#api-versions) de este artículo. |
| Servicio de metadatos de instancia de Azure | Azure Instance Metadata Service proporciona información sobre instancias de máquina virtual en ejecución que pueden usarse para administrar y configurar la máquina virtual.  | Azure Instance Metadata Service no es compatible con Azure Stack Hub. |
| Conjuntos de disponibilidad de máquinas virtuales|Varios dominios de error (2 o 3 por región).<br>Varios dominios de actualización.|Varios dominios de error (2 o 3 por región).<br>Dominio de actualización individual, con migración en vivo para proteger las cargas de trabajo durante la actualización. Veinte dominios de actualización admitidos para la compatibilidad de plantillas.<br>La máquina virtual y el conjunto de disponibilidad deben estar en la misma ubicación y grupo de recursos.|
| Conjuntos de escalado de máquinas virtuales|Compatible con la escalabilidad automática.|No compatible con la escalabilidad automática.<br><br>Agregar más instancias a un conjunto de escalado con el portal, las plantillas de Resource Manager o PowerShell. |
| Testigo de Cloud | Seleccione los puntos de conexión de las propiedades de la cuenta de almacenamiento disponibles en Azure Stack Hub. | El [testigo en la nube](/windows-server/failover-clustering/deploy-cloud-witness) es un tipo de testigo de cuórum de clúster de conmutación por error que usa Microsoft Azure para proporcionar un voto en el cuórum de clúster.<br>Los puntos de conexión de Azure global pueden tener el siguiente aspecto en comparación con los de Azure Stack Hub:<br>Para Azure global:<br>`https://mywitness.blob.core.windows.net/`<br>Para Azure Stack Hub:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnóstico de máquina virtual | Se admiten los diagnósticos de máquinas virtuales Linux. | En Azure Stack Hub no se admiten diagnósticos de máquinas virtuales Linux. Si implementa una máquina virtual Linux con diagnósticos de máquina virtual habilitado, se producirá un error en la implementación. Tampoco se podrá realizar la implementación si habilita las métricas básicas de máquina virtual Linux a través de la configuración de diagnóstico. |
| Tamaños de máquina virtual de la virtualización anidada | Compatible | No compatible |

## <a name="vm-sizes"></a>Tamaños de VM

Azure Stack Hub impone límites de recursos para evitar el consumo excesivo de recursos (nivel de servicio y local del servidor). Estos límites mejoran la experiencia del inquilino, ya que reducen el efecto del consumo de recursos por parte de otros inquilinos.

- Para la salida de redes de la máquina virtual, hay extremos de ancho de banda. Los extremos de Azure Stack Hub son los mismos que los de Azure.
- En el caso de los recursos de almacenamiento, Azure Stack Hub implementa límites de IOPS (operaciones de entrada/salida por segundo) de almacenamiento para evitar el consumo excesivo básico de recursos por parte de los inquilinos para el uso de almacenamiento.
- En el caso de los discos de máquina virtual, las operaciones de entrada y salida por segundo de disco en Azure Stack Hub son una función del tamaño de la máquina virtual, en lugar del tipo de disco. Esto significa que para una VM de la serie Standard_Fs, independientemente de si elige SSD o HDD para el tipo de disco, el límite de IOPS de un segundo disco de datos es de solo 2300 IOPS.

En la tabla siguiente se enumeran las máquinas virtuales que se admiten en Azure Stack Hub, junto con su configuración:

| Tipo            | Size          | Intervalo de tamaños admitidos |
| ----------------| ------------- | ------------------------ |
|Uso general  |A básico        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Uso general  |Estándar A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Uso general  |Serie Av2     |[A1_v2 - A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|Uso general  |Serie D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Uso general  |Serie Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Uso general  |Serie DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Uso general  |DSv2-series    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Memoria optimizada |Serie D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Memoria optimizada |Serie DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Memoria optimizada |Serie Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Memoria optimizada |DSv2-series    |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|Proceso optimizado|Serie F       |[F1 - F16](azure-stack-vm-sizes.md#f-series)    |
|Proceso optimizado|Serie Fs      |[F1s - F16s](azure-stack-vm-sizes.md#fs-series)    |
|Proceso optimizado|Serie Fsv2    |[F2s_v2 - F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |

Los tamaños de máquina virtual y sus cantidades de recursos asociados son coherentes entre Azure Stack Hub y Azure. Esto incluye la cantidad de memoria, el número de núcleos, y la cantidad y el tamaño de los discos de datos que se pueden crear. Sin embargo, el rendimiento de las máquinas virtuales con el mismo tamaño depende de las características subyacentes de un entorno de Azure Stack Hub concreto.

## <a name="vm-extensions"></a>Extensiones de máquina virtual

Azure Stack Hub incluye un pequeño conjunto de extensiones. Las actualizaciones y las extensiones adicionales están disponibles a través de la redifusión de Marketplace.

Use el siguiente script de PowerShell para obtener la lista de extensiones de máquina virtual disponibles en su entorno de Azure Stack Hub:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Si aprovisionar una extensión en una implementación de VM tarda demasiado tiempo, deje que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la VM.

## <a name="api-versions"></a>Versiones de API

Las características de las máquinas virtuales de Azure Stack Hub admiten las siguientes versiones de API:

"2017-12-01", "2017-03-30", "2016-03-30", "2015-06-15"

Puede usar el siguiente script de PowerShell para obtener las versiones de API para las características de máquinas virtuales que están disponibles en su entorno de Azure Stack Hub:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

La lista de los tipos de recursos compatibles y las de versiones de API pueden variar si el operador de la nube actualiza su entorno de Azure Stack Hub a una versión más reciente.

## <a name="windows-activation"></a>Activación de Windows

Los productos de Windows deben utilizarse de acuerdo con los derechos de uso del producto y los términos de licencia de Microsoft. Azure Stack Hub usa [Activación automática de máquina virtual](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn303421(v%3dws.11)) (AVMA) para activar máquinas virtuales de Windows Server.

- El host de Azure Stack Hub activa Windows con claves de AVMA para Windows Server 2016. Todas las VM que ejecutan Windows Server 2012 R2 o versiones posteriores se activan automáticamente.
- Las máquinas virtuales que ejecutan Windows Server 2012 o versiones anteriores no se activan automáticamente y este proceso debe realizarse mediante la [activación de MAK](/previous-versions/tn-archive/ff793438(v=technet.10)). Para usar la activación de MAK, debe proporcionar su propia clave de producto.

Microsoft Azure utiliza la activación de KMS para activar las VM de Windows. Si mueve una máquina virtual de Azure Stack Hub a Azure y encuentra problemas de activación, consulte [Solución de problemas de activación de máquinas virtuales Windows de Azure](/azure/virtual-machines/windows/troubleshoot-activation-problems). Puede encontrar información adicional en la entrada del blog del equipo de soporte técnico de Azure [Troubleshooting Windows activation failures on Azure VMs](/archive/blogs/mast/troubleshooting-windows-activation-failures-on-azure-vms) (Solucionar problemas de errores de activación de Windows en máquinas virtuales de Azure).

## <a name="high-availability"></a>Alta disponibilidad

Es posible que la máquina virtual esté sujeta a un reinicio debido a un mantenimiento planeado según lo programado por el operador de Azure Stack Hub. Para conseguir alta disponibilidad en un sistema de producción con varias máquinas virtuales en Azure, las máquinas virtuales se colocan en un [conjunto de disponibilidad](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) que las distribuye entre varios dominios de error y dominios de actualización. En la escala más pequeña de Azure Stack Hub, un dominio de error en un conjunto de disponibilidad se define como un único nodo en la unidad de escalado.  

Aunque la infraestructura de Azure Stack Hub ya es resistente a errores, la tecnología subyacente (los clústeres de conmutación por error) puede experimentar un cierto tiempo de inactividad de las máquinas virtuales ubicadas en un servidor físico en caso de error de hardware. Azure Stack Hub admite un conjunto de disponibilidad con un máximo de tres dominios de error para ser coherente con Azure.

|                   |             |
|-------------------|-------------|
| **Dominios de error** | Las máquinas virtuales colocadas en conjuntos de disponibilidad se aislarán físicamente entre sí al distribuirlas de la manera más uniforme que sea posible en varios dominios de error (nodos de Azure Stack Hub). Si se produce un error de hardware, las máquinas virtuales del dominio de error se reiniciarán en otros dominios de error. Se mantendrán en dominios de error independientes de las otras máquinas virtuales, pero en el mismo conjunto de disponibilidad, si es posible. Cuando el hardware vuelva a estar en línea, las máquinas virtuales se volverán a equilibrar para mantener la alta disponibilidad. |
| **Dominios de actualización**| Los dominios de actualización son otra manera que tiene Azure de proporcionar alta disponibilidad en los conjuntos de disponibilidad. Un dominio de actualización es un grupo lógico de hardware adyacente que puede someterse a mantenimiento al mismo tiempo. Las máquinas virtuales que se encuentran en el mismo dominio de actualización se reiniciarán en conjunto durante el mantenimiento planeado. Cuando los inquilinos crean máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Azure las distribuye de manera automática entre estos dominios de actualización. <br>En Azure Stack Hub, las máquinas virtuales se migran en vivo entre los otros hosts en línea del clúster antes de que se actualice su host subyacente. Como no hay tiempo de inactividad para el inquilino durante una actualización del host, la característica de actualización de dominio de Azure Stack Hub solo existe por motivos de compatibilidad de la plantilla con Azure. Las máquinas virtuales de un conjunto de disponibilidad muestran 0 como número de dominio de actualización en el portal. |

## <a name="next-steps"></a>Pasos siguientes

[Creación de una máquina virtual Windows mediante PowerShell en Azure Stack Hub](azure-stack-quick-create-vm-windows-powershell.md)
