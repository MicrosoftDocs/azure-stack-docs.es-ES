---
title: Características de las máquinas virtuales de Azure Stack | Microsoft Docs
description: Aprenda sobre las diferentes características y aspectos a la hora de trabajar con máquinas virtuales en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: e3601f4489a0a80881cccd2ba64f98b61e14683a
ms.sourcegitcommit: 70147e858956443bc66b3541ec278c102bb45c07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72381440"
---
# <a name="azure-stack-vm-features"></a>Características de las máquinas virtuales de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Las máquinas virtuales de Azure Stack proporcionan recursos informáticos escalables y a petición. Para poder implementar máquinas virtuales, debe aprender las diferencias entre las características de las máquinas virtuales disponibles en Azure Stack y en Microsoft Azure. Este artículo describe estas diferencias e identifica las principales consideraciones para planificar implementaciones de máquina virtual. Para obtener información acerca de las diferencias de alto nivel entre Azure y Azure Stack, consulte el artículo [Key considerations](azure-stack-considerations.md) (Consideraciones clave).

## <a name="vm-differences"></a>Diferencias entre máquinas virtuales

| Característica | Azure (global) | Azure Stack |
| --- | --- | --- |
| Imágenes de máquinas virtuales | Azure Marketplace tiene imágenes que puede usar para crear una máquina virtual. Consulte la página de [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) para ver la lista de imágenes que están disponibles en Azure Marketplace. | De forma predeterminada, no hay ninguna imagen disponible en Marketplace de Azure Stack. El administrador de la nube de Azure Stack debe publicar o descargar imágenes en el Marketplace de Azure Stack antes de que los usuarios puedan usarlas. |
| Tamaños de máquina virtual | Azure admite una amplia variedad de tamaños de máquinas virtuales. Para aprender más acerca de las opciones y los tamaños disponibles, consulte los temas [Tamaños de máquinas virtuales Windows](/azure/virtual-machines/virtual-machines-windows-sizes) y [Tamaños de máquina virtual Linux](/azure/virtual-machines/linux/sizes). | Azure Stack admite un subconjunto de tamaños de máquinas virtuales que están disponibles en Azure. Para ver la lista de tamaños admitidos, consulte la sección [Tamaños de máquina virtual](#vm-sizes) de este artículo. |
| Cuotas de máquinas virtuales | Los [límites de cuota](/azure/azure-subscription-service-limits#service-specific-limits) los establece Microsoft. | El administrador de la nube de Azure Stack debe asignar cuotas antes de ofrecer máquinas virtuales a los usuarios. |
| Extensiones de máquina virtual |Azure admite una amplia variedad de extensiones de máquinas virtuales. Para aprender acerca de las extensiones disponibles, consulte el artículo [Extensiones y características de las máquinas virtuales](/azure/virtual-machines/windows/extensions-features).| Azure Stack admite un subconjunto de extensiones que están disponibles en Azure y cada una de ellas tiene una versión específica. El administrador de la nube de Azure Stack puede elegir qué extensiones están disponibles para sus usuarios. Para ver la lista de extensiones admitidas, consulte la sección [Extensiones de máquina virtual](#vm-extensions) de este artículo. |
| Red de máquinas virtuales | Las direcciones IP públicas asignadas a una máquina virtual del inquilino son accesibles a través de Internet.<br><br><br>Las máquinas virtuales de Azure tienen un nombre DNS fijo. | Las direcciones IP públicas asignadas a una máquina virtual del inquilino son accesibles solo desde el entorno del Kit de desarrollo de Azure Stack. Un usuario debe tener acceso al Kit de desarrollo de Azure Stack a través de un [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) o una [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) para conectarse a una máquina virtual que se crea en Azure Stack.<br><br>Las máquinas virtuales creadas dentro de una instancia específica de Azure Stack tienen un nombre DNS basado en el valor que se configura mediante el administrador de la nube. |
| Almacenamiento de máquina virtual | Admite [discos administrados](/azure/virtual-machines/windows/managed-disks-overview). | Los discos administrados se admiten en Azure Stack con la versión 1808 y versiones posteriores. |
| Rendimiento de los discos de la máquina virtual | Depende del tamaño y tipo de disco. | Depende del tamaño de la máquina virtual a la que están conectados los discos. Para más información, consulte el artículo [Tamaños de máquina virtual admitidos en Azure Stack](azure-stack-vm-sizes.md).
| Versiones de API | Azure tiene siempre las últimas versiones de API para todas las características de la máquina virtual. | Azure Stack es compatible con servicios específicos de Azure y versiones de API específicas para estos servicios. Para ver la lista de versiones de API compatibles, consulte la sección [versiones de API](#api-versions) de este artículo. |
| Servicio de metadatos de instancia de Azure | Azure Instance Metadata Service proporciona información sobre instancias de máquina virtual en ejecución que pueden usarse para administrar y configurar la máquina virtual.  | Azure Instance Metadata Service no es compatible con Azure Stack. |
| Conjuntos de disponibilidad de máquinas virtuales|Varios dominios de error (2 o 3 por región).<br>Varios dominios de actualización.|Varios dominios de error (2 o 3 por región).<br>Dominio de actualización individual, con migración en vivo para proteger las cargas de trabajo durante la actualización. Veinte dominios de actualización admitidos para la compatibilidad de plantillas.<br>La máquina virtual y el conjunto de disponibilidad deben estar en la misma ubicación y grupo de recursos.|
| Conjuntos de escalado de máquinas virtuales|Compatible con la escalabilidad automática.|No compatible con la escalabilidad automática.<br><br>Agregar más instancias a un conjunto de escalado con el portal, las plantillas de Resource Manager o PowerShell. |
| Testigo de Cloud | Seleccione los puntos de conexión de las propiedades de la cuenta de almacenamiento disponibles en Azure Stack. | El [testigo en la nube](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) es un tipo de testigo de cuórum de clúster de conmutación por error que usa Microsoft Azure para proporcionar un voto en el cuórum de clúster.<br>Los puntos de conexión de Azure global en comparación con Azure Stack pueden tener el siguiente aspecto:<br>Para Azure global:<br>`https://mywitness.blob.core.windows.net/`<br>Para Azure Stack:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnóstico de máquina virtual | Se admiten los diagnósticos de máquinas virtuales Linux. | No se admiten los diagnósticos de una máquina virtual Linux en Azure Stack. Si implementa una máquina virtual Linux con diagnósticos de máquina virtual habilitado, se producirá un error en la implementación. Tampoco se podrá realizar la implementación si habilita las métricas básicas de máquina virtual Linux a través de la configuración de diagnóstico. |

## <a name="vm-sizes"></a>Tamaños de VM

Azure Stack impone límites de recursos para evitar el consumo excesivo de recursos (nivel de servicio y local del servidor). Estos límites mejoran la experiencia del inquilino, ya que reducen el efecto del consumo de recursos por parte de otros inquilinos.

- Para la salida de redes de la máquina virtual, hay extremos de ancho de banda. Los extremos de Azure Stack son los mismos que en Azure.
- En el caso de los recursos de almacenamiento, Azure Stack implementa límites de IOPS (operaciones de entrada/salida por segundo) de almacenamiento para evitar el consumo excesivo básico de recursos por parte de los inquilinos para el uso de almacenamiento.
- Para los discos de máquina virtual, las operaciones de entrada y salida por segundo de disco en Azure Stack es una función del tamaño de máquina virtual en lugar del tipo de disco. Esto significa que para una VM de la serie Standard_Fs, independientemente de si elige SSD o HDD para el tipo de disco, el límite de IOPS de un segundo disco de datos es de solo 2300 IOPS.

En la tabla siguiente se enumeran las máquinas virtuales que se admiten en Azure Stack junto con su configuración:

| type            | Size          | Intervalo de tamaños admitidos |
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

Los tamaños de máquina virtual y sus cantidades de recursos asociados son coherentes entre Azure y Azure Stack. Esto incluye la cantidad de memoria, el número de núcleos, y la cantidad y el tamaño de los discos de datos que se pueden crear. Sin embargo, el rendimiento del mismo tamaño de VM depende de las características subyacentes de un entorno de Azure Stack concreto.

## <a name="vm-extensions"></a>Extensiones de máquina virtual

Azure Stack incluye un pequeño conjunto de extensiones. Las actualizaciones y las extensiones adicionales están disponibles a través de la redifusión de Marketplace.

Use el siguiente script de PowerShell para obtener la lista de extensiones de máquinas virtuales que están disponibles en su entorno de Azure Stack:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Si aprovisionar una extensión en una implementación de VM tarda demasiado tiempo, deje que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la VM.

## <a name="api-versions"></a>Versiones de API

Las características de la máquina virtual en Azure Stack admiten las siguientes versiones de API:

"2017-12-01", "2017-03-30", "2016-03-30", "2015-06-15"

Puede usar el siguiente script de PowerShell para obtener las versiones de API para las características de máquinas virtuales que están disponibles en su entorno de Azure Stack:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

La lista de versiones de API y tipos de recursos admitidos puede variar si el operador de nube actualiza su entorno de Azure Stack a una versión más reciente.

## <a name="windows-activation"></a>Activación de Windows

Los productos de Windows deben utilizarse de acuerdo con los derechos de uso del producto y los términos de licencia de Microsoft. Azure Stack usa la [activación automática de VM](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) para activar máquinas virtuales de Windows Server.

- El host de Azure Stack activa Windows con claves de AVMA para Windows Server 2016. Todas las VM que ejecutan Windows Server 2012 R2 o versiones posteriores se activan automáticamente.
- Las máquinas virtuales que ejecutan Windows Server 2012 o versiones anteriores no se activan automáticamente y este proceso debe realizarse mediante la [activación de MAK](https://technet.microsoft.com/library/ff793438.aspx). Para usar la activación de MAK, debe proporcionar su propia clave de producto.

Microsoft Azure utiliza la activación de KMS para activar las VM de Windows. Si traslada una máquina virtual de Azure Stack a Azure y encuentra problemas de activación, consulte [Solución de problemas de activación de máquinas virtuales Windows de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Puede encontrar información adicional en la entrada del blog del equipo de soporte técnico de Azure [Troubleshooting Windows activation failures on Azure VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) (Solucionar problemas de errores de activación de Windows en máquinas virtuales de Azure).

## <a name="next-steps"></a>Pasos siguientes

[Creación de una máquina virtual Windows mediante PowerShell en Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
