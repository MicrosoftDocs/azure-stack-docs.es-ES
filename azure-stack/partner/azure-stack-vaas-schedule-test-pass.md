---
title: Programación de una prueba en el portal de Azure Stack Hub Validation
titleSuffix: Azure Stack Hub
description: Aprenda a programar una prueba en el portal de Azure Stack Hub Validation.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: ccaae2e6d7625687f7739cba4fa77eda3ab91520
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625465"
---
# <a name="schedule-a-test-in-azure-stack-validation-portal"></a>Programación de una prueba en el portal de Azure Stack Validation

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Programe una prueba en el portal de Microsoft Azure Stack Validation para su solución de Azure Stack Hub. Una solución de validación como servicio (VaaS) representa una solución de Azure Stack Hub con una lista de materiales de hardware en particular. Puede programar una prueba para comprobar que el hardware puede ejecutar Azure Stack Hub.

Para comprobar la solución, cree el flujo de trabajo para una prueba. Un flujo de trabajo VaaS funciona dentro del contexto de una solución VaaS. Representa una serie de conjuntos de pruebas que actúan sobre la funcionalidad de implementación de Azure Stack Hub en su hardware. Agregue los parámetros del entorno de su solución y seleccione una o varias pruebas para que se ejecuten en su solución.

Aunque el flujo de trabajo de prueba superada se puede usar para ejecutar cualquier prueba proporcionada por VaaS, incluidas las pruebas de los flujos de trabajo de validación, los resultados del flujo de trabajo de pruebas superadas no se consideran *oficiales*. Para obtener información acerca de los flujos de trabajo de validación oficiales, consulte [Flujos de trabajo](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Prerrequisitos

Antes de seguir este tutorial de inicio rápido, finalice las siguientes tareas:

- [Configuración los recursos de validación como servicio](azure-stack-vaas-set-up-resources.md).
- [Implementación del agente local](azure-stack-vaas-local-agent.md) (obligatorio).
- [Conceptos clave de la validación como servicio](azure-stack-vaas-key-concepts.md) (obligatorio).

## <a name="start-a-workflow"></a>Inicio de un flujo de trabajo

![Inicio de sesión en el portal de VaaS](media/vaas_portalsignin.png)

Inicie sesión en el portal, seleccione o cree una solución y, luego, seleccione la solución.

1. Inicie sesión en el [portal de VaaS](https://azurestackvalidation.com).
2. Escriba el nombre de una solución existente o seleccione **Nueva solución** para crear una solución. Para obtener instrucciones, consulte [Creación de una solución en el portal de VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-azure-stack-hub-validation-portal).
3. Seleccione **Iniciar** en el icono **Pruebas superadas**.

## <a name="specify-parameters"></a>Especificar parámetros

![Especificar parámetros en el portal de VaaS](media/vaas_test_pass_parameters.png)

Proporcione los parámetros aplicables a todas las pruebas del flujo de trabajo.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Seleccione **Siguiente** para seleccionar las pruebas que va a programar.

## <a name="select-tests-to-run"></a>Selección de las pruebas que se ejecutarán

Las pruebas que seleccione se programarán una vez creado el flujo de trabajo.

1. Seleccione las pruebas que quiere ejecutar en el flujo de trabajo.

    Si quiere invalidar los parámetros habituales (los parámetros proporcionados en la sección anterior) para cualquier prueba, seleccione el vínculo **Editar** a un lado para especificar valores nuevos.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Seleccione **Siguiente** para revisar el flujo de trabajo.

## <a name="review-and-submit"></a>Revisión y envío

Finalice la creación del flujo de trabajo.

1. Revise la información que se muestra.

    El servicio crea el flujo de trabajo con la información proporcionada y se programan las pruebas seleccionadas.

    Si algo parece incorrecto, use el botón **Anterior** para ir a una sección anterior.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md)
