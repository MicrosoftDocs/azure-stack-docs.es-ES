---
title: Configuración del servidor de hora en Azure Stack Hub
description: Obtenga información acerca de cómo configurar el servidor de hora en Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 2/19/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: e50bb123e7f52c17e5d180d0dd33cc84669882ab
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871419"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>Configuración del servidor de hora para Azure Stack Hub

Puede usar el punto de conexión con privilegios (PEP) para actualizar el servidor de hora en Azure Stack Hub. Use un nombre de host que se resuelva como dos o más direcciones IP del servidor NTP (Protocolo de tiempo de red).

Azure Stack Hub usa el protocolo de tiempo de red (NTP) para conectarse a los servidores de hora de Internet. Los servidores NTP proporcionan la hora exacta del sistema. La hora se usa en los conmutadores de red física de Azure Stack Hub, el host de ciclo de vida de hardware, el servicio de infraestructura y las máquinas virtuales. Si el reloj no está sincronizado, Azure Stack Hub puede experimentar problemas graves con la red y la autenticación. Los archivos de registro, los documentos y otros archivos se podrían crear con marcas de tiempo incorrectas.

Es necesario proporcionar un servidor horario (NTP) para que Azure Stack Hub pueda sincronizar la hora. Al implementar Azure Stack Hub, se proporciona la dirección de un servidor NTP. La hora es un servicio de infraestructura de los centros de recursos críticos. Si el servicio cambia, tendrá que actualizar la hora.

> [!NOTE]
> Azure Stack Hub admite la sincronización del tiempo con un solo servidor horario (NTP). No puede proporcionar varios NTP para Azure Stack Hub con los que sincronizar la hora.

## <a name="configure-time"></a>Configuración de la hora

1. [Conéctese al PEP](azure-stack-privileged-endpoint.md).
    > [!Note]  
    > No es necesario abrir una incidencia de soporte técnico para desbloquear el punto de conexión con privilegios.

2. Ejecute el siguiente comando para revisar el servidor NTP configurado actualmente:

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Ejecute el siguiente comando para actualizar Azure Stack Hub para que use el nuevo servidor NTP y sincronice la hora inmediatamente.

    > [!Note]  
    > Este procedimiento no actualiza el servidor de hora en los conmutadores físicos. Si el servidor de hora no es un servidor NTP basado en Windows, debe agregar la marca `0x8`.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

    Para otros servidores distintos de los servidores de hora basados en Windows:

    ```PowerShell
    Set-AzsTimeSource -TimeServer "NEWTIMESERVERIP,0x8" -resync
    ```

4. Revise la salida del comando para ver si hay errores.


## <a name="next-steps"></a>Pasos siguientes

[Vista del informe de preparación](azure-stack-validation-report.md)  
[Consideraciones generales sobre la integración de Azure Stack Hub](azure-stack-datacenter-integration.md)  
