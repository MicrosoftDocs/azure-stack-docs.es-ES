---
title: Auditoría de dispositivos físicos de Azure Stack
description: Obtenga información sobre cómo integrar la auditoría de acceso a dispositivos físicos en Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
keywords: ''
ms.openlocfilehash: 5ded88064fa91222ca061073f72c9f3b539c374c
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828286"
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
