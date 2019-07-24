---
title: Configuración de la identidad de nube híbrida con aplicaciones de Azure y Azure Stack | Microsoft Docs
description: Aprenda a configurar la identidad de nube híbrida con aplicaciones de Azure y Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: solution
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: 3ed0c109e0253fe6d710801dbc30de04c0b5a6e5
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286825"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Configuración de la identidad de nube híbrida para aplicaciones de Azure y Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Aprenda a configurar la identidad de nube híbrida para sus aplicaciones de Azure y Azure Stack.

Tiene dos opciones para conceder acceso a sus aplicaciones tanto en Azure global como en Azure Stack.

 * Si Azure Stack tiene conexión continua a Internet, puede usar Azure Active Directory (Azure AD).
 * Si Azure Stack no tiene conexión a Internet, puede usar los Servicios de federación de Active Directory (AD FS).

Las entidades de servicio se usan para conceder acceso a las aplicaciones de Azure Stack para la implementación o configuración mediante Azure Resource Manager en Azure Stack.

En esta solución, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Establecer una identidad híbrida en Azure global y Azure Stack
> - Recuperar un token para acceder a la API de Azure Stack.

Para seguir los pasos de esta solución, debe tener permisos de operador de Azure Stack.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack es una extensión de Azure. Azure Stack aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En el artículo [Consideraciones de diseño para aplicaciones híbridas](azure-stack-edge-pattern-overview.md) se examinan los pilares de la calidad de software (selección de ubicación, escalabilidad, disponibilidad, resistencia, manejabilidad y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Crear a una entidad de servicio para Azure AD en el portal

Si implementó Azure Stack con Azure AD como el almacén de identidades, puede crear entidades de servicio como lo hace para Azure. La sección [Uso de una identidad de aplicación para acceder a recursos](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) muestra cómo realizar los pasos desde el portal. Asegúrese de que dispone de los [permisos de Azure AD necesarios](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) antes de comenzar.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Creación de una entidad de servicio para AD FS mediante PowerShell

Si implementó Azure Stack con AD FS, puede usar PowerShell para crear una entidad de servicio, asignar un rol para el acceso e iniciar sesión en Powershell con dicha identidad. La sección [Uso de una identidad de aplicación para acceder a recursos](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) muestra cómo realizar los pasos necesarios mediante PowerShell.

## <a name="using-the-azure-stack-api"></a>Uso de la API de Azure Stack

La solución [API de Azure Stack](azure-stack-rest-api-use.md) le guía por el proceso de recuperación de un token de acceso a la API de Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Conexión a Azure Stack con Powershell

La guía de inicio rápido para [empezar a trabajar con PowerShell en Azure Stack](../operator/azure-stack-powershell-install.md) le guía por los pasos necesarios para instalar Azure PowerShell y conectarse a su instalación de Azure Stack.

### <a name="prerequisites"></a>Requisitos previos

Necesita una instalación de Azure Stack conectada a Azure Active Directory con una suscripción a la que puede acceder. Si no tiene una instalación de Azure Stack, puede usar estas instrucciones para configurar un [Kit de desarrollo de Azure Stack](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-using-code"></a>Conexión a Azure Stack mediante código

Para conectarse a Azure Stack mediante código, use la API de los puntos de conexión de Azure Resource Manager para obtener los puntos de conexión de autenticación y Graph para la instalación de Azure Stack y, luego, autentíquese mediante solicitudes de REST. Puede encontrar una aplicación cliente de ejemplo en [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>A menos que el SDK de Azure correspondiente al lenguaje seleccionado admita perfiles de API de Azure, puede que el SDK no funcione con Azure Stack. Para más información acerca de los perfiles de la API de Azure, consulte el artículo [Administración de los perfiles de la versión de API](azure-stack-version-profiles.md).

## <a name="next-steps"></a>Pasos siguientes

 - Para más información sobre cómo se trata la identidad en Azure Stack, consulte [Arquitectura de identidad para Azure Stack](../operator/azure-stack-identity-architecture.md).
 - Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
