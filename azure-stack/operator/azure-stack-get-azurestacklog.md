---
title: Uso del punto de conexión con privilegios (PEP) para recopilar registros de diagnóstico
description: Aprenda a recopilar los registros de diagnóstico a petición en Azure Stack Hub mediante el portal del administrador o un script de PowerShell.
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: df5a98e8526181a84d8b214fbdf82eb1dba00088
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520654"
---
# <a name="send-azure-stack-hub-diagnostic-logs-by-using-the-privileged-endpoint-pep"></a>Envío de los registros de diagnóstico de Azure Stack Hub mediante el punto de conexión con privilegios

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->


Para ejecutar Get-AzureStackLog en un sistema integrado, debe tener acceso al punto de conexión con privilegios. Este es un script de ejemplo que se puede ejecutar mediante el punto de conexión con privilegios para recopilar los registros. Si va a cancelar una recopilación de registros en ejecución para iniciar una nueva, espere 5 minutos antes de iniciar la nueva recopilación de registros y escriba `Remove-PSSession -Session $session`.


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="examples"></a>Ejemplos

* Recopilar todos los registros de todos los roles:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* Recopilar registros de los roles VirtualMachines y BareMetal:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* Recopilar registros de los roles VirtualMachines y BareMetal, filtrando por fecha los archivos de registro de las últimas 8 horas:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Recopilar registros de los roles VirtualMachines y BareMetal, filtrando por fecha los archivos de registro del período comprendido entre las últimas entre 8 y 2 horas:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* Recopilar registros de implementaciones de inquilino que ejecutan clústeres de Kubernetes autoadministrados (motor de AKS) en Azure Stack. Los registros de Kubernetes deben almacenarse en una cuenta de almacenamiento del inquilino en un formato que permita que el intervalo de tiempo de recopilación se aplique también a estos. 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  Por ejemplo:

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
  ```

* Recopilación de registros para los proveedores de recursos de valor añadido. La sintaxis general es:
 
  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Para recopilar los registros de IoT Hub: 

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider IotHub
  ```
 
  Para recopilar los registros de Event Hubs:

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider eventhub
  ```
 
  Para recopilar los registros de Azure Stack Edge:

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvide databoxedge
  ```

* Recopilar registros y almacenarlos en el contenedor de blobs de Azure Storage especificado. La sintaxis general para esta operación es como sigue:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Por ejemplo:

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Este procedimiento es útil para cargar registros. Aunque no tenga ningún recurso compartido de SMB al que se pueda acceder o acceso a Internet, puede crear una cuenta de almacenamiento de blobs en Azure Stack Hub para transferir los registros y, después, usar el cliente para recuperarlos.  

  Para generar el token de SAS para la cuenta de almacenamiento, se requieren los permisos siguientes:

  * Acceso al servicio Blob Storage.
  * Acceso al tipo de recurso del contenedor.

  Para generar un valor de URI de SAS que se pueda usar para el parámetro `-OutputSasUri`, realice los pasos siguientes:

  1. Para crear una cuenta de almacenamiento, siga los pasos [de este artículo](/azure/storage/common/storage-quickstart-create-account).
  2. Abra una instancia del Explorador de Azure Storage.
  3. Conéctese a la cuenta de almacenamiento creada en el paso 1.
  4. Vaya a **Blob Containers** (Contenedores de blobs) en **Servicios de almacenamiento**.
  5. Seleccione **Crear contenedor nuevo**.
  6. Haga clic con el botón derecho en el contenedor y haga clic en **Get Shared Access Signature** (Obtener firma de acceso compartido).
  7. Seleccione la **Hora de inicio** y la **Hora de fin** válidas según sus requisitos.
  8. Para los permisos necesarios, seleccione **Lectura**, **Escritura** y **Lista**.
  9. Seleccione **Crear**.
  10. Obtendrá una firma de acceso compartido. Copie la parte de la dirección URL y proporciónesela al parámetro `-OutputSasUri`.

