---
title: Recopilación proactiva de registros de diagnóstico en Azure Stack Hub
description: Aprenda a configurar la recopilación proactiva de registros de diagnóstico en Ayuda y soporte técnico de Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 04/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 04/17/2020
ms.openlocfilehash: 067817e29858113fc22b596aa394d6e0942e921a
ms.sourcegitcommit: b2b0fe629d840ca8d5b6353a90f1fcb392a73bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2020
ms.locfileid: "85377177"
---
# <a name="proactive-diagnostic-log-collection-in-azure-stack-hub"></a>Recopilación proactiva de registros de diagnóstico en Azure Stack Hub

::: moniker range=">= azs-2002"

Ahorre tiempo con el equipo de soporte técnico al cliente mediante la recopilación proactiva de registros de diagnóstico cuando se genera una alerta en Azure Stack Hub.

Si es necesario investigar el estado de mantenimiento del sistema, los registros se pueden cargar automáticamente para su análisis antes de abrir una incidencia para el equipo de Soporte técnico de Microsoft.

## <a name="steps-to-configure-proactive-log-collection"></a>Pasos para configurar la recopilación proactiva de registros

Siga estos pasos para configurar la recopilación proactiva de registros. La recopilación de registros proactiva puede deshabilitarse y habilitarse de nuevo en cualquier momento.  

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
1. Abra la **información general de la ayuda y el soporte técnico**.
1. Si aparece el banner, seleccione **Enable proactive log collection** (Habilitar la recopilación proactiva de registros). =

   ![Captura de pantalla que muestra dónde habilitar la recopilación de registros en Ayuda y soporte técnico](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

   O bien, puede seleccionar **Settings** (Configuración), establecer **Proactive log collection** (Recopilación proactiva de registros) en **Enable** (Habilitar) y seleccionar **Save** (Guardar).

   ![Captura de pantalla que muestra dónde habilitar la recopilación de registros en Ayuda y soporte técnico](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

Recomendamos configurar la característica de recopilación de registros de diagnóstico automática para optimizar su experiencia de asistencia al cliente y recopilación de registros.

En caso de que deban investigarse las condiciones de mantenimiento del sistema, Soporte técnico de Microsoft puede cargar los registros automáticamente para su análisis.

## <a name="create-an-azure-blob-container-sas-url"></a>Creación de una dirección URL de SAS de contenedor de blobs de Azure

Para poder configurar la recopilación de registros automática, deberá obtener una firma de acceso compartido (SAS) para un contenedor de blobs. Una SAS le permite conceder acceso a los recursos de su cuenta de almacenamiento sin compartir las claves de cuenta.

Puede guardar los archivos de registro de Azure Stack Hub en un contenedor de blobs en Azure y, a continuación, proporcionar la dirección URL de SAS donde Soporte técnico de Microsoft puede recopilar los registros.

### <a name="prerequisites"></a>Requisitos previos

Puede usar un contenedor de blobs nuevo o existente en Azure. Para crear un contenedor de blobs en Azure, necesita al menos el [rol de colaborador de Storage Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) o el [permiso específico](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). Los administradores globales también tienen el permiso necesario.

Para ver procedimientos recomendados sobre cómo elegir parámetros para la cuenta de almacenamiento de recopilación de registros automática, consulte [Procedimientos recomendados para la recopilación de registros automática de Azure Stack Hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md). Para más información sobre los tipos de cuentas de almacenamiento, consulte [Introducción a las cuentas de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

### <a name="create-a-blob-storage-account"></a>Creación de una cuenta de almacenamiento de blobs

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione **Cuentas de almacenamiento** > **Agregar**.
1. Cree un contenedor de blobs con esta configuración:

   - **Suscripción**: Elija la suscripción de Azure.
   - **Grupo de recursos**: especifique un grupo de recursos.
   - **Nombre de la cuenta de almacenamiento**: especifique un nombre de cuenta de almacenamiento único.
   - **Ubicación**: elija un centro de datos según la directiva de su compañía.
   - **Rendimiento**: Estándar.
   - **Tipo de cuenta**: StorageV2 (de uso general v2).
   - **Replication** (Replicación): Almacenamiento con redundancia local (LRS).
   - **Nivel de acceso**: Esporádico.

   ![Propiedades del contenedor de blobs](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Seleccione **Revisar y crear** y, luego, **Crear**.  

### <a name="create-a-blob-container"></a>Creación de un contenedor de blobs

1. Una vez que la implementación se haya realizado correctamente, seleccione **Ir al recurso**. También puede anclar la cuenta de almacenamiento al Panel para facilitar el acceso.
1. Seleccione **Explorador de Storage (versión preliminar)** , haga clic con el botón derecho en **Contenedores de blobs** y seleccione **Crear contenedor de blobs**.
1. Escriba un nombre para el nuevo contenedor y seleccione **Aceptar**.

## <a name="create-a-sas-url"></a>Creación de una dirección URL de SAS

1. Haga clic con el botón derecho en el contenedor y seleccione **Obtener firma de acceso compartido**.
   
   ![Obtención de la firma de acceso compartido de un contenedor de blobs](media/azure-stack-automatic-log-collection/get-sas.png)

1. Elija estas propiedades:

   - Hora de inicio: opcionalmente, puede hacer que retroceda la hora de inicio
   - Hora de expiración: dos años
   - Zona horaria: UTC
   - Permisos: Lectura, Escritura y Lista

   ![Propiedades de la firma de acceso compartido](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Seleccione **Crear**.  

Copie la dirección URL y escríbala al [configurar la recopilación de registros automática](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Para obtener más información sobre las direcciones URL de SAS, consulte [Uso de firmas de acceso compartido (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="steps-to-configure-automatic-log-collection"></a>Pasos para configurar la recopilación de registros automática

Siga estos pasos para agregar la dirección URL de SAS a la interfaz de usuario de recopilación de registros:

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
1. Abra la **información general de la ayuda y el soporte técnico**.
1. Haga clic en **Automatic collection settings** (Configuración de la recopilación automática).

   ![Dónde habilitar la recopilación de registros en Ayuda y soporte técnico](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Establezca la recopilación de registros automática en **Habilitado**.
1. Escriba la dirección URL de firma de acceso compartido (SAS) del contenedor de blobs de la cuenta de almacenamiento.

   ![URL de SAS de Blob](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>La recopilación de registros automática puede deshabilitarse y habilitarse de nuevo en cualquier momento. La configuración de la dirección URL de SAS no cambiará. Si se vuelve a habilitar la recopilación de registros automática, la dirección URL de SAS indicada anteriormente se someterá a las mismas comprobaciones de validación y se rechazará una dirección URL de SAS expirada.

::: moniker-end

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

## <a name="see-also"></a>Consulte también

[Control de datos de clientes y registro de Azure Stack Hub](azure-stack-data-collection.md)
