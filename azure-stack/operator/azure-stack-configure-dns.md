---
title: Actualización del reenviador DNS en Azure Stack Hub
description: Aprenda a actualizar el reenviador DNS en Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 6d5138868b07e2a3d552cd154976352266941760
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704478"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>Actualización del reenviador DNS en Azure Stack Hub

Para que la infraestructura de Azure Stack Hub resuelva los nombres externos se necesita al menos un reenviador DNS accesible. En la implementación de Azure Stack Hub se debe proporcionar un reenviador DNS. Esta entrada se usa en los servidores DNS internos de Azure Stack Hub como reenviador y permite la resolución de nombres externos para servicios como la autenticación, la administración de Marketplace o el uso.

DNS es un servicio esencial en la infraestructura del centro de datos que puede cambiar; si esto sucede, Azure Stack Hub debe actualizarse.

En este artículo se describe el uso del punto de conexión con privilegios (PEP) para actualizar el reenviador DNS en Azure Stack Hub. Se recomienda usar dos direcciones IP de reenviador DNS confiables.

1. Conéctese al [punto de conexión con privilegios](azure-stack-privileged-endpoint.md). Tenga en cuenta que no es necesario desbloquear el punto de conexión con privilegios mediante una incidencia de soporte técnico.

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
