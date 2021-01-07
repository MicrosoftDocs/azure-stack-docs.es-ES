---
title: Actualización del reenviador DNS en Azure Stack Hub
description: Aprenda a actualizar el reenviador DNS en Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 09c827d3b00470dd8e241181fc243bace62fc38b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871181"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>Actualización del reenviador DNS en Azure Stack Hub

Para que la infraestructura de Azure Stack Hub resuelva los nombres externos se necesita al menos un reenviador DNS accesible. En la implementación de Azure Stack Hub se debe proporcionar un reenviador DNS. Esta entrada se usa en los servidores de nombres de dominio internos de Azure Stack Hub como reenviador y permite la resolución de nombres externos para servicios como la autenticación, la administración de Marketplace o el uso.

El DNS es un servicio de infraestructura de los centros de datos críticos que puede cambiar. Si lo hace, se debe actualizar Azure Stack Hub.

En este artículo se describe el uso del punto de conexión con privilegios (PEP) para actualizar el reenviador DNS en Azure Stack Hub. Se recomienda usar dos direcciones IP de reenviador de DNS confiables.

## <a name="steps-to-update-the-dns-forwarder"></a>Pasos para actualizar el reenviador de DNS

1. Conéctese al [punto de conexión con privilegios](azure-stack-privileged-endpoint.md). No es necesario desbloquear el punto de conexión con privilegios mediante la apertura de una incidencia de soporte técnico.

2. Ejecute el siguiente comando para revisar el reenviador DNS configurado actualmente. Como alternativa, también puede usar las propiedades de la región del portal de administración:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Ejecute el siguiente comando para actualizar Azure Stack Hub de modo que use el nuevo reenviador DNS:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Revise la salida del comando para ver si hay errores.

## <a name="next-steps"></a>Pasos siguientes

[Integración del firewall](azure-stack-firewall.md)
