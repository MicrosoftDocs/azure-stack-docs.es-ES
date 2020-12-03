---
title: Implementación de plantillas con Visual Studio en Azure Stack Hub
description: Aprenda a implementar las plantillas con Visual Studio en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 894e04e8e7b54d9e87d51af93c98f9abfd074aee
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525360"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>Implementación de plantillas en Azure Stack Hub con Visual Studio

Puede usar Visual Studio para implementar plantillas de Azure Resource Manager en Azure Stack Hub.

## <a name="to-deploy-a-template"></a>Para implementar una plantilla

1. [Instale Azure Stack Hub y conéctese](azure-stack-install-visual-studio.md) a esta infraestructura con Visual Studio.
2. Abra Visual Studio.
3. Seleccione **Archivo** y, a continuación, seleccione **Nuevo**. En **Nuevo proyecto**, seleccione **Grupo de recursos de Azure**.
4. Escriba un **Nombre** para el nuevo proyecto y, a continuación, seleccione **Aceptar**.
5. En **Seleccionar plantilla de Azure**, elija **Azure Stack Hub Quickstart** (Inicio rápido de Azure Stack Hub) en la lista desplegable.
6. Haga clic en **101-create-storage-account** y, a continuación, seleccione **Aceptar**.
7. En el nuevo proyecto, expanda el nodo **Plantillas** en el **Explorador de soluciones** para ver las plantillas disponibles.
8. En el **Explorador de soluciones**, elija el nombre del proyecto y, a continuación, seleccione **Implementar**. Seleccione **Nueva implementación**.
9. En **Implementar en grupo de recursos**, use la lista desplegable **Suscripción** para seleccionar la suscripción de Microsoft Azure Stack Hub.
10. En la lista **Grupo de recursos**, elija un grupo de recursos existente o cree uno.
11. En la lista **Ubicación del grupo de recursos**, elija una ubicación y seleccione **Implementar**.
12. En **Editar parámetros**, escriba los valores para los parámetros (que varían según la plantilla) y seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de plantillas con la línea de comandos](azure-stack-deploy-template-command-line.md)
* [Desarrollo de plantillas para Azure Stack Hub](azure-stack-develop-templates.md)
