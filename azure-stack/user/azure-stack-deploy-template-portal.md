---
title: Implementación de una plantilla desde el portal en Azure Stack Hub
description: Aprenda a usar el portal de Azure Stack Hub para implementar plantillas.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 8425b4f8492e57502ca93b0be802b63601f6ba34
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704325"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Implementación de una plantilla desde el portal en Azure Stack Hub

Puede usar el portal para implementar plantillas de Azure Resource Manager en Azure Stack Hub.

## <a name="to-deploy-a-template"></a>Para implementar una plantilla

1. Inicie sesión en el portal, seleccione **+ Crear un recurso** y, a continuación, seleccione **Personalizado**.

   ![Creación de un recurso en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Seleccione **Implementación de plantillas**.

   ![Implementación de una plantilla en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Seleccione **Editar plantilla** y, a continuación, pegue el código de la plantilla JSON en la ventana de código. Seleccione **Guardar**.

   ![Edición de plantillas en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Seleccione **Editar parámetros**, escriba los valores para los parámetros que se muestran y, a continuación, seleccione **Aceptar**.

   ![Edición de parámetros en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Seleccione**Suscripción**. Elija la suscripción que quiere usar y, a continuación, seleccione **Aceptar**.

   ![Selección de una suscripción en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Seleccione **Grupo de recursos**. Elija un grupo de recursos existente o cree uno y, a continuación, seleccione **Aceptar**.

   ![Selección de un grupo de recursos en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Seleccione **Crear**. Un icono nuevo en el panel realiza el seguimiento del progreso de la implementación de la plantilla.

   ![Creación de una plantilla en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo implementar plantillas, consulte el siguiente artículo:

- [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
