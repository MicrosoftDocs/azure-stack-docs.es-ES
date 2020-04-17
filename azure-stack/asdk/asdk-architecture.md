---
title: Arquitectura de ASDK
description: Obtenga información acerca de la arquitectura del Kit de desarrollo de Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 207b99b9a3cbb6c030a6e79137d036820b3b3f60
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "79295176"
---
# <a name="asdk-architecture"></a>Arquitectura de ASDK
El Kit de desarrollo de Azure Stack (ASDK) es una implementación de un único nodo de Azure Stack que se ejecuta en un único equipo host. Los componentes de enrutamiento perimetral se instalan en el equipo host para proporcionar funcionalidades NAT y VPN para Azure Stack. Los roles de infraestructura de Azure Stack se ejecutan en la capa de Hyper-V del equipo host físico.


## <a name="virtual-machine-roles"></a>Roles de máquina virtual
El Kit de desarrollo de Azure Stack ofrece servicios a través de las siguientes máquinas virtuales hospedadas en el equipo host del kit de desarrollo:

| Nombre | Descripción |
| ----- | ----- |
| **AzS-ACS01** | Servicios de almacenamiento de Azure Stack.|
| **AzS-ADFS01** | Servicios de federación de Active Directory (AD FS).  |
| **AzS-CA01** | Servicios de entidad de certificación para servicios de rol de Azure Stack.|
| **AzS-DC01** | Servicios de Active Directory, DNS y DHCP para Microsoft Azure Stack.|
| **AzS-ERCS01** | Máquina virtual de la consola de recuperación de emergencia. |
| **AzS-GWY01** | Servicios perimetrales de puerta de enlace, como las conexiones de sitio a sitio de VPN para redes de inquilinos.|
| **AzS-NC01** | Controladora de red, que administra los servicios de red de Azure Stack.  |
| **AzS-SLB01** | Servicios de multiplexor de equilibrio de carga en Azure Stack para los inquilinos y los servicios de infraestructura de Azure Stack.  |
| **AzS-SQL01** | Almacén de datos internos para los roles de infraestructura de Azure Stack.  |
| **AzS-WAS01** | Portal de administración de Azure Stack y servicios de Azure Resource Manager.|
| **AzS-WASP01**| Portal del usuario (inquilino) de Azure Stack y servicios de Azure Resource Manager.|
| **AzS-XRP01** | Controlador de administración de infraestructura para Microsoft Azure Stack, incluidos los proveedores de recursos de Compute, Networks y Storage.|
| **AzS-SRNG01** | VM de anillo de soporte que hospeda el servicio de recopilación de registros de Azure Stack. |

## <a name="next-steps"></a>Pasos siguientes
[Información acerca de las tareas de administración básicas de ASDK](asdk-admin-basics.md)
