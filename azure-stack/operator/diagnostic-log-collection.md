---
title: Recopilación de registros de diagnóstico
description: Información sobre la recopilación de registros de diagnóstico.
author: PatAltimore
ms.topic: article
ms.date: 02/03/2021
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 02/03/2021
ms.openlocfilehash: ad5f0a7f6028249dba3d63490cdc3c91d7a45e72
ms.sourcegitcommit: 69c700a456091adc31e4a8d78e7a681dfb55d248
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/10/2021
ms.locfileid: "100013224"
---
# <a name="diagnostic-log-collection"></a>Recopilación de registros de diagnóstico

Puede compartir los registros de diagnóstico creados por Azure Stack Hub. Los componentes de Windows y los servicios locales de Azure crean estos registros. El equipo de Soporte técnico de Microsoft puede usar los registros para corregir o identificar problemas con la instancia de Azure Stack Hub.

Para empezar a trabajar con la recopilación de registros de diagnóstico de Azure Stack Hub, debe registrar la instancia. Si Azure Stack Hub no está registrado, [utilice el punto de conexión con privilegios (PEP)](azure-stack-get-azurestacklog.md) para compartir los registros. 

::: moniker range=">= azs-2005"

Tiene varias maneras de enviar registros de diagnóstico al equipo de Soporte técnico de Microsoft. En función de la conectividad con Azure, las opciones son:
* [Envío proactivo de registros (recomendable)](#send-logs-proactively)
* [Envío inmediato de registros](#send-logs-now)
* [Almacenamiento local de los registros](#save-logs-locally)

En el diagrama de flujo se muestra qué opción utilizar para enviar los registros de diagnóstico. Si Azure Stack Hub se conecta a Azure, habilite la **recopilación proactiva de registros**. La recopilación proactiva de registros cargará automáticamente los registros de diagnóstico en un blob de almacenamiento controlado por Microsoft en Azure cuando se genere una alerta crítica. También puede recopilar los registros a petición mediante **Send logs now** (Enviar registros ahora). Para un entorno de Azure Stack Hub que se ejecuta en un entorno desconectado, o si tiene problemas de conectividad, elija **Save logs locally** (Guardar los registros localmente).

![Diagrama de flujo que muestra el envío inmediato de registros a Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

::: moniker-end

## <a name="send-logs-proactively"></a>Envío de registros de forma proactiva

La recopilación de registros proactiva recopila y envía automáticamente los registros de diagnóstico de Azure Stack Hub a Microsoft antes de abrir una incidencia de soporte técnico. Estos registros solo se recopilan cuando se genera una alerta de mantenimiento del sistema y solo se tiene acceso a ellos desde el equipo de Soporte técnico de Microsoft en el contexto de una incidencia de soporte técnico.

::: moniker range=">= azs-2008"

A partir de Azure Stack Hub versión 2008, la recopilación proactiva de registros usa un algoritmo mejorado que captura los registros incluso durante condiciones de error que no son visibles para un operador. Esto garantiza que la información de diagnóstico correcta se recopila en el momento adecuado sin necesidad de interacción del operador. El servicio de soporte técnico de Microsoft puede comenzar a solucionar problemas antes en algunos casos. Las mejoras del algoritmo inicial se centran en las operaciones de revisión y actualización.

Azure Stack Hub recopila registros de alertas y otros eventos de error ocultos que no son visibles para el operador.

Azure Stack Hub recopila registros de forma proactiva para:

- Error al actualizar.
- La actualización necesita atención.

Cuando un evento desencadena estas alertas, Azure Stack Hub envía de forma proactiva los registros a Microsoft.

Además, Azure Stack Hub envía registros a Microsoft desencadenados por otros eventos de error. Estos eventos no son visibles para el operador.

Se recomienda habilitar recopilaciones proactivas de registros, ya que se optimizan más operaciones y aumentan las ventajas.

::: moniker-end

La recopilación de registros proactiva puede deshabilitarse y habilitarse de nuevo en cualquier momento. Siga estos pasos para configurar la recopilación de registros proactiva.

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
1. Abra la **información general de la ayuda y el soporte técnico**.
1. Si aparece el banner, seleccione **Enable proactive log collection** (Habilitar la recopilación proactiva de registros). O bien, puede seleccionar **Settings** (Configuración), establecer **Proactive log collection** (Recopilación proactiva de registros) en **Enable** (Habilitar) y seleccionar **Save** (Guardar).

> [!NOTE]
> Si la configuración de la ubicación de registro es para un recurso compartido de archivos local, asegúrese de que las directivas de administración del ciclo de vida impidan que el almacenamiento de recursos compartidos alcance su cuota de tamaño. Azure Stack Hub no supervisa el recurso compartido de archivos local ni aplica ninguna directiva de retención.   

### <a name="how-the-data-is-handled"></a>Cómo se controlan los datos

Usted acepta que Microsoft recopile periódicamente los registros relativos únicamente a las alertas de mantenimiento del sistema de Azure Stack Hub. También acepta y da su consentimiento a la carga y retención de dichos registros en una cuenta de Azure Storage administrada y controlada por Microsoft.

Los datos se usarán solo para solucionar problemas de alertas de mantenimiento del sistema, no para marketing, publicidad ni ningún otro propósito comercial sin su consentimiento. Los datos se pueden conservar durante un máximo de 90 días y cualquier dato que Microsoft recopile se controlará según nuestros [procedimientos de privacidad estándar](https://privacy.microsoft.com/).

Los datos recopilados previamente con su consentimiento no se verán afectados por la revocación del permiso.

Los registros recopilados mediante la **recopilación proactiva de registros** se cargan en una cuenta de Azure Storage administrada y controlada por Microsoft. Microsoft puede acceder a estos registros en el contexto de una incidencia de soporte técnico y para mejorar el mantenimiento de Azure Stack Hub.

## <a name="send-logs-now"></a>Envío inmediato de registros

> [!TIP]
> Ahorre tiempo mediante la opción [Send logs proactively](#send-logs-proactively) (Enviar recursos de forma proactiva) en lugar de con Send logs now (Enviar registros ahora).

Send logs now (Enviar registros ahora) es una opción en la que se recopilan y cargan manualmente los registros de diagnóstico de Azure Stack Hub, normalmente antes de abrir una incidencia de soporte técnico.

Hay dos maneras de enviar manualmente los registros de diagnóstico a Soporte técnico de Microsoft:
* [Portal de administración (recomendado)](#send-logs-now-with-the-administrator-portal)
* [PowerShell](#send-logs-now-with-powershell)

Si Azure Stack Hub está conectado a Azure, se recomienda usar el portal de administración, ya que es la forma más sencilla de enviar los registros directamente a Microsoft. Si el portal no está disponible, debe usar PowerShell para enviar los registros.

### <a name="send-logs-now-with-the-administrator-portal"></a>Enviar registros ahora con el portal de administración

Para enviar registros ahora con el portal de administración:

1. Abra **Ayuda y soporte técnico > Recopilación de registros > Send logs now** (Enviar registros ahora). 
1. Especifique la hora de inicio y la hora de finalización de la recopilación de registros. 
1. Elija la zona horaria local.
1. Seleccione **Collect and Upload** (Recopilar y cargar).

Si está desconectado de Internet o desea guardar los registros solo de forma local, use el método [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para enviar los registros.

### <a name="send-logs-now-with-powershell"></a>Enviar registros ahora con PowerShell

Si usa el método para **enviar registros ahora** y desea usar PowerShell en lugar del portal de administración, puede usar el cmdlet `Send-AzureStackDiagnosticLog` para recopilar y enviar registros específicos.

* Los parámetros **FromDate** y **ToDate** pueden utilizarse para recopilar registros durante un período de tiempo determinado. Si no se especifican los parámetros, se recopilan los registros de las últimas cuatro horas de forma predeterminada.

* Use el parámetro **FilterByNode** para filtrar los registros por nombre de equipo. Por ejemplo:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* Use el parámetro **FilterByLogType** para filtrar los registros por tipo. Puede elegir filtrar por archivo, por recurso compartido o por WindowsEvent. Por ejemplo:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* Use el parámetro **FilterByResourceProvider** para enviar registros de diagnóstico para proveedores de recursos de valor agregado (RP). La sintaxis general es:
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```

  ::: moniker range=">= azs-2008"

  Para enviar registros de diagnóstico para SQL RP: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider SQLAdapter
  ```
  Para enviar registros de diagnóstico para MySQL RP: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider MySQLAdapter
  ```
  
  ::: moniker-end

  Para enviar registros de diagnóstico para IoT Hub: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  Para enviar registros de diagnóstico para Event Hubs:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  Para enviar registros de diagnóstico para Azure Stack Edge:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* Use el parámetro **FilterByRole** para enviar registros de diagnóstico desde los roles VirtualMachines y BareMetal:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* Para enviar registros de diagnóstico desde los roles VirtualMachines y BareMetal, filtrando por fecha los archivos de registro de las últimas 8 horas:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Para enviar registros de diagnóstico desde los roles VirtualMachines y BareMetal, filtrando por fecha los archivos de registro del período comprendido entre las últimas 2 a 8 horas:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

> [!NOTE]
> Si está desconectado de Internet o desea guardar los registros solo de forma local, use el método [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para enviar los registros. 

### <a name="how-the-data-is-handled"></a>Cómo se controlan los datos

Al iniciar la recopilación de registros de diagnóstico desde Azure Stack Hub, acepta y da su consentimiento para cargar esos registros y conservarlos en una cuenta de Azure Storage administrada y controlada por Microsoft. Soporte técnico de Microsoft puede acceder a estos registros de inmediato con la incidencia de soporte técnico sin tener que ponerse en contacto con el cliente para la recopilación de los registros.

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>Almacenamiento local de los registros

Se pueden guardar los registros en un recurso compartido de Bloque de mensajes del servidor (SMB) local cuando Azure Stack Hub está desconectado de Azure. Por ejemplo, puede ejecutar un entorno desconectado. Si está conectado normalmente pero está experimentando problemas de conectividad, puede guardar los registros localmente para ayudar a solucionar los problemas.

 En la hoja **Settings** (Configuración), escriba la ruta de acceso y un nombre de usuario y una contraseña con permiso para escribir en el recurso compartido. Durante una incidencia de soporte técnico, Soporte técnico de Microsoft proporcionará pasos detallados sobre cómo se transfieren estos registros locales. Si el portal de administración no está disponible, puede usar [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para guardar los registros localmente.

![Captura de pantalla de opciones de recopilación de registros de diagnóstico](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Consideraciones sobre el ancho de banda

El tamaño medio de la recopilación de los registros de diagnóstico varía en función de si se ejecuta de forma proactiva o manual. El tamaño medio de la **recopilación proactiva de registros** es de aproximadamente 2 GB. El tamaño de la recopilación de la opción **Send logs now** (Enviar registros ahora) depende del número de horas que se van a recopilar.

En la tabla siguiente se enumeran las consideraciones para entornos con conexiones limitadas o de uso medido con Azure.

| Conexión de red | Impacto |
|----|---|
| Conexión de alta latencia o ancho de banda bajo | La carga de registros tardará mucho tiempo en completarse. |
| Conexión compartida | La carga solo puede afectar a otras aplicaciones o usuarios que comparten la conexión de red. |
| Conexión de uso medido | Su ISP puede realizar otro cargo por el uso adicional de la red. |

## <a name="view-log-collection"></a>Visualización de la recopilación de registros

El historial de registros recopilados de Azure Stack Hub aparece en la página **Recopilación de registros** en **Ayuda y soporte técnico**, con las siguientes fechas y horas:

- **Hora de recopilación**: cuándo se inició la operación de recopilación de registros.
- **Estado**: en curso o finalizada.
- **Inicio del registro**: inicio del período de tiempo durante el cual desea llevar a cabo la recopilación.
- **Fin del registro**: final del período de tiempo.
- **Tipo**: si se trata de una recopilación de registros manual o proactiva.

![Colecciones de registros en Ayuda y soporte técnico](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="see-also"></a>Consulte también

[Control de datos de clientes y registro de Azure Stack Hub](./azure-stack-data-collection.md)
