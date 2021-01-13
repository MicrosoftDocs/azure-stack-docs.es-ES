---
title: Solución de problemas de informes de validación de clústeres
description: Solucionar problemas de generación de informes de validación de clústeres y validar la configuración de QoS para clústeres de Azure Stack HCI
author: khdownie
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.author: v-kedow
ms.reviewer: JasonGerend
ms.openlocfilehash: a5b6ef03701daf1c1f4115f88a2a4e44bac1bd61
ms.sourcegitcommit: 0e2c814cf2c154ea530a4e51d71aaf0835fb2b5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918632"
---
# <a name="troubleshoot-cluster-validation-reporting"></a>Solución de problemas de informes de validación de clústeres

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Este tema le ayuda a solucionar los problemas de los informes de validación de clústeres para la configuración de QoS (calidad del servicio) de red y almacenamiento en los servidores de un clúster de Azure Stack HCl y comprueba que se han definido reglas importantes. Para lograr una conectividad y un rendimiento óptimos, el proceso de validación de clústeres comprueba que la configuración de QoS del Protocolo de puente del centro de datos (DCB) es coherente y, si está definida, contiene las reglas adecuadas para los clústeres de conmutación por error y las clases de tráfico SMB/SMB directo.

DCB es necesario para redes RDMA sobre Ethernet convergente (RoCE) y es opcional (pero se recomienda) para redes del protocolo RDMA de área extensa de Internet (iWARP).

## <a name="install-data-center-bridging"></a>Instalar el protocolo de puente del centro de datos

El protocolo de puente del centro de datos debe estar instalado para usar cmdlets específicos de QoS. Para comprobar si la característica de protocolo de puente del centro de datos ya está instalada en un servidor, ejecute el siguiente cmdlet en PowerShell:

```PowerShell
Get-WindowsFeature -Name Data-Center-Bridging -ComputerName Server1
```

Si el protocolo de puente del centro de datos no está instalado, instálelo. Para hacerlo, ejecute el siguiente cmdlet en cada servidor del clúster:

```PowerShell
Install-WindowsFeature –Name Data-Center-Bridging -ComputerName Server1
```

## <a name="run-a-cluster-validation-test"></a>Ejecución de una prueba de validación de clúster

Para usar la característica Validar de Windows Admin Center, seleccione **Herramientas > Servidores > Inventario > Validar clúster** o ejecute el siguiente comando de PowerShell:

```PowerShell
Test-Cluster –Node Server1, Server2
```

Entre otras cosas, la prueba validará que la configuración de QoS de DCB es coherente y que todos los servidores del clúster tienen el mismo número de clases de tráfico y reglas de QoS. También comprobará que todos los servidores tienen reglas de QoS definidas para los clústeres de conmutación por error y las clases de tráfico SMB/SMB directo.

Puede ver el informe de validación en Windows Admin Center o mediante el acceso a un archivo de registro en el directorio de trabajo actual. Por ejemplo: C:\Users\<username>\AppData\Local\Temp\

Cerca de la parte inferior del informe, verá una opción para validar la configuración de QoS y un informe correspondiente para cada servidor del clúster.

Para saber qué clases de tráfico ya están establecidas en un servidor, use el cmdlet `Get-NetQosTrafficClass`.

Consulte [Validación de un clúster de Azure Stack HCI](../deploy/validate.md) para obtener más información.

## <a name="validate-networking-qos-rules"></a>Validación de las reglas de QoS

Valide la coherencia del estado de aceptación de DCB y de la configuración de estado de control de flujo de prioridad entre los servidores del clúster.

### <a name="dcb-willing-status"></a>Estado de aceptación de DCB

Los adaptadores de red que admiten el protocolo de intercambio de funcionalidad del de puente del centro de datos (DCBX) pueden aceptar configuraciones de un dispositivo remoto. Para habilitar esta funcionalidad, el bit de aceptación del adaptador de red debe definirse como true. Si el bit de aceptación se define como false, el dispositivo rechazará todos los intentos de configuración de los dispositivos remotos y aplicará solo las configuraciones locales. Si usa adaptadores de RDMA sobre Ethernet convergente (RoCE), el bit de aceptación se debe definir como false en todos los servidores.

