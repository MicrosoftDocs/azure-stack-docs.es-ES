---
title: Actualización del reenviador DNS en Azure Stack | Microsoft Docs
description: Aprenda a actualizar el reenviador DNS en Azure Stack.
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
ms.openlocfilehash: 22e49f28dee6b4aa97b9e84cf52950dd678450e4
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308246"
---
# <a name="update-the-dns-forwarder-in-azure-stack"></a>Actualización del reenviador DNS en Azure Stack

Para que la infraestructura de Azure Stack resuelva los nombres externos se necesita al menos un reenviador DNS accesible. En la implementación de Azure Stack se debe proporcionar un reenviador DNS. Esta entrada se usa en los servidores DNS internos de Azure Stack como reenviador y permite la resolución de nombres externos para servicios como la autenticación, la administración de Marketplace o el uso.

DNS es un servicio esencial en la infraestructura del centro de datos que puede cambiar; si esto sucede, Azure Stack debe actualizarse.

En este artículo se describe el uso del punto de conexión con privilegios (PEP) para actualizar el reenviador DNS en Azure Stack. Se recomienda usar dos direcciones IP de reenviador DNS confiables.

1. Conéctese al [punto de conexión con privilegios](azure-stack-privileged-endpoint.md). Tenga en cuenta que no es necesario desbloquear el punto de conexión con privilegios mediante una incidencia de soporte técnico.

2. Ejecute el siguiente comando para revisar el reenviador DNS configurado actualmente. Como alternativa, también puede usar las propiedades de la región del portal de administración:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Ejecute el siguiente comando para actualizar Azure Stack de modo que use el nuevo reenviador DNS:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Revise la salida del comando para ver si hay errores.

## <a name="next-steps"></a>Pasos siguientes

[Integración del firewall](azure-stack-firewall.md)
