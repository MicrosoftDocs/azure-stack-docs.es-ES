---
title: Recopilación de datos de Azure Stack HCI
description: En este tema se describe el diseño y las directivas pertinentes para los datos de diagnóstico que recopila Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 91f578bcb0dc3a9d2debd086e6ed95cb591de90d
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2020
ms.locfileid: "97053145"
---
# <a name="azure-stack-hci-data-collection"></a>Recopilación de datos de Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2

En este tema se describen los datos necesarios recopilados para mantener Azure Stack HCI seguro, actualizado y funcionando según lo previsto para la fase de disponibilidad general (GA) de diciembre de 2020.

Es necesario que Microsoft proporcione los datos descritos a continuación para Azure Stack HCI. Estos datos se recopilan una vez al día y los eventos de recopilación de datos se pueden ver en los registros de eventos. Azure Stack HCI recopila los datos mínimos necesarios para mantener los clústeres actualizados, seguros y funcionando correctamente.

   > [!IMPORTANT]
   > Los datos que se describen a continuación y que recopila Azure Stack HCI son independientes de los datos de diagnóstico de Windows, los cuales se pueden configurar para distintos niveles de recopilación. En Azure Stack HCI, el valor predeterminado para la recopilación de datos de diagnóstico de Windows es Seguridad (desactivada), lo que significa que no se enviarán datos de diagnóstico de Windows a menos que el administrador cambie la configuración de los datos de diagnóstico. Para obtener más información, consulte [Configurar los datos de diagnóstico de Windows en la organización](/windows/privacy/configure-windows-diagnostic-data-in-your-organization). Microsoft es un controlador independiente de los datos de diagnóstico de Windows recopilados en conexión con Azure Stack HCI. Microsoft administrará los datos de diagnóstico de Windows de acuerdo con la [declaración de privacidad de Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="data-collection-and-residency"></a>Recopilación y residencia de datos

Estos datos de Azure Stack HCI: 

- No se envían a Microsoft hasta que el producto se registra en Azure. Si Azure Stack HCI no está registrado, esta recopilación de datos se detiene.
- Se registran en el canal de eventos Microsoft-AzureStack-HCI/Analytic. 
- Están en formato JSON, para que los administradores del sistema puedan examinar y analizar los datos que se envían.
- Se almacenan en Estados Unidos en un centro de datos protegido que administra Microsoft.

## <a name="data-retention"></a>Retención de datos

Una vez que Azure Stack HCI recopila estos datos, se conservan durante 90 días. Es posible que los datos agregados, sin identificación, se conserven más tiempo.

## <a name="what-data-is-collected"></a>¿Qué datos se recopilan?

Azure Stack HCI recopila:

- Información sobre los servidores como la versión del sistema operativo, el modelo de procesador, el número de núcleos de este, el tamaño de la memoria, el identificador de clúster y el hash del identificador de hardware
- Lista de las características de servidor de Azure Stack HCI instaladas (por ejemplo, BitLocker)
- Información necesaria para calcular la confiabilidad del sistema operativo Azure Stack HCI
- Información necesaria para calcular la confiabilidad de los datos de estado de la colección
- Información recopilada del registro de eventos para errores específicos como, por ejemplo, el error al descargar la actualización
- Información para calcular la confiabilidad del almacenamiento
- Información para calcular la confiabilidad del disco físico
- Información para calcular la confiabilidad del cifrado de volumen
- Información para calcular la confiabilidad y el rendimiento de la reparación de Espacios de almacenamiento directo
- Información para validar la seguridad del sistema operativo Azure Stack HCI
- Información para calcular la confiabilidad del estado del antivirus o antimalware del sistema operativo Azure Stack HCI
- Información para poner en correlación la confiabilidad de los componentes de la red
- Información para poner en correlación el rendimiento de la red
- Información para poner en correlación la confiabilidad de las actualizaciones y las instalaciones
- Información para medir la confiabilidad de Hyper-V
- Información para medir y poner en correlación la confiabilidad de los componentes de la agrupación en clústeres
- Información para realizar un seguimiento del éxito de la característica de actualización compatible con clústeres (CAU)
- Información para medir y poner en correlación la confiabilidad de la característica de recuperación ante desastres
- Información para describir los límites de ancho de banda de SMB que se aplican a los servidores de Azure Stack HCI

## <a name="view-this-data"></a>Visualización de estos datos

1. Habilite el registro analítico con el siguiente comando de PowerShell:

   ```PowerShell
   wevtutil sl Microsoft-AzureStack-HCI/Analytic /e:True
   ```

2. Mire el registro para ver los datos recopilados:

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest
   ```

3. Dé formato a los datos para su exportación:

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest `
   | Where-Object Id -eq 802 `
   | ForEach-Object { 
       [pscustomobject] @{
           TimeCreated = $_.TimeCreated 
           EventName=$_.Properties[0].Value 
           Value=$_.Properties[1].Value 
       } 
   }
   ```
 
El resultado debe parecerse a este:

```shell
TimeCreated            EventName                                                  Value
-----------            ---------                                                  -----
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.Core                   {"OEMName":"Microsoft Corporation"...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ProductFeatures        {"InstalledFeatures":["Server-Core...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.OSReliability          {"DailyDirtyRestarts":0,"WeeklyDir...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.DiagnosticHealth       {"DailySuccessfulDiagnosticUploads...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ErrorSummary           {"ErrorSummary":[{"EventName":"Win...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.VolumeSummary          {"VolumeCount":2,"HealthyVolumeCou...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.DiskSummary            {"DiskCount":33,"Summary":[]}
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.BitlockerVolumeSummary {"BitlockerVolumeCount":0,"Summary...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageErrors          {"ErrorSummary":[{"EventName":"Sto...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageRepairSummary   {"DailyRepairStartCount":0,"Weekly...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.TrustedPlatformModule  {"Manufacturer":"MSFT","Manufactur...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.MicrosoftDefender      {"AMEngineVersion":"1.1.17600.5","...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkInfo            {"NetworkDirect":true,"NetworkDire...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkAdapterSummary  {"NetworkAdapterGroup":[{"DriverNa...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.OSDeploy               {"OSInstallType":0}
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.ClusterProperties      {"Id":"fd2fc061-b924-4d61-a45b-3b3...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.DisasterRecovery       {"IsDisasterRecoveryEnabled":false...
```