Todos los servidores de un clúster de Azure Stack HCl deben tener el bit de aceptación definido de la misma manera.

Use el cmdlet `Set-NetQosDcbxSetting` para definir el bit de aceptación de DCB en true o false, como en el ejemplo siguiente:

```PowerShell
Set-NetQosDcbxSetting –Willing $false
```

### <a name="dcb-flow-control-status"></a>Estado de control del flujo de DCB

El control de flujo basado en la prioridad es fundamental si el protocolo de la capa superior, como el canal de fibra, asume un transporte subyacente sin pérdida. El control de flujo de DCB puede habilitarse o deshabilitarse globalmente o para adaptadores de red individuales. Si se habilita, permite la creación de directivas de QoS que priorizan cierto tráfico de la aplicación.

Para que las directivas de QoS funcionen sin problemas durante la conmutación por error, todos los servidores de un clúster de Azure Stack HCl deben tener la misma configuración de estado de control del flujo. Si usa adaptadores de RoCE, el control de flujo de prioridad debe estar habilitado en todos los servidores.

Use el cmdlet `Get-NetQosFlowControl` para obtener la configuración de control de flujo actual. Todas las prioridades están deshabilitadas de manera predeterminada.

Use los cmdlets `Enable-NetQosFlowControl` y `Disable-NetQosFlowControl` con el parámetro -priority para activar o desactivar el control de flujo de prioridad. Por ejemplo, el siguiente comando habilita el control de flujo en el tráfico etiquetado con prioridad 3:

```PowerShell
Enable-NetQosFlowControl –Priority 3
```

## <a name="validate-storage-qos-rules"></a>Validar las reglas de QoS de almacenamiento

Valide que todos los nodos tengan una regla de QoS para los clústeres de conmutación por error y para SMB o SMB directo. De lo contrario, pueden producirse problemas de conectividad y de rendimiento.

### <a name="qos-rule-for-failover-clustering"></a>Regla de QoS para los clústeres de conmutación por error

Si se define **cualquier** regla de QoS de almacenamiento en un clúster, debe existir una regla de QoS para los clústeres de conmutación por error. De lo contrario, se pueden producir problemas de conectividad. Para agregar una nueva regla de QoS para los clústeres de conmutación por error, use el cmdlet `New-NetQosPolicy` como en el ejemplo siguiente:

```PowerShell
New-NetQosPolicy "Cluster" -IPDstPort 3343 -Priority 6
```

### <a name="qos-rule-for-smb"></a>Regla de QoS para SMB

Si algunos o todos los nodos tienen reglas de QoS definidas, pero no tienen una regla de QoS para SMB, esto puede provocar problemas de conectividad y rendimiento para SMB. Para agregar una nueva regla de QoS de red para SMB, use el cmdlet `New-NetQosPolicy` como en el ejemplo siguiente:

```PowerShell
New-NetQosPolicy -Name "SMB" -SMB -PriorityValue8021Action 3
```

### <a name="qos-rule-for-smb-direct"></a>Regla de QoS para SMB directo

SMB directo omite la pila de red, en lugar de usar métodos RDMA para transferir datos. Si algunos o todos los nodos tienen reglas de QoS definidas, pero no tienen una regla de QoS para SMB directo, esto puede provocar problemas de conectividad y rendimiento para SMB directo. Para crear una nueva directiva de QoS para SMB directo, emita los siguientes comandos:

```PowerShell
New-NetQosPolicy "SMB Direct" –NetDirectPort 445 –Priority 3
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Protocolo de puente del centro de datos](/windows-server/networking/technologies/dcb/dcb-top)
- [Administración del protocolo de puente del centro de datos](/windows-server/networking/technologies/dcb/dcb-manage)
- [Configuraciones comunes de QoS](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj735302(v=ws.11))