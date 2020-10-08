---
title: Búsqueda del identificador de nube
description: Cómo buscar el identificador de nube en Ayuda y soporte técnico de Azure Stack Hub.
author: myoungerman
ms.topic: article
ms.date: 10/08/2019
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e14a2a000b52f09f664112467ce8694134243681
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778236"
---
# <a name="find-your-cloud-id"></a>Búsqueda del identificador de nube

En este tema se explica cómo obtener el identificador de nube mediante el portal de administración o el punto de conexión con privilegios. El identificador de nube es el identificador único para el seguimiento de los datos de compatibilidad cargados desde una unidad de escalado específica. Cuando los registros de diagnóstico se cargan para el análisis de compatibilidad, el identificador de nube es la forma en que los registros se asocian a esa unidad de escalado.

## <a name="use-the-administrator-portal"></a>Uso del portal de administración

1. Abra el portal de administrador. 
1. Seleccione **Region management** (Administración de regiones).

   ![Captura de pantalla del panel](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Seleccione **Properties** (Propiedades) y copie el valor de **Stamp Cloud ID** (Id. de nube del stamp).

   ![Captura de pantalla de las propiedades de la región con el identificador de nube del stamp](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>Uso del punto de conexión con privilegios

1. Abra una sesión de PowerShell con privilegios elevados y ejecute el siguiente script. Reemplace la dirección IP de la máquina virtual del punto de conexión con privilegios y las credenciales del administrador de la nube correspondientes al entorno. 

   ```powershell
   $ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

   $password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
   $cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)
   $session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

   $stampInfo = Invoke-Command -Session $session { Get-AzureStackStampInformation }
   if ($session) {
       Remove-PSSession -Session $session
   }

   $stampInfo.CloudID
   ```

## <a name="next-steps"></a>Pasos siguientes

* [Envío de registros de forma proactiva](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively)
* [Envío inmediato de registros](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now)
