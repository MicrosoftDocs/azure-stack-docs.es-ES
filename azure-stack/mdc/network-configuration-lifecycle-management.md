---
title: Administración del ciclo de vida de configuración de red del Centro de datos modular
description: Obtenga información sobre la administración del ciclo de vida de configuración de red para el dispositivo MDC.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 78e4a98a606b100e42d4beb465874d4d0648c971
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183453"
---
# <a name="configuration-lifecycle-management"></a>Administración del ciclo de vida de configuración

En este tema se trata la administración del ciclo de vida de la configuración de red.

## <a name="update-configuration"></a>actualizar la configuración


Metodología para actualizar configuraciones de conmutadores sobre el terreno. Esto se aplica a todos y cada uno de los tipos de actualización de la configuración. Debido a las numerosas variables desconocidas fuera de nuestro control, como las configuraciones manuales que aplican los clientes o el OEM, este es un proceso manual de varios pasos. En este momento no hay ninguna garantía de tiempo de actividad y las actualizaciones deben realizarse durante una ventana de mantenimiento.

**\#1 -** Realice una copia de seguridad de los archivos de configuración actuales para los conmutadores perimetral 1, perimetral 2, tor 1, tor 2 y BMC. Copie estos archivos fuera del conmutador.

**\#2 -** Tome nota del número de compilación del kit de herramientas de la configuración existente.
Todas las configuraciones deberían tenerlo en el banner **motd** . Busque *"BUILDNUMBER"* .

**\#3 -** Vuelva a generar las configuraciones iniciales con la misma versión del kit de herramientas del paso \#2.

**\#4 -** Cargue las configuraciones del paso \#3 en sus respectivos conmutadores.
El objetivo de esta acción es *lavar* la configuración generada por nuestras herramientas a través de un conmutador para obtener una base de referencia. El OEM puede realizar esta acción en hardware independiente, como en el laboratorio del OEM o in situ con el cliente.

**\#5 -** Realice una copia de seguridad del archivo de configuración del paso \#4 y cópielo en una ubicación remota.

**\#6 -** Con la herramienta de comparación de su elección, compare la copia de seguridad de la configuración actual del paso \#1 con la copia de seguridad de la configuración de la base de referencia del paso \#5. Anote o copie todas las diferencias que se deben pasar a la configuración del conmutador actualizada por dispositivo.

**\#7 -** Ejecute el nuevo kit de herramientas que generará las configuraciones de conmutador actualizadas.

**\#8 -** Combine las diferencias del paso 6 con las nuevas configuraciones de conmutador.

**\#9 -** Cargue las nuevas configuraciones en los conmutadores respectivos y ejecute los comandos posteriores a la validación proporcionados en el directorio de salida de nuestras herramientas.

**\#10 -** Guarde las configuraciones.

## <a name="ntp"></a>NTP

Puede usar el punto de conexión con privilegios (PEP) para actualizar el servidor de hora en Azure Stack. Use un nombre de host que se resuelva como dos o más direcciones IP del servidor NTP.

Azure Stack usa el protocolo de tiempo de red (NTP) para conectarse a los servidores de hora de Internet. Los servidores NTP proporcionan la hora exacta del sistema. La hora se usa en los conmutadores de red física de Azure Stack, el host de ciclo de vida de hardware, el servicio de infraestructura y las máquinas virtuales. Si el reloj no está sincronizado, Azure Stack puede experimentar problemas graves con la red y la autenticación. Los archivos de registro, los documentos y otros archivos se podrían crear con marcas de tiempo incorrectas.

Es necesario proporcionar un servidor horario (NTP) para que Azure Stack pueda sincronizar la hora.
Al implementar Azure Stack, se proporciona la dirección de un servidor NTP. La hora es un servicio de infraestructura de los centros de recursos críticos. Si el servicio cambia, tendrá que actualizar la hora.

Azure Stack admite la sincronización del tiempo con un solo servidor horario (NTP). No puede proporcionar varios NTP para Azure Stack con los que sincronizar la hora. Se recomienda configurar la entrada DNS que se resuelve en varios servidores NTP. 


### <a name="update-ntp-post-deployment"></a>Actualización de NTP después de la implementación

1.  Conéctese al punto de conexión con privilegios (PEP). No es necesario abrir una incidencia de soporte técnico para desbloquear el punto de conexión con privilegios. 

2.  Ejecute el siguiente comando para revisar el servidor NTP configurado actualmente:

    ```powershell
    Get-AzsTimeSource
    ```

3.  Ejecute el siguiente comando para actualizar Azure Stack para que use el nuevo servidor NTP y sincronice la hora inmediatamente.

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

    >[!NOTE] 
    >Este procedimiento no actualiza el servidor de hora en los conmutadores físicos. 


## <a name="dns"></a>DNS

En esta sección se explica cómo actualizar los reenviadores DNS para resolver nombres externos.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>Actualización del reenviador DNS en Azure Stack

Para que la infraestructura de Azure Stack resuelva los nombres externos se necesita al menos un reenviador DNS accesible. En la implementación de Azure Stack se debe proporcionar un reenviador DNS. Esta entrada se usa en los servidores DNS internos de Azure Stack como reenviador y permite la resolución de nombres externos para servicios como la autenticación, la administración de Marketplace o el uso.

DNS es un servicio esencial en la infraestructura del centro de datos que puede cambiar; si esto sucede, Azure Stack debe actualizarse.

En este artículo se describe el uso del punto de conexión con privilegios (PEP) para actualizar el reenviador DNS en Azure Stack. Se recomienda usar dos direcciones IP de reenviador DNS confiables.

1.  Conéctese al punto de conexión con privilegios. Tenga en cuenta que no es necesario desbloquear el punto de conexión con privilegios mediante una incidencia de soporte técnico.

2.  Ejecute el siguiente comando para revisar el reenviador DNS configurado actualmente. Como alternativa, también puede usar las propiedades de la región del portal de administración:

    ```powershell
    Get-AzsDnsForwarder 
    ```

3.  Ejecute el siguiente comando para actualizar Azure Stack de modo que use el nuevo reenviador DNS:

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ``` |

4.  Review the output of the command for any errors.
