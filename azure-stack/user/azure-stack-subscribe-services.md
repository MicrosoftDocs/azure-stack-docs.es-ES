---
title: En este tutorial, aprenderá a suscribirse a una oferta de Azure Stack | Microsoft Docs
description: En este tutorial se muestra cómo crear una nueva suscripción a los servicios de Azure Stack y probar la oferta mediante la creación de una máquina virtual de prueba.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 3f577ebd5a57b271a449b5db262ea70779ebdb3d
ms.sourcegitcommit: a427e72e4f3b6cd6000b1459af9bbf221e049e08
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66506367"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Tutorial: Creación y prueba de una suscripción

En este tutorial se muestra cómo crear una suscripción que contiene una oferta y, después, probarla. Para la prueba, inicie sesión en el portal de usuarios de Azure Stack como administrador de la nube, se suscribirá a la oferta y, después, creará una máquina virtual.

> [!TIP]
> Para una experiencia de evaluación más avanzada, puede [crear una suscripción para un usuario en particular](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) y después iniciar sesión como ese usuario en el portal de usuarios.

En este tutorial se muestra cómo suscribirse a una oferta de Azure Stack.

Lo qué aprenderá:

> [!div class="checklist"]
> * Suscripción a una oferta 
> * Probar la oferta

## <a name="subscribe-to-an-offer"></a>Suscripción a una oferta

Para suscribirse a una oferta como usuario, inicie sesión en el portal de usuarios de Azure Stack para descubrir los servicios que ha ofrecido el operador de Azure Stack.

1. Inicie sesión en el portal de usuario y seleccione **Obtener una suscripción**.

   ![Obtener una suscripción](media/azure-stack-subscribe-services/get-subscription.png)

2. En el campo **Nombre para mostrar** escriba un nombre para la suscripción. Después, seleccione **Oferta** para seleccionar una de las ofertas disponibles en la sección **Elegir una oferta**. Seleccione **Crear**.

   ![Creación de una oferta](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Debe actualizar ahora el portal de usuarios para empezar a usar la suscripción.

3. Para ver la suscripción que creó, seleccione **Todos los servicios**. Luego, en la categoría **GENERAL** seleccione **Suscripciones** y, después, seleccione la suscripción nueva. Después de suscribirse a una oferta, actualice el portal para ver si se han incluido nuevos servicios como parte de la nueva suscripción. En este ejemplo, se ha agregado **Máquinas virtuales**.

   ![Ver suscripción](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Probar la oferta

Al iniciar sesión en el portal de usuarios, puede probar la oferta mediante el aprovisionamiento de una máquina virtual con las nuevas funcionalidades de suscripción.

> [!NOTE]
> Esta prueba requiere que una máquina virtual de Windows Server 2016 Datacenter que ya se agregó a Marketplace de Azure Stack.

1. Inicie sesión en el portal de usuario.

2. En el portal de usuarios, seleccione **Máquinas virtuales**, después, **Agregar**, **Windows Server 2016 Datacenter** y, finalmente, haga clic en **Crear**.

3. En la sección **Datos básicos**, escriba un **nombre**, **nombre de usuario** y **contraseña**, elija una **suscripción**, cree un **grupo de recursos** (o seleccione uno existente) y, a continuación, seleccione **Aceptar**.

4. En la sección **Elegir un tamaño**, seleccione **A1 Estándar** y, después, **Seleccionar**.  

5. En la hoja de **configuración**, acepte los valores predeterminados y seleccione **Aceptar**.

6. En la sección **Resumen**, haga clic en **Aceptar** para crear la máquina virtual.  

7. Para ver la nueva máquina virtual, seleccione **Máquinas virtuales** y, a continuación, busque la nueva máquina virtual y haga clic en su nombre.

    ![Todos los recursos](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> La implementación de la máquina virtual tarda unos minutos en completarse.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió:

> [!div class="checklist"]
> * Suscripción a una oferta 
> * Probar la oferta

> [!div class="nextstepaction"]
> [Creación de una máquina virtual a partir de una plantilla de comunidad](azure-stack-create-vm-template.md)