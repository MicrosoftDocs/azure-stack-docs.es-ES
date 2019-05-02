---
title: Arquitectura del Kit de desarrollo de Azure Stack | Microsoft Docs
description: Describe la arquitectura del Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: c69b124f84e87e8f0b937dfa275378c376894f9b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306225"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitectura del Kit de desarrollo de Microsoft Azure Stack
El Kit de desarrollo de Azure Stack (ASDK) es una implementación de un único nodo de Azure Stack que se ejecuta en un único equipo host. Los componentes de enrutamiento perimetral se instalan en el equipo host para proporcionar funcionalidades NAT y VPN para Azure Stack. Los roles de infraestructura de Azure Stack se ejecutan en la capa de Hyper-V del equipo host físico.


## <a name="virtual-machine-roles"></a>Roles de máquina virtual
El Kit de desarrollo de Azure Stack ofrece servicios a través de las siguientes máquinas virtuales hospedadas en el equipo host del kit de desarrollo:

| NOMBRE | DESCRIPCIÓN |
| ----- | ----- |
| **AzS-ACS01** | Servicios de almacenamiento de Azure Stack.|
| **AzS-ADFS01** | Servicios de federación de Active Directory (AD FS).  |
| **AzS-CA01** | Servicios de entidad de certificación para servicios de rol de Azure Stack.|
| **AzS-DC01** | Servicios de Active Directory, DNS y DHCP para Microsoft Azure Stack.|
| **AzS-ERCS01** | Máquina virtual de la consola de recuperación de emergencia. |
| **AzS-GWY01** | Servicios perimetrales de puerta de enlace, como las conexiones de sitio a sitio de VPN para redes de inquilinos.|
| **AzS-NC01** | Controladora de red, que administra los servicios de red de Azure Stack.  |
| **AzS-SLB01** | Servicios de multiplexor del equilibrio de carga en Azure Stack para los inquilinos y los servicios de infraestructura de Azure Stack.  |
| **AzS-SQL01** | Almacén de datos internos para los roles de infraestructura de Azure Stack.  |
| **AzS-WAS01** | Portal de administración de Azure Stack y servicios de Azure Resource Manager.|
| **AzS-WASP01**| Portal del usuario (inquilino) de Azure Stack y servicios de Azure Resource Manager.|
| **AzS-XRP01** | Controlador de administración de infraestructura para Microsoft Azure Stack, incluidos los proveedores de recursos de Compute, Networks y Storage.|


## <a name="next-steps"></a>Pasos siguientes
[Información acerca de las tareas de administración básicas de ASDK](asdk-admin-basics.md)
