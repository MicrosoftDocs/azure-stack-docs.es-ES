---
title: Configuración de los controles de seguridad de Azure Stack Hub
description: Aprenda a configurar los controles de seguridad de Azure Stack Hub
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: 4cfc5ee7f9e2284b3f7f5c632980235b24d2e956
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "78367512"
---
# <a name="configure-azure-stack-hub-security-controls"></a>Configuración de los controles de seguridad de Azure Stack Hub

En este artículo se explican los controles de seguridad que se pueden cambiar en Azure Stack Hub y se resaltan los inconvenientes cuando procede.

La arquitectura de Azure Stack Hub se basa en dos pilares de principios de seguridad: supuesto de infracción y brecha y protección predeterminada. Para más información sobre la seguridad de Azure Stack Hub, consulte la [posición de seguridad de la infraestructura de Azure Stack Hub](azure-stack-security-foundations.md). Aunque la posición de seguridad predeterminada de Azure Stack Hub está preparada para producción, hay algunos escenarios de implementación que requieren protección adicional.

## <a name="tls-version-policy"></a>Directiva de versión TLS

El protocolo de Seguridad de la capa de transporte (TLS) es un protocolo criptográfico ampliamente adoptado para establecer una comunicación cifrada a través de la red. TLS ha evolucionado con el tiempo y se han publicado varias versiones. La infraestructura de Azure Stack Hub usa TLS 1.2 exclusivamente para todas sus comunicaciones. En el caso de interfaces externas, Azure Stack Hub usa actualmente TLS 1.2 de forma predeterminada. Sin embargo, para la compatibilidad con versiones anteriores, también admite hasta las versiones TLS 1.1 y 1.0. Cuando un cliente de TLS solicita comunicarse a través de TLS 1.1 o TLS 1.0, Azure Stack Hub respeta la solicitud y admite una versión anterior de TLS. Si el cliente solicita TLS 1.2, Azure Stack Hub establecerá una conexión TLS mediante TLS 1.2.

Dado que las versiones TLS 1.0 y 1.1 están cada vez más en desuso o prohibidas por las organizaciones y las normas de cumplimiento, a partir de la actualización 1906 puede configurar la directiva TLS en Azure Stack Hub. Puede aplicar una directiva TLS 1.2 exclusivamente donde cualquier intento de establecer una sesión TLS con una versión anterior a 1.2 no se permita y se rechace.

> [!IMPORTANT]
> Microsoft recomienda usar la directiva TLS 1.2 exclusivamente para entornos de producción de Azure Stack Hub.

## <a name="get-tls-policy"></a>Obtener la directiva TLS

Use el [punto de conexión con privilegios (PEP)](azure-stack-privileged-endpoint.md) para ver la directiva TLS de todos los puntos de conexión de Azure Stack Hub:

```powershell
Get-TLSPolicy
```

Salida de ejemplo:

    TLS_1.2

## <a name="set-tls-policy"></a>Establecer la directiva TLS

Use el [punto de conexión con privilegios (PEP)](azure-stack-privileged-endpoint.md) para establecer la directiva TLS de todos los puntos de conexión de Azure Stack Hub:

```powershell
Set-TLSPolicy -Version <String>
```

Parámetros del cmdlet *Set-TLSPolicy*:

| Parámetro | Descripción | Tipo | Obligatorio |
|---------|---------|---------|---------|
| *Versión* | Versiones de TLS permitidas en Azure Stack Hub | String | sí|

Para configurar las versiones de TLS permitidas para todos los puntos de conexión de Azure Stack Hub, use uno de los valores siguientes:

| Valor de la versión | Descripción |
|---------|---------|
| *TLS_All* | Los puntos de conexión de TLS de Azure Stack Hub admiten TLS 1.2, pero se admiten las versiones anteriores TLS 1.1 y TLS 1.0. |
| *TLS_1.2* | Los puntos de conexión de TLS de Azure Stack Hub solo admiten TLS 1.2. | 

La actualización de la directiva TLS tarda unos minutos en completarse.

### <a name="enforce-tls-12-configuration-example"></a>Ejemplo de configuración para aplicar TLS 1.2

En este ejemplo se establece la directiva TLS para aplicar solo TLS 1.2.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Salida de ejemplo:

    VERBOSE: Successfully setting enforce TLS 1.2 to True
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is enforced

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Ejemplo de configuración para permitir todas las versiones de TLS (1.2, 1.1 y 1.0)

En este ejemplo se establece la directiva TLS para permitir todas las versiones de TLS (1.2, 1.1 y 1.0).

```powershell
Set-TLSPolicy -Version TLS_All
```

Salida de ejemplo:

    VERBOSE: Successfully setting enforce TLS 1.2 to False
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is not enforced

## <a name="next-steps"></a>Pasos siguientes

- [Aprenda sobre la posición de seguridad de la infraestructura de Azure Stack Hub](azure-stack-security-foundations.md)
- [Aprenda a cambiar los secretos en Azure Stack Hub](azure-stack-rotate-secrets.md)
- [Actualización de Antivirus de Windows Defender en Azure Stack Hub](azure-stack-security-av.md)
