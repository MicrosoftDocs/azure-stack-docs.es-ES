---
title: Códigos e indicadores de EMC PowerEdge Tactical
description: Conozca los códigos e indicadores de EMC PowerEdge Tactical.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 8a820d02ef1832343ba5110a244793c70b73aed3
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910285"
---
# <a name="dell-emc-poweredge-tactical-indicators-and-codes"></a>Códigos e indicadores de EMC PowerEdge Tactical de Dell

Las características descritas en esta sección son las mismas en los servidores de host de ciclo de vida de hardware y de nodo de unidad de escalado.

## <a name="system-information-label"></a>Etiqueta de información del sistema

En la figura siguiente se muestra la etiqueta de información del sistema, que se encuentra en el panel frontal.


Figura 10. Comportamiento del LED

![Etiqueta de información del sistema para el comportamiento del LED.](media/image-75.png)

## <a name="idrac-direct-led-indicator-codes"></a>Códigos del indicador LED de iDRAC Direct

El indicador LED de iDRAC Direct se enciende para indicar que el puerto está conectado y se usa como parte del subsistema iDRAC.

Puede configurar iDRAC Direct mediante un cable USB a micro USB (tipo AB), que puede conectar a su portátil o tableta. En la tabla siguiente se describe la actividad de iDRAC Direct cuando su puerto está activo y se explican los códigos del indicador LED de iDRAC Direct.

Tabla 4. Códigos del indicador LED

| **Códigos de indicador**                                         | **Condition**                                                |
|-------------------------------------------------------------|--------------------------------------------------------------|
| Verde sólido durante dos segundos                                 | Indica que el portátil o la tableta están conectados.            |
| Verde parpadeante (encendido dos segundos y apagado dos segundos) | Indica que se reconocen el portátil o la tableta conectados. |
| No se enciende                                             | Indica que el portátil o la tableta no están conectados.            |

## <a name="left-control-panel"></a>Panel de control izquierdo

En la figura siguiente se muestran los indicadores de estado y mantenimiento del panel de control izquierdo.



Figura 11. Panel de control izquierdo

![Diagrama en el que se muestran los indicadores de estado y mantenimiento del panel de control izquierdo.](media/image-76.png)

En la tabla siguiente se describen las características del panel izquierdo.

Tabla 5. Indicadores de estado y mantenimiento del panel de control izquierdo de PowerEdge

|    <br>Elemento       |    <br>Indicador o botón                       |    <br>Icono                                |    <br>Descripción                                                                                                  |
|-------------------|--------------------------------------------------|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
|    <br>1          |    <br>Indicadores LED de estado                     |    <br>N/D                                 |    <br>Indican el estado del sistema. Para obtener más información, consulte la tabla Indicadores LED de estado de PowerEdge.    |
|    <br>2 y 3    |    <br>Indicadores de identificación y estado del sistema    | ![Icono de mantenimiento del sistema](media/image-77.png) |    <br>Indica el estado del sistema.                                                                                 |

En la tabla siguiente se incluyen los indicadores LED de estado de PowerEdge.

> [!NOTE]
> Los indicadores LED de estado siempre están apagados y solo cambian a un ámbar sólido si se produce algún error.

Tabla 6. Indicadores LED de estado de PowerEdge

