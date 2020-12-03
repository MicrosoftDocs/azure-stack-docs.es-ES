---
title: Implementación de una plantilla desde el portal en Azure Stack Hub
description: Aprenda a usar el portal de Azure Stack Hub para implementar plantillas.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 7b5508f7c4e1bbd9b21f2160aecb6fd27c9a10e3
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525445"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Implementación de una plantilla desde el portal en Azure Stack Hub

Puede usar el portal de usuarios de Azure Stack Hub para implementar plantillas de Azure Resource Manager en Azure Stack Hub.

## <a name="to-deploy-a-template"></a>Para implementar una plantilla

1. Inicie sesión en el portal de usuarios de Azure Stack Hub y seleccione **+ Crear un recurso** > **Personalizar** > **Template Deployment**.

   ![Creación de un recurso en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy1a.png)

2. Puede seleccionar **Type to start filter** (Escribir para empezar a filtrar) para elegir una plantilla de inicio rápido de GitHub, o bien elija **Cree su propia plantilla en el editor**.

   ![Implementación de una plantilla en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy2a.png)

    Un miembro de la comunidad de Azure Stack Hub (y no Microsoft) es quien ha creado [**AzureStack-QuickStart-Templates**](https://github.com/Azure/AzureStack-QuickStart-Templates). Su propietario (no Microsoft) le permite usar las plantillas con arreglo a un acuerdo de licencia. Microsoft no se responsabiliza de estas plantillas en cuanto a su seguridad, compatibilidad ni rendimiento. Las plantillas de la comunidad no reciben ningún tipo de soporte en ningún servicio ni programa de soporte de Microsoft. Dichas plantillas se proporcionan *tal cual*, sin garantía de ningún tipo.

3. Si seleccionó **Cree su propia plantilla en el editor**, pegue el código de la plantilla JSON en la ventana de código.

   ![Edición de plantillas en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy3a.png)

    - Seleccione **Plantilla de inicio rápido** para cargar una plantilla de la comunidad en el editor.

    - Seleccione **Cargar archivo** para cargar una plantilla de Azure Resource Manager desde la máquina local al editor.

    - Seleccione **Descargar** para guardar la plantilla de Azure Resource Manager en la máquina local.

    Cuando haya terminado de realizar cambios en la plantilla, seleccione **Guardar**.

4. Seleccione **Suscripción**. Elija la suscripción que desee usar. Seleccione **Grupo de recursos**. Puede elegir un grupo de recursos existente o crear uno y, a continuación, seleccionar **Aceptar**. Después, seleccione **Revisar y crear**.

   ![Edición de parámetros en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy4a.png)

5. Seleccione **Crear**.

   ![Selección de una suscripción en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy5a.png)

6. Un icono nuevo en el panel realiza el seguimiento del progreso de la implementación de la plantilla.

   ![Selección de un grupo de recursos en el portal de Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy6a.png)

   Puede utilizar las plantillas de Azure Resource Manager para implementar y aprovisionar todos los recursos para su aplicación en una única operación coordinada. También se pueden volver a implementar plantillas para realizar cambios en los recursos de un grupo de recursos. Para más información sobre el uso de plantillas con Azure Stack Hub, consulte [Uso de plantillas de Administrador de recursos de Azure en Azure Stack Hub](azure-stack-arm-templates.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo implementar plantillas, consulte el siguiente artículo:

- [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
