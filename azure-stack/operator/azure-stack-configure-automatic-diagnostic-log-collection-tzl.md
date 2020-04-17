---
title: Recopilación proactiva de registros de diagnóstico cuando se genera una alerta en Azure Stack Hub
description: Cómo configurar la recopilación proactiva de registros en Ayuda y soporte técnico de Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/12/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/12/2020
ms.openlocfilehash: 0c6bfe4dba2b3795e1069419d90624c8e3b55cda
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520546"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>Envío proactivo de registros de diagnóstico de Azure Stack Hub

Puede ahorrar tiempo con el equipo de soporte técnico al cliente mediante la recopilación proactiva de registros de diagnóstico cuando se genera una alerta en Azure Stack Hub.
Si es necesario investigar el estado de mantenimiento del sistema, los registros se pueden cargar automáticamente para su análisis antes de abrir una incidencia con los servicios de soporte técnico al cliente de Microsoft. 

## <a name="steps-to-configure-proactive-log-collection"></a>Pasos para configurar la recopilación proactiva de registros

Siga estos pasos para configurar la recopilación proactiva de registros. La recopilación de registros automática puede deshabilitarse y habilitarse de nuevo en cualquier momento.  

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
1. Abra la **información general de la ayuda y el soporte técnico**.
1. Si aparece el banner, haga clic en **Enable proactive log collection** (Habilitar la recopilación proactiva de registros). 

   ![Captura de pantalla que muestra dónde habilitar la recopilación de registros en Ayuda y soporte técnico](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


   O bien, puede hacer clic en **Settings** (Configuración), establecer **Proactive log collection** (Recopilación proactiva de registros) en **Enable** (Habilitar) y hacer clic en **Save** (Guardar).

   ![Captura de pantalla que muestra dónde habilitar la recopilación de registros en Ayuda y soporte técnico](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)


## <a name="view-log-collection"></a>Visualización de la recopilación de registros

El historial de registros recopilados de Azure Stack Hub aparece en la página **Recopilación de registros** en Ayuda y soporte técnico, con las siguientes fechas y horas:

- **Hora de recopilación**: cuándo se inició la operación de recopilación de registros
- **Estado**: en curso o finalizada
- **Inicio del registro**: inicio del período de tiempo durante el cual desea llevar a cabo la recopilación
- **Fin del registro**: final del período de tiempo
- **Tipo**: si se trata de una recopilación de registros manual o proactiva 


![Captura de pantalla que muestra recopilaciones de registros](media/azure-stack-help-and-support/azure-stack-log-collection.png)


## <a name="proactive-diagnostic-log-collection-alerts"></a>Alertas de la recopilación proactiva de registros de diagnóstico 

Si está habilitada, la recopilación proactiva de registros carga los registros solo cuando se produce uno de los siguientes eventos. 

Por ejemplo, **Error de actualización** es una alerta que desencadena la recopilación proactiva de registros de diagnóstico. Si la opción está habilitada, los registros de diagnóstico se capturarán de forma proactiva durante un error de actualización para ayudar a los servicios de soporte técnico al cliente a solucionar el problema. Los registros de diagnóstico solo se recopilan cuando se produce la alerta **Error de actualización**. 

|Título de la alerta  | FaultIdType|    
|-------------|------------|
|No se puede conectar al servicio remoto |  UsageBridge.NetworkError|
|Error de actualización |    Urp.UpdateFailure   |          
|Infraestructura del proveedor de recursos de almacenamiento o dependencias no disponibles |  StorageResourceProviderDependencyUnavailable     |     
|Nodo no conectado al controlador|  ServerHostNotConnectedToController   |     
|Error de publicación de rutas |    SlbMuxRoutePublicationFailure | 
|Almacén de datos interno del proveedor de recursos de almacenamiento no disponible |    StorageResourceProvider. DataStoreConnectionFail     |       
|Error del dispositivo de almacenamiento | Microsoft.Health.FaultType.VirtualDisks.Detached   |      
|El controlador de mantenimiento no puede acceder a la cuenta de almacenamiento | Microsoft.Health.FaultType.StorageError |    
|Se perdió la conectividad con un disco físico |    Microsoft.Health.FaultType.PhysicalDisk.LostCommunication    |    
|Blob Service no se está ejecutando en un nodo | StorageService.The.blob.service.is.not.running.on.a.node-Critical | 
|rol de infraestructura incorrecto |    Microsoft.Health.FaultType.GenericExceptionFault |        
|Errores de Table service | StorageService.Table.service.errors-Critical |              
|Un recurso compartido de archivos está por encima del 80 % de uso |    Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |       
|Scale unit node is offline (Nodo de la unidad de escalado desconectado) | FRP.Heartbeat.PhysicalNode |  
|Instancia del rol de infraestructura no disponible | FRP.Heartbeat.InfraVM   |    
|Instancia del rol de infraestructura no disponible  |    FRP.Heartbeat.NonHaVm     |        
|El rol de infraestructura, administración de directorios, ha detectado errores de sincronización de hora |  DirectoryServiceTimeSynchronizationError |     
|Expiración de certificado externo pendiente |  CertificateExpiration.ExternalCert.Warning |
|Expiración de certificado externo pendiente |  CertificateExpiration.ExternalCert.Critical |
|No se puede aprovisionar máquinas virtuales para la clase y tamaño específicos debido a la baja capacidad de memoria |  AzureStack.ComputeController.VmCreationFailure.LowMemory |
|Node inaccessible for virtual machine placement (Nodo inaccesible para la colocación de la máquina virtual) |  AzureStack.ComputeController.HostUnresponsive | 
|Error de la copia de seguridad  | AzureStack.BackupController.BackupFailedGeneralFault |    
|Se omitió la copia de seguridad programada debido a un conflicto con operaciones erróneas  | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |   


## <a name="see-also"></a>Consulte también

[Control de datos de clientes y registro de Azure Stack Hub](azure-stack-data-collection.md)





