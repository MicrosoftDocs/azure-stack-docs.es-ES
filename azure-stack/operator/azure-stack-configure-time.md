---
title: Configuración del servidor de hora para Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo configurar el servidor de hora para Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: cc432538715c1c990a9efe6473b33303deb78734
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72280556"
---
# <a name="configure-the-time-server-for-azure-stack"></a>Configuración del servidor de hora para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*  

Puede usar el punto de conexión con privilegios (PEP) para actualizar el servidor de hora en Azure Stack. Use un nombre de host que se resuelva como dos o más direcciones IP del servidor NTP.

Azure Stack usa el protocolo de tiempo de red (NTP) para conectarse a los servidores de hora de Internet. Los servidores NTP proporcionan la hora exacta del sistema. La hora se usa en los conmutadores de red física de Azure Stack, el host de ciclo de vida de hardware, el servicio de infraestructura y las máquinas virtuales. Si el reloj no está sincronizado, Azure Stack puede experimentar problemas graves con la red y la autenticación. Los archivos de registro, los documentos y otros archivos se podrían crear con marcas de tiempo incorrectas.

Se necesita al menos un servidor de hora (NTP) para que Azure Stack pueda sincronizar la hora. Al implementar Azure Stack, se proporciona la dirección de un servidor NTP. La hora es un servicio de infraestructura de los centros de recursos críticos. Si el servicio cambia, tendrá que actualizar la hora.

## <a name="configure-time"></a>Configuración de la hora

1. [Conéctese al PEP](azure-stack-privileged-endpoint.md). 
    > [!Note]  
    > No es necesario abrir una incidencia de soporte técnico para desbloquear el punto de conexión con privilegios.

2. Ejecute el siguiente comando para revisar el servidor NTP configurado actualmente:

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Ejecute el siguiente comando para actualizar Azure Stack para que use el nuevo servidor NTP y sincronice la hora inmediatamente.

    > [!Note]  
    > Este procedimiento no actualiza el servidor de hora en los conmutadores físicos.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Revise la salida del comando para ver si hay errores.


## <a name="next-steps"></a>Pasos siguientes

[Vista del informe de preparación](azure-stack-validation-report.md)  
[Consideraciones generales sobre la integración de Azure Stack](azure-stack-datacenter-integration.md)  
