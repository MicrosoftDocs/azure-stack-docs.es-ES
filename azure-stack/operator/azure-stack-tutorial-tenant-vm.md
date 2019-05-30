---
title: Máquinas virtuales disponibles para los usuarios de Azure Stack | Microsoft Docs
description: Más información sobre cómo hacer que las máquinas virtuales estén disponibles en Azure Stack
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 09/11/2018
ms.custom: mvc
ms.openlocfilehash: b39eaf4365f74e59c6dd60db67fdadf1162cb91d
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268131"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Tutorial: Máquinas virtuales disponibles para los usuarios de Azure Stack

Como administrador de la nube de Azure Stack, puede crear ofertas a las que se pueden suscribir los usuarios (a veces denominados inquilinos). Al suscribirse a una oferta, los usuarios pueden consumir servicios de Azure Stack que proporciona la oferta.

En este tutorial se muestra cómo crear una oferta para una máquina virtual y, después, iniciar sesión como usuario para probar la oferta.

Lo qué aprenderá:

> [!div class="checklist"]
> * Creación de una oferta
> * Añadir una imagen
> * Probar la oferta

En Azure Stack, los servicios se prestan a los usuarios mediante planes, ofertas y suscripciones. Los usuarios pueden suscribirse a varias ofertas. Una oferta puede tener uno o varios planes, y un plan puede tener uno o varios servicios.

![Planes, ofertas y suscripciones](media/azure-stack-key-features/image4.png)

Para más información, vea [Características y conceptos clave de Azure Stack](azure-stack-overview.md).

## <a name="create-an-offer"></a>Creación de una oferta

Las ofertas son grupos de uno o varios planes que los proveedores presentan a los usuarios para que estos los compren o se suscriban a ellos. El proceso de creación de una oferta consta de varios pasos. Primero, se le solicitará que cree la oferta, después un plan y, finalmente, las cuotas.

