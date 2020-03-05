---
title: Integración de la auditoría de dispositivos físicos con Azure Stack Hub en el centro de datos
description: Aprenda a integrar la auditoría de acceso a dispositivos físicos con Azure Stack Hub en el centro de datos.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: fe76a0cd0763f7a5d8b1eddcba61402806a15c11
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699412"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-hub-datacenter"></a>Integración de la auditoría de dispositivos físicos con Azure Stack Hub en el centro de datos

Todos los dispositivos físicos de Azure Stack Hub, como los controladores de administración de placa base (BMC) y los conmutadores de red, emiten registros de auditoría. Puede integrar los registros de auditoría en la solución general de auditoría. Puesto que los dispositivos varían entre los diversos proveedores de hardware de OEM de Azure Stack Hub, póngase en contacto con su proveedor para obtener la documentación de integración de auditoría. Las secciones siguientes proporcionan información general sobre la auditoría de dispositivos físicos en Azure Stack Hub.  

## <a name="physical-device-access-auditing"></a>Auditoría de acceso a dispositivos físicos

Todos los dispositivos físicos de Azure Stack Hub admiten el uso de TACACS o RADIUS. La compatibilidad incluye el acceso al controlador de administración de placa base (BMC) y a los conmutadores de red.

Las soluciones de Azure Stack Hub no se suministran con RADIUS o TACACS integrado. Sin embargo, las soluciones se han validado para que admitan el uso de las soluciones de RADIUS o TACACS ya existentes disponibles en el mercado.

Solo para RADIUS, se validó MSCHAPv2. Esta representa la implementación más segura mediante RADIUS. Consulte con su proveedor de hardware de OEM para poder habilitar TACACS o RADIUS en los dispositivos incluidos en su solución de Azure Stack Hub.

## <a name="syslog-forwarding-for-network-devices"></a>Reenvío de Syslog para dispositivos de red

Todos los dispositivos físicos de redes de Azure Stack Hub admiten mensajes de Syslog. Las soluciones de Azure Stack Hub se suministran sin un servidor de Syslog. No obstante, los dispositivos se han validado para que admitan el envío de mensajes a las soluciones de Syslog existentes que hay disponibles en el mercado.

La dirección de destino de Syslog es un parámetro opcional que se recopila en la implementación, pero se puede agregar también después de esta. Consulte con su proveedor de hardware de OEM para configurar el reenvío de Syslog en los dispositivos de redes.

## <a name="next-steps"></a>Pasos siguientes

[Directiva de mantenimiento](azure-stack-servicing-policy.md)
