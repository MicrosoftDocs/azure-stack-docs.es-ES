---
title: Inicio y detención del Kit de desarrollo de Azure Stack (ASDK) | Microsoft Docs
description: Aprenda a iniciar y detener el Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: d3289ae5db5e54ee6ad5d1948653ef82946d16ff
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267459"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Inicio y detención del Kit de desarrollo de Azure Stack (ASDK)
No se recomienda simplemente reiniciar el equipo que hospeda ASDK. En su lugar, siga los procedimientos que se describen en este artículo para detener y reiniciar los servicios de ASDK correctamente. 

## <a name="stop-azure-stack"></a>Detención de Azure Stack 
Para detener correctamente los servicios de Azure Stack y el equipo que hospeda ASDK, use los siguientes comandos de PowerShell:

1. Inicie sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK.
2. Abra PowerShell como administrador (no ISE de PowerShell).
3. Ejecute los comandos siguientes para establecer una sesión de punto de conexión con privilegios (PEP): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. A continuación, en la sesión PEP, use el cmdlet **Stop-AzureStack** para detener los servicios de Azure Stack y apagar el equipo que hospeda ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Revise la salida de PowerShell para asegurarse de que todos los servicios de Azure Stack se detienen correctamente antes de apagar el equipo que hospeda ASDK. El apagado tarda varios minutos.

## <a name="start-azure-stack"></a>Inicio de Azure Stack 
Los servicios de ASDK deben iniciarse automáticamente con el equipo que los hospeda. Sin embargo, el tiempo de arranque de los servicios de la infraestructura ASDK varía según el rendimiento de la configuración de hardware del equipo que los hospeda. En ocasiones, el reinicio correcto de todos los servicios puede tardar horas.

Independientemente de cómo se detuviera ASDK, debe seguir estos pasos para verificar que todos los servicios de Azure Stack están iniciados y funcionan por completo una vez encendido el equipo que los hospeda: 

1. Encienda el equipo que hospeda ASDK. 
2. Inicie sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK.
3. Abra PowerShell como administrador (no ISE de PowerShell).
4. Ejecute los comandos siguientes para establecer una sesión de punto de conexión con privilegios (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. A continuación, en la sesión PEP, ejecute los comandos siguientes para comprobar el estado de arranque de los servicios de Azure Stack:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Revise la salida para asegurarse de que los servicios de Azure Stack se han reiniciado correctamente.

Para más información sobre los procedimientos recomendados para detener y reiniciar los servicios de Azure Stack correctamente, consulte [Inicio y detención de Azure Stack](../operator/azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Solución de problemas de arranque y detención 
Si los servicios de Azure Stack no se inician correctamente en un plazo de dos horas tras el encendido del equipo que hospeda ASDK, realice estos pasos:

1. Inicie sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK.
2. Abra PowerShell como administrador (no ISE de PowerShell).
3. Ejecute los comandos siguientes para establecer una sesión de punto de conexión con privilegios (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. A continuación, en la sesión PEP, ejecute los comandos siguientes para comprobar el estado de arranque de los servicios de Azure Stack:

   ```powershell
   Test-AzureStack
   ```
5. Revise la salida y resuelva los errores. Para obtener más información, consulte [Run a validation test of Azure Stack](../operator/azure-stack-diagnostic-test.md) (Ejecutar una prueba de validación de Azure Stack).
6. Reinicie los servicios de Azure Stack desde la sesión PEP; para ello, ejecute el cmdlet **Start-AzureStack**:

   ```powershell
   Start-AzureStack
   ```

Si la ejecución de **Start-AzureStack** produce un error, visite el [foro de soporte técnico de Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) para obtener ayuda de solución de problemas con ASDK. 

## <a name="next-steps"></a>Pasos siguientes 
Para más información sobre la herramienta de diagnóstico de Azure Stack y el registro de problemas, consulte [Herramientas de diagnóstico de Azure Stack](../operator/azure-stack-diagnostics.md).
