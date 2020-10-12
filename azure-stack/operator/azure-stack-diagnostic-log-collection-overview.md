---
title: Recopilación de registros de diagnóstico en Azure Stack Hub
description: Obtenga información acerca de la recopilación de registros de diagnóstico en Ayuda y soporte técnico de Azure Stack Hub.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 88f2b267f493c05fdcd8a5419718d08d5b6efe37
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778258"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Recopilación de registros de diagnóstico en Azure Stack Hub

Azure Stack Hub es una colección de componentes de Windows y servicios locales de Azure que interactúan entre sí. Todos estos componentes y servicios generan su propio conjunto de registros. Como Soporte técnico de Microsoft usa estos registros para identificar y corregir los problemas de forma eficaz, ofrecemos la recopilación de registros de diagnóstico. La recopilación de registros de diagnóstico le ayuda a recopilar y a compartir rápidamente registros de diagnóstico con Soporte técnico de Microsoft mediante una interfaz de usuario sencilla que no requiere PowerShell. Los registros se recopilan aunque haya otros servicios de infraestructura inactivos.  

Se recomienda usar este enfoque de recopilación de registros y recurrir al [uso del punto de conexión con privilegios (PEP)](azure-stack-get-azurestacklog.md) únicamente si el portal de administración o la hoja Ayuda y soporte técnico no están disponibles. 

>[!NOTE]
>Azure Stack Hub debe estar registrado para usar la colección de registros de diagnóstico. Si Azure Stack Hub no está registrado, [utilice el punto de conexión con privilegios (PEP)](azure-stack-get-azurestacklog.md) para compartir los registros. 

## <a name="collection-options-and-data-handling"></a>Opciones de recopilación y control de datos

::: moniker range=">= azs-2005"

En función de la conectividad a Azure, Azure Stack Hub cuenta con métodos adecuados de recopilar, guardar y enviar registros de diagnóstico a CSS. Si Azure Stack Hub puede conectarse a Azure, el método recomendado es habilitar la **recopilación proactiva de registros**, que cargará automáticamente los registros de diagnóstico en un blob de almacenamiento controlado por Microsoft en Azure cuando se genere una alerta crítica. Como alternativa, se pueden recopilar registros a petición mediante **Send logs now** (Enviar registros ahora), o se pueden guardar los registros localmente si Azure Stack Hub está desconectado de Azure. 

En las secciones siguientes se explica cada opción y cómo se controlan los datos en cada caso. 

::: moniker-end

La característica de recopilación de registros de diagnóstico ofrece dos opciones para enviar los registros:
* Envío de registros de forma proactiva
* Envío de registros

También puede guardar los registros localmente.

Esto muestra qué opción utilizar para enviar los registros de diagnóstico en cada caso. 

![Diagrama de flujo que muestra el envío inmediato de registros a Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

En las secciones siguientes se explica cada opción y cómo se controlan los datos en cada caso. 

### <a name="send-logs-proactively"></a>Envío de registros de forma proactiva

La recopilación de registros proactiva recopila y envía automáticamente los registros de diagnóstico de Azure Stack Hub a Microsoft antes de abrir una incidencia de soporte técnico. Estos registros solo se recopilan cuando se genera una [alerta de mantenimiento del sistema](#proactive-diagnostic-log-collection-alerts) y solo se tiene acceso a ellos desde el equipo de Soporte técnico de Microsoft en el contexto de una incidencia de soporte técnico.

La recopilación de registros proactiva puede deshabilitarse y habilitarse de nuevo en cualquier momento. Siga estos pasos para configurar la recopilación de registros proactiva.

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
1. Abra la **información general de la ayuda y el soporte técnico**.
1. Si aparece el banner, seleccione **Enable proactive log collection** (Habilitar la recopilación proactiva de registros). O bien, puede seleccionar **Settings** (Configuración), establecer **Proactive log collection** (Recopilación proactiva de registros) en **Enable** (Habilitar) y seleccionar **Save** (Guardar).

>[!NOTE]
>Si la configuración de la ubicación de registro es para un recurso compartido de archivos local, asegúrese de que las directivas de administración del ciclo de vida impidan que el almacenamiento de recursos compartidos alcance su cuota de tamaño. Azure Stack Hub no supervisa el recurso compartido de archivos local ni aplica ninguna directiva de retención.   

### <a name="send-logs-now"></a>Envío de registros

> [!TIP]
> Ahorre tiempo mediante la [recopilación de registros proactiva](#send-logs-proactively) en lugar de con el envío inmediato de registros.

El envío inmediato de registros es una opción en la que se recopilan y cargan manualmente los registros de diagnóstico de Azure Stack Hub, normalmente antes de abrir una incidencia de soporte técnico.

Puede enviar manualmente los registros de diagnóstico al Soporte técnico de Microsoft mediante el portal de administración o PowerShell. Si Azure Stack Hub está conectado a Azure, se recomienda usar el portal de administración, ya que es la forma más sencilla de enviar los registros directamente a Microsoft. Si el portal no está disponible, debe usar PowerShell para enviar los registros.

Para enviar los registros de forma inmediata:

1. Abra **Ayuda y soporte técnico > Recopilación de registros > Send logs now** (Enviar registros ahora). 
1. Especifique la hora de inicio y la hora de finalización de la recopilación de registros. 
1. Elija la zona horaria local.
1. Seleccione **Collect and Upload** (Recopilar y cargar).

Si está desconectado de Internet o solo desea guardar los registros de forma local, use el método [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para enviar los registros. 

::: moniker range=">= azs-2005"

### <a name="save-logs-locally"></a>Almacenamiento local de los registros

Se pueden guardar los registros en un recurso compartido de Bloque de mensajes del servidor (SMB) local cuando Azure Stack Hub está desconectado de Azure. En la hoja **Settings** (Configuración), escriba la ruta de acceso y un nombre de usuario y una contraseña con permiso para escribir en el recurso compartido. Durante una incidencia de soporte técnico, Soporte técnico de Microsoft proporcionará pasos detallados sobre cómo se transfieren estos registros locales. Si el portal de administración no está disponible, puede usar [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para guardar los registros localmente.

![Captura de pantalla de opciones de recopilación de registros de diagnóstico](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Consideraciones sobre el ancho de banda

El tamaño medio de la recopilación de los registros de diagnóstico varía en función de si se ejecuta de forma proactiva o manual. El tamaño medio de la **recopilación proactiva de registros** es de aproximadamente 2 GB. El tamaño de la recopilación de la opción **Send logs now** (Enviar registros ahora) depende del número de horas que se van a recopilar.

En la tabla siguiente se enumeran las consideraciones para entornos con conexiones limitadas o de uso medido con Azure.

| Conexión de red | Impacto |
|----|---|
| Conexión de alta latencia o ancho de banda bajo | La carga de registros tardará mucho tiempo en completarse. |
| Conexión compartida | La carga solo puede afectar a otras aplicaciones o usuarios que comparten la conexión de red. |
| Conexión de uso medido | Su ISP puede realizarle un cargo adicional por el uso adicional de la red. |

## <a name="parameter-considerations"></a>Consideraciones sobre los parámetros 

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

Ahorre tiempo con el equipo de soporte técnico al cliente mediante la recopilación proactiva de registros de diagnóstico cuando se genera una alerta en Azure Stack Hub.

Si es necesario investigar el estado de mantenimiento del sistema, los registros se pueden cargar automáticamente para su análisis antes de abrir una incidencia para el equipo de Soporte técnico de Microsoft.

>[!NOTE]
>Si está desconectado de Internet o solo desea guardar los registros de forma local, use el método [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para enviar los registros. 

## <a name="view-log-collection"></a>Visualización de la recopilación de registros

El historial de registros recopilados de Azure Stack Hub aparece en la página **Recopilación de registros** en **Ayuda y soporte técnico**, con las siguientes fechas y horas:

- **Hora de recopilación**: cuándo se inició la operación de recopilación de registros.
- **Estado**: en curso o finalizada.
- **Inicio del registro**: inicio del período de tiempo durante el cual desea llevar a cabo la recopilación.
- **Fin del registro**: final del período de tiempo.
- **Tipo**: si se trata de una recopilación de registros manual o proactiva.

![Colecciones de registros en Ayuda y soporte técnico](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="proactive-diagnostic-log-collection-alerts"></a>Alertas de la recopilación proactiva de registros de diagnóstico

Si está habilitada, la recopilación proactiva de registros carga los registros solo cuando se produce uno de los siguientes eventos.

Por ejemplo, **Error de actualización** es una alerta que desencadena la recopilación proactiva de registros de diagnóstico. Si la opción está habilitada, los registros de diagnóstico se capturarán de forma proactiva durante un error de actualización para ayudar a Soporte técnico de Microsoft a solucionar el problema. Los registros de diagnóstico solo se recopilan cuando se produce la alerta **Error de actualización**.

| Título de la alerta | FaultIdType |
|---|---|
|No se puede conectar al servicio remoto | UsageBridge.NetworkError|
|Error de actualización | Urp.UpdateFailure |
|Infraestructura del proveedor de recursos de almacenamiento o dependencias no disponibles |    StorageResourceProviderDependencyUnavailable |
|Nodo no conectado al controlador| ServerHostNotConnectedToController |  
|Error de publicación de rutas | SlbMuxRoutePublicationFailure |
|Almacén de datos interno del proveedor de recursos de almacenamiento no disponible |    StorageResourceProvider. DataStoreConnectionFail |
|Error del dispositivo de almacenamiento | Microsoft.Health.FaultType.VirtualDisks.Detached |
|El controlador de mantenimiento no puede acceder a la cuenta de almacenamiento | Microsoft.Health.FaultType.StorageError |
|Se perdió la conectividad con un disco físico | Microsoft.Health.FaultType.PhysicalDisk.LostCommunication |
|Blob Service no se está ejecutando en un nodo | StorageService.The.blob.service.is.not.running.on.a.node-Critical |
|rol de infraestructura incorrecto | Microsoft.Health.FaultType.GenericExceptionFault |
|Errores de Table service | StorageService.Table.service.errors-Critical |
|Un recurso compartido de archivos está por encima del 80 % de uso | Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |
|Scale unit node is offline (Nodo de la unidad de escalado desconectado) | FRP.Heartbeat.PhysicalNode |
|Instancia del rol de infraestructura no disponible | FRP.Heartbeat.InfraVM |
|Instancia del rol de infraestructura no disponible  | FRP.Heartbeat.NonHaVm |
|El rol de infraestructura, administración de directorios, ha detectado errores de sincronización de hora | DirectoryServiceTimeSynchronizationError |
|Expiración de certificado externo pendiente | CertificateExpiration.ExternalCert.Warning |
|Expiración de certificado externo pendiente | CertificateExpiration.ExternalCert.Critical |
|No se puede aprovisionar máquinas virtuales para la clase y tamaño específicos debido a la baja capacidad de memoria | AzureStack.ComputeController.VmCreationFailure.LowMemory |
|Node inaccessible for virtual machine placement (Nodo inaccesible para la colocación de la máquina virtual) | AzureStack.ComputeController.HostUnresponsive |
|Error de la copia de seguridad  | AzureStack.BackupController.BackupFailedGeneralFault |
|Se omitió la copia de seguridad programada debido a un conflicto con operaciones erróneas    | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |

## <a name="how-the-data-is-handled"></a>Cómo se controlan los datos

Si habilita el **envío de registros proactivo**, acepta que Microsoft recopile periódicamente los registros relativos únicamente a las alertas de mantenimiento del sistema de Azure Stack Hub. También acepta y da su consentimiento a la carga y retención de dichos registros en una cuenta de Azure Storage administrada y controlada por Microsoft.

Los datos se usarán solo para solucionar problemas de alertas de mantenimiento del sistema, no para marketing, publicidad ni ningún otro propósito comercial sin su consentimiento. Los datos se pueden conservar durante un máximo de 90 días y cualquier dato que Microsoft recopile se controlará según nuestros [procedimientos de privacidad estándar](https://privacy.microsoft.com/).

Los datos recopilados previamente con su consentimiento no se verán afectados por la revocación del permiso.

## <a name="see-also"></a>Consulte también

[Control de datos de clientes y registro de Azure Stack Hub](./azure-stack-data-collection.md)
