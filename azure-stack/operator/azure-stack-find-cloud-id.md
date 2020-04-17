---
title: Búsqueda del identificador de nube
description: Cómo buscar el identificador de nube en Ayuda y soporte técnico de Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e0045ae6bf76b6b4e5973f65c6c0a5f758e0d46b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520624"
---
# <a name="find-your-cloud-id"></a>Búsqueda del identificador de nube

En este tema se explica cómo obtener el identificador de nube mediante el portal del administrador o el punto de conexión con privilegios. 

## <a name="use-the-administrator-portal"></a>Uso del portal de administración

1. Abra el portal de administrador. 
1. Haga clic en **Region management** (Administración de regiones).

   ![Captura de pantalla del panel](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Haga clic en **Properties** (Propiedades) y copie el valor de **Stamp Cloud ID** (Id. de nube del stamp).

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

* [Envío proactivo de registros](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)
* [Envío inmediato de registros](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)






