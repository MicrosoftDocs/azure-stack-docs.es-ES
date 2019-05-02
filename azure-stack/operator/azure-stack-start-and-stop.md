---
title: Inicio y detención de Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo iniciar y apagar Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/19/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 6b14f5e8967567030ce854d05c53a8d26027e9a6
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307305"
---
# <a name="start-and-stop-azure-stack"></a>Inicio y detención de Azure Stack
Siga los procedimientos que se describen en este artículo para detener y reiniciar los servicios de Azure Stack correctamente. El apagado apagará físicamente la totalidad del entorno de Azure Stack. El inicio activa todos los roles de infraestructura y devuelve los recursos del inquilino al estado de energía en el que se encontraban antes del apagado.

## <a name="stop-azure-stack"></a>Detención de Azure Stack 

Realice los pasos siguientes para apagar Azure Stack:

1. Prepare todas las cargas de trabajo que se ejecutan en los recursos de inquilino del entorno de Azure Stack para el próximo apagado. 

2. Abra una sesión de punto de conexión con privilegios (PEP) desde un equipo con acceso de red a las máquinas virtuales de Azure Stack ERCS. Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md).

3. Desde el PEP, ejecute lo siguiente:

    ```powershell
      Stop-AzureStack
    ```

4. Espere a que todos los nodos físicos de Azure Stack se apaguen.

> [!Note]  
> Para comprobar el estado de encendido de un nodo físico siga las instrucciones del fabricante de equipos originales (OEM) que le proporcionó el hardware de Azure Stack. 

## <a name="start-azure-stack"></a>Inicio de Azure Stack 

Realice los pasos siguientes para iniciar Azure Stack. Siga los pasos que se indican a continuación independientemente del modo en que se detuvo Azure Stack.

1. Encienda cada uno de los nodos físicos de su entorno de Azure Stack. Consulte las instrucciones de los nodos físicos. Para ello, siga las instrucciones del fabricante de equipos originales (OEM) que le proporcionó el hardware de Azure Stack.

2. Espere hasta que se inicien los servicios de infraestructura de Azure Stack. El proceso de inicio de los servicios de infraestructura de Azure Stack puede tardar hasta dos horas en completarse. Puede comprobar el estado de inicio de Azure Stack con el cmdlet [**Get-ActionStatus**](#get-the-startup-status-for-azure-stack).

3. Asegúrese de que todos los recursos de inquilino han vuelto al estado en el que se encontraban antes del apagado. Es posible que el administrador de las cargas de trabajo tenga que volver a configurar las que se ejecutan en los recursos del inquilino después del inicio.

## <a name="get-the-startup-status-for-azure-stack"></a>Consulta del estado de inicio de Azure Stack

Realice los pasos siguientes para obtener la rutina de inicio de Azure Stack:

1. Abra una sesión de punto de conexión con privilegios desde un equipo con acceso de red a las máquinas virtuales de Azure Stack ERCS.

2. Desde el PEP, ejecute lo siguiente:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Solución de problemas de inicio y apagado de Azure Stack

Siga los pasos que se indican a continuación en caso de que los servicios de infraestructura e inquilinos no se inicien a las 2 horas de haber encendido el entorno de Azure Stack. 

1. Abra una sesión de punto de conexión con privilegios desde un equipo con acceso de red a las máquinas virtuales de Azure Stack ERCS.

2. Ejecutar: 

    ```powershell
      Test-AzureStack
      ```

3. Revise el resultado y resuelva los errores de estado. Para obtener más información, consulte [Run a validation test of Azure Stack](azure-stack-diagnostic-test.md) (Ejecutar una prueba de validación de Azure Stack).

4. Ejecutar:

    ```powershell
      Start-AzureStack
    ```

5. Si la ejecución de **Start_AzureStack** produce un error, póngase en contacto con el Soporte técnico de servicios al cliente de Microsoft. 

## <a name="next-steps"></a>Pasos siguientes 

Más información sobre las [herramientas de diagnóstico de Azure Stack](azure-stack-diagnostics.md)
