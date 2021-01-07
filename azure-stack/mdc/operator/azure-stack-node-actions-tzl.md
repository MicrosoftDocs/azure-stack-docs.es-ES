---
title: Acciones del nodo de unidad de escalado en Azure Stack Hub para un MDC
description: Obtenga información sobre las acciones del nodo de unidad de escalado, incluidas encender, apagar, deshabilitar y reanudar, así como visualizar el estado de los nodos en los sistemas integrados de Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 10/26/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 835b6a39e089bbe959c7c5cf5283d2b516b6d4f7
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872065"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub---modular-data-center-mdc"></a>Acciones del nodo de unidad de escalado en Azure Stack Hub: centro de datos modular (MDC)

En este artículo se describe cómo ver el estado de una unidad de escalado. Puede ver los nodos de la unidad. Puede ejecutar acciones de nodo, como son conectar, desconectar, apagar, purgar, reanudar y reparar. Normalmente, estas acciones de nodo se utilizan durante el reemplazo de componentes o para ayudar a recuperar un nodo.

> [!Important]  
> Todas las acciones de nodo que se describen en este artículo deben destinarse a un nodo cada vez.

## <a name="view-the-node-status"></a>Ver el estado del nodo

En el portal de administrador, puede ver el estado de una unidad de escalado y sus nodos asociados.

Para ver el estado de una unidad de escalado:

1. En el icono **Administración de regiones**, seleccione la región.
2. A la izquierda, bajo **Infrastructure resources** (Recursos de infraestructura), seleccione **Scale units** (Unidades de escalado).
3. En los resultados, seleccione la unidad de escalado.
4. En el lado izquierdo, bajo **General**, seleccione **Nodos**.

   Vea la siguiente información:

   - La lista de nodos individuales
   - Estado operativo (consulte la lista siguiente)
   - Estado de encendido (en funcionamiento o detenido)
   - Modelo de servidor
   - dirección IP del controlador de administración de placa base (BMC)
   - Número total de núcleos
   - Cantidad total de memoria

     ![estado de una unidad de escalado](media/azure-stack-node-actions/multinode-actions.png)

### <a name="node-operational-states"></a>Estados operativos de nodo

| Estado | Descripción |
|----------------------|-------------------------------------------------------------------|
| En ejecución | El nodo está participando activamente en la unidad de escalado. |
| Detenido | El nodo no está disponible. |
| Agregando | El nodo se está agregando activamente a la unidad de escalado. |
| Reparando | El nodo se está reparando activamente. |
| Mantenimiento | El nodo está en pausa y no se está ejecutando ninguna carga de trabajo de usuario activa. |
| Requiere corrección | Se detectó un error que requiere que el nodo se repare. |

## <a name="scale-unit-node-actions"></a>Acciones de nodo de unidad de escalado

Al ver la información sobre un nodo de la unidad de escalado, también puede realizar acciones de nodo como:

 - Iniciar y detener (según el estado actual de energía).
 - Deshabilitar y reanudar (según el estado de las operaciones).
 - Reparar.
 - Apagar.

El estado operativo del nodo determina qué opciones están disponibles.

Debe instalar los módulos de PowerShell de Azure Stack Hub. Estos cmdlets están en el módulo **Azs.Fabric.Admin**. Para instalar o comprobar la instalación de PowerShell para Azure Stack Hub, consulte [Instalación de PowerShell para Azure Stack Hub](../../operator/powershell-install-az-module.md).

## <a name="stop"></a>Stop

La acción **Detener** desactiva el nodo. Equivale a presionar el botón de encendido. No envía una señal de apagado al sistema operativo. Para las operaciones de detención planeadas, intente siempre llevar a cabo la operación de apagado primero.

Esta acción se utiliza normalmente cuando un nodo se encuentra en un estado que no responde.

Para ejecutar la acción de detención, abra un símbolo de sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

En el improbable caso de que la acción de detención no funcione, vuelva a intentar la operación y, si produce un error una segunda vez, use la interfaz web de BMC en su lugar.

Para más información, consulte [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Start

La acción de **inicio** activa el nodo. Es lo mismo que si se presiona el botón de encendido.

Para ejecutar la acción de inicio, abra un símbolo de sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

En el caso improbable de que la acción de inicio no funcione, vuelva a intentar la operación. Si se produce un error por segunda vez, utilice la interfaz web de BMC en su lugar.

Para más información, consulte [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Purga

La acción de **purga** mueve todas las cargas de trabajo activas a los nodos restantes de esa unidad de escalado concreta.

Esta acción se suele utilizar durante el reemplazo de componentes, por ejemplo de un nodo completo.

> [!Important]
> Asegúrese de que la operación de purga se realiza en un nodo durante un período de mantenimiento planeado del cual se haya notificado a los usuarios. En determinadas condiciones, las cargas de trabajo activas pueden experimentar interrupciones.

Para ejecutar la acción de purga, abra un símbolo de sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Para más información, consulte [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Reanudación

La acción de **reanudación** reanuda un nodo deshabilitado y lo marca como activo para la colocación de cargas de trabajo. Las cargas de trabajo anteriores que se estuvieran ejecutando en el nodo no conmutan por recuperación. (Si realiza una operación de purga en un nodo, asegúrese de desconectarlo. Al encenderlo de nuevo, no se marca como activo para la colocación de cargas de trabajo. Cuando esté listo, debe usar la acción de reanudación para marcar el nodo como activo).

Para reanudar la acción de purga, abra un símbolo de sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Para más información, consulte [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Reparación

La acción **Reparar** repara un nodo. Úsela solamente para alguno de los escenarios siguientes:

- Al reemplazar un nodo completo (con o sin discos de datos nuevos).
- Después del error de un componente de hardware y de su reemplazo (si se recomienda en la documentación de la unidad reemplazable de campo [FRU]).

> [!Important]  
> Consulte la documentación de la FRU de su proveedor de hardware OEM para conocer los pasos exactos cuando necesite reemplazar un nodo o componentes de hardware individuales. La documentación de la FRU especificará si tiene que ejecutar la acción de reparación después de reemplazar un componente de hardware.

Al ejecutar la acción de reparación, debe especificar la dirección IP de BMC.

Para reanudar la acción de reparación, abra un símbolo de sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

La acción de **apagado** mueve primero todas las cargas de trabajo activas a los nodos restantes de la misma unidad de escalado. Después, la acción cierra discretamente el nodo de unidad de escalado.

Después de iniciar un nodo que se ha apagado, deberá ejecutar la acción de [reanudación](#resume). Las cargas de trabajo anteriores que se estuvieran ejecutando en el nodo no conmutan por recuperación.

Si se produce un error en la operación de apagado, intente la operación de [purgado](#drain) seguida de la operación de apagado.

Para ejecutar la acción de apagado, abra un símbolo de sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```

## <a name="next-steps"></a>Pasos siguientes

[Obtenga información sobre el módulo del operador de tejido de Azure Stack Hub](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).