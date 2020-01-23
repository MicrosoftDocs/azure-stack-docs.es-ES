---
title: Creación de cuentas de almacenamiento en Azure Stack Hub | Microsoft Azure
titleSuffix: Azure Stack Hub
description: Aprenda a crear cuentas de almacenamiento en Azure Stack Hub.
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
ms.openlocfilehash: 0319ab52f27eb6dd269db0eb161727a623841961
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809889"
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