| <br>Icono | <br>Descripción           | <br>Condición                                                                                                                                                                       | <br>Acción correctiva                                                                                                                                                                                                                                                                                                    |
|----------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|          | <br>Indicador de unidad       | <br>El indicador se enciende con un color ámbar sólido si hay un error de unidad.                                                                                                                      | 1. Compruebe el registro de eventos del sistema para determinar si la unidad tiene un error.<br>2. Ejecute la prueba de diagnóstico en línea adecuada.<br>3. Reinicie el sistema y ejecute diagnósticos insertados (ePSA).<br>4. Si las unidades están configuradas en una matriz RAID, reinicie el sistema y acceda al programa de utilidad de configuración del adaptador de host. |
|          | <br>Indicador de temperatura | <br>El indicador se enciende con un color ámbar sólido si el sistema sufre un error térmico (por ejemplo, la temperatura ambiente está fuera del intervalo o falla un ventilador).                 | Asegúrese de que no se ha producido ninguna de las condiciones siguientes:<br>* Se ha quitado o ha fallado un ventilador de refrigeración. <br>* Se ha quitado la cobertura del sistema, la tapa del aire, la protección del módulo de memoria o el soporte de relleno trasero.<br>* La temperatura ambiente es demasiado alta.<br>* El flujo de aire externo está obstruido.                                                 |
|          | <br>Indicador eléctrico  | <br>El indicador se enciende con un color ámbar sólido si el sistema sufre un error eléctrico (por ejemplo, voltaje fuera del intervalo o fallo de una unidad de fuente de alimentación o un regulador de voltaje). | 1. Compruebe el registro de eventos del sistema o los mensajes del sistema para determinar el problema específico.<br>2. Si el registro indica un problema con la unidad de fuente de alimentación (PSU), compruebe el LED en ella.<br>3. Vuelva a colocar la PSU.                                                                                                                                              |
|          | <br>Indicador de memoria      | <br>El indicador se enciende con un color ámbar sólido si hay un error de memoria.                                                                                                                       | 1. Compruebe el registro de eventos del sistema o los mensajes del sistema para determinar la ubicación de la memoria que ha producido el error.<br>2. Vuelva a colocar el módulo de memoria.                                                                                                                                                                                                  |
|          | <br>Indicador de PCIe      | <br>El indicador se enciende con un color ámbar sólido si hay un error en una tarjeta PCIe.                                                                                                          | 1. Reinicie el sistema.<br>2. Actualice los controladores necesarios para la tarjeta PCIe.<br>3. Vuelva a instalar la tarjeta.                                                                                                                                                                                                                    |

## <a name="power-supply-unit-indicator-codes"></a>Códigos de indicador de la unidad de fuente de alimentación

Las unidades de fuente de alimentación (PSU) de CA tienen un asa traslúcida iluminada que actúa como indicador. Las PSU de CC tienen un LED que actúa como un indicador. El indicador muestra si hay alimentación o si se ha producido un error de alimentación.

En la siguiente figura se muestra la PSU de CA.

Ilustración 12. Unidad de fuente de alimentación de CA

![Diagrama en el que se muestra la PSU de CA, con el LED indicado.](media/image-83.png)

En la tabla siguiente se definen los códigos del indicador.

Tabla 7. Códigos de indicador de la PSU de CA

| Códigos de indicador de alimentación         | Condición                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Verde                         | Hay una fuente de energía válida conectada a la PSU y esta está operativa.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Ámbar parpadeante                | Indica un problema con la PSU.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| No se enciende               | No hay alimentación conectada a la PSU.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Verde parpadeante                | Cuando se actualiza el firmware de la PSU, su asa parpadea en verde. <br>No desconecte el cable de alimentación ni desconecte la PSU cuando actualice el firmware. Si se interrumpe la actualización del firmware, la PSU no funcionará.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Verde parpadeante y se apaga  | Cuando se conecta en caliente una PSU, su asa parpadea cinco veces a una velocidad de 4 Hz y después se apaga. Esto indica una incompatibilidad de la unidad con respecto a la eficacia, el conjunto de características, el estado de mantenimiento o el voltaje admitido. <br><br>Si se instalan dos PSU, ambas deben tener el mismo tipo de etiqueta; por ejemplo, la etiqueta de rendimiento energético extendido. No se admite la combinación de PSU de generaciones anteriores de servidores PowerEdge, aunque la PSU tenga la misma clasificación energética. Esto provoca una condición de incompatibilidad de la PSU o un error al activar el sistema. <br>A la hora de corregir una incompatibilidad de PSU, reemplace solo la PSU en la que parpadea el indicador. El intercambio de la PSU para establecer un par coincidente puede producir una condición de error y <br>el cierre inesperado del sistema. Para cambiar de una configuración de alto rendimiento a una configuración de bajo rendimiento o viceversa, debe apagar el sistema. <br>La PSU de CA admite voltajes de entrada de 240 V y 120 V con la excepción de las PSU de titanio, que solo admiten 240 V. Cuando dos PSU idénticas reciben voltajes de entrada diferentes, pueden generar diferentes vatajes y desencadenar una incompatibilidad. <br>Si se usan dos PSU, deben ser del mismo tipo y tener la misma potencia de salida máxima. <br>No se admite la combinación de PSU de CA y CC; desencadena una incompatibilidad. |
