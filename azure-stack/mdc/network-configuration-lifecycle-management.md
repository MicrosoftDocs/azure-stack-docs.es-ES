---
title: Administración del ciclo de vida de configuración de red del Centro de datos modular
description: Obtenga información sobre la administración del ciclo de vida de configuración de red para el centro de datos modular de Azure.
author: PatAltimore
ms.author: patricka
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 5453738374f9377645a6ba594dda08b8668931fe
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872337"
---
# <a name="configuration-lifecycle-management"></a>Administración del ciclo de vida de configuración

En este artículo se proporciona información sobre la administración del ciclo de vida de configuración de red para el centro de datos modular de Azure.

## <a name="update-configuration"></a>actualizar la configuración

Esta metodología para actualizar las configuraciones de conmutadores en el campo se aplica a todos los tipos de actualizaciones de la configuración. Debido a las numerosas variables desconocidas fuera de nuestro control, como las configuraciones manuales que aplican los clientes o el OEM, este proceso manual tiene varios pasos. En este momento, no hay ninguna garantía de tiempo de actividad. Las actualizaciones deben realizarse durante una ventana de mantenimiento.

1. Realice una copia de seguridad de los archivos de configuración actuales para los conmutadores edge1, edge2, tor1, tor2 y BMC. Copie estos archivos fuera del conmutador.

1. Tome nota del número de compilación del kit de herramientas de la configuración existente. Todas las configuraciones deberían tenerlo en el banner **motd**. Busque **BUILDNUMBER**.

1. Vuelva a generar las configuraciones iniciales con la misma versión del kit de herramientas del paso 2.

1. Cargue las configuraciones del paso 3 en sus respectivos conmutadores. El objetivo de esta acción es *lavar* la configuración que generan nuestras herramientas a través de un conmutador para obtener una base de referencia. El OEM puede realizar esta acción en hardware independiente, como en el laboratorio del OEM o in situ con el cliente.

1. Realice una copia de seguridad del archivo de configuración del paso 4. Cópielo en una ubicación remota.

1. Con la herramienta de comparación de su elección, compare la copia de seguridad de la configuración actual del paso 1 con la copia de seguridad de la configuración de la base de referencia del paso 5. Anote o copie todas las diferencias que se deben pasar a la configuración del conmutador actualizada por dispositivo.

1. Ejecute el nuevo kit de herramientas que generará las configuraciones del conmutador actualizadas.

1. Combine las diferencias del paso 6 con las nuevas configuraciones del conmutador.

1. Cargue las nuevas configuraciones en los conmutadores respectivos. Ejecute los comandos posteriores a la validación proporcionados en el directorio de salida desde nuestras herramientas.

1. Guarde las configuraciones.

## <a name="ntp"></a>NTP

Puede usar el punto de conexión con privilegios (PEP) para actualizar el servidor de hora en Azure Stack. Use un nombre de host que se resuelva como dos o más direcciones IP del servidor del Protocolo de tiempo de redes (NTP).

Azure Stack usa NTP para conectarse a los servidores de hora de Internet. Los servidores NTP proporcionan la hora exacta del sistema. La hora se usa en los conmutadores de red física de Azure Stack, el host de ciclo de vida de hardware, el servicio de infraestructura y las máquinas virtuales. Si el reloj no está sincronizado, Azure Stack puede experimentar problemas graves con la red y la autenticación. Los archivos de registro, los documentos y otros archivos se podrían crear con marcas de tiempo incorrectas.

Es necesario proporcionar un servidor horario (NTP) para que Azure Stack pueda sincronizar la hora. Al implementar Azure Stack, se proporciona la dirección de un servidor NTP. La hora es un servicio de infraestructura de los centros de recursos críticos. Si el servicio cambia, tendrá que actualizar la hora.

Azure Stack admite la sincronización del tiempo con un solo servidor horario (NTP). No puede proporcionar varios NTP para Azure Stack con los que sincronizar la hora. Le recomendamos que configure una entrada del Sistema de nombres de dominio (DNS) que se resuelva en varios servidores NTP.

### <a name="update-ntp-post-deployment"></a>Actualización de NTP después de la implementación

1. Conéctese al PEP. No es necesario que abra ninguna incidencia de soporte técnico para desbloquearlo.

1. Ejecute el siguiente comando para revisar el servidor NTP configurado actualmente.

    ```powershell
    Get-AzsTimeSource
    ```

1. Ejecute el siguiente comando para actualizar Azure Stack para que use el nuevo servidor NTP y sincronice la hora inmediatamente.

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

>[!NOTE]
>Este procedimiento no actualiza el servidor de hora en los conmutadores físicos.

## <a name="dns"></a>DNS

En esta sección se explica cómo actualizar los reenviadores DNS para resolver nombres externos.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>Actualización del reenviador DNS en Azure Stack

Para que la infraestructura de Azure Stack resuelva los nombres externos se necesita al menos un reenviador DNS accesible. En la implementación de Azure Stack se debe proporcionar un reenviador DNS. Esta entrada se usa en los servidores DNS internos de Azure Stack como reenviador y permite la resolución de nombres externos para servicios como la autenticación, la administración de marketplace o el uso.

El DNS es un servicio de infraestructura de los centros de datos críticos que puede cambiar. Si lo hace, se debe actualizar Azure Stack.

En este artículo se describe el uso de PEP para actualizar el reenviador DNS en Azure Stack. Le recomendamos que use dos direcciones IP de reenviador de DNS confiables.

1. Conéctese al PEP. No es necesario abrir una incidencia de soporte técnico para desbloquear PEP.

1. Ejecute el siguiente comando para revisar el reenviador DNS configurado actualmente. Como alternativa, también puede usar las propiedades de la región del portal de administración.

    ```powershell
    Get-AzsDnsForwarder 
    ```

1. Ejecute el siguiente comando para actualizar Azure Stack de modo que use el nuevo reenviador de DNS.

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ```

1. Revise la salida del comando para ver si hay errores.
