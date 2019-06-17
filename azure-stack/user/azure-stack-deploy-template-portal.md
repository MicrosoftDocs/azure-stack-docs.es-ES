---
title: Implementación de una plantilla desde el portal en Azure Stack | Microsoft Docs
description: Aprenda a usar Azure Stack Portal para implementar plantillas.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 8f61667351a6d22094d5f8a0ba39348cc92549a8
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692016"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack"></a>Implementación de una plantilla desde el portal en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar el portal para implementar plantillas de Azure Resource Manager en Azure Stack.

## <a name="to-deploy-a-template"></a>Para implementar una plantilla

1. Inicie sesión en el portal, seleccione **+ Crear un recurso** y, a continuación, seleccione **Personalizado**.

   ![Creación de un recurso en Azure Stack Portal](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Seleccione **Implementación de plantillas**.

   ![Implementación de una plantilla en Azure Stack Portal](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Seleccione **Editar plantilla** y, a continuación, pegue el código de la plantilla JSON en la ventana de código. Seleccione **Guardar**.

   ![Edición de plantillas en Azure Stack Portal](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Seleccione **Editar parámetros**, escriba los valores para los parámetros que se muestran y, a continuación, seleccione **Aceptar**.

   ![Edición de parámetros en Azure Stack Portal](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Seleccione**Suscripción**. Elija la suscripción que quiere usar y, a continuación, seleccione **Aceptar**.

   ![Selección de una suscripción en Azure Stack Portal](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Seleccione **Grupo de recursos**. Elija un grupo de recursos existente o cree uno y, a continuación, seleccione **Aceptar**.

   ![Selección de un grupo de recursos en Azure Stack Portal](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Seleccione **Crear**. Un icono nuevo en el panel realiza el seguimiento del progreso de la implementación de la plantilla.

   ![Creación de una plantilla en Azure Stack Portal](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo implementar plantillas, consulte el siguiente artículo:

- [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
