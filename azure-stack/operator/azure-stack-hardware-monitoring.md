---
title: Supervisión del mantenimiento de hardware de Azure Stack | Microsoft Docs
description: Aprenda a supervisar el mantenimiento de los componentes de hardware de Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: b41a60aaf45d4b4fbbbf00945a4048dbf9dfb13a
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308258"
---
# <a name="monitor-azure-stack-hardware-components"></a>Supervisar componentes de hardware de Azure Stack

El sistema de mantenimiento y supervisión de Azure Stack ya supervisa el estado del subsistema de almacenamiento y genera alertas cuando sea necesario. Con la versión 1910 de Azure Stack, el sistema de mantenimiento y supervisión ya puede generar también alertas para los siguientes componentes de hardware:

- Ventiladores del sistema
- Temperatura del sistema
- Fuente de alimentación
- CPU
- Memoria
- Unidades de arranque

> [!NOTE]
> Antes de habilitar esta característica, debe asegurarse con el asociado de hardware que estén listas. El asociado de hardware también proporcionará los pasos detallados para habilitar esta característica en el BMC.

## <a name="snmp-listener-scenario"></a>Escenario del cliente de escucha de SNMP

Se ejecuta un cliente de escucha de SNMP v3 en las tres instancias de ERCS en el puerto TCP 162. El controlador de administración de placa base (BMC) debe estar configurado para enviar capturas de SNMP al cliente de escucha de Azure Stack. Puede obtener las tres direcciones IP de PEP desde el portal de administración abriendo la vista de propiedades de la región.

El envío de capturas al cliente de escucha requiere autenticación y se debe usar la misma credencial que para acceder al propio BMC de base.

Cuando se recibe una captura de SNMP en cualquiera de las tres instancias de ERCS en el puerto TCP 162, se hace coincidir el OID internamente y se genera una alerta. El sistema de mantenimiento y supervisión de Azure Stack solo acepta OID definidos por el asociado de hardware. Si un OID es desconocido en Azure Stack, no lo hará coincidir con ninguna alerta.

Una vez que se reemplaza un componente defectuoso, se envía un evento desde el BMC al cliente de escucha de SNMP que indica el cambio de estado y la alerta se cerrará automáticamente en Azure Stack.

> [!NOTE]
> Las alertas existentes no se cerrarán automáticamente cuando se reemplace todo el nodo o la placa base. Lo mismo se aplica cuando el BMC pierde su configuración; por ejemplo, debido a un restablecimiento de fábrica.

## <a name="next-steps"></a>Pasos siguientes

[Integración del firewall](azure-stack-firewall.md)
