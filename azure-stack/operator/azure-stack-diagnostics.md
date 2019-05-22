---
title: Diagnósticos de Azure Stack
description: Cómo recopilar archivos de registro de diagnósticos en Azure Stack
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 04/30/2019
ms.author: mabrigg
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: aaa929924399eb7e999bccdaa45e5ae9ce0dfa3a
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985357"
---
# <a name="azure-stack-diagnostics-tools"></a>Herramientas de diagnóstico de Azure Stack

Azure Stack es una gran colección de componentes que funcionan juntos e interactúan entre sí. Todos estos componentes generan sus propios registros únicos. Esto puede dificultar el diagnóstico de problemas, especialmente en el caso de errores procedentes de varios componentes de Azure Stack que interactúan.

Nuestras herramientas de diagnóstico ayudan a asegurar que el mecanismo de recopilación de registros es fácil y eficaz. En el siguiente diagrama se muestra cómo funcionan las herramientas de recopilación de registros en Azure Stack:

![Herramientas de diagnóstico de Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)

## <a name="trace-collector"></a>Recopilador de seguimiento

El recopilador de seguimiento está habilitado de forma predeterminada y se ejecuta continuamente en segundo plano para recopilar el todos los registros de Seguimiento de eventos para Windows (ETW) de los servicios de componentes de Azure Stack. Los registros de ETW se almacenan en un recurso compartido local común con un límite de antigüedad de cinco días. Una vez que se alcanza este límite, se eliminan los archivos más antiguos cuando se crean nuevos. El tamaño máximo predeterminado permitido para cada archivo es de 200 MB. Se realiza una comprobación de tamaño cada dos minutos y si el archivo actual es > = 200 MB, se guarda y se genera otro. También hay un límite de 8 GB en el tamaño total del archivo generado por cada sesión de eventos.

## <a name="log-collection-tool"></a>Herramienta de recopilación de registros

El cmdlet de PowerShell **Get-AzureStackLog** se puede utilizar para recopilar registros de todos los componentes en un entorno de Azure Stack. Los guarda en archivos ZIP en una ubicación definida por el usuario. Si el equipo de soporte técnico de Azure Stack necesita los registros para ayudarle a solucionar un problema, puede que le pidan que ejecute esta herramienta.

> [!CAUTION]
> Estos archivos de registro pueden contener información de identificación personal (PII). Tenga esto en cuenta antes de publicar los archivos de registro.

Estos son algunos ejemplos de tipos de registro que se recopilan:

* **Registros de implementación de Azure Stack**
* **Registros de eventos de Windows**
* **Registros de Panther**
* **Registros de clúster**
* **Registros de diagnóstico de almacenamiento**
* **Registros de ETW**

El Recopilador de seguimiento recopila estos archivos y los guarda en un recurso compartido. El cmdlet de PowerShell **Get-AzureStackLog** se puede utilizar después para recopilarlos cuando sea necesario.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Para ejecutar Get-AzureStackLog en un sistema Azure Stack integrado

Para ejecutar la herramienta de recopilación de registros en un sistema integrado, debe tener acceso al punto de conexión con privilegios (PEP). Este es un script de ejemplo que se puede ejecutar mediante el PEP para recopilar registros en un sistema integrado:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Ejecución de Get-AzureStackLog en un sistema Kit de desarrollo de Azure Stack (ASDK)

Siga estos pasos para ejecutar `Get-AzureStackLog` en un equipo que hospede ASDK.

1. Inicie sesión como **AzureStack\CloudAdmin** en el equipo host de ASDK.
2. Abra una nueva ventana de PowerShell como administrador.
3. Ejecute el cmdlet de PowerShell en **AzureStackLog Get**.

#### <a name="examples"></a>Ejemplos

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

* Recopilar registros y almacenarlos en el contenedor de blobs de Azure Storage especificado. La sintaxis general para esta operación es como sigue:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -OutputSasUri "<Blob service SAS Uri>"
  ```

  Por ejemplo: 

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS Token>"
  ```

  Para generar el token de SAS para la cuenta de almacenamiento, se requieren los permisos siguientes:

  * Acceso al servicio Blob Storage
  * Acceso al tipo de recurso del contenedor

  Para generar un valor de URI de SAS que usar para el parámetro `-OutputSasUri`, realice los pasos siguientes:

  1. Para crear una cuenta de almacenamiento, siga los pasos de este [artículo](/azure/storage/common/storage-quickstart-create-account).
  2. Abra una instancia del Explorador de Azure Storage.
  3. Conéctese a la cuenta de almacenamiento creada en el paso 1.
  4. Vaya a **Blob Containers** (Contenedores de blobs) en **Servicios de almacenamiento**.
  5. Seleccione **Crear contenedor nuevo**.
  6. Haga clic con el botón derecho en el contenedor y haga clic en **Get Shared Access Signature** (Obtener firma de acceso compartido).
  7. Seleccione la **Hora de inicio** y la **Hora de fin** válidas según sus requisitos.
  8. Para los permisos necesarios, seleccione **Lectura**, **Escritura** y **Lista**.
  9. Seleccione **Crear**.
  10. Obtendrá una firma de acceso compartido. Copie la parte de la dirección URL y proporciónesela al parámetro `-OutputSasUri`.
