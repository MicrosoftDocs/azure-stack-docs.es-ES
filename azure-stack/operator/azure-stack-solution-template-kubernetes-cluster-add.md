---
title: Adición de Kubernetes a Azure Stack Marketplace
titleSuffix: Azure Stack
description: Obtenga información sobre cómo agregar Kubernetes a Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 985d0e33fd5a15329a1a47bd2d6b11e50cd82a1c
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780820"
---
# <a name="add-kubernetes-to-azure-stack-marketplace"></a>Adición de Kubernetes a Azure Stack Marketplace

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!note]  
> Use únicamente el elemento Kubernetes de Marketplace de Azure Stack para implementar clústeres como prueba de concepto. Para los clústeres de Kubernetes admitidos en Azure Stack, utilice el [motor de AKS](azure-stack-aks-engine.md).

Puede ofrecer Kubernetes como un artículo de Marketplace a los usuarios. Así, los usuarios pueden implementar Kubernetes en una sola operación coordinada.

En este artículo se explica cómo usar una plantilla de Azure Resource Manager para implementar y aprovisionar los recursos para un clúster de Kubernetes independiente. Antes de empezar, compruebe Azure Stack y la configuración de inquilino de Azure global. Recopile la información necesaria sobre su entorno de Azure Stack. Agregue los recursos necesarios a su inquilino y a Azure Stack Marketplace. El clúster depende de que haya un servidor de Ubuntu, un script personalizado y el elemento de clúster de Kubernetes en Azure Stack Marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Crear un plan, una oferta y una suscripción

Cree un plan, una oferta y una suscripción para el elemento de Kubernetes de Marketplace. También puede usar un plan y una oferta existentes.

1. Inicie sesión en el [portal de administrador](https://adminportal.local.azurestack.external).

1. Cree un plan como plan base. Para obtener instrucciones, vea [Creación de un plan en Azure Stack](azure-stack-create-plan.md).

1. Cree una oferta. Para obtener instrucciones, vea [Creación de una oferta en Azure Stack](azure-stack-create-offer.md).

1. Seleccione **Ofertas** y busque la que ha creado.

1. Seleccione **Información general** en la hoja Oferta.

1. Seleccione **Cambiar estado**. Seleccione **Público**.

1. Seleccione **+ Crear un recurso** > **Offers and Plans** (Ofertas y planes)  > **Suscripción** para crear una suscripción.

    a. Especifique un **Nombre para mostrar**.

    b. Especifique un **Usuario**. Use la cuenta de Azure AD asociada con su inquilino.

    c. **Descripción del proveedor**

    d. Establezca el **Inquilino de directorio** en el inquilino de Azure AD de su entorno de Azure Stack. 

    e. Seleccione **Oferta**. Seleccione el nombre de la oferta que ha creado. Tome nota del identificador de suscripción.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Creación de una entidad de servicio y las credenciales de AD FS

Si usa Servicios de federación de Active Directory (AD FS) para el servicio de administración de identidades, deberá crear una entidad de servicio para que los usuarios implementen un clúster de Kubernetes. Cree una entidad de servicio mediante un secreto de cliente. Para obtener instrucciones, consulte [Creación de una entidad de servicio mediante un secreto de cliente](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-an-ubuntu-server-image"></a>Agregar una imagen de Ubuntu Server

Agregue la siguiente imagen de servidor Ubuntu a Azure Stack Marketplace:

1. Inicie sesión en el [portal de administración](https://adminportal.local.azurestack.external).

1. Seleccione **Todos los servicios** y, a continuación, en la categoría **Administración**, seleccione **Marketplace management** (Administración de Marketplace).

1. Seleccione **+ Add from Azure** (+ Agregar desde Azure).

1. Escriba `Ubuntu Server`.

1. Seleccione la versión más reciente del servidor. Compruebe la versión completa y asegúrese de que tiene instalada la versión más reciente:
    - **Publicador**: Canonical
    - **Oferta**: UbuntuServer
    - **Versión**: 16.04.201806120 (o la versión más reciente)
    - **SKU**: 16.04-LTS

1. Seleccione **Descargar**.

## <a name="add-a-custom-script-for-linux"></a>Agregar un script personalizado para Linux

Agregue Kubernetes desde Azure Stack Marketplace:

1. Abra el [portal de administrador](https://adminportal.local.azurestack.external).

1. Seleccione **Todos los servicios** y, a continuación, en la categoría **Administración**, seleccione **Marketplace management** (Administración de Marketplace).

1. Seleccione **+ Add from Azure** (+ Agregar desde Azure).

1. Escriba `Custom Script for Linux`.

1. Seleccione el script con el perfil siguiente:
   - **Oferta**: Script personalizado para Linux 2.0
   - **Versión**: 2.0.6 (o la versión más reciente)
   - **Publicador**: Microsoft Corp

     > [!Note]  
     > Podría aparecer más de una versión del script personalizado para Linux. Deberá agregar la versión más reciente del elemento.

1. Seleccione **Descargar**.

## <a name="add-kubernetes-to-the-marketplace"></a>Adición de Kubernetes a Marketplace

1. Abra el [portal de administrador](https://adminportal.local.azurestack.external).

1. Seleccione **Todos los servicios** y, luego, en la categoría **ADMINISTRACIÓN**, seleccione **Administración de Marketplace**.

1. Seleccione **+ Add from Azure** (+ Agregar desde Azure).

1. Escriba `Kubernetes`.

1. Seleccione `Kubernetes Cluster`.

1. Seleccione **Descargar**.

    > [!note]  
    > El elemento podría tardar cinco minutos en aparecer en Azure Stack Marketplace.

    ![Elemento de Kubernetes en Azure Stack Marketplace](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Actualización o eliminación de Kubernetes

Al actualizar el elemento de Kubernetes, eliminará el elemento que se encontraba en Azure Stack Marketplace. Siga estas instrucciones para agregar la actualización de Kubernetes a Azure Stack Marketplace.

Para quitar el elemento de Kubernetes:

1. Conéctese a Azure Stack con PowerShell como operador. Para obtener instrucciones, vea [Configuración del entorno de PowerShell de Azure Stack](azure-stack-powershell-configure-admin.md).

2. Busque el elemento actual Clúster de Kubernetes en la galería.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Anote el nombre del elemento actual, por ejemplo, `Microsoft.AzureStackKubernetesCluster.0.3.0`.

4. Use el siguiente cmdlet de PowerShell para quitar el elemento:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Pasos siguientes

[Implementación de un clúster de Kubernetes en Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Introducción a la oferta de servicios en Azure Stack](service-plan-offer-subscription-overview.md)
