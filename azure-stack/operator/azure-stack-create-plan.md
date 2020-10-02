---
title: Creación de un plan en Azure Stack Hub
description: Aprenda a crear un plan en Azure Stack Hub que permita a los suscriptores aprovisionar máquinas virtuales.
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 12de836d74382f26efd49ec8873aaa99a919ecd5
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/23/2020
ms.locfileid: "91107088"
---
# <a name="create-a-plan-in-azure-stack-hub"></a>Creación de un plan en Azure Stack Hub

Los [planes de Azure Stack Hub](azure-stack-overview.md) son agrupaciones de uno o varios servicios y sus cuotas. Como proveedor puede crear planes y ofrecérselos a sus usuarios. A su vez, los usuarios se suscriben a las ofertas para usar los planes, los servicios y las cuotas que incluyen. Este ejemplo muestra cómo crear un plan que incluya a los proveedores de recursos de proceso, de red y de almacenamiento. Este plan ofrece a los suscriptores la capacidad de aprovisionar máquinas virtuales.

::: moniker range=">=azs-1902"
## <a name="create-a-plan-1902-and-later"></a>Crear un plan (1902 y posteriores)

1. Inicie sesión en el portal de administración de Azure Stack Hub en `https://adminportal.local.azurestack.external`.

2. Para crear un plan y una oferta a la que los usuarios puedan suscribirse, seleccione **+ Crear un recurso**, **Ofertas y planes** y, finalmente, **Plan**.
  
   ![Captura de pantalla que muestra cómo seleccionar un plan en el portal de administración de Azure Stack Hub.](media/azure-stack-create-plan/select-plan.png)

3. Aparece una interfaz de usuario con pestañas que le permite especificar el nombre del plan, agregar servicios y definir cuotas para cada uno de los servicios seleccionados. Lo más importante es que puede revisar los detalles de la oferta que crea, antes de decidirse a crearla.

   En la pestaña **Conceptos básicos** de la ventana **Nuevo plan**, escriba un **Nombre para mostrar** y un **Nombre de recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los operadores. En el portal de administrador, los detalles del plan solo son visibles para los operadores.

   ![Captura de pantalla que muestra cómo especificar los detalles del nuevo plan en Azure Stack Hub.](media/azure-stack-create-plan/plan-name.png)

4. Cree un nuevo **Grupo de recursos** o seleccione uno existente, como contenedor para el plan.

   ![Captura de pantalla que muestra cómo especificar el grupo de recursos del nuevo plan en Azure Stack Hub.](media/azure-stack-create-plan/resource-group.png)

5. Seleccione la pestaña **Services** (Servicios) o haga clic en el botón **Next: Services >** (Siguiente: Servicios) y, a continuación, seleccione la casilla **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage**.
  
   ![Captura de pantalla que muestra cómo seleccionar los servicios del nuevo plan en Azure Stack Hub.](media/azure-stack-create-plan/services.png)

6. Seleccione la pestaña **Quotas** (Cuotas) o haga clic en el botón **Next: Quotas >** (Siguiente: Cuotas). Junto a **Microsoft.Storage**, elija la cuota predeterminada del menú desplegable, o bien seleccione **Crear nueva** para crear una cuota personalizada.
  
   ![Captura de pantalla que muestra cómo especificar las cuotas del nuevo plan en Azure Stack Hub.](media/azure-stack-create-plan/quotas.png)

7. Si va a crear una nueva cuota, escriba un **Nombre** para la cuota y, a continuación, especifique los valores de cuota. Seleccione **Aceptar** para crear la cuota.

   ![Captura de pantalla que muestra cómo crear una cuota para el nuevo plan en Azure Stack Hub.](media/azure-stack-create-plan/new-quota.png)

