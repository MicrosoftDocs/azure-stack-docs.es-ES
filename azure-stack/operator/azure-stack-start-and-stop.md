---
title: Inicio y detención
titleSuffix: Azure Stack Hub
description: Aprenda a iniciar y detener Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 2c8211606ae797b4a88da1c268a7fb36b7de214b
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512378"
---
# <a name="start-and-stop-azure-stack-hub"></a>Inicio y detención de Azure Stack Hub

Siga los procedimientos que se describen en este artículo para apagar y reiniciar los servicios de Azure Stack Hub correctamente. La *detención* apagará físicamente la totalidad del entorno de Azure Stack Hub. El *inicio* activa todos los roles de infraestructura y devuelve los recursos del inquilino al estado de encendido en el que se encontraban antes del apagado.

## <a name="stop-azure-stack-hub"></a>Detención de Azure Stack Hub

Realice los pasos siguientes para detener o apagar Azure Stack Hub:

1. Prepare todas las cargas de trabajo que se ejecutan en los recursos de inquilino del entorno de Azure Stack Hub para el próximo apagado.

2. Abra una sesión de punto de conexión con privilegios (PEP) desde una máquina con acceso de red a las máquinas virtuales de Azure Stack Hub ERCS. Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](azure-stack-privileged-endpoint.md).

3. Desde el PEP, ejecute lo siguiente:

    ```powershell
      Stop-AzureStack
    ```

4. Espere a que todos los nodos físicos de Azure Stack Hub se apaguen.

> [!Note]
> Para comprobar el estado de encendido de un nodo físico siga las instrucciones del fabricante de equipos originales (OEM) que le proporcionó el hardware de Azure Stack Hub.

## <a name="start-azure-stack-hub"></a>Inicio de Azure Stack Hub

Realice los pasos siguientes para iniciar Azure Stack Hub. Siga los pasos que se indican a continuación independientemente del modo en que se detuvo Azure Stack Hub.

1. Encienda cada uno de los nodos físicos de su entorno de Azure Stack Hub. Consulte las instrucciones de encendido de los nodos físicos. Para ello, siga las instrucciones del fabricante de equipos originales (OEM) que le proporcionó el hardware de Azure Stack Hub.

2. Espere hasta que se inicien los servicios de infraestructura de Azure Stack Hub. El proceso de inicio de los servicios de infraestructura de Azure Stack Hub puede tardar hasta dos horas en completarse. Puede comprobar el estado de inicio de Azure Stack Hub con el [cmdlet **Get-ActionStatus**](#get-the-startup-status-for-azure-stack-hub).

3. Asegúrese de que todos los recursos de inquilino han vuelto al estado en el que se encontraban antes del apagado. Es posible que el administrador de las cargas de trabajo tenga que volver a configurar las que se ejecutan en los recursos del inquilino después del inicio.

## <a name="get-the-startup-status-for-azure-stack-hub"></a>Consulta del estado de inicio de Azure Stack Hub

Realice los pasos siguientes para obtener el estado de la rutina de inicio de Azure Stack Hub:

1. Abra una sesión de punto de conexión con privilegios desde una máquina con acceso de red a las máquinas virtuales de Azure Stack Hub ERCS.

2. Desde el PEP, ejecute lo siguiente:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack-hub"></a>Solución de problemas de inicio y apagado de Azure Stack Hub

Realice los pasos que se indican a continuación en caso de que los servicios de infraestructura e inquilinos no se inicien a las 2 horas de haber encendido el entorno de Azure Stack Hub.

1. Abra una sesión de punto de conexión con privilegios desde una máquina con acceso de red a las máquinas virtuales de Azure Stack Hub ERCS.

2. Ejecutar:

    ```powershell
      Test-AzureStack
      ```

3. Revise el resultado y resuelva los errores de estado. Para más información, consulte [Validación del estado del sistema de Azure Stack Hub](azure-stack-diagnostic-test.md).

4. Ejecutar:

    ```powershell
      Start-AzureStack
    ```

5. Si la ejecución de **Start-AzureStack** produce un error, póngase en contacto con el Soporte técnico de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las [herramientas de diagnóstico de Azure Stack Hub](azure-stack-diagnostic-log-collection-overview-tzl.md)
