---
title: Creación de cuentas de almacenamiento en Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Aprenda a crear cuentas de almacenamiento en Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 2d4085605fc37e95c3f68db074d4a517f5296a7a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "78367920"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>Creación de cuentas de almacenamiento en Azure Stack Hub

Las cuentas de almacenamiento de Azure Stack Hub incluyen los servicios Blob y Table, y el espacio de nombres único de los objetos de datos de almacenamiento. De forma predeterminada, los datos de su cuenta están disponibles solo para usted, el propietario de la cuenta de almacenamiento.

1. En el equipo de la prueba de concepto de Azure Stack Hub, inicie sesión en `https://adminportal.local.azurestack.external` como [administrador](../asdk/asdk-connect.md) y, luego, haga clic en **+ Crear un recurso** > **Datos y almacenamiento** > **Cuenta de almacenamiento**.

   ![Creación de la cuenta de almacenamiento en el portal del administrador de Azure Stack Hub](media/azure-stack-provision-storage-account/image01.png)

2. En la hoja **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta de almacenamiento. Cree un nuevo **grupo de recursos**, o seleccione uno existente y, a continuación, haga clic en **Crear** para crear la cuenta de almacenamiento.

   ![Revisión de la cuenta de almacenamiento en el portal del administrador de Azure Stack Hub](media/azure-stack-provision-storage-account/image02.png)

3. Para ver la nueva cuenta de almacenamiento, haga clic en **Todos los recursos** y, a continuación, busque la cuenta de almacenamiento y haga clic en su nombre.

    ![El nombre de la cuenta de almacenamiento en el portal del administrador de Azure Stack Hub](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Pasos siguientes

- [Utilización de plantillas de Azure Resource Manager](../user/azure-stack-arm-templates.md)
- [Más información acerca de las cuentas de Almacenamiento de Azure](/azure/storage/common/storage-create-storage-account)
- [Descargue la guía de validación de almacenamiento coherente con Azure para Azure Stack Hub](https://aka.ms/azurestacktp1doc)
