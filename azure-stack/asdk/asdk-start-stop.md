---
title: Inicio y detención del kit de desarrollo de Azure Stack
description: Obtenga información sobre cómo iniciar y detener el Kit de desarrollo de Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 07/18/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 1801174977ef0370e8433e6c41e2d8c13b497ae8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77694040"
---
# <a name="start-and-stop-the-asdk"></a>Inicio y detención del kit de desarrollo de Azure Stack
No se recomienda simplemente reiniciar el equipo host del ASDK. En su lugar, siga los procedimientos que se describen en este artículo para detener y reiniciar los servicios de ASDK correctamente.

## <a name="stop-azure-stack"></a>Detención de Azure Stack 
Para detener correctamente los servicios de Azure Stack y el equipo que hospeda ASDK, use los siguientes comandos de PowerShell:

1. Inicie sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK.
2. Abra PowerShell como administrador (no como PowerShell ISE).
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
Los servicios de ASDK deben iniciarse automáticamente con el equipo que los hospeda. Sin embargo, el tiempo de inicio de los servicios de infraestructura del ASDK varía según el rendimiento de la configuración de hardware del equipo host del ASDK. En ocasiones, el reinicio correcto de todos los servicios puede tardar horas.

Independientemente de cómo se detuviera ASDK, debe seguir estos pasos para verificar que todos los servicios de Azure Stack están iniciados y funcionan por completo una vez encendido el equipo que los hospeda: 

1. Encienda el equipo que hospeda ASDK. 
2. Inicie sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK.
3. Abra PowerShell como administrador (no como PowerShell ISE).
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
2. Abra PowerShell como administrador (no como PowerShell ISE).
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
Para más información sobre la herramienta de diagnóstico de Azure Stack y el registro de problemas, consulte [Herramientas de diagnóstico de Azure Stack](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).