8. Repita los pasos 6 y 7 para crear y asignar cuotas de **Microsoft.Network** y **Microsoft.Compute**. Cuando los tres servicios tienen cuotas asignadas, son similares a las del ejemplo siguiente.

   ![Captura de pantalla que muestra cómo completar las asignaciones de cuotas del nuevo plan en Azure Stack Hub.](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Seleccione **Revisar y crear** para revisar el plan. Revise todos los valores y las cuotas para asegurarse de que son correctos. La interfaz le permite expandir las cuotas de los planes elegidos, de una en una, para ver los detalles de cada cuota en un plan. También puede volver atrás para realizar las modificaciones necesarias.

   ![Captura de pantalla que muestra cómo crear el plan en Azure Stack Hub.](media/azure-stack-create-plan/create.png)

10. Cuando esté listo, seleccione **Crear** para crear el plan.

11. Para ver el nuevo plan, en el lado izquierdo haga clic en **All services** (Todos los servicios), seleccione **Plans** (Planes) y, después, busque el plan y seleccione su nombre. Si la lista de recursos es larga, utilice **Buscar** para encontrar el plan por su nombre.
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-a-plan-1901-and-earlier"></a>Crear un plan (1901 y anteriores)

1. Inicie sesión en el portal de administración de Azure Stack Hub en `https://adminportal.local.azurestack.external`.

2. Para crear un plan y una oferta a la que los usuarios puedan suscribirse, seleccione **+ New** (+Nuevo), después **Offers + Plans** (Ofertas y planes) y, finalmente, **Plan**.
  
   ![Selección de un plan en el portal del administrador de Azure Stack Hub](media/azure-stack-create-plan/select-plan1901.png)

3. En **Nuevo plan**, escriba un **nombre para mostrar** y un **nombre del recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los usuarios. Solo el administrador puede ver el nombre del recurso que usan los administradores para trabajar con el plan como un recurso de Azure Resource Manager.

   ![Especificación de los detalles de un nuevo plan en Azure Stack Hub](media/azure-stack-create-plan/plan-name1901.png)

4. Cree un nuevo **Grupo de recursos** o seleccione uno existente, como contenedor para el plan.

   ![Especificación del grupo de recursos de un nuevo plan en Azure Stack Hub](media/azure-stack-create-plan/resource-group1901.png)

5. Seleccione **Servicios** y, a continuación, seleccione la casilla de verificación de **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage**. A continuación, elija **Seleccionar** para guardar la configuración. Las casillas de verificación aparecen cuando se desplaza el mouse sobre cada opción.
  
   ![Selección de los servicios de un nuevo plan en Azure Stack Hub](media/azure-stack-create-plan/services1901.png)

6. Seleccione **Cuotas**, **Microsoft.Storage (local)** y, a continuación, elija la cuota predeterminada o seleccione **Crear nueva cuota** para crear una cuota personalizada.
  
   ![Especificación de las cuotas de un nuevo plan en Azure Stack Hub](media/azure-stack-create-plan/quotas1901.png)

7. Si va a crear una nueva cuota, escriba un **Nombre** para la cuota > especifique los valores de cuota > seleccione **Aceptar**. El cuadro de diálogo **Crear cuota** se cierra.

   ![Creación de una nueva cuota para un nuevo plan en Azure Stack Hub](media/azure-stack-create-plan/new-quota1901.png)

   A continuación, seleccione la nueva cuota que ha creado. Al seleccionar la cuota, esta se asigna y se cierra el cuadro de diálogo de selección.
  
   ![Asignación de la cuota de un nuevo plan en Azure Stack Hub](media/azure-stack-create-plan/assign-quota1901.png)

8. Repita los pasos 6 y 7 para crear y asignar cuotas de **Microsoft.Network (local)** y **Microsoft.Compute (local)** . Cuando los tres servicios tienen cuotas asignadas, son similares a las del ejemplo siguiente.

   ![Asignaciones de cuota realizadas para un nuevo plan en Azure Stack Hub](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. En **Cuotas**, elija **Aceptar** y, a continuación, en **Nuevo plan** elija **Crear** para crear el plan.

    ![Creación del plan en Azure Stack Hub](media/azure-stack-create-plan/create1901.png)

10. Para ver el nuevo plan, seleccione **Todos los recursos** y, a continuación, busque el plan y seleccione su nombre. Si la lista de recursos es larga, utilice **Buscar** para encontrar el plan por su nombre.

    ![Revisión del nuevo plan en Azure Stack Hub](media/azure-stack-create-plan/plan-overview1901.png)
::: moniker-end

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una oferta](azure-stack-create-offer.md)
