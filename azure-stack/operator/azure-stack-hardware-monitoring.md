---
title: Supervisión del estado del hardware en Azure Stack Hub
description: Aprenda a supervisar el mantenimiento de los componentes de hardware de Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: dff881fe332367b0911c21c0e23745210feb9051
ms.sourcegitcommit: c9737939f4e437f1d954e163db972d58b3f98ffd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2020
ms.locfileid: "84813755"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>Supervisión de componentes de hardware de Azure Stack Hub

El sistema de mantenimiento y supervisión de Azure Stack Hub supervisa el estado del subsistema de almacenamiento y genera alertas cuando es necesario. El sistema de mantenimiento y supervisión puede generar también alertas para los siguientes componentes de hardware:

- Ventiladores del sistema
- Temperatura del sistema
- Fuente de alimentación
- CPU
- Memoria
- Unidades de arranque

> [!NOTE]
> Antes de habilitar esta característica, debe ponerse en contacto con el asociado de hardware para asegurarse de que estén listas. El asociado de hardware también proporcionará los pasos detallados para habilitar esta característica en el controlador de administración de placa base (BMC).

## <a name="snmp-listener-scenario"></a>Escenario del cliente de escucha de SNMP

Se ejecuta un cliente de escucha de SNMP v3 en las tres instancias de ERCS en el puerto TCP 162. El controlador de administración de placa base debe estar configurado para enviar capturas de SNMP al cliente de escucha de Azure Stack Hub. Las tres direcciones IP de PEP se pueden obtener en el portal de administración abriendo la vista de propiedades de la región.

El envío de capturas al cliente de escucha requiere autenticación y se debe usar la misma credencial que para acceder al propio BMC de base.

Cuando se recibe una captura de SNMP en cualquiera de las tres instancias de ERCS en el puerto TCP 162, se hace coincidir el OID internamente y se genera una alerta. El sistema de mantenimiento y supervisión de Azure Stack Hub solo acepta OID definidos por el asociado de hardware. Si alguno de los OID no es conocido en Azure Stack Hub, no aparecerá en ninguna alerta.

Una vez que se reemplaza un componente defectuoso, se envía un evento desde el BMC al cliente de escucha de SNMP que indica el cambio de estado y la alerta se cierra automáticamente en Azure Stack Hub.

> [!NOTE]
> Las alertas existentes no se cierran automáticamente cuando se reemplaza todo el nodo o la placa base. Lo mismo se aplica cuando el BMC pierde su configuración; por ejemplo, debido a un restablecimiento de fábrica.

## <a name="next-steps"></a>Pasos siguientes

[Integración del firewall](azure-stack-firewall.md)
