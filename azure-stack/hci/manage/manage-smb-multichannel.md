---
title: Administración de SMB multicanal
description: Use este tema para usar SMB multicanal para aumentar el rendimiento y la tolerancia a errores de la red.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/30/2020
ms.openlocfilehash: b7dc0c8050bc9d813115d2c3ebd7716b87bd89fa
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585974"
---
# <a name="manage-smb-multichannel"></a>Administración de SMB multicanal

> Se aplica a: Azure Stack HCI, versión 20H2, Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows 10

SMB multicanal forma parte del protocolo Bloque de mensajes del servidor (SMB) 3.0, que permite aumentar el rendimiento de la red y la disponibilidad de los servidores de archivos.

SMB multicanal permite que los servidores de archivos usen varias conexiones de red simultáneamente. Permite agregar con facilidad ancho de banda y tolerancia a errores de la red cuando hay varias rutas disponibles entre el cliente de SMB 3.0 y el servidor de SMB 3.0. Esto permite que las aplicaciones de servidor aprovechen todo el ancho de banda disponible y las hace más resistentes a los errores de la red.

SMB multicanal proporciona las funcionalidades siguientes:

- **Aumento de rendimiento.** El servidor de archivos puede transmitir simultáneamente datos adicionales mediante el uso de varias conexiones para adaptadores de red de alta velocidad o adaptadores de red múltiples.

- **Tolerancia a errores de la red.** Si los clientes usan simultáneamente varias conexiones de red, pueden seguir funcionando incluso si se produce la pérdida de una conexión de red.

- **Configuración automática.** SMB multicanal detecta automáticamente varias rutas de acceso de red disponibles y agrega dinámicamente las conexiones según sea necesario.

## <a name="requirements-for-smb-multichannel"></a>Requisitos de SMB multicanal

Como SMB multicanal está habilitado de forma predeterminada, no es necesario instalar roles, servicios de rol o características adicionales. El cliente de SMB detecta y utiliza automáticamente varias conexiones de red cuando se identifica la configuración. No obstante, SMB multicanal tiene los siguientes requisitos:

- Al menos dos equipos que ejecuten el sistema operativo Azure Stack HCI, Windows Server o Windows 10.