### <a name="parameter-considerations"></a>Consideraciones sobre los parámetros 

* Los parámetros **OutputSharePath** y **OutputShareCredential** se utilizan para almacenar registros en una ubicación especificada por el usuario.

* Los parámetros **FromDate** y **ToDate** pueden utilizarse para recopilar registros durante un período de tiempo determinado. Si no se especifican los parámetros, se recopilan los registros de las últimas cuatro horas de forma predeterminada.

* Use el parámetro **FilterByNode** para filtrar los registros por nombre de equipo. Por ejemplo:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* Use el parámetro **FilterByLogType** para filtrar los registros por tipo. Puede elegir filtrar por archivo, por recurso compartido o por WindowsEvent. Por ejemplo:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* Puede usar el parámetro **TimeOutInMinutes** para establecer el tiempo de espera para la colección de registros. Se establece en 150 (2,5 horas) de forma predeterminada.
* La recopilación de registros de archivos de copia de seguridad está deshabilitada de forma predeterminada. Para habilitarla, utilice el parámetro de modificador **IncludeDumpFile**.
* Actualmente, no se puede usar el parámetro **FilterByRole** para filtrar la colección de registros de los roles siguientes:

  |   |   |   |    |     |
  | - | - | - | -  |  -  |
  |ACS                   |CA                             |HRP                            |OboService                |VirtualMachines|
  |ACSBlob               |CacheService                   |IBC                            |OEM                       |WAS            |
  |ACSDownloadService    |Proceso                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB                       |         |
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL                       |         |
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP                       |         |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Storage                   |         |
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController         |         |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP                       |         |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |SupportBridgeController   |         |
  |AzureBridge           |FRP                            |MonRP                          |SupportRing               |         |
  |AzureMonitor          |Puerta de enlace                        |NC                             |SupportRingServices       |         |
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |SupportBridgeRP           |         |
  |BRP                   |HintingServiceV2               |NRP                            |UsageBridge               |         |
  |   |   |   |    |     | 

### <a name="additional-considerations-on-diagnostic-logs"></a>Consideraciones adicionales sobre los registros de diagnóstico

* Este comando tardará un rato en ejecutarse, en función de los roles que recopilen los registros. Entre los factores que contribuyen, se incluye la duración especificada para la recopilación de registros y el número de nodos en el entorno de Azure Stack Hub.
* Cuando se ejecuta la recopilación de registros, compruebe la nueva carpeta creada en el parámetro **OutputSharePath** especificado en el comando.
* Cada rol tiene sus registros dentro de archivos ZIP individuales. Según el tamaño de los registros recopilados, un rol puede dividir sus registros en varios archivos ZIP. Para ese rol, si desea que todos los archivos de registro se descompriman en una sola carpeta, use una herramienta que pueda descomprimir de forma masiva. Seleccione todos los archivos comprimidos para el rol y seleccione **extraer aquí**. Todos los archivos de registro de ese rol se descomprimirán en una sola carpeta combinada.
* También se crea un archivo denominado **Get-AzureStackLog_Output.log** en la carpeta que contiene los archivos de registro comprimidos. Este archivo es un registro de la salida del comando, que se puede usar para solucionar problemas durante la recopilación de registros. En ocasiones, el archivo de registro incluye entradas `PS>TerminatingError` que se pueden omitir sin problema, a menos que falten los archivos de registro esperados después de ejecutar la recopilación de registros.
* Para investigar un error específico, es posible que sean necesarios registros de más de un componente.

  * Los registros de eventos y del sistema de todas las máquinas virtuales de la infraestructura se recopilan en el rol **VirtualMachines**.
  * Los registros de eventos y del sistema de todos los hosts se recopilan en el rol **BareMetal**.
  * Los registros de eventos de Hyper-V y del clúster de conmutación por error se recopilan en el rol **Storage**.
  * Los registros de ACS se recopilan en los roles **Storage** y **ACS**.

> [!NOTE]
> Se aplican límites en el tamaño y la antigüedad de los registros recopilados, ya que es esencial garantizar un uso eficaz del espacio de almacenamiento y evitar que este reciba demasiados registros. Sin embargo, cuando se diagnostica un problema, a veces se necesitan registros que podrían no existir ya debido a estos límites. Por lo tanto, es **muy recomendable** que descargue los registros en un espacio de almacenamiento externo (una cuenta de almacenamiento de Azure, un dispositivo de almacenamiento local adicional, etc.) cada 8-12 horas y los conserve allí de 1 a 3 meses según necesite. Debe asegurarse también de que la ubicación de almacenamiento está cifrada.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Puede usar el cmdlet **Invoke-AzureStackOnDemandLog** para generar registros a petición para ciertos roles (consulte la lista al final de esta sección). Los registros generados por este cmdlet no están presentes de forma predeterminada en el paquete de registro que recibe al ejecutar el cmdlet **Get-AzureStackLog**. Además, se recomienda recopilar estos registros solo cuando así lo solicite el equipo de soporte técnico de Microsoft.

Actualmente, no se puede usar el parámetro `-FilterByRole` para filtrar la colección de registros de los roles siguientes:

* OEM
* NC
* SLB
* Puerta de enlace

#### <a name="example-of-collecting-on-demand-diagnostic-logs"></a>Ejemplo de recopilación de registros a petición

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session {
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" # Provide the supported on-demand role name e.g. OEM, NC, SLB, Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate
}

if ($session) {
   Remove-PSSession -Session $session
}
```

### <a name="how-diagnostic-log-collection-using-the-pep-works"></a>Cómo funciona la recopilación de registros de diagnóstico con PEP

Las herramientas de diagnóstico de Azure Stack Hub ayudan a que la recopilación de registros sea fácil y eficaz. En el diagrama siguiente se muestra cómo funcionan las herramientas de diagnóstico:

![Diagrama de flujo de trabajo de las herramientas de diagnóstico de Azure Stack Hub](media/azure-stack-diagnostics/get-azslogs.png)


#### <a name="trace-collector"></a>Recopilador de seguimiento

El recopilador de seguimiento está habilitado de forma predeterminada y se ejecuta continuamente en segundo plano para recopilar todos los registros de Seguimiento de eventos para Windows (ETW) de los servicios de componentes de Azure Stack Hub. Los registros de ETW se almacenan en un recurso compartido local común con un límite de antigüedad de cinco días. Una vez que se alcanza este límite, se eliminan los archivos más antiguos cuando se crean nuevos. El tamaño máximo predeterminado permitido para cada archivo es de 200 MB. Se realiza una comprobación de tamaño cada dos minutos y si el archivo actual es > = 200 MB, se guarda y se genera otro. También hay un límite de 8 GB en el tamaño total del archivo generado por cada sesión de eventos.

#### <a name="get-azurestacklog"></a>Get-AzureStackLog

El cmdlet de PowerShell Get-AzureStackLog se puede utilizar para recopilar registros de todos los componentes en un entorno de Azure Stack Hub. Los guarda en archivos ZIP en una ubicación definida por el usuario. Si el equipo de soporte técnico de Azure Stack Hub necesita los registros para ayudarle a solucionar un problema, puede que le pidan que ejecute Get-AzureStackLog.

> [!CAUTION]
> Estos archivos de registro pueden contener información de identificación personal (PII). Tenga esto en cuenta antes de publicar los archivos de registro.

Estos son algunos ejemplos de tipos de registro que se recopilan:

* **Registros de implementación de Azure Stack Hub**
* **Registros de eventos de Windows**
* **Registros de Panther**
* **Registros de clúster**
* **Registros de diagnóstico de almacenamiento**
* **Registros de ETW**

El Recopilador de seguimiento recopila estos archivos y los guarda en un recurso compartido. Get-AzureStackLog se puede utilizar después para recopilarlos cuando sea necesario.

