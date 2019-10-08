---
title: Agregar los requisitos previos del motor de Azure Kubernetes Service (AKS) al Marketplace de Azure Stack | Microsoft Docs
description: Aprenda a agregar los requisitos previos del motor de AKS a Marketplace de Azure Stack.
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
ms.date: 09/25/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: bcb9dab55d00c48e5b77e7184fae0567ff1f620e
ms.sourcegitcommit: 5e53eb5d43d28ab07b4f84891dd269bbfcf65622
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2019
ms.locfileid: "71311282"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-marketplace"></a>Agregar los requisitos previos del motor de Azure Kubernetes Service (AKS) al Marketplace de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede permitir que los usuarios configuren el motor de Azure Kubernetes Services (AKS) agregando los elementos que se describen en este artículo a Azure Stack. Después, los usuarios pueden implementar un clúster de Kubernetes en una sola operación coordinada. Este artículo le guía por los pasos necesarios para hacer que el motor de AKS esté disponible para los usuarios, tanto en entornos conectados como desconectados. El motor de AKS depende de una identidad de entidad de servicio y, en Marketplace, una extensión de script personalizado y la imagen base de AKS.

[AKS-Engine](https://github.com/Azure/aks-engine) usa una imagen compilada, la imagen base de AKS. Cualquier versión del motor de AKS depende de una versión de imagen específica que puede poner a disposición en Azure Stack. Vea la tabla que muestre las versiones de AKS-Engine y las versiones de Kubernetes correspondientes en [Versiones admitidas de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions).

> [!IMPORTANT]
> AKS-Engine actualmente está en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="check-your-users-service-offering"></a>Comprobar la oferta de servicio del usuario

Será necesario que los usuarios tengan un plan, una oferta y una suscripción a Azure Stack con espacio suficiente. Con frecuencia, los usuarios querrán implementar clústeres de hasta seis máquinas virtuales, formados por tres maestros y tres nodos de trabajo. Posiblemente quiera asegurarse de que tienen una cuota lo suficientemente grande.

Si necesita más información sobre la planeación y configuración de una oferta de servicio, consulte [Introducción a la oferta de servicios en Azure Stack](azure-stack-offer-services-overview.md)

## <a name="create-a-service-principal-and-credentials"></a>Creación de una entidad de seguridad de servicio y sus credenciales

El clúster de Kubernetes necesitará permisos de entidad de seguridad de servicio (SPN) y basados en roles en Azure Stack.

### <a name="create-an-spn-in-azure-ad"></a>Crear un SPN en Azure AD

Si usa Azure Active Directory (Azure AD) para el servicio de administración de identidades, deberá crear una entidad de servicio para que los usuarios implementen un clúster de Kubernetes. Cree una entidad de servicio mediante un secreto de cliente. Para obtener instrucciones, consulte [Creación de una entidad de servicio mediante un secreto de cliente](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential).

### <a name="create-an-spn-in-ad-fs"></a>Crear un SPN en AD FS

Si usa Active Directory Federated Services (AD FS) para el servicio de administración de identidades, deberá crear una entidad de servicio para que los usuarios implementen un clúster de Kubernetes. Cree una entidad de servicio mediante un secreto de cliente. Para obtener instrucciones, consulte [Creación de una entidad de servicio mediante un secreto de cliente](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-the-aks-base-image"></a>Agregar la imagen base de AKS

Puede agregar la imagen base de AKS a Marketplace obteniendo el elemento de Azure. Pero si Azure Stack está desconectado, siga las instrucciones de [Descarga de elementos de Marketplace desde Azure a Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) para agregar el elemento. Agregue el elemento especificado en el paso cinco.

Agregue el siguiente elemento a Marketplace:

1. Inicie sesión en el [portal de administración](https://adminportal.local.azurestack.external).

1. Seleccione **Todos los servicios** y, a continuación, en la categoría **Administración**, seleccione **Marketplace management** (Administración de Marketplace).

1. Seleccione **+ Add from Azure** (+ Agregar desde Azure).

1. Escriba `AKS Base Image`.

1. Seleccione la versión de la imagen que coincida con la versión del motor de AKS. Puede encontrar una lista de la imagen base de AKS con la versión del motor de AKS en [Versiones de Kubernetes admitidas](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). 

    En la lista, seleccione:
    - **Publicador**: Microsoft-AKS
    - **Oferta**: AKS
    - **Versión**: 2019.07.30 (o la versión que se asigna al motor de AKS)
    - **SKU**: aks-ubuntu-1604-201907

1. Seleccione **Descargar**.

## <a name="add-a-custom-script-extension"></a>Agregar una extensión Custom Script para Linux

Puede agregar el script personalizado a Marketplace obteniendo el elemento de Azure. Pero si Azure Stack está desconectado, siga las instrucciones de [Descarga de elementos de Marketplace desde Azure a Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) para agregar el elemento.  Agregue el elemento especificado en el paso cinco.

1. Abra el [portal de administración](https://adminportal.local.azurestack.external).

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

## <a name="next-steps"></a>Pasos siguientes

[Descripción del motor de AKS en Azure Stack](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Introducción a la oferta de servicios en Azure Stack](azure-stack-offer-services-overview.md)