- Al menos una de las siguientes configuraciones:

    - Adaptadores de red múltiples

    - Uno o más adaptadores de red que admitan el ajuste de escala en lado de recepción (RSS)

    - Varios adaptadores de red agrupados en equipo (consulte [Formación de equipos NIC](#nic-teaming))

    - Uno o más adaptadores de red que admitan el acceso directo a memoria remota (RDMA)

## <a name="configure-smb-multichannel"></a>Configuración de SMB multicanal

En esta sección se describen algunas opciones de configuración para implementar SMB multicanal mediante una matriz de adaptadores de red. Estas son solo configuraciones de ejemplo. Hay muchas otras configuraciones posibles que no se incluyen en este tema.

### <a name="single-rss-capable-network-adapter"></a>Adaptador de red compatible con RSS

En esta configuración típica, un cliente de SMB y un servidor de SMB se configuran mediante un único adaptador de red Ethernet de 10 Gigabits (10 GbE). Si SMB se implementa sin SMB multicanal y solo hay una sesión de SMB establecida, SMB creará una única conexión TCP/IP. Con un solo núcleo de CPU, esta configuración conduce intrínsecamente a la congestión, especialmente cuando se realizan muchas operaciones de E/S pequeñas. Por lo tanto, la posibilidad de que se produzca un cuello de botella de rendimiento es significativa.

La mayoría de los adaptadores de red actuales ofrecen una funcionalidad denominada ajuste de escala en lado de recepción (RSS), que permite que varias conexiones se repartan automáticamente entre varios núcleos de CPU. Sin embargo, si usa una sola conexión, RSS no podrá ayudarle. Cuando se usa SMB multicanal con un adaptador de red compatible con RSS, SMB crea varias conexiones TCP/IP para esa sesión en particular. Esta configuración evita un posible cuello de botella en un solo núcleo de CPU si se requieren muchas operaciones de E/S pequeñas.

### <a name="multiple-network-adapters"></a>Adaptadores de red múltiples

En esta configuración, un cliente de SMB y un servidor de SMB se configuran mediante varios adaptadores de red de 10 GbE. Si SMB se implementa sin SMB multicanal y solo hay una sesión de SMB establecida, SMB usará uno de los adaptadores de red disponibles para crear una única conexión TCP/IP. En este escenario, no es posible agregar el ancho de banda de los diferentes adaptadores de red; por ejemplo, no podrá conseguir 2 gigabits por segundo (Gbps) si utiliza 2 adaptadores de red de 1-GbE. También se puede producir un error si el adaptador de red seleccionado está desconectado o deshabilitado.

Si SMB se implementa con SMB multicanal, SMB creará varias conexiones TCP/IP para una única sesión con al menos una o más conexiones por interfaz si los adaptadores de red son compatibles con RSS. Esta configuración permite a SMB usar el ancho de banda del adaptador de red combinado que está disponible y permite que el cliente de SMB continúe sin interrupción si se produce un error en un adaptador de red.

### <a name="nic-teaming"></a>Formación de equipos NIC

Azure Stack HCI y Windows Server admiten la posibilidad de combinar varios adaptadores en un solo adaptador de red mediante una característica denominada formación de equipos NIC. Aunque un equipo siempre proporciona tolerancia a errores, cuando SMB se implementa sin SMB multicanal, SMB solo crea una conexión TCP/IP por equipo. Esta configuración conduce a limitaciones en el número de núcleos de CPU que se usan y en el uso máximo del ancho de banda del equipo.

Cuando SMB se implementa con SMB multicanal, SMB crea varias conexiones TCP/IP para una única sesión con el fin de lograr un mejor equilibrio entre los núcleos de CPU y un mejor uso del ancho de banda disponible. La formación de equipos NIC sigue ofreciendo la funcionalidad de conmutación por error, la cual funciona más rápido que el uso de SMB multicanal por sí mismo. La formación de equipos NIC también es recomendable porque ofrece funcionalidades de conmutación por error a otras cargas de trabajo que no se basan en SMB, ya que esas cargas de trabajo no se pueden beneficiar de las funcionalidades de conmutación por error de SMB multicanal.

Si usa un conjunto dedicado de adaptadores de red para el tráfico de Espacios de almacenamiento directo, como se hace a veces con Azure Stack HCI, la formación de equipos de estos adaptadores de red de almacenamiento es completamente opcional, ya que no proporciona beneficios ni desventajas significativos.

>[!IMPORTANT]
>En Windows Server 2012 R2 y versiones anteriores, no use la formación de equipos NIC si tiene previsto utilizar las funcionalidades de RDMA de los adaptadores de red. En estos sistemas operativos, el equipo de adaptadores de red compatibles con RDMA siempre aparecerá como no compatible con RDMA, ya que la formación de equipos deshabilita la funcionalidad RDMA del adaptador de red.

### <a name="single-or-multiple-rdma-capable-network-adapters"></a>Un adaptador de red compatible con RDMA o varios

SMB multicanal detecta las funcionalidades de RDMA de los adaptadores de red, lo cual permite la característica SMB directo, también llamada SMB directo a través de RDMA. Sin SMB multicanal, SMB usa conexiones TCP/IP normales con los adaptadores de red compatibles con RDMA, donde todos los adaptadores de red proporcionan una pila de TCP/IP que existe en paralelo con la nueva pila de RDMA.

Cuando SMB se implementa con SMB multicanal, SMB detecta la funcionalidad de RDMA de un adaptador de red y crea varias conexiones RDMA para esa sesión única, con dos conexiones de RDMA por interfaz. Esta configuración permite a SMB aprovechar el alto rendimiento, la baja latencia y el bajo uso de la CPU que ofrecen los adaptadores de red compatibles con RDMA. También ofrece tolerancia a errores cuando se usan varias interfaces de RDMA.

>[!IMPORTANT]
>Después de crear una conexión de RDMA, dejará de utilizarse la conexión TCP/IP para la negociación del protocolo original. Sin embargo, esa conexión se mantendrá en caso de que se produzca un error en una conexión de RDMA diferente.

### <a name="smb-multichannel-rdma-capable-network-adapters-and-nic-teaming-compatibility"></a>SMB multicanal, adaptadores de red compatibles con RDMA y compatibilidad con la formación de equipos NIC

En la tabla siguiente se resumen las distintas funcionalidades que están disponibles al combinar SMB multicanal, RDMA (SMB directo) y formación de equipos NIC.

| Configuración                                           | Throughput | Tolerancia a errores para SMB | Tolerancia a errores para protocolos distintos a SMB | Bajo uso de la CPU |
|:-------------------------------------------------------:|:----------:|:-----------------------:|:---------------------------:|:---------------------:|
| Un solo adaptador de red (sin RSS)                         | *          |                         |                             |                       |
| Varios adaptadores de red (sin RSS)                      | **         | *                       |                             |                       |
| Varios adaptadores de red (sin RSS) con formación de equipos NIC     | **         | **                      | *                           |                       |
| Un solo adaptador de red con RSS                         | *          |                         |                             |                       |
| Varios adaptadores de red con RSS                      | **         | *                       |                             |                       |
| Varios adaptadores de red con RSS y formación de equipos NIC      | **         | **                      | *                           |                       |
| Un solo adaptador de red compatible con RDMA                     | *          |                         |                             | *                     |
| Varios adaptadores de red compatibles con RDMA                  | ***        | *                       |                             | *                     |
| Varios adaptadores de red compatibles con RDMA con formación de equipos NIC | ***        | **                      | *                           | *                     |

Si está ejecutando Windows Server 2016 o una versión posterior, la solución ideal es usar varios adaptadores de red compatibles con RDMA y combinar la formación de equipos NIC con SMB multicanal. Esta combinación proporciona el mejor rendimiento, ofrece tolerancia a errores para las aplicaciones que usan SMB y otros protocolos, y tiene el menor impacto en la CPU.

Como se mencionó anteriormente, al usar adaptadores de red compatibles con RDMA en Windows Server 2012 R2 o versiones anteriores, la formación de equipos NIC no es una buena opción, ya que deshabilita la funcionalidad de RDMA del adaptador de red.

### <a name="example-configurations-without-smb-multichannel"></a>Configuraciones de ejemplo sin SMB multicanal

Si tiene pensado usar un solo adaptador de red sin RSS, no se beneficiará de las conexiones de red múltiples y, por lo tanto, no se usará SMB multicanal. Además, si tiene previsto usar adaptadores de red de distintas velocidades, SMB multicanal seleccionará automáticamente el más rápido. Esto se debe a que los adaptadores de red que son del mismo tipo (como RDMA, RSS u otro) y que tienen la misma velocidad se usan simultáneamente en SMB multicanal. Los adaptadores de red más lentos están inactivos.

## <a name="disable-smb-multichannel"></a>Deshabilitar SMB multicanal
Normalmente, no es necesario deshabilitar SMB multicanal. Sin embargo, si desea deshabilitar SMB multicanal, por ejemplo en un entorno de prueba, use los siguientes procedimientos de Windows PowerShell.

En primer lugar, conéctese a uno de los servidores; para ello, abra una sesión de PowerShell:

```PowerShell
Enter-PSSession <server-name>
```

Para deshabilitar SMB multicanal en el lado del servidor, use el siguiente cmdlet:

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $false
```

Para deshabilitar SMB multicanal en el lado del cliente, use el siguiente cmdlet:

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $false
```

>[!NOTE]
>Deshabilitar SMB multicanal en el cliente o en el servidor impide que ambos sistemas lo usen.

## <a name="re-enable-smb-multichannel"></a>Volver a habilitar SMB multicanal

Si ha deshabilitado SMB multicanal y desea volver a habilitarlo, use los procedimientos siguientes.

Para volver a habilitar SMB multicanal en el lado del servidor, use el siguiente cmdlet:

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $true
```

Para volver a habilitar SMB multicanal en el lado del servidor, use el siguiente cmdlet:

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $true
```

>[!NOTE]
>Debe volver a habilitar SMB multicanal en el cliente y en el servidor para empezar a utilizarlo de nuevo.

## <a name="test-smb-multichannel"></a>Probar SMB multicanal

En esta sección se describen algunos escenarios para probar SMB multicanal entre los que se incluyen la comparación entre una copia de archivos con SMB multicanal y, posteriormente, sin SMB multicanal, y provocar, de forma intencionada, un error en un adaptador de red durante una operación de copia de archivos.

### <a name="compare-a-file-copy-with-and-without-smb-multichannel"></a>Comparación entre una copia de archivos con y sin SMB multicanal

Para medir la mejora del rendimiento que proporciona SMB multicanal, realice las siguientes pruebas. Reinicie el servidor y el cliente antes de cada prueba para asegurarse de que funcionan en condiciones similares.

1. Configure SMB multicanal mediante una de las configuraciones descritas anteriormente.

1. Mida la cantidad de tiempo que se tarda en realizar una operación de copia de archivos de ejecución prolongada mediante SMB multicanal.

1. Deshabilite SMB multicanal. Para obtener instrucciones, consulte [Deshabilitar SMB multicanal](#disable-smb-multichannel).

1. Mida la cantidad de tiempo que se tarda en realizar la misma operación de copia de archivos sin SMB multicanal.

1. Volver a habilitar SMB multicanal. Para obtener instrucciones, consulte [Volver a habilitar SMB multicanal](#re-enable-smb-multichannel).

1. Compare los dos resultados.

>[!IMPORTANT]
>Para evitar el impacto en el rendimiento del almacenamiento en caché, copie primero una gran cantidad de datos que supere el tamaño de la memoria disponible. A continuación, vuelva a realizar la operación de copia, con la primera operación de copia como preparación. Mida el tiempo solo de la segunda operación de copia.

### <a name="cause-one-of-the-network-adapters-to-fail-during-a-file-copy-with-smb-multichannel"></a>Provoque un error en uno de los adaptadores de red durante una copia de archivos con SMB multicanal

Para confirmar la funcionalidad de conmutación por error de SMB multicanal:

1. Asegúrese de que SMB multicanal funciona en una configuración de varios adaptadores de red.

1. Realice una operación de copia de archivos de ejecución prolongada.

1. Mientras se ejecuta la operación de copia de archivos, simule un error de una de las rutas de acceso de red mediante la desconexión de uno de los cables o deshabilitando uno de los adaptadores de red.

1. Confirme que la copia de archivos sigue usando el adaptador de red restante y que funciona sin errores.

Asegúrese de que no haya otras cargas de trabajo que usen la ruta de acceso de la conexión de red desconectada. Esta precaución evita un posible error en las cargas de trabajo que no usan SMB multicanal.

## <a name="verify-that-smb-multichannel-works"></a>Comprobación de que SMB multicanal funciona

Use el siguiente procedimiento para comprobar que SMB multicanal funciona.

1. Para comprobar que la configuración del adaptador de red es correcta, escriba lo siguiente en Windows PowerShell en el servidor y en el cliente de SMB.

   ```PowerShell
   Get-NetAdapter
   Get-NetAdapterRSS
   Get-NetAdapterRDMA
   Get-NetAdapterHardwareInfo
   ```

2. Para comprobar que SMB multicanal está habilitado, confirme que SMB identifica correctamente los adaptadores de red y que las funcionalidades de RSS y RDMA del adaptador de red se han identificado correctamente, y haga lo siguiente:

   En el cliente de SMB, escriba lo siguiente en Windows PowerShell:

   ```PowerShell
   Get-SmbClientConfiguration | Select EnableMultichannel
   Get-SmbClientNetworkInterface
   ```

   En el servidor de SMB, escriba lo siguiente en Windows PowerShell:

   ```PowerShell
   Get-SmbServerConfiguration | Select EnableMultichannel
   Get-SmbServerNetworkInterface
   ```

3. En el cliente de SMB, realice una operación de copia de archivos de ejecución prolongada que cree una sesión en curso con el servidor de SMB. Mientras se está ejecutando la operación de copia, escriba lo siguiente en Windows PowerShell para comprobar que la conexión usa la versión correcta de SMB y que SMB multicanal funciona.

   ```PowerShell
   Get-SmbConnection
   Get-SmbMultichannelConnection
   Get-SmbMultichannelConnection -IncludeNotSelected
   ```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Información general sobre el uso compartido de archivos mediante el protocolo SMB 3 en Windows Server](/windows-server/storage/file-server/file-server-smb-overview)
- [SMB multicanal simplificado y redes de clústeres con varias NIC](/windows-server/failover-clustering/smb-multichannel)
- [SMB directo](/windows-server/storage/file-server/smb-direct)
