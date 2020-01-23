---
title: Configuración de la recopilación de registros automática de Azure Stack Hub | Microsoft Docs
description: Cómo configurar la recopilación de registros automática en Ayuda y soporte técnico de Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 63ee429c37d5ec7bf9258e3e940d9dbfa3786907
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882596"
---
# <a name="configure-automatic-azure-stack-hub-diagnostic-log-collection"></a>Configuración de la recopilación de registros de diagnóstico automática de Azure Stack Hub

Recomendamos configurar la característica de recopilación de registros de diagnóstico automática para optimizar su experiencia de asistencia al cliente y recopilación de registros. En caso de que deban investigarse las condiciones de mantenimiento del sistema, los servicios de soporte al cliente (CSS) de Microsoft pueden cargar los registros automáticamente para su análisis. 

## <a name="create-an-azure-blob-container-sas-url"></a>Creación de una dirección URL de SAS de contenedor de blobs de Azure 

Para poder configurar la recopilación de registros automática, deberá obtener una firma de acceso compartido (SAS) para un contenedor de blobs. Una SAS le permite conceder acceso a los recursos de su cuenta de almacenamiento sin compartir las claves de cuenta. Puede guardar los archivos de registro de Azure Stack Hub en un contenedor de blobs en Azure y, a continuación, proporcionar la dirección URL de SAS donde CSS puede recopilar los registros. 

### <a name="prerequisites"></a>Prerequisites

Puede usar un contenedor de blobs nuevo o existente en Azure. Para crear un contenedor de blobs en Azure, necesita al menos el [rol de colaborador de Storage Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) o el [permiso específico](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). Los administradores globales también tienen el permiso necesario. 

Para ver procedimientos recomendados sobre cómo elegir parámetros para la cuenta de almacenamiento de recopilación de registros automática, consulte [Procedimientos recomendados para la recopilación de registros automática de Azure Stack Hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md). Para obtener más información sobre los tipos de cuentas de almacenamiento, consulte [Introducción a las cuentas de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

### <a name="create-a-blob-storage-account"></a>Creación de una cuenta de almacenamiento de blobs
 
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Haga clic en **Cuentas de almacenamiento** > **Agregar**. 
1. Cree un contenedor de blobs con esta configuración:
   - **Suscripción**: Elegir la suscripción de Azure
   - **Grupo de recursos**: especifique un grupo de recursos.
   - **Nombre de la cuenta de almacenamiento**: especifique un nombre de cuenta de almacenamiento único.
   - **Ubicación**: elija un centro de datos según la directiva de su compañía.
   - **Rendimiento**: elija Estándar.
   - **Tipo de cuenta**: elija StorageV2 (uso general v2). 
   - **Replication** (Replicación): elija Almacenamiento con redundancia local (LRS).
   - **Nivel de acceso**: elija Acceso esporádico.

   ![Captura de pantalla que muestra las propiedades del contenedor de blobs](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Haga clic en **Revisar y crear** y, a continuación, en **Crear**.  

### <a name="create-a-blob-container"></a>Creación de un contenedor de blobs 

1. Una vez que la implementación se haya realizado correctamente, haga clic en **Ir al recurso**. También puede anclar la cuenta de almacenamiento al Panel para facilitar el acceso. 
1. Haga clic en **Explorador de Storage (versión preliminar)** , haga clic con el botón derecho en **Contenedores de blobs** y haga clic en **Crear contenedor de blobs**. 
1. Escriba un nombre para el nuevo contenedor y haga clic en **Aceptar**.

## <a name="create-a-sas-url"></a>Creación de una dirección URL de SAS

1. Haga clic con el botón derecho en el contenedor y haga clic en **Obtener firma de acceso compartido**. 
   
   ![Captura de pantalla que muestra cómo obtener la firma de acceso compartido de un contenedor de blobs](media/azure-stack-automatic-log-collection/get-sas.png)

1. Elija estas propiedades:
   - Hora de inicio: opcionalmente, puede hacer que retroceda la hora de inicio 
   - Hora de expiración: dos años
   - Zona horaria: UTC
   - Permisos: Lectura, Escritura y Lista

   ![Captura de pantalla que muestra las propiedades de firma de acceso compartido](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Haga clic en **Crear**.  

Copie la dirección URL y escríbala al [configurar la recopilación de registros automática](azure-stack-configure-automatic-diagnostic-log-collection.md). Para obtener más información sobre las direcciones URL de SAS, consulte [Uso de firmas de acceso compartido (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 


## <a name="steps-to-configure-automatic-log-collection"></a>Pasos para configurar la recopilación de registros automática

Siga estos pasos para agregar la dirección URL de SAS a la interfaz de usuario de recopilación de registros: 

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
1. Abra la **información general de la ayuda y el soporte técnico**.
1. Haga clic en **Automatic collection settings** (Configuración de la recopilación automática).

   ![Captura de pantalla que muestra dónde habilitar la recopilación de registros en Ayuda y soporte técnico](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Establezca la recopilación de registros automática en **Habilitado**.
1. Escriba la dirección URL de firma de acceso compartido (SAS) del contenedor de blobs de la cuenta de almacenamiento.

   ![Captura de pantalla que muestra la dirección URL de SAS de blob](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>La recopilación de registros automática puede deshabilitarse y habilitarse de nuevo en cualquier momento. La configuración de la dirección URL de SAS no cambiará. Si se vuelve a habilitar la recopilación de registros automática, la dirección URL de SAS indicada anteriormente se someterá a las mismas comprobaciones de validación y se rechazará una dirección URL de SAS expirada. 

## <a name="view-log-collection"></a>Visualización de la recopilación de registros

El historial de registros recopilados de Azure Stack Hub aparece en la página **Recopilación de registros** en Ayuda y soporte técnico, con las siguientes fechas y horas:

- **Hora de la recopilación**: cuándo se inició la operación de recopilación de registros 
- **Desde fecha**: inicio del período de tiempo durante el cual desea llevar a cabo la recopilación
- **Hasta fecha**: final del período de tiempo

![Captura de pantalla que muestra recopilaciones de registros](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

Si se produce un error en la recopilación de registros de diagnóstico, compruebe que la dirección URL de SAS es válida. Si el error continúa o ve varios errores, llame a CSS de Microsoft para recibir ayuda. 

Los operadores también pueden comprobar la cuenta de almacenamiento para los registros recopilados de forma automática. Por ejemplo, en esta captura de pantalla se muestran recopilaciones de registros mediante la versión preliminar del Explorador de Storage de Azure Portal:

![Captura de pantalla que muestra recopilaciones de registros](media/azure-stack-automatic-log-collection/check-storage-account.png)

## <a name="automatic-diagnostic-log-collection-alerts"></a>Alertas de recopilación de registros de diagnóstico automática 

Si está habilitada, la recopilación automática de registros de diagnóstico solo se produce cuando es necesario. Solo las alertas de la tabla siguiente desencadenan la recopilación. 

Por ejemplo, **Error de actualización** es una alerta que desencadena la recopilación de registros de diagnóstico automática. Si la recopilación automática está habilitada, los registros de diagnóstico se capturarán de forma proactiva durante un error de actualización para ayudar a CSS a solucionar el problema. Los registros de diagnóstico solo se recopilan cuando se produce la alerta **Error de actualización**. 

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

[Control de datos de clientes y registro de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Uso de Firmas de acceso compartido (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Procedimientos recomendados para la recopilación automática de registros de Azure Stack Hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md)





