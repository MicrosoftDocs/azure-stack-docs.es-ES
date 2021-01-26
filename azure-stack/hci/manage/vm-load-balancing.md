---
title: Equilibrio de carga de máquinas virtuales
description: Use este tema para aprender a configurar la característica de equilibrio de carga de máquinas virtuales en Azure Stack HCI y Windows Server.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 1/14/2021
ms.openlocfilehash: 9caf8efdd8db46479cce3c5c4299fb5588c7d37a
ms.sourcegitcommit: 51ce5ba6cf0a377378d25dac63f6f2925339c23d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224779"
---
# <a name="virtual-machine-load-balancing"></a>Equilibrio de carga de máquinas virtuales

> Se aplica a: Azure Stack HCI, versión 20H2, Windows Server 2019 y Windows Server 2016

Una consideración clave para las implementaciones de HCI es el gasto de capital (CapEx) necesario para entrar en producción. Es habitual agregar redundancia para evitar un defecto de capacidad durante el tráfico de los picos en producción, pero esto aumenta el gasto de capital. Esta redundancia suele ser necesaria porque algunos servidores del clúster hospedan más máquinas virtuales, mientras que otros servidores están infrautilizados.

Habilitado de forma predeterminada en Azure Stack HCI, Windows Server 2019 y Windows Server 2016, el equilibrio de carga de máquinas virtuales es una característica que permite optimizar el uso de los servidores en los clústeres. Identifica los servidores sobrecargados y migra en vivo las máquinas virtuales de esos servidores a los servidores con menos carga. Se respetan las directivas de error, como la antiafinidad, los dominios de error (sitios) y los posibles propietarios.

El equilibrio de carga de máquinas virtuales evalúa la carga de un servidor en función de la heurística siguiente:

- **Presión de memoria actual:** la memoria es la restricción de recursos más común en un host de Hyper-V.
- **Promedio de uso de CPU en un período de cinco minutos:** mitiga el uso excesivo de los servidores del clúster.

## <a name="how-does-vm-load-balancing-work"></a>¿Cómo funciona el equilibrio de carga de máquinas virtuales?

El equilibrio de carga de máquinas virtuales se produce automáticamente cuando se agrega un nuevo servidor al clúster y también se puede configurar para realizar un equilibrio de carga periódico y recurrente.

### <a name="when-a-new-server-is-added-to-a-cluster"></a>Cuando se agrega un nuevo servidor a un clúster

Cuando se une un nuevo servidor al clúster, la característica de equilibrio de carga de máquinas virtuales equilibra automáticamente la capacidad de los servidores existentes en el servidor recién agregado en el siguiente orden:

1. Se evalúan la presión de memoria y el uso de CPU en los servidores existentes en el clúster.
2. Se identifican todos los servidores que superen el umbral.
3. Se identifican los servidores con la máxima presión de memoria y uso de CPU para determinar la prioridad del equilibrio.
4. Se migran en vivo las máquinas virtuales (sin tiempo de inactividad) desde un servidor que supera el umbral al servidor recién agregado al clúster.

:::image type="content" source="media/vm-load-balancing/server-added.png" alt-text="Imagen que muestra el nuevo servidor que se va a agregar a un clúster" border="false"::: 

### <a name="recurring-load-balancing"></a>Equilibrio de carga recurrente

De forma predeterminada, el equilibrio de carga de máquinas virtuales está configurado para el equilibrio periódico: se evalúan la presión de memoria y el uso de CPU de cada servidor del clúster para equilibrar la carga cada 30 minutos. Este es el flujo de los pasos:

1. Se evalúan la presión de memoria y el uso de CPU en todos los servidores del clúster.
2. Se identifican todos los servidores que superan el umbral y los que están por debajo del umbral.
3. Se identifican los servidores con la máxima presión de memoria y uso de CPU para determinar la prioridad del equilibrio.
4. Se migran en vivo las máquinas virtuales (sin tiempo de inactividad) desde un servidor que supera el umbral a otro servidor que esté por debajo del umbral mínimo.

:::image type="content" source="media/vm-load-balancing/periodic-balancing.png" alt-text="Imagen que muestra un clúster activo que se reequilibra automáticamente" border="false"::: 

## <a name="configure-vm-load-balancing-using-windows-admin-center"></a>Configuración del equilibrio de carga de máquinas virtuales mediante Windows Admin Center

La forma más fácil de configurar el equilibrio de carga de máquinas virtuales es usar Windows Admin Center. 

:::image type="content" source="media/vm-load-balancing/vm-load-balancing.png" alt-text="Configuración del equilibrio de carga de máquinas virtuales con Windows Admin Center" lightbox="media/vm-load-balancing/vm-load-balancing.png":::

1. Conéctese al clúster y vaya a **Tools > Settings** (Herramientas > Configuración).

2. En **Settings** (Configuración), seleccione **Virtual machine load balancing** (Equilibrio de carga de máquinas virtuales).

3. En **Balance virtual machines** (Equilibrar máquinas virtuales), seleccione **Always** (Siempre) para equilibrar la carga después de una unión de servidor y cada 30 minutos, **Server joins** (Uniones de servidores) para equilibrar la carga únicamente en las uniones de servidores, o **Never** (Nunca) para deshabilitar la característica de equilibrio de carga de máquinas virtuales. La configuración predeterminada es **Always** (Siempre).

4. En **Aggressiveness** (Agresividad), seleccione **Low** (Baja) para migrar en vivo máquinas virtuales cuando el servidor tiene más del 80 % de carga, **Medium** (Media) para migrar cuando el servidor tiene más del 70 % de carga, o **High** (Alta) para calcular el promedio de los servidores del clúster y migrar cuando el servidor supere el 5 % de la media. La configuración predeterminada es **Low** (Baja).

## <a name="configure-vm-load-balancing-using-windows-powershell"></a>Configuración del equilibrio de carga de máquinas virtuales mediante Windows PowerShell

Puede configurar si se va a producir y cuándo se produce el equilibrio de carga mediante la propiedad común del clúster `AutoBalancerMode`. Para controlar cuándo se debe equilibrar el clúster, ejecute lo siguiente en PowerShell, sustituyendo un valor de la tabla siguiente:

```PowerShell
(Get-Cluster).AutoBalancerMode = <value>
```

|AutoBalancerMode |Comportamiento|
|-----------------|-----------|
| 0 | Disabled |
| 1 | Equilibrio de carga después de una unión de servidor |
| 2 (predeterminado) | Equilibrio de carga después de una unión de servidor y cada 30 minutos |

También puede configurar la agresividad del equilibrio mediante el uso de la propiedad común del clúster `AutoBalancerLevel`. Para controlar el umbral de agresividad, ejecute lo siguiente en PowerShell, sustituyendo un valor de la tabla siguiente:

```PowerShell
(Get-Cluster).AutoBalancerLevel = <value>
```

| AutoBalancerLevel | Agresividad | Comportamiento |
|-------------------|----------------|----------|
| 1 (predeterminado) | Bajo | Mover cuando el host supera el 80 % de carga |
| 2 | Media | Mover cuando el host supera el 70 % de carga |
| 3 | Alto | Calcular el promedio de los servidores del clúster y mover cuando el host está un 5 % por encima del promedio |

Para comprobar cómo se establecen las propiedades `AutoBalancerLevel` y `AutoBalancerMode`, ejecute lo siguiente en PowerShell:

```PowerShell
Get-Cluster | fl AutoBalancer*
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Administración de máquinas virtuales](vm.md)
- [Administración de máquinas virtuales con PowerShell](vm-powershell.md)
- [Creación de reglas de afinidad de máquina virtual](vm-affinity.md)
