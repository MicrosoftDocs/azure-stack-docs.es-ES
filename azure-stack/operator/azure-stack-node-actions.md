---
title: Acciones de los nodos de la unidad de escalado en Azure Stack Hub
description: Obtenga información sobre las acciones de los nodos de la unidad de escalado, incluidas las de conectar, desconectar, deshabilitar y reanudar, así como visualizar el estado de los nodos en los sistemas integrados de Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 11/11/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 5941fb3d433512bad69c36a0620d6ec32988d44a
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509745"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub"></a>Acciones de los nodos de la unidad de escalado en Azure Stack Hub

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

   - La lista de nodos individuales.
   - El estado operativo (consulte la lista siguiente).
   - El estado de encendido (p. ej., en funcionamiento o detenido).
   - El modelo de servidor.
   - La dirección IP del controlador de administración de placa base (BMC).
   - El número total de núcleos.
   - La cantidad total de memoria.

![estado de una unidad de escalado](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Estados operativos de nodo

| Status | Descripción |
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

Debe instalar los módulos de PowerShell de Azure Stack Hub. Estos cmdlets están en el módulo **Azs.Fabric.Admin**. Para instalar o comprobar la instalación de PowerShell para Azure Stack Hub, consulte [Instalación de PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).

## <a name="stop"></a>Stop

La acción **Detener** desactiva el nodo. Equivale a presionar el botón de encendido. No envía una señal de apagado al sistema operativo. Para las operaciones de detención planeadas, intente siempre llevar a cabo la operación de apagado primero.

Esta acción se utiliza normalmente cuando un nodo está en un estado bloqueado y ya no responde a las solicitudes.

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

> [!CAUTION]  
> La nivelación del firmware es fundamental para el éxito de la operación que se describe en este artículo. La omisión de este paso puede dar lugar a inestabilidad del sistema, disminución del rendimiento, amenazas de seguridad o error cuando la automatización de Azure Stack Hub implementa el sistema operativo. Consulte siempre la documentación del asociado de hardware cuando reemplace hardware para asegurarse de que el firmware aplicado coincide con la versión de OEM que se muestra en el [portal del administrador de Azure Stack Hub](azure-stack-updates.md).<br><br>
Para obtener más información y vínculos a la documentación del asociado, vea [Sustitución de un componente de hardware](azure-stack-replace-component.md).

| Asociado de hardware | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [Guía de operaciones de Cisco Integrated System para Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Notas de la versión de Cisco Integrated System para Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | All | [Nube para Microsoft Azure Stack Hub 14G (cuenta e inicio de sesión necesarios)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Nube para Microsoft Azure Stack Hub 13G (cuenta e inicio de sesión necesarios)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPÓN | [Departamento de soporte técnico de servicio administrado de Fujitsu (cuenta e inicio de sesión necesarios)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Soporte técnico de TI de Fujitsu para productos y sistemas](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [Fujitsu MySupport (cuenta e inicio de sesión necesarios)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | All | [HPE ProLiant para Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM Best Recipes](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

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