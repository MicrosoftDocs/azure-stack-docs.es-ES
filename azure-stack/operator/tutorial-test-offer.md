---
title: Pruebe una oferta de servicios de Azure Stack Hub.
description: Obtenga información sobre cómo probar una oferta de servicio mediante la creación de una suscripción y la implementación de recursos.
author: BryanLa
ms.author: bryanla
ms.topic: tutorial
ms.date: 10/13/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/06/2019
ms.openlocfilehash: 2aae33b324be4828b514ffbd019992aae989f50b
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "80423842"
---
# <a name="tutorial-test-a-service-offering"></a>Tutorial: Prueba de una oferta de un servicio

En el tutorial anterior, creó una oferta para los usuarios. En este tutorial se muestra cómo probar esa oferta, usándola para crear una suscripción. A continuación, deberá crear e implementar los recursos en los servicios fundamentales a los que tiene derecho la suscripción.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * una suscripción
> * Creación e implementación de recursos

## <a name="prerequisites"></a>Prerrequisitos

Antes de comenzar con este tutorial, debe completar los siguientes requisitos previos:

- Complete el tutorial [Ofrecer un servicio a los usuarios](tutorial-offer-services.md). En él aprenderá a crear la oferta que se usa en este tutorial.

- Asimismo, la oferta a la que se suscribe en este tutorial permite la implementación de un recurso de máquina virtual (VM). Si quiere probar la implementación de la máquina virtual, primero debe hacer que una imagen de una máquina virtual esté disponible en Marketplace de Azure Stack Hub. Para ello, descárguela de Azure Marketplace. Consulte [Descarga de elementos de Marketplace desde Azure a Azure Stack Hub](azure-stack-download-azure-marketplace-item.md) para más información. 

## <a name="subscribe-to-the-offer"></a>Suscripción a la oferta

1. Inicie sesión en el portal de usuario con una cuenta de usuario. 

   - En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador y tendrá el formato https://portal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
   - Si usa el Kit de desarrollo de Azure Stack, la dirección del portal es https://portal.local.azurestack.external.

1. Seleccione el icono **Get a Subscription** (Obtener una suscripción).

   ![Obtener una suscripción](media/tutorial-test-offer/1-get-subscription.png)

1. En **Get a Subscription** (Obtener una suscripción), escriba un nombre de la nueva suscripción en el campo **Display Name** (Nombre para mostrar). Seleccione **Offer** (Oferta) y elija aquella que creó en el tutorial anterior, de la lista **Choose an offer** (Elegir una oferta). Seleccione **Crear**.

   ![Creación de una oferta](media/tutorial-test-offer/2-create-subscription.png)

1. Para ver la suscripción, seleccione **Todos los servicios** y, después, en la categoría **GENERAL** seleccione **Suscripciones**. Seleccione la nueva suscripción para ver la oferta a la que está asociada y sus propiedades.

   >[!NOTE]
   >Después de suscribirse a una oferta, es posible que tenga que actualizar el portal para ver los servicios que forman parte de la nueva suscripción.

::: moniker range=">=azs-1902"
## <a name="deploy-a-storage-account-resource"></a>Implementar un recurso de cuenta de almacenamiento

En el portal de usuarios, debe aprovisionar una cuenta de almacenamiento mediante la suscripción que creó en la sección anterior.

1. Inicie sesión en el portal de usuario con una cuenta de usuario.

1. Seleccione **+Crear un recurso** > **Datos y almacenamiento** > **Cuenta de almacenamiento: blob, archivo, tabla, cola**.

1. En **Create storage account** (Crear cuenta de almacenamiento), proporcione la siguiente información:
  
   - Escriba un **nombre**.
   - Seleccione su nueva **suscripción**
   - Seleccione un **grupo de recursos** (o cree uno). 
   - Seleccione **Crear** para crear la cuenta de almacenamiento.

1. Una vez se inicie la implementación, volverá al panel. Para ver la nueva cuenta de almacenamiento, seleccione **All resources** (Todos los recursos). Busque la cuenta de almacenamiento y seleccione su nombre en los resultados de búsqueda. Desde aquí, puede administrar la cuenta de almacenamiento y su contenido.

## <a name="deploy-a-virtual-machine-resource"></a>Implementación de un recurso de máquina virtual

