---
title: Uso de herramientas de transferencia de datos en Azure Stack Hub Storage
description: Obtenga información sobre las herramientas de transferencia de datos de Azure Stack Hub Storage.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: cde8b474a27b577beca757a806a6411d934465c0
ms.sourcegitcommit: a90b146769279ffbdb09c68ca0506875a867e177
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98123721"
---
# <a name="use-data-transfer-tools-in-azure-stack-hub-storage"></a>Uso de herramientas de transferencia de datos en Azure Stack Hub Storage

Azure Stack Hub proporciona un conjunto de servicios de almacenamiento para discos, blobs, tablas, colas y funciones de administración de cuentas. Hay algunas herramientas de Azure Storage disponibles para administrar o mover datos a Azure Stack Hub Storage o desde este servicio. En este artículo se proporciona información general de las herramientas disponibles.

Los requisitos determinan cuál de las siguientes herramientas es la que más le conviene:

* [AzCopy](#azcopy)

    Utilidad de línea de comandos específica para el almacenamiento que se puede descargar para copiar datos de un objeto en otro dentro de las cuentas de almacenamiento o entre ellas.

* [Azure PowerShell](#azure-powershell)

    Un shell de línea de comandos basado en tareas y un lenguaje de scripts diseñado especialmente para la administración del sistema.

* [CLI de Azure](#azure-cli)

    Una herramienta multiplataforma de código abierto que proporciona un conjunto de comandos para trabajar con las plataformas Azure y Azure Stack Hub.

* [Explorador de Microsoft Azure Storage](#microsoft-azure-storage-explorer)

    Una aplicación independiente fácil de usar con una interfaz de usuario.

* [Blobfuse](#blobfuse)

    Controlador virtual del sistema de archivos para Azure Blob Storage que permite acceder a los datos de blobs en bloques existentes en la cuenta de almacenamiento a través del sistema de archivos Linux.

Dadas las diferencias en los servicios de almacenamiento entre Azure y Azure Stack Hub, puede haber ciertos requisitos específicos de cada herramienta que se describen en las secciones siguientes. Para obtener una comparación entre Azure Stack Hub Storage y Azure Storage, consulte [Azure Stack Hub Storage: diferencias y consideraciones](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy es una utilidad de línea de comandos diseñada para copiar datos tanto a Microsoft Azure Blob Storage y Table Storage como desde ellos mediante comandos sencillos con un rendimiento óptimo. Puede copiar datos de un objeto en otro dentro de las cuentas de almacenamiento o entre ellas.

### <a name="download-and-install-azcopy"></a>Descarga e instalación de AzCopy

::: moniker range=">=azs-1811"
* Para la actualización 1811 o versiones más recientes, [descargue AzCopy v10+](/azure/storage/common/storage-use-azcopy-v10#download-azcopy).
::: moniker-end

::: moniker range="<azs-1811"
* Para versiones anteriores (actualizaciones 1802 a 1809), [descargue AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
::: moniker-end

### <a name="azcopy-101-configuration-and-limits"></a>Configuración y límites de AzCopy 10.1

AzCopy 10.1 ahora se puede configurar para usar versiones anteriores de la API. De este modo se habilita la compatibilidad (limitada) con Azure Stack Hub.
Para configurar la versión de la API de AzCopy compatible con Azure Stack Hub, establezca la variable de entorno `AZCOPY_DEFAULT_SERVICE_API_VERSION` en `2017-11-09`.

| Sistema operativo | Get-Help  |
|--------|-----------|
| **Windows** | En un símbolo del sistema, use: `set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`<br> En PowerShell, use: `$env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"`|
| **Linux** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |
| **macOS** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |

En AzCopy 10.1, las siguientes características son compatibles con Azure Stack Hub:

| Característica | Acciones admitidas |
| --- | --- |
|Manage container (Administrar contenedor)|Crear un contenedor<br>Enumerar el contenido de los contenedores
|Manage job (Administrar trabajo)|Mostrar trabajos<br>Reanudar un trabajo
|Remove blob (Quitar blob)|Quitar un solo blob<br>Quitar el directorio virtual de manera completa o parcial
|Carga de archivo|Cargar un archivo<br>Subir un directorio<br>Subir el contenido de un directorio
|Descarga de archivo|Descarga de un archivo<br>Descargar un directorio<br>Descargar el contenido de un directorio
|Synchronize file (Sincronizar archivo)|Sincronizar un contenedor con un sistema de archivos local<br>Sincronizar un sistema de archivos local con un contenedor

   > [!NOTE]
   > * Azure Stack Hub no permite proporcionar credenciales de autorización a AzCopy mediante Azure Active Directory (AD). Debe acceder a los objetos de almacenamiento en Azure Stack Hub con un token de firma de acceso compartido (SAS).
   > * Azure Stack Hub no admite la transferencia de datos sincrónica entre dos ubicaciones de blob de Azure Stack Hub ni entre Azure Storage y Azure Stack Hub. No se puede usar "azcopy cp" para mover datos de Azure Stack Hub a Azure Storage (ni viceversa) directamente con AzCopy 10.1.

### <a name="azcopy-command-examples-for-data-transfer"></a>Ejemplos del comando AzCopy para la transferencia de datos

En los ejemplos siguientes se muestran varios escenarios habituales para copiar datos a los blobs de Azure Stack Hub y desde ellos. Para obtener más información, consulte [Introducción a AzCopy](/azure/storage/common/storage-use-azcopy-v10).

### <a name="download-all-blobs-to-a-local-disk"></a>Descarga de todos los blobs en un disco local

```
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

### <a name="upload-single-file-to-virtual-directory"></a>Carga de un solo archivo en el directorio virtual

```
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

### <a name="azcopy-known-issues"></a>Problemas conocidos de AzCopy

 - Las operaciones de AzCopy en un almacén de archivos no están disponibles, ya que el almacenamiento de archivos aún no está disponible en Azure Stack Hub.
 - Si quiere transferir datos entre dos ubicaciones de blob de Azure Stack Hub, o entre Azure Stack Hub y Azure Storage mediante AzCopy 10.1, primero debe descargar los datos en una ubicación local y luego volver a cargarlos en el directorio de destino en Azure Stack Hub o Azure Storage. Puede usar AzCopy 7.1 y especificar la transferencia con la opción **/SyncCopy** para copiar los datos.  
 - La versión de Linux de AzCopy solo admite la actualización 1802 o versiones posteriores y no admite Table service.
 - Si desea copiar datos desde y hacia su servicio Azure Table Storage, [instale AzCopy versión 7.3.0.](https://aka.ms/azcopyforazurestack20171109)
 
## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell es un módulo que proporciona cmdlets para administrar servicios tanto en Azure como en Azure Stack Hub. Se trata de un shell de línea de comandos basado en tareas y un lenguaje de scripts diseñado especialmente para la administración del sistema.

### <a name="install-and-configure-powershell-for-azure-stack-hub"></a>Instalación y configuración de PowerShell para Azure Stack Hub

Se requieren módulos de Azure PowerShell compatibles con Azure Stack Hub para trabajar con Azure Stack Hub. Para más información, consulte [Instalación de PowerShell para Azure Stack Hub](../operator/powershell-install-az-module.md) y [Configuración del entorno de PowerShell del usuario de Azure Stack Hub](azure-stack-powershell-configure-user.md).

### <a name="powershell-sample-script-for-azure-stack-hub"></a>Script de ejemplo de PowerShell para Azure Stack Hub 
### <a name="az-modules"></a>[Modules de Az](#tab/az1)

En este ejemplo se supone que ha [instalado PowerShell para Azure Stack Hub](../operator/powershell-install-az-module.md) correctamente. Este script le ayudará a completar la configuración y a pedir las credenciales del inquilino de Azure Stack Hub para agregar la cuenta al entorno local de PowerShell. Luego el script establece la suscripción predeterminada de Azure, crea una nueva cuenta de almacenamiento en Azure, crea un nuevo contenedor en ella y carga un archivo de imagen existente (blob) en dicho contenedor. Una vez que el script enumere todos los blobs de dicho contenedor, creará un nuevo directorio de destino en el equipo local y descargará el archivo de imagen.

1. Instale los [módulos de Azure PowerShell compatibles con Azure Stack Hub](../operator/powershell-install-az-module.md).
2. Descargue las [herramientas necesarias para trabajar con Azure Stack Hub](../operator/azure-stack-powershell-download.md).
3. Abra **Windows PowerShell ISE** y haga clic en **Ejecutar como administrador**, **Archivo** > **Nuevo** para crear un archivo de script nuevo.
4. Copie el siguiente script y péguelo en el archivo de script nuevo.
5. Actualice las variables del script en función de su configuración.
   > [!NOTE]
   > Este script se tiene que ejecutar en el directorio raíz de **AzureStack_Tools**.

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack Hub environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an Az environment that targets your Azure Stack Hub instance
Add-AzEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder

# end
```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)

En este ejemplo se supone que ha [instalado PowerShell para Azure Stack Hub](../operator/azure-stack-powershell-install.md) correctamente. Este script le ayudará a completar la configuración y a pedir las credenciales del inquilino de Azure Stack Hub para agregar la cuenta al entorno local de PowerShell. Luego el script establece la suscripción predeterminada de Azure, crea una nueva cuenta de almacenamiento en Azure, crea un nuevo contenedor en ella y carga un archivo de imagen existente (blob) en dicho contenedor. Una vez que el script enumere todos los blobs de dicho contenedor, creará un nuevo directorio de destino en el equipo local y descargará el archivo de imagen.

1. Instale los [módulos de Azure PowerShell compatibles con Azure Stack Hub](../operator/azure-stack-powershell-install.md).
2. Descargue las [herramientas necesarias para trabajar con Azure Stack Hub](../operator/azure-stack-powershell-download.md).
3. Abra **Windows PowerShell ISE** y haga clic en **Ejecutar como administrador**, **Archivo** > **Nuevo** para crear un archivo de script nuevo.
4. Copie el siguiente script y péguelo en el archivo de script nuevo.
5. Actualice las variables del script en función de su configuración.
   > [!NOTE]
   > Este script se tiene que ejecutar en el directorio raíz de **AzureStack_Tools**.

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack Hub environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack Hub instance
Add-AzureRMEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRMAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRMSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRMResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRMStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRMCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureRMureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureRMureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureRMureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder

# end
```

---



### <a name="powershell-known-issues"></a>Problemas conocidos de PowerShell

La versión actual del módulo de Azure PowerShell compatible con Azure Stack Hub es la 1.2.11 para las operaciones de usuario. Tiene ciertas diferencias con la versión más reciente de Azure PowerShell. Esta diferencia afecta al funcionamiento de los servicios de almacenamiento de la siguiente forma:

El formato del valor de retorno de `Get-AzStorageAccountKey` en la versión 1.2.11 tiene dos propiedades: `Key1` y `Key2`, mientras que la versión actual de Azure devuelve una matriz que contiene todas las claves de la cuenta.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

Para obtener más información, consulte [Get-AzureRMStorageAccountKey](/powershell/module/Az.storage/Get-AzStorageAccountKey).

## <a name="azure-cli"></a>Azure CLI

La CLI de Azure es la forma de usar la línea de comandos de Azure para administrar los recursos de Azure. Puede instalarla en macOS, Linux y Windows, y ejecutarla desde la línea de comandos.

La CLI de Azure está optimizada para administrar recursos de Azure desde la línea de comandos y para compilar scripts de automatización que funcionen con Azure Resource Manager. Proporciona muchas de las funciones que se encuentran en el portal de Azure Stack Hub, lo que incluye el acceso a datos enriquecidos.

Azure Stack Hub requiere la versión 2.0 o superior de la CLI de Azure. Para más información acerca de cómo instalar y configurar la CLI de Azure con Azure Stack Hub, consulte [Instalación y configuración de la CLI de Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). Para más información sobre cómo usar la CLI de Azure para realizar varias tareas relativas al trabajo con recursos en la cuenta de almacenamiento de Azure Stack Hub, consulte [Uso de la CLI de Azure con Azure Storage](/azure/storage/storage-azure-cli).

### <a name="azure-cli-sample-script-for-azure-stack-hub"></a>Script de ejemplo de la CLI de Azure para Azure Stack Hub

Cuando haya completado la instalación y configuración de la CLI, puede probar los siguientes pasos para trabajar con un pequeño script de ejemplo de shell pequeño para interactuar con los recursos de almacenamiento Azure Stack Hub. Este script realiza las acciones siguientes:

* Crea un contenedor nuevo en su cuenta de almacenamiento.
* Carga un archivo existente (en forma de blob) en el contenedor.
* Enumera todos los blobs del contenedor.
* Descarga el archivo a un destino del equipo local que especifique.

Antes de ejecutar este script, asegúrese de que puede conectarse correctamente a la instancia de Azure Stack Hub de destino e iniciar sesión en ella.

1. Abra el editor de texto que prefiera; copie y pegue el script anterior en el editor.
2. Actualice las variables del script para incluir sus opciones de configuración.
3. Después de actualizar las variables necesarias, guarde el script y salga del editor. En los siguientes pasos se da por hecho que ha llamado al script **my_storage_sample.sh**.
4. Marque el script como archivo ejecutable si es necesario: `chmod +x my_storage_sample.sh`
5. Ejecute el script. Por ejemplo, en Bash: `./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack Hub storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Explorador de Microsoft Azure Storage

El Explorador de Azure Storage es una aplicación independiente de Microsoft. Permite trabajar fácilmente con los datos de Azure Storage y Azure Stack Hub Storage en equipos Windows, macOS y Linux. Si desea una manera fácil de administrar los datos de Azure Stack Hub Storage, considere la posibilidad de usar el Explorador de Microsoft Azure Storage.

* Para más información sobre cómo configurar el Explorador de Azure Storage para que funcione con Azure Stack Hub, consulte [Conexión del Explorador de Storage a una suscripción de Azure Stack Hub](azure-stack-storage-connect-se.md).
* Para obtener más información sobre el Explorador de Microsoft Azure Storage, vea [Introducción al Explorador de Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer).

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) es un controlador virtual del sistema de archivos para Azure Blob Storage que permite acceder a los datos de blobs en bloques existentes en la cuenta de almacenamiento a través del sistema de archivos Linux. Azure Blob Storage es un servicio de almacenamiento de objetos y, por lo tanto, no tiene un espacio de nombres jerárquico. Blobfuse proporciona el espacio de nombres mediante el esquema de directorio virtual con el uso de la barra oblicua `/` como delimitador. Blobfuse funciona tanto en Azure como en Azure Stack Hub. 

Para obtener más información sobre el montaje de almacenamiento de blobs como un sistema de archivos con Blobfuse en Linux, vea [Cómo montar el almacenamiento de blobs como sistema de archivos con blobfuse](/azure/storage/blobs/storage-how-to-mount-container-linux). 

En Azure Stack Hub, se debe especificar *blobEndpoint* mientras se configuran las credenciales de la cuenta de almacenamiento con accountName, accountKey/sasToken y containerName.

En el Kit de desarrollo de Azure Stack (ASDK), *blobEndpoint* debe ser `myaccount.blob.local.azurestack.external`. En el sistema integrado de Azure Stack Hub, si no está seguro del punto de conexión, póngase en contacto con el administrador de la nube.

*accountKey* y *sasToken* solo se pueden configurar de uno en uno. Cuando se proporciona una clave de cuenta de almacenamiento, el archivo de configuración de credenciales tiene el siguiente formato:

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Cuando se proporciona un token de acceso compartido, el archivo de configuración de credenciales tiene el siguiente formato:

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Pasos siguientes

* [Conexión del Explorador de Storage a una suscripción de Azure Stack Hub](azure-stack-storage-connect-se.md)
* [Introducción al Explorador de Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md) (Azure Stack Storage: diferencias y consideraciones)
* [Introducción a Microsoft Azure Storage](/azure/storage/common/storage-introduction)
