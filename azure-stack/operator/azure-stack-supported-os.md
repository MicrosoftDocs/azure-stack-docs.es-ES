---
title: Sistemas operativos invitados compatibles con Azure Stack | Microsoft Docs
description: Estos sistemas operativos invitados pueden usarse en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2018
ms.openlocfilehash: 53acfa3a2d85e73b5d8136c7a1f42ee00d8ed1be
ms.sourcegitcommit: 07c51a03f07a6a3ee2721aa942d31a7a4c6a339b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/12/2019
ms.locfileid: "67028268"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemas operativos invitados compatibles con Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="windows"></a>Windows

Azure Stack admite los sistemas operativos invitados Windows que se indican en la siguiente tabla:

| Sistema operativo | DESCRIPCIÓN | Disponible en Marketplace |
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
> Para implementar sistemas operativos cliente Windows 10 en Azure Stack, debe tener una [licencia por usuario de Windows](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) o adquirir a través de un proveedor de servicios de hosting cualificado multiinquilino ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Las imágenes de Marketplace están disponibles para licencias de pago por uso o BYOL (EA/SPLA). No se admite el uso de ambas en una sola instancia de Azure Stack. Durante la implementación, Azure Stack inserta una versión adecuada del agente invitado en la imagen.

Las ediciones de Datacenter están disponibles en Marketplace para su descarga; los clientes pueden traer sus propias imágenes de servidor, incluidas otras ediciones. Las imágenes del cliente Windows no están disponibles en Marketplace.

## <a name="linux"></a>Linux

Las distribuciones Linux indicadas como disponibles en Marketplace incluyen el agente Linux de Microsoft Azure (WALA) necesario. Si trae su propia imagen para Azure Stack, siga las instrucciones de [Agregar imágenes de Linux a Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Las imágenes personalizadas se deben compilar con la última versión pública de WALA (en la compilación de Azure Stack 1903 o superior, o con la revisión 1901/1902) o con la versión 2.2.20. Es posible que las versiones anteriores a 2.2.20 y las comprendidas entre 2.2.20 y 2.2.35 (no inclusive) no funcionen correctamente en Azure Stack.
>
> No se admite [cloud-init](https://cloud-init.io/) en Azure Stack en este momento.

| Distribución | DESCRIPCIÓN | Publicador | Marketplace |
| --- | --- | --- | --- |
| Basado en CentOS 6.9 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.5 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.3 | 64 bits | Rogue Wave | Sí |
| ClearLinux | 64 bits | ClearLinux.org | Sí |
| Container Linux |  64 bits | CoreOS | Stable |
| Debian 8 "Jessie" | 64 bits | credativ |  Sí |
| Debian 9 "Stretch" | 64 bits | credativ | Sí |
| Red Hat Enterprise Linux 7.1 (y posterior) | 64 bits | Red Hat | Traiga su propia imagen |
| SLES 11SP4 | 64 bits | SUSE | Sí |
| SLES 12SP3 | 64 bits | SUSE | Sí |
| Ubuntu 14.04 LTS | 64 bits | Canonical | Sí |
| Ubuntu 16.04 LTS | 64 bits | Canonical | Sí |
| Ubuntu 18.04-LTS | 64 bits | Canonical | Sí |

Para obtener información de soporte técnico de Red Hat Enterprise Linux, consulte [Red Hat and Azure Stack: Frequently Asked Questions](https://access.redhat.com/articles/3413531) (Red Hat y Azure Stack: preguntas más frecuentes).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Azure Stack Marketplace, vea los siguientes artículos:

- [Descarga de elementos de Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Crear y publicar un elemento en Marketplace](azure-stack-create-and-publish-marketplace-item.md)
