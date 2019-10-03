---
title: Implementación de plantillas con Visual Studio en Azure Stack | Microsoft Docs
description: Aprenda a implementar las plantillas con Visual Studio en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: c735e2a2d58e31cdd6b9e94c461a101b2b79b914
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714691"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Implementación de plantillas en Azure Stack con Visual Studio

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar Visual Studio para implementar plantillas de Azure Resource Manager en Azure Stack.

## <a name="to-deploy-a-template"></a>Para implementar una plantilla

1. [Instale Azure Stack y conéctese](azure-stack-install-visual-studio.md) a esta infraestructura con Visual Studio.
2. Abra Visual Studio.
3. Seleccione **Archivo** y, a continuación, seleccione **Nuevo**. En **Nuevo proyecto**, seleccione **Grupo de recursos de Azure**.
4. Escriba un **Nombre** para el nuevo proyecto y, a continuación, seleccione **Aceptar**.
5. En **Seleccionar plantilla de Azure**, elija **Azure Stack Quickstart** (Inicio rápido de Azure Stack) en la lista desplegable.
6. Haga clic en **101-create-storage-account** y, a continuación, seleccione **Aceptar**.
7. En el nuevo proyecto, expanda el nodo **Plantillas** en el **Explorador de soluciones** para ver las plantillas disponibles.
8. En el **Explorador de soluciones**, elija el nombre del proyecto y, a continuación, seleccione **Implementar**. Seleccione **Nueva implementación**.
9. En **Implementar en grupo de recursos**, use la lista desplegable **Suscripción** para seleccionar la suscripción de Microsoft Azure Stack.
10. En la lista **Grupo de recursos**, elija un grupo de recursos existente o cree uno.
11. En la lista **Ubicación del grupo de recursos**, elija una ubicación y seleccione **Implementar**.
12. En **Editar parámetros**, escriba los valores para los parámetros (que varían según la plantilla) y seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de plantillas con la línea de comandos](azure-stack-deploy-template-command-line.md)
* [Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)
