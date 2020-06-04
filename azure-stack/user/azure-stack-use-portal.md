---
title: Uso del portal de usuarios de Azure Stack Hub
description: Aprenda a acceder y a usar al portal de usuarios de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: f60094812a5bc0b27a05ec7e66ac7bf65d747d76
ms.sourcegitcommit: db3c9179916a36be78b43a8a47e1fd414aed3c2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84147029"
---
# <a name="use-the-azure-stack-hub-user-portal"></a>Uso del portal de usuarios de Azure Stack Hub

El portal de Azure Stack Hub se usa para suscribirse a ofertas públicas y usar los servicios que proporcionan esas ofertas. Si ha utilizado la versión general de Azure Portal, ya está familiarizado con la manera en que funciona el sitio.

## <a name="access-the-portal"></a>Acceso al portal

El operador de Azure Stack Hub (un proveedor de servicios o un administrador de la organización) le indicará cuál es la dirección URL correcta para acceder al portal.

- En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador y tendrá el formato https://portal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
- Si usa el Kit de desarrollo de Azure Stack (ASDK), la dirección del portal es https://portal.local.azurestack.external.
- La zona horaria predeterminada para todas las implementaciones de Azure Stack Hub se establece conforme a la hora universal coordinada (UTC). Puede seleccionar una zona horaria al instalar Azure Stack Hub, pero esta se revierte automáticamente a UTC como valor predeterminado durante la instalación.

## <a name="customize-the-dashboard"></a>Personalización del panel

El panel contiene un conjunto predeterminado de iconos. Seleccione **Editar panel** para modificar el panel predeterminado o **Nuevo panel** para crear uno personalizado. Puede personalizar fácilmente un panel agregando o quitando iconos. Por ejemplo, para agregar un icono de Compute, seleccione **+ Crear un recurso**. Haga clic con el botón derecho en **Proceso** y luego seleccione **Anclar al panel**.

![Captura de pantalla del portal de usuarios de Azure Stack Hub](media/azure-stack-use-portal/userportal.png)

Para restaurar el panel a la configuración original:
1.  Seleccione **Editar panel**. 
2.  Haga clic con el botón derecho y seleccione **Restaurar al estado predeterminado**.

## <a name="create-subscription-and-browse-available-resources"></a>Creación de una suscripción y examen de los recursos disponibles

Si todavía no tiene una suscripción, tiene que suscribirse a una oferta. Luego, puede examinar los recursos disponibles. Para examinar y crear recursos, use cualquiera de las siguientes estrategias:

- Seleccione el icono **Marketplace** del panel.
- En el icono **Todos los recursos**, seleccione **Crear recursos**.
- En el panel de navegación izquierdo, seleccione **+ Crear un recurso**.

## <a name="learn-how-to-use-available-services"></a>Aprender a usar los servicios disponibles

Si necesita instrucciones sobre cómo usar los servicios disponibles, puede haber diferentes opciones disponibles.

- Su organización o un proveedor de servicios puede proporcionar su propia documentación, que suele ser el caso si ofrecen servicios o aplicaciones personalizados.
- Las aplicaciones de terceros tienen su propia documentación.
- En el caso de servicios coherentes con Azure, se recomienda encarecidamente revisar antes la documentación de Azure Stack Hub. Para acceder a la documentación de usuario de Azure Stack Hub, seleccione el icono de ayuda ( **?** ) y luego **Ayuda y soporte técnico**.

    ![Ayuda y opción de soporte técnico en la interfaz de usuario](media/azure-stack-use-portal/HelpAndSupport.png)

    En concreto, se recomienda que revise los artículos siguientes para empezar a trabajar:

    - [Consideraciones clave: uso de los servicios o creación de aplicaciones para Azure Stack Hub](azure-stack-considerations.md).
    - En la sección **Uso de servicios** de la documentación, hay un artículo de consideraciones para cada servicio. En la página de consideraciones se describen las diferencias entre el servicio que se ofrece en Azure y el mismo servicio cuando se ofrece en Azure Stack Hub. Para un ejemplo, vea [VM considerations](azure-stack-vm-considerations.md) (Consideraciones para VM). En la sección **Uso de servicios** puede haber otra información que sea única para Azure Stack Hub.

      Puede usar la documentación de Azure como referencia general para un servicio, pero debe tener en cuenta estas diferencias. Sepa que los vínculos de la documentación del icono **Tutoriales de inicio rápido** apuntan a la documentación de Azure.

## <a name="get-support"></a>Obtención de soporte técnico

Si necesita ayuda, póngase en contacto con su organización o proveedor de servicios.

Si usa el Kit de desarrollo de Azure Stack (ASDK), el [foro de Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) es la única fuente de soporte técnico.

## <a name="next-steps"></a>Pasos siguientes

[Consideraciones clave: uso de los servicios o creación de aplicaciones para Azure Stack Hub](azure-stack-considerations.md)