1. [Inicie sesión](../asdk/asdk-connect.md) en el portal como administrador de la nube y, a continuación, seleccione **+ Crear un recurso** > **Ofertas + Planes** > **Oferta**.

   ![Nueva oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

1. En **Nueva oferta**, escriba el **Nombre para mostrar** y el **Nombre de recurso** y, después, seleccione un **Grupo de recursos** nuevo o existente. El nombre para mostrar es el nombre descriptivo de la oferta. Solo el operador de la nube puede ver el nombre del recurso, que es el que usan los administradores para trabajar con la oferta como un recurso de Azure Resource Manager.

   ![Nombre para mostrar](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Seleccione **Planes base** y, en la sección **Plan**, seleccione **Agregar** para agregar un nuevo plan a la oferta.

   ![Agregar un plan](media/azure-stack-tutorial-tenant-vm/image03.png)

1. En la sección **Nuevo plan**, rellene el **Nombre para mostrar** y el **Nombre del recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los usuarios. Solo el operador de la nube puede ver el nombre del recurso, que es el que usan los operadores de la nube para trabajar con el plan como un recurso de Azure Resource Manager.

   ![Nombre para mostrar del plan](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Seleccione **Servicios**. En la lista Servicios, seleccione **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage**. Elija **Seleccionar** para agregar estos servicios al plan.

   ![Servicios del plan](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Seleccione **Cuotas** y, a continuación, seleccione el primer servicio para el que desea crear una cuota. Para una cuota de IaaS, utilice el siguiente ejemplo como guía para configurar cuotas para los servicios Compute, Network y Storage.

   - Primero, cree una cuota para el servicio Compute. En la lista de espacio de nombres, seleccione **Microsoft.Compute** y, después, seleccione **Crear nueva cuota**.

     ![Crear nueva cuota](media/azure-stack-tutorial-tenant-vm/image06.png)

   - En **Crear cuota**, escriba un nombre para la cuota. Puede cambiar o aceptar cualquiera de los valores de cuota que se muestran para la cuota que se va a crear. En este ejemplo, aceptamos la configuración predeterminada y seleccionamos **Aceptar**.

     ![Nombre de cuota](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Elija **Microsoft.Compute** en la lista de espacio de nombres y, a continuación, seleccione la cuota que ha creado. De esta forma se vincula la cuota con el servicio Compute.

     ![Seleccione la cuota](media/azure-stack-tutorial-tenant-vm/image08.png)

      Repita estos pasos para los servicios Network y Storage. Cuando haya terminado, seleccione **Aceptar** en **Cuotas** para guardar todas las cuotas.

1. En **Nuevo plan**, seleccione **Aceptar**.

1. En **Plan**, seleccione el nuevo plan y, después, **Seleccionar**.

1. En **Nueva oferta**, seleccione **Crear**. Verá una notificación cuando se crea la oferta.

1. En el menú del panel, seleccione **Ofertas** y, a continuación, haga clic en la oferta que ha creado.

1. Seleccione **Cambiar estado** y, después, elija **Público**.

    ![Estado público](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Añadir una imagen

Antes de poder aprovisionar a las máquinas virtuales, debe agregar una imagen en Marketplace de Azure Stack. Puede agregar la imagen que desee, incluidas las imágenes de Linux, de Azure Marketplace.

Si trabaja en un escenario conectado y si ha registrado la instancia de Azure Stack con Azure, a continuación, puede descargar la imagen de la VM de Windows Server 2016 de Azure Marketplace mediante el uso de los pasos descritos en el tema [Descarga de elementos Marketplace de Azure en Azure Stack](azure-stack-download-azure-marketplace-item.md).

Para obtener información acerca de cómo agregar diferentes elementos a Marketplace, vea [Marketplace de Azure Stack](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Probar la oferta

Ahora que ha creado una oferta, puede probarla. Inicie sesión como usuario, suscríbase a la oferta y, después, agregue una máquina virtual.

1. **Suscripción a una oferta**

    a. Inicie sesión en el portal de usuarios con una cuenta de usuario y seleccione el icono **Obtener una suscripción**.
   - En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador y tendrá el formato https://portal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
   - Si usa el Kit de desarrollo de Azure Stack, la dirección del portal es https://portal.local.azurestack.external.

   ![Obtener una suscripción](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. En **Obtener una suscripción**, escriba un nombre para la suscripción en el campo **Nombre para mostrar**. Seleccione **Oferta** y elija una de las ofertas en la lista **Elegir una oferta**. Seleccione **Crear**.

   ![Creación de una oferta](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. Para ver la suscripción, seleccione **Todos los servicios** y, después, en la categoría **GENERAL** seleccione **Suscripciones**. Seleccione su nueva suscripción para ver los servicios que forman parte de ella.

   >[!NOTE]
   >Después de suscribirse a una oferta, es posible que tenga que actualizar el portal para ver los servicios que forman parte de la nueva suscripción.

1. **Aprovisionamiento de una máquina virtual**

   Desde el portal de usuarios puede aprovisionar una máquina virtual mediante la nueva suscripción.

    a. Inicie sesión en el portal de usuario con una cuenta de usuario.
   - En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador y tendrá el formato https://portal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
     - Si usa el Kit de desarrollo de Azure Stack, la dirección del portal es https://portal.local.azurestack.external.

   b.  En el panel, seleccione **+Crear un recurso** > **Proceso** > **Windows Server 2016 Datacenter Eval** y, luego, seleccione **Crear**.

   c. En **Datos básicos**, proporcione la siguiente información:
      - Escriba un **nombre**.
      - Escriba un **nombre de usuario**.
      - Escriba una **contraseña**.
      - Elija una **Suscripción**
      - Cree un **grupo de recursos** (o seleccione uno existente). 
      - Seleccione **Aceptar** para guardar esta información.

   d. En **Elegir un tamaño**, seleccione **A1 Estándar** y, después, en **Seleccionar**.  

   e. En **Configuración**, seleccione **Red virtual**.

   f. En **Elegir red virtual**, seleccione **Crear nuevo**.

   g. En **Crear red virtual**, acepte todos los valores predeterminados y seleccione **Aceptar**.

   h. Seleccione **Aceptar** en **Configuración** para guardar la configuración de red.

      i. En **Resumen**, seleccione **Aceptar** para crear la máquina virtual.  

   j. Para ver la nueva máquina virtual, seleccione **Todos los recursos**. Busque la máquina virtual y seleccione su nombre en los resultados de búsqueda.

   
## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Creación de una oferta
> * Añadir una imagen
> * Probar la oferta

Prosiga con el siguiente tutorial para aprender a:
> [!div class="nextstepaction"]
> [Bases de datos SQL disponibles para los usuarios de Azure Stack](azure-stack-tutorial-sql-server.md)