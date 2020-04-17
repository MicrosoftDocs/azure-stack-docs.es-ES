---
title: Envío de los registros de diagnóstico de Azure Stack Hub a Azure mediante el punto de conexión con privilegios (PEP)
description: Aprenda a enviar los registros de diagnóstico de Azure Stack Hub a Azure mediante el punto de conexión con privilegios (PEP).
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: ca6240cb4f1e54c5e5fbfda79c46697909612063
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520564"
---
# <a name="send-azure-stack-hub-diagnostic-logs-to-azure-using-the-privileged-endpoint-pep"></a>Envío de los registros de diagnóstico de Azure Stack Hub a Azure mediante el punto de conexión con privilegios (PEP)

No es necesario proporcionar ninguna ubicación de almacenamiento de destino al utilizar este método. Si no se proporcionan los parámetros **fromDate** y **toDate**, pasan a tener una duración predeterminada de 4 horas a partir de la hora de ejecución. 

Opcionalmente, puede especificar que los parámetros **FilterByRole** o **FilterByResourceProvider** incluyan solo los registros de un rol o proveedor de recursos de valor agregado. 

Este es un script de ejemplo que se puede ejecutar mediante el punto de conexión con privilegios para recopilar registros en un sistema integrado. 


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP address. 
 
$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force 
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password) 
 
$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred 
 
$fromDate = (Get-Date).AddHours(-8) # Optional. 
$toDate = (Get-Date).AddHours(-2) # Optional. Provide the time that includes the period for your issue 
 
Invoke-Command -Session $session {Send-AzureStackDiagnosticLog -FromDate $using:fromDate -ToDate $using:toDate} 
 
if ($session) { 
    Remove-PSSession -Session $session 
} 
```

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


## <a name="next-steps"></a>Pasos siguientes

[Uso del punto de conexión con privilegios para enviar los registros de diagnóstico de Azure Stack Hub](azure-stack-get-azurestacklog.md)