En el portal de usuarios, debe aprovisionar una máquina virtual mediante la suscripción que creó en la sección anterior.

1. Inicie sesión en el portal de usuario con una cuenta de usuario.

1. Seleccione **+Crear un recurso** > **Procesar** > **\<image-name\>** , donde "image-name" es el nombre de la máquina virtual que descargó en los requisitos previos.
1. En **Crear máquina virtual** / **Datos básicos**, proporcione la siguiente información:
  
   - Escriba un **nombre** para la VM.
   - Escriba un **nombre de usuario** para la cuenta de administrador.
   - En VM de Linux, seleccione "Password" (Contraseña) para el **tipo de autenticación**.
   - Escriba una **contraseña** y úsela en **Confirm password** (Confirmar contraseña), en la cuenta de administrador.
   - Seleccione su nueva **suscripción**.
   - Seleccione un **grupo de recursos** (o cree uno). 
   - Seleccione **OK** (Aceptar) para validar esta información y continuar.

1. En **Choose a size** (Elegir un tamaño), filtre la lista si es necesario, seleccione una SKU de VM y haga clic en **Select** (Seleccionar).  
1. En la **configuración**, especifique los puertos que se abrirán en **Select public inbound ports** (Seleccionar los puertos de entrada públicos) y haga clic en **OK** (Aceptar).
   > [!NOTE]
   > Al seleccionar "RDP (3389)" por ejemplo, podrá conectarse de forma remota a la VM cuando se ejecuta.
1. En el **resumen**, revise sus opciones y luego seleccione **OK** (Aceptar) para crear la máquina virtual.  
1. Una vez se inicie la implementación, volverá al panel. Para ver la nueva máquina virtual, seleccione **Todos los recursos**. Busque la máquina virtual y seleccione su nombre en los resultados de búsqueda. Desde aquí puede tener acceso a la máquina virtual y administrarla.
   > [!NOTE]
   > La implementación completa y el inicio de la VM pueden tardar varios minutos. Una vez que la VM esté lista para usarse, el [estado](/azure/virtual-machines/windows/states-lifecycle) cambiará a "En ejecución".

::: moniker-end

::: moniker range="<=azs-1901"
## <a name="deploy-a-virtual-machine-resource-1901-and-earlier"></a>Implementar un recurso de máquina virtual (1901 y anterior)

Desde el portal de usuarios puede aprovisionar una máquina virtual mediante la nueva suscripción.

1. Inicie sesión en el portal de usuario con una cuenta de usuario.

1. En el panel, seleccione **+Crear un recurso** > **Procesar**>**Windows Server 2016 Datacenter Eval** y, luego, seleccione **Crear**.

1. En **Datos básicos**, proporcione la siguiente información:
  
   - Escriba un **nombre**.
   - Escriba un **nombre de usuario**.
   - Escriba una **contraseña**.
   - Elija su nueva **suscripción**
   - Cree un **grupo de recursos** (o seleccione uno existente). 
   - Seleccione **Aceptar** para guardar esta información.

1. En **Elegir un tamaño**, seleccione **A1 Estándar** y, después, en **Seleccionar**.  
1. En **Configuración**, seleccione **Red virtual**.

1. En **Elegir red virtual**, seleccione **Crear nuevo**.

1. En **Crear red virtual**, acepte todos los valores predeterminados y seleccione **Aceptar**.

1. Seleccione **Aceptar** en **Configuración** para guardar la configuración de red.

1. En **Resumen**, seleccione **Aceptar** para crear la máquina virtual.  

1. Para ver la nueva máquina virtual, seleccione **Todos los recursos**. Busque la máquina virtual y seleccione su nombre en los resultados de búsqueda.
::: moniker-end

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * una suscripción
> * Creación e implementación de recursos 

A continuación, obtenga información sobre la implementación de proveedores de recursos para servicios de valor agregado. Estos le permitirán ofrecer incluso más servicios a los usuarios de los planes:

- [Ofrecer SQL en Azure Stack Hub](azure-stack-sql-resource-provider.md)
- [Ofrecer MySQL en Azure Stack Hub](azure-stack-mysql-resource-provider.md)
- [Ofrecer App Service en Azure Stack Hub](azure-stack-app-service-overview.md)
- [Ofrecer Event Hubs en Azure Stack Hub](event-hubs-rp-overview.md)