```powershell
Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Consideraciones sobre los parámetros para ASDK y sistemas integrados

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

  |   |   |   |    |
  | - | - | - | -  |
  |ACS                   |CacheService                   |IBC                            |OEM|
  |ACSDownloadService    |Proceso                        |InfraServiceController         |OnboardRP|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
  |ACSMonitoringService  |Dominio                         |KeyVaultInternalDataPlane      |SeedRingServices|
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Almacenamiento|
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
  |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
  |AzureMonitor          |Puerta de enlace                        |NC                             |WAS|
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
  |BRP                   |HintingServiceV2               |NRP                            |   |
  |CA                    |HRP                            |OboService                     |   |
  |   |   |   |    |

### <a name="additional-considerations"></a>Consideraciones adicionales

* Este comando tardará un rato en ejecutarse, en función de los roles que recopilen los registros. Entre los factores que contribuyen, se incluye la duración especificada para la colección de registros y el número de nodos en el entorno de Azure Stack.
* Cuando se ejecuta la recopilación de registros, compruebe la nueva carpeta creada en el parámetro **OutputSharePath** especificado en el comando.
* Cada rol tiene sus registros dentro de archivos ZIP individuales. Según el tamaño de los registros recopilados, un rol puede dividir sus registros en varios archivos ZIP. Para tal rol, si desea que todos los archivos de registro se descompriman en una sola carpeta, use una herramienta que pueda descomprimir de forma masiva (por ejemplo, 7zip). Seleccione todos los archivos comprimidos para el rol y seleccione **extraer aquí**. Así se descomprimen todos los archivos de registro para ese rol en una sola carpeta combinada.
* También se crea un archivo denominado **Get-AzureStackLog_Output.log** en la carpeta que contiene los archivos de registro comprimidos. Este archivo es un registro de la salida del comando, que se puede usar para solucionar problemas durante la recopilación de registros. En ocasiones, el archivo de registro incluye entradas `PS>TerminatingError` que se pueden omitir sin problema, a menos que falten los archivos de registro esperados después de ejecutar la recopilación de registros.
* Para investigar un error específico, es posible que sean necesarios registros de más de un componente.

  * Los registros de eventos y del sistema de todas las máquinas virtuales de la infraestructura se recopilan en el rol **VirtualMachines**.
  * Los registros de eventos y del sistema de todos los hosts se recopilan en el rol **BareMetal**.
  * Los registros de eventos de Hyper-V y del clúster de conmutación por error se recopilan en el rol **Storage**.
  * Los registros de ACS se recopilan en los roles **Storage** y **ACS**.

> [!NOTE]
> Se aplican límites en el tamaño y la antigüedad para los registros recopilados, ya que resulta esencial para garantizar un uso eficaz del espacio de almacenamiento y para asegurarse de que este no recibe demasiados registros. Sin embargo, cuando se diagnostica un problema, a veces se necesitan registros que podrían no existir ya debido a estos límites. Por lo tanto, le **recomendamos encarecidamente** que descargue los registros en un espacio de almacenamiento externo (una cuenta de almacenamiento de Azure, un dispositivo de almacenamiento local adicional, etc.) cada 8-12 horas y los conserve allí de 1 a 3 meses según necesite. Asegúrese también de que la ubicación de almacenamiento está cifrada.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Puede usar el cmdlet **Invoke-AzureStackOnDemandLog** para generar registros a petición para ciertos roles (consulte la lista al final de esta sección). Los registros generados por este cmdlet no están presentes de forma predeterminada en el paquete de registro que recibe al ejecutar el cmdlet **Get-AzureStackLog**. Además, se recomienda recopilar estos registros solo cuando así lo solicite el equipo de soporte técnico de Microsoft.

Actualmente, no se puede usar el parámetro `-FilterByRole` para filtrar la colección de registros de los roles siguientes:

* OEM
* NC
* SLB
* Puerta de enlace

#### <a name="example-of-collecting-on-demand-logs"></a>Ejemplo de recopilación de registros a petición

```powershell
$ip = "<IP address of the PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<domain name>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s
{
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" #Provide the supported on-demand role name : OEM, NC, SLB , Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate

}

if($s)
{
   Remove-PSSession $s
}
```

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas de Microsoft Azure Stack](azure-stack-troubleshooting.md)
