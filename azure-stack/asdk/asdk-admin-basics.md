---
title: Conceptos básicos del Kit de desarrollo de Azure Stack | Microsoft Docs
description: Describe cómo realizar la s tareas de administración básicas del Kit de desarrollo de Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 4dc4f1696266234d5aaaa14f27fbf4a0ba458108
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306231"
---
# <a name="asdk-administration-basics"></a>Conceptos básicos de administración del Kit de desarrollo de Azure Stack 
Hay varias cosas que debe saber si no está familiarizado con la administración del Kit de desarrollo de Azure Stack. Esta guía proporciona información general acerca de su rol como operador de Azure Stack en el entorno de evaluación y cómo asegurarse de que los usuarios de prueba puede ser productivos rápidamente.

En primer lugar, debe leer el artículo [¿Qué es el Kit de desarrollo de Azure Stack?](asdk-what-is.md) para asegurarse de que conoce el propósito del Kit de desarrollo de Azure Stack y sus limitaciones. El kit de desarrollo se debe usar como un "espacio aislado" en el que puede evaluar Azure Stack para desarrollar y probar sus aplicaciones en un entorno que no es de producción. 

Al igual que Azure, Azure Stack innova rápidamente, por lo que lanzaremos con regularidad nuevas compilaciones del ASDK. Sin embargo, el ASDK no se puede actualizar como las implementaciones de sistemas integrados de Azure Stack. Por tanto, si desea pasar a la compilación más reciente, debe [volver a implementar el ASDK](asdk-redeploy.md) totalmente. no puede aplicar paquetes de actualización. Este proceso lleva un tiempo, pero su ventaja es que puede probar las características más recientes en cuanto estén disponibles. 

## <a name="what-account-should-i-use"></a>¿Qué cuenta debo usar?
Al administrar Azure Stack, debe considerar algunos aspectos con respecto a la cuenta, especialmente en las implementaciones que utilizan Servicios de federación de Active Directory (AD FS) de Windows Server como proveedor de identidades en lugar de Azure Active Directory (Azure AD). Las siguientes consideraciones sobre la cuenta se aplican a los sistemas integrados de Azure Stack y las implementaciones de ASDK:

|Cuenta|Azure AD|AD FS|
|-----|-----|-----|
|Administrador local (.\Administrator)|Administrador del host de ASDK|Administrador del host de ASDK|
|AzureStack\AzureStackAdmin|Administrador del host de ASDK<br><br>Se puede usar para iniciar sesión en el portal de administración de Azure Stack<br><br>Acceso para ver y administrar anillos de Service Fabric|Administrador del host de ASDK<br><br>Sin acceso al portal de administración de Azure Stack<br><br>Acceso para ver y administrar anillos de Service Fabric<br><br>Ya no es propietario de la suscripción de proveedor predeterminada (DPS)|
|AzureStack\CloudAdmin|Puede acceder a comandos permitidos, y ejecutarlos, en el punto de conexión con privilegios|Puede acceder a comandos permitidos, y ejecutarlos, en el punto de conexión con privilegios<br><br>No puede iniciar sesión en el host de ASDK<br><br>Propietario de la suscripción de proveedor predeterminada (DPS)|
|Administrador global de Azure AD|Se usa durante la instalación<br><br>Propietario de la suscripción de proveedor predeterminada (DPS)|No aplicable|
|

## <a name="what-tools-do-i-use-to-manage"></a>¿Qué herramientas debo usar para la administración?
Puede usar el [Portal del administrador Portal Azure Stack ](https://adminportal.local.azurestack.external) o PowerShell para administrar Azure Stack. La manera más fácil de aprender los conceptos básicos es a través del portal. Si desea usar PowerShell, es preciso que instale [PowerShell para Azure pila](asdk-post-deploy.md#install-azure-stack-powershell) y [descargar las herramientas de Azure Stack desde GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure Stack usa Azure Resource Manager como mecanismo subyacente de implementación, administración y organización. Si va a administrar Azure Stack y ayudar a los usuarios de soporte, debe obtener información acerca de Azure Resource Manager. Para más información, lea el documento [Getting Started with Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) (Introducción a Azure Resource Manager).

## <a name="your-typical-responsibilities"></a>Sus responsabilidades típicas
Los usuarios quieren usar los servicios. Desde la perspectiva de estos, su rol principal es poner estos servicios a su disposición. Con el ASDK puede saber qué servicios ofrecer y poner dichos servicios a disposición de los usuarios mediante la [creación de planes, ofertas y cuotas](../operator/azure-stack-tutorial-tenant-vm.md). También deberá agregar elementos al Marketplace, como imágenes de máquinas virtuales. La forma más fácil de hacerlo es [descargar elementos de Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) de Azure a Azure Stack.

> [!NOTE]
> Si desea probar sus planes, ofertas y servicios, debe utilizar el [portal de usuario](https://portal.local.azurestack.external), no el [portal de administrador](https://adminportal.local.azurestack.external).

Además de ofrecer servicios, debe realizar todas las tareas habituales de un operador de Azure Stack para mantener ASDK en funcionamiento. Dichas tareas incluyen:
- Agregar cuentas de usuario para las implementaciones de Azure Active Directory (Azure AD) o de Servicios de federación de Active Directory (AD FS).
- Asignar roles de control de acceso basado en rol (RBAC) (esto no se restringe a los administradores)
- Supervisar el mantenimiento de la infraestructura
- Administrar los recursos de la red y del almacenamiento
- Reemplazar el hardware que no funcione del equipo host del kit de desarrollo 

## <a name="where-to-get-support"></a>Dónde obtener soporte técnico
En el caso del kit de desarrollo, la única opción de soporte técnico es realizar preguntas en el [foro de Microsoft Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Si hace clic el icono de ayuda y soporte técnico (signo de interrogación) en la esquina superior derecha del portal de administración y, a continuación, hace clic en **Nueva solicitud de soporte técnico**, se abrirá el sitio de foros directamente. Estos foros se supervisan periódicamente. 

> [!IMPORTANT]
> Como el ASDK es un entorno de evaluación, no se ofrece soporte técnico oficial a través de los servicios de soporte técnico al cliente (CSS) de Microsoft.

## <a name="next-steps"></a>Pasos siguientes
[Implementación del Kit de desarrollo de Azure Stack](asdk-install.md)

