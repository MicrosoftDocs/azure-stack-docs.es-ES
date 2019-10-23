---
title: Administrar las actualizaciones en Azure Stack | Microsoft Docs
description: Aprenda a administrar las actualizaciones en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 0a784a80931113b7901a021a7c2ff707ce7df355
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283271"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Introducción a la administración de actualizaciones en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Azure Stack se mantiene actualizado gracias tanto a las actualizaciones, completas o rápidas, y las revisiones como a las actualizaciones de controladores y firmware del fabricante de equipo original (OEM). En este artículo se explican los diferentes tipos de actualizaciones, cuándo se espera su lanzamiento y dónde se puede encontrar más información sobre la versión actual.

> [!Note]  
> No puede aplicar actualizaciones de Azure Stack al Kit de desarrollo de Azure Stack (ASDK). Las actualizaciones están diseñadas para sistemas integrados. Para obtener información, consulte [Volver a implementar el ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Tipos de actualizaciones

Hay tres tipos de actualizaciones para los sistemas integrados:

-   **Actualizaciones de software de Azure Stack**. Microsoft es responsable del ciclo de vida de servicio de un extremo a otro de las actualizaciones de software de Microsoft. Estas actualizaciones pueden incluir las últimas actualizaciones de seguridad de Windows Server, las actualizaciones que no son de seguridad y las actualizaciones de características de Azure Stack. Descargue estas actualizaciones directamente desde Microsoft.

    Cada actualización tiene un tipo correspondiente, **Completo** o **Rápido**. 
 
    Las actualizaciones **completas** actualizan los sistemas operativos del host físico de la unidad de escalado y requieren una ventana de mantenimiento mayor. 

    Las actualizaciones **rápidas** están limitadas y no actualizan los sistemas operativos subyacentes del host físico.

-   **Revisiones de Azure Stack**. Microsoft ofrece revisiones para Azure Stack que solucionan un determinado problema que suele ser de naturaleza preventiva o urgente. Todas las revisiones se publican con su correspondiente artículo de Microsoft Knowledge Base que detalla el problema, la causa y la resolución. Descargue e instale revisiones como las actualizaciones completas normales para Azure Stack. Las revisiones son acumulativas y pueden instalarse en cuestión de minutos.

-   **Actualizaciones suministradas por el proveedor de hardware de OEM**. Los asociados de hardware de Azure Stack son responsables del ciclo de vida de servicio de un extremo a otro (lo que incluye las instrucciones) de las actualizaciones de controladores y firmware relacionados con el hardware. Asimismo, poseen y mantienen las instrucciones de todo el software y el hardware del host de ciclo de vida del hardware. El proveedor de hardware OEM hospeda estas actualizaciones en su propio sitio de descarga.

## <a name="when-to-update"></a>Cuándo debe realizar la actualización

Los tres tipos de actualizaciones se lanzan con la siguiente cadencia:

-   **Actualizaciones de software de Azure Stack**. Microsoft suele lanzar paquetes de actualización de software cada mes.

-   **Revisiones de Azure Stack**. Las revisiones son versiones sujetas a una limitación temporal que se pueden publicar en cualquier momento.

-   **Actualizaciones suministradas por el proveedor de hardware OEM**. Los proveedores de hardware de OEM lanzan sus actualizaciones en función de la necesidad.

Para seguir recibiendo soporte técnico, debe mantener su entorno de Azure Stack con una versión de software de Azure Stack compatible. Para más información, consulte la [Directiva de mantenimiento de Azure Stack](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Dónde recibirá el aviso de una actualización

El aviso de actualizaciones varía en función de un par de factores, como su conexión a Internet y el tipo de actualización.

- **Actualizaciones y revisiones de software de Microsoft** 

    Aparecerá una alerta de actualización para las actualizaciones y revisiones de software de Microsoft en la hoja Actualización para las instancias de Azure Stack que están conectadas a Internet. Si no aparece la hoja Actualizar, reinicie la máquina virtual del controlador de administración de la infraestructura.

    Si su instancia no está conectada y desea recibir una notificación sobre cada versión de revisión, suscríbase a la fuente [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) o [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom).

- **Actualizaciones suministradas por el proveedor de hardware de OEM**

    Las actualizaciones de OEM dependerán de su fabricante. Tendrá que establecer un canal de comunicación con su OEM para saber cuándo tiene actualizaciones de su OEM que debe aplicar. Para obtener más información sobre los OEM y el proceso de actualización de los OEM, consulte [Aplicar las actualizaciones del fabricante de equipos originales (OEM) de Azure Stack](azure-stack-update-oem.md).

## <a name="update-processes"></a>Procesos de actualización

Una vez que sepa que tiene una actualización, aplíquela con los siguientes pasos.

![Proceso de actualización de Azure Stack](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Planee la actualización**.

    Prepare Azure Stack para que el proceso de actualización se realice de la forma más fluida posible a fin de que tenga un impacto mínimo para sus usuarios. Notifique a los usuarios cualquier posible interrupción del servicio y, luego, siga los pasos para preparar la instancia para la actualización. Para obtener más pasos para planificar la actualización, consulte [Planear una actualización de Azure Stack](azure-stack-update-plan.md).

2. **Cargue y prepare la actualización**.

    Para los entornos de Azure Stack conectados a Internet, las actualizaciones y revisiones de software de Azure Stack se importan automáticamente al sistema y se preparan para la actualización.

    En el caso de los entornos de Azure Stack sin conexión a Internet y los entornos con conectividad a Internet débil o intermitente, los paquetes de actualización se importan al almacenamiento de Azure Stack a través del portal de administrador de Azure Stack. Para obtener más pasos para cargar y preparar la actualización, consulte [Cargar y preparar una actualización de Azure Stack](azure-stack-update-prepare-package.md).

    Todas las actualizaciones de OEM se importan manualmente al entorno, independientemente de la conectividad a Internet del sistema de Azure Stack. Para obtener más pasos para importar y preparar la actualización, consulte [Cargar y preparar una actualización de Azure Stack](azure-stack-update-prepare-package.md).

3. **Aplique la actualización**.

    Aplique la actualización con la hoja **Actualización** en Azure Stack. Durante la actualización, supervise su progreso y solucione los posibles problemas. Para más información, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>El proveedor de recursos de actualización

Azure Stack incluye un proveedor de recursos de actualización que controla la aplicación de las actualizaciones de software de Microsoft. Este proveedor comprueba que las actualizaciones se apliquen en todos los hosts físicos, entornos en tiempo de ejecución y aplicaciones de Service Fabric, y en todas las máquinas virtuales de la infraestructura y sus servicios asociados.

Cuando se instalan las actualizaciones, puede ver el estado general a medida que el proceso de actualización alcanza los diversos subsistemas de Azure Stack (por ejemplo, hosts físicos y máquinas virtuales de la infraestructura).

## <a name="next-steps"></a>Pasos siguientes

- Para comenzar el proceso de actualización, consulte los pasos en [Planear una actualización de Azure Stack](azure-stack-update-plan.md).
- Para saber qué versiones de Azure Stack son compatibles, consulte [Directivas de servicio de Azure Stack](azure-stack-servicing-policy.md).  
- Para obtener más información sobre las actualizaciones actuales y recientes, consulte las [Notas de la versión de Azure Stack](release-notes.md).
