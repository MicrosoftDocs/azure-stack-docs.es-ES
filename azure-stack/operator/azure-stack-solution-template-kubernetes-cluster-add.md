---
title: Incorporación de Kubernetes a Marketplace de Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Aprenda cómo agregar Kubernetes a Marketplace de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 03/24/2020v
ms.openlocfilehash: b96b8a2bed340201b9450b37b1aad166ebbdf26d
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869243"
---
# <a name="add-kubernetes-to-azure-stack-hub-marketplace"></a>Incorporación de Kubernetes a Marketplace de Azure Stack Hub

> [!note]  
> Use únicamente el elemento Kubernetes de Marketplace de Azure Stack Hub para implementar clústeres como prueba de concepto. Para los clústeres de Kubernetes admitidos en Azure Stack Hub, utilice el [motor de AKS](azure-stack-aks-engine.md).

Puede ofrecer Kubernetes como un artículo de Marketplace a los usuarios. Así, los usuarios pueden implementar Kubernetes en una sola operación coordinada.

En este artículo se explica cómo usar una plantilla de Azure Resource Manager para implementar y aprovisionar los recursos para un clúster de Kubernetes independiente. Antes de empezar, compruebe Azure Stack Hub y la configuración del inquilino de Azure global. Recopile la información necesaria sobre su entorno de Azure Stack Hub. Agregue los recursos necesarios a su inquilino y a Marketplace de Azure Stack Hub. El clúster depende de que haya un servidor de Ubuntu, un script personalizado y el elemento de clúster de Kubernetes en Marketplace de Azure Stack Hub.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Crear un plan, una oferta y una suscripción

Cree un plan, una oferta y una suscripción para el elemento de Kubernetes de Marketplace. También puede usar un plan y una oferta existentes.

1. Inicie sesión en el portal de administración en `https://adminportal.local.azurestack.external`.

1. Cree un plan como plan base. Para instrucciones, consulte [Creación de un plan en Azure Stack Hub](azure-stack-create-plan.md).

1. Cree una oferta. Para obtener instrucciones, consulte [Creación de una oferta en Azure Stack Hub](azure-stack-create-offer.md).

1. Seleccione **Ofertas** y busque la que ha creado.

1. Seleccione **Información general** en la hoja Oferta.

1. Seleccione **Cambiar estado**. Seleccione **Público**.

1. Seleccione **+ Crear un recurso** > **Offers and Plans** (Ofertas y planes)  > **Suscripción** para crear una suscripción.

    a. Especifique un **Nombre para mostrar**.

    b. Especifique un **Usuario**. Use la cuenta de Azure AD asociada con su inquilino.

    c. **Descripción del proveedor**

    d. Establezca el **Inquilino de directorio** en el inquilino de Azure AD de su entorno de Azure Stack Hub. 

    e. Seleccione **Oferta**. Seleccione el nombre de la oferta que ha creado. Tome nota del identificador de suscripción.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Creación de una entidad de servicio y las credenciales de AD FS

Si usa Servicios de federación de Active Directory (AD FS) para el servicio de administración de identidades, deberá crear una entidad de servicio para que los usuarios implementen un clúster de Kubernetes. Cree una entidad de servicio mediante un secreto de cliente. Para obtener instrucciones, consulte [Creación de una entidad de servicio mediante un secreto de cliente](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-an-ubuntu-server-image"></a>Agregar una imagen de Ubuntu Server

Agregue la siguiente imagen de servidor Ubuntu a Marketplace de Azure Stack Hub:

1. Inicie sesión en el portal de administración en `https://adminportal.local.azurestack.external`.

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

Agregue Kubernetes desde Marketplace de Azure Stack Hub:

1. Abra el portal de administración `https://adminportal.local.azurestack.external`.

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

1. Abra el portal de administración `https://adminportal.local.azurestack.external`.

1. Seleccione **Todos los servicios** y, luego, en la categoría **ADMINISTRACIÓN**, seleccione **Administración de Marketplace**.

1. Seleccione **+ Add from Azure** (+ Agregar desde Azure).

1. Escriba `Kubernetes`.

1. Seleccione `Kubernetes Cluster`.

1. Seleccione **Descargar**.

    > [!note]  
    > El elemento podría tardar cinco minutos en aparecer en Marketplace de Azure Stack Hub.

    ![Elemento de Kubernetes en Marketplace de Azure Stack Hub](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Actualización o eliminación de Kubernetes

Al actualizar el elemento de Kubernetes, eliminará el elemento que se encontraba en Marketplace de Azure Stack Hub. Siga estas instrucciones para agregar la actualización de Kubernetes a Marketplace de Azure Stack Hub.

Para quitar el elemento de Kubernetes:

1. Conéctese a Azure Stack Hub como operador con PowerShell. Para instrucciones, consulte el artículo [Conexión a Azure Stack Hub como operador con PowerShell](azure-stack-powershell-configure-admin.md).

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

[Implementación de Kubernetes en Azure Stack Hub](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Introducción a la oferta de servicios en Azure Stack Hub](service-plan-offer-subscription-overview.md)
