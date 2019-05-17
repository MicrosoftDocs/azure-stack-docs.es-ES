---
title: Uso del portal de administración de Azure Stack | Microsoft Docs
description: Como operador de Azure Stack, debe aprender a usar el portal de administración.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 71932642d8c44d2043fe185eab3ebda25be7bb3f
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618619"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Inicio rápido: Uso del portal de administración de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Hay dos portales en Azure Stack: el portal de administración y el portal de usuarios (también llamado en ocasiones portal de *inquilinos*). Como operador de Azure Stack, puede usar el portal de administración para la administración y las operaciones diarias de Azure Stack.

## <a name="access-the-administrator-portal"></a>Acceso al portal de administración

Para acceder al portal de administración, vaya a la dirección URL del portal e inicie sesión con las credenciales de un operador de Azure Stack. En un sistema integrado, la dirección URL del portal varía según el nombre de la región y el nombre de dominio completo (FQDN) externo de la implementación de Azure Stack. La dirección URL del portal de administración es siempre la misma para las implementaciones del Kit de desarrollo de Azure Stack (ASDK). 

| Entorno | Dirección URL del portal de administración |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Sistemas integrados | https://adminportal.&lt;*región*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> En un entorno de ASDK, primero deberá asegurarse de que puede [conectarse al host del kit de desarrollo](../asdk/asdk-connect.md) mediante Conexión a Escritorio remoto o una red privada virtual (VPN).

 ![Portal de administración](media/azure-stack-manage-portals/admin-portal.png)

La zona horaria predeterminada para todas las implementaciones de Azure Stack se establece conforme a la Hora universal coordinada (UTC). 

En el portal de administración, puede hacer lo siguiente:

* [Registro de Azure Stack en Azure](azure-stack-registration.md)
* [Rellenado de Marketplace](azure-stack-download-azure-marketplace-item.md)
* [Creación de planes, ofertas y suscripciones para los usuarios](azure-stack-plan-offer-quota-overview.md)
* [Supervisión de estado y alertas](azure-stack-monitor-health.md)
* [Administración de las actualizaciones de Azure Stack](azure-stack-updates.md)

El icono del **tutorial de inicio rápido** proporciona vínculos a documentación en línea para las tareas más comunes.

Aunque un operador puede crear recursos, como máquinas virtuales, redes virtuales y cuentas de almacenamiento en el portal de administración, debe [iniciar sesión en el portal de usuarios](../user/azure-stack-use-portal.md) para crear y probar los recursos.

>[!NOTE]
>El vínculo **Crear una máquina virtual** del icono del tutorial de inicio rápido permite crear una máquina virtual en el portal de administración, pero su única función es validar Azure Stack después de que se haya implementado correctamente.

## <a name="understand-subscription-behavior"></a>Información acerca del comportamiento de una suscripción

Hay tres suscripciones creadas de manera predeterminada en el portal de administración: consumo, proveedor predeterminado y medición. Como operador, principalmente usará la *suscripción del proveedor predeterminada*. No se pueden agregar otras suscripciones y usarlas en el portal de administración. 

Los usuarios crean otras suscripciones en el portal de usuarios en función de los planes y ofertas que se creen para ellos. Sin embargo, el portal de usuarios no proporciona acceso a ninguna de las funcionalidades administrativas u operativas del portal de administración.

Los portales de administración y de usuarios se apoyan en instancias independientes de Azure Resource Manager. Debido a la separación de Azure Resource Manager, las suscripciones no se cruzan de un portal a otro. Por ejemplo, si, como operador de Azure Stack, inicia sesión en el portal de usuarios, no puede acceder a la *suscripción de proveedor predeterminada*. Aunque no tiene acceso a las funciones administrativas, puede crear suscripciones para usted mismo a partir de ofertas públicas disponibles. Siempre que haya iniciado sesión en el portal de usuarios se le considera un usuario inquilino.

  >[!NOTE]
  >En el entorno del ASDK, si un usuario pertenece al mismo directorio de inquilino que el operador de Azure Stack, no se le impide iniciar sesión en el portal de administración. Sin embargo, no pueden acceder a las funciones de administración ni agregar suscripciones desde el portal de administración para acceder a las ofertas disponibles en el portal de usuarios.

## <a name="administration-portal-tips"></a>Sugerencias del portal de administración

### <a name="customize-the-dashboard"></a>Personalización del panel

El panel contiene un conjunto de iconos predeterminados. Puede seleccionar **Edit dashboard** (Editar panel) para modificar el panel predeterminado o **New dashboard** (Nuevo panel) para agregar un panel personalizado. Es muy fácil agregar iconos a un panel. Por ejemplo, puede seleccionar **+ Create a resource** (+ Crear un recurso), hacer clic con el botón derecho en **Offers + Plans** (Ofertas y planes) y luego seleccionar **Pin to dashboard** (Anclar al panel).

En ocasiones, puede que vea un panel en blanco en el portal. Para recuperar el panel, haga clic en **Edit Dashboard** (Editar panel) y, a continuación, haga clic con el botón derecho y seleccione **Reset to default state** (Restaurar al estado predeterminado).

### <a name="quick-access-to-online-documentation"></a>Acceso rápido a la documentación en línea

Para acceder a la documentación del operador de Azure Stack, use el icono de Ayuda y soporte técnico (signo de interrogación) de la esquina superior derecha del portal de administración. Mueva el cursor hasta el icono y seleccione **Ayuda y soporte técnico**.

### <a name="quick-access-to-help-and-support"></a>Acceso rápido a ayuda y soporte técnico

Si selecciona el icono de ayuda y soporte técnico (signo de interrogación) en la esquina superior derecha del portal de administración y, a continuación, seleccione **New support request** (Nueva solicitud de soporte técnico), se producirá uno de los siguientes resultados:

- Si usa un sistema integrado, esta acción abre un sitio donde puede abrir directamente un vale de soporte técnico para los servicios de soporte al cliente de Microsoft (CSS). Consulte [dónde obtener soporte técnico](azure-stack-manage-basics.md#where-to-get-support) para saber cuándo debe acudir al servicio de soporte técnico de Microsoft o al soporte técnico del proveedor de hardware OEM (fabricante de equipo original).
- Si usa ASDK, esta acción abre directamente el [sitio de foros de Azure Stack](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack). Estos foros se supervisan periódicamente. Dado que ASDK es un entorno de evaluación, el servicio de asistencia al cliente (CSS) de Microsoft no ofrece soporte técnico oficial.

### <a name="quick-access-to-the-azure-roadmap"></a>Acceso rápido a la hoja de ruta de Azure

Si selecciona **Ayuda y soporte técnico** (el signo de interrogación) en la esquina superior derecha del portal de administración y después **Hoja de ruta de Azure**, se abrirá una nueva pestaña del explorador con la hoja de ruta de Azure. Si escribe **Azure Stack** en el cuadro de búsqueda de **Productos**, podrá ver todas las actualizaciones de la hoja de ruta de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

[Regístrese en Azure Stack con Azure](azure-stack-registration.md) y rellene [Marketplace de Azure Stack](azure-stack-marketplace.md) con los elementos para ofrecer a los usuarios. 
