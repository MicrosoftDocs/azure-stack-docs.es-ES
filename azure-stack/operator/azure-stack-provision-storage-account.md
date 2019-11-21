---
title: Creación de cuentas de almacenamiento en Azure Stack | Microsoft Azure
titleSuffix: Azure Stack
description: Aprenda a crear cuentas de almacenamiento en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 449d9e39b650e6f7ccd91f4703709ea033e7a5dc
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802349"
---
# <a name="create-storage-accounts-in-azure-stack"></a>Creación de cuentas de almacenamiento en Azure Stack

Las cuentas de almacenamiento de Azure Stack incluyen los servicios Blob y Table, y el espacio de nombres único para los objetos de datos del almacenamiento. De forma predeterminada, los datos de su cuenta están disponibles solo para usted, el propietario de la cuenta de almacenamiento.

1. En el equipo de la prueba de concepto de Azure Stack, inicie sesión en `https://adminportal.local.azurestack.external` como [un administrador](../asdk/asdk-connect.md) y, a continuación, haga clic en **+ Crear un recurso** > **Datos y almacenamiento**  >  **Cuenta de almacenamiento**.

   ![Creación de la cuenta de almacenamiento en el portal de administración de Azure Stack](media/azure-stack-provision-storage-account/image01.png)

2. En la hoja **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta de almacenamiento. Cree un nuevo **grupo de recursos**, o seleccione uno existente y, a continuación, haga clic en **Crear** para crear la cuenta de almacenamiento.

   ![Revisión de la cuenta de almacenamiento en el portal de administración de Azure Stack](media/azure-stack-provision-storage-account/image02.png)

3. Para ver la nueva cuenta de almacenamiento, haga clic en **Todos los recursos** y, a continuación, busque la cuenta de almacenamiento y haga clic en su nombre.

    ![El nombre de la cuenta de almacenamiento en el portal de administración de Azure Stack](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Pasos siguientes

- [Utilización de plantillas del Administrador de recursos de Azure](../user/azure-stack-arm-templates.md)
- [Más información acerca de las cuentas de Almacenamiento de Azure](/azure/storage/common/storage-create-storage-account)
- [Descargue la guía de validación de almacenamiento coherente con Azure para Azure Stack](https://aka.ms/azurestacktp1doc)
