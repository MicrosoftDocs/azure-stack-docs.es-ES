---
title: Auditoría de dispositivos físicos de Azure Stack
description: Obtenga información sobre cómo integrar la auditoría de acceso a dispositivos físicos en Azure Stack
services: azure-stack
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.topic: article
origin.date: 02/11/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 02/11/2019
keywords: ''
ms.openlocfilehash: 7e39370879884dc8900671d174fc6e0708907d83
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307113"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Integración del centro de datos de Azure Stack: auditoría de dispositivos físicos

Todos los dispositivos físicos de Azure Stack, como los controladores de administración de placa base (BMC) y los conmutadores de red, emiten registros de auditoría. Puede integrar los registros de auditoría en la solución general de auditoría. Puesto que los dispositivos varían entre los diversos proveedores de hardware de OEM de Azure Stack, póngase en contacto con su proveedor para obtener la documentación de integración de auditoría.
Las secciones siguientes proporcionan información general sobre la auditoría de dispositivos físicos en Azure Stack.  

## <a name="physical-device-access-auditing"></a>Auditoría de acceso a dispositivos físicos

Todos los dispositivos físicos de Azure Stack admiten el uso de TACACS o RADIUS. La compatibilidad incluye el acceso al controlador de administración de placa base (BMC) y a los conmutadores de red.

Las soluciones de Azure Stack no se suministran con RADIUS o TACACS integrado. Sin embargo, las soluciones se han validado para que admitan el uso de las soluciones de RADIUS o TACACS ya existentes disponibles en el mercado.

Solo para RADIUS, se validó MSCHAPv2. Esta representa la implementación más segura mediante RADIUS.
Consulte con su proveedor de hardware de OEM para poder habilitar TACACS o RADIUS en los dispositivos incluidos en su solución de Azure Stack.

## <a name="syslog-forwarding-for-network-devices"></a>Reenvío de Syslog para dispositivos de red

Todos los dispositivos físicos de redes de Azure Stack admiten mensajes de Syslog. Las soluciones de Azure Stack se suministran sin un servidor de Syslog. No obstante, los dispositivos se han validado para que admitan el envío de mensajes a las soluciones de Syslog existentes que hay disponibles en el mercado.

La dirección de destino de Syslog es un parámetro opcional que se recopila en la implementación, pero se puede agregar también después de esta. Consulte con su proveedor de hardware de OEM para configurar el reenvío de Syslog en los dispositivos de redes.

## <a name="next-steps"></a>Pasos siguientes

[Directiva de mantenimiento](azure-stack-servicing-policy.md)
