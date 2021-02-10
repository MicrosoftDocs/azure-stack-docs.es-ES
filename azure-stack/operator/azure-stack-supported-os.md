---
title: Sistemas operativos invitados compatibles con Azure Stack Hub
titleSuffix: Azure Stack
description: Aprenda qué sistemas operativos invitados pueden usarse en Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 02/01/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: 336a4d27adf5f0ab52fffc6b624ce84f7fd0b53c
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245915"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Sistemas operativos invitados compatibles con Azure Stack Hub

## <a name="windows"></a>Windows

Azure Stack Hub admite los sistemas operativos invitados Windows que se indican en la siguiente tabla:

| Sistema operativo | Descripción | Disponible en Marketplace de Azure Stack Hub |
| --- | --- | --- |
| Windows Server, versión 1709 | 64 bits | Core con Containers |
| Windows Server 2019 | 64 bits |  Datacenter, Datacenter Core, Datacenter con Containers |
| Windows Server 2016 | 64 bits |  Datacenter, Datacenter Core, Datacenter con Containers |
| Windows Server 2012 R2 | 64 bits |  Centro de datos |
| Windows Server 2012 | 64 bits |  Centro de datos |
| Windows Server 2008 R2 SP1 | 64 bits |  Centro de datos |
| Windows Server 2008 SP2 | 64 bits |  Traiga su propia imagen |
| Windows 10 *(vea la nota 1)* | 64 bits, Pro y Enterprise | Traiga su propia imagen |

> [!NOTE]
> Para implementar sistemas operativos cliente Windows 10 en Azure Stack Hub, debe tener una [licencia por usuario de Windows](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) o adquirir una mediante un [proveedor de servicios de hosting cualificado multiinquilino (QMTH)](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

Las imágenes de Marketplace están disponibles para licencias de pago por uso o BYOL (EA/SPLA). No se admite el uso de ambas en una sola instancia de Azure Stack Hub. Durante la implementación, Azure Stack Hub inserta una versión adecuada del agente invitado en la imagen.

Las ediciones Datacenter se pueden descargar en Marketplace de Azure Stack Hub; los clientes pueden traer sus propias imágenes de servidor, incluidas otras ediciones. Las imágenes del cliente Windows no están disponibles en Marketplace de Azure Stack Hub.

## <a name="linux"></a>Linux

Las distribuciones Linux indicadas como disponibles en el Marketplace de Azure Stack Hub incluyen el agente Linux de Microsoft Azure (WALA) necesario. Si trae su propia imagen para Azure Stack, siga las instrucciones de [Agregar imágenes de Linux a Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Las imágenes personalizadas se deben compilar con la última versión pública de WALA (en la compilación de Azure Stack Hub 1903 o superior, o con la revisión 1901/1902) o con la versión 2.2.20. Es posible que las versiones anteriores a 2.2.20 y las comprendidas entre 2.2.21 y 2.2.34 (ambas incluidas) no funcionen correctamente en Azure Stack Hub. En la versión 1910 de Azure Stack Hub y posteriores, todas las versiones del agente WALA de Azure funcionan con Azure Stack Hub.
>
> [cloud-init](https://cloud-init.io/) se admite en la versión 1910 y posterior de Azure Stack Hub.

| Distribución | Descripción | Publicador | Marketplace de Azure Stack Hub |
| --- | --- | --- | --- |
| Basado en CentOS 8.0 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.8 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.7 LVM | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.7 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.6 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.5 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.5 LVM | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.4 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.3 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 6.9 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 6.10 | 64 bits | Rogue Wave | Sí |
| ClearLinux | 64 bits | ClearLinux.org | Sí |
| CoreOS Linux (estable) |  64 bits | CoreOS | Sí |
| Debian 8 "Jessie" | 64 bits | credativ |  Sí |
| Debian 9 "Stretch" | 64 bits | credativ | Sí |
| Oracle Linux | 64 bits | Oracle | Sí |
| Red Hat Enterprise Linux 7.1 (y posterior) | 64 bits | Red Hat | Traiga su propia imagen |
| SLES 11SP4 | 64 bits | SUSE | Sí |
| SLES 12SP3 | 64 bits | SUSE | Sí |
| Ubuntu 14.04 LTS | 64 bits | Canonical | Sí |
| Ubuntu 16.04 LTS | 64 bits | Canonical | Sí |
| Ubuntu 18.04-LTS | 64 bits | Canonical | Sí |
| Ubuntu Server 20.04 LTS | 64 bits | Canonical | Sí |

Para información de soporte técnico de Red Hat Enterprise Linux, consulte [Red Hat y Azure Stack Hub: Frequently Asked Questions](https://access.redhat.com/articles/3413531) (Red Hat y Azure Stack: preguntas más frecuentes).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Marketplace de Azure Stack Hub, consulte los siguientes artículos:

- [Descarga de elementos de Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Crear y publicar un elemento en Marketplace](azure-stack-create-and-publish-marketplace-item.md)
