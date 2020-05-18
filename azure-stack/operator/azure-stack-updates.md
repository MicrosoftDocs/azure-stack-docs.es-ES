---
title: Administración de actualizaciones
description: Aprenda a administrar las actualizaciones en Azure Stack Hub
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 05/13/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: cdf2ba4d3dd226ea727efe05dc8be671ba172f8b
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374607"
---
# <a name="manage-updates-in-azure-stack-hub"></a>Administración de actualizaciones en Azure Stack Hub

Azure Stack Hub se mantiene actualizado gracias tanto a las actualizaciones, completas o rápidas, y las revisiones, como a las actualizaciones de controladores y firmware del fabricante de equipos originales. En este artículo se explican los diferentes tipos de actualizaciones, cuándo se espera su lanzamiento y dónde se puede encontrar más información sobre la versión actual.

> [!Note]  
> No puede aplicar actualizaciones de Azure Stack Hub al kit de desarrollo de Azure Stack (ASDK). Las actualizaciones están diseñadas para sistemas integrados. Para obtener información, consulte [Volver a implementar el ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Tipos de actualizaciones

Hay tres tipos de actualizaciones para los sistemas integrados:

- **Actualizaciones de software de Azure Stack Hub**. Microsoft es responsable del ciclo de vida de servicio de un extremo a otro de las actualizaciones de software de Microsoft. Estos paquetes pueden incluir las últimas actualizaciones de seguridad de Windows Server, actualizaciones que no son de seguridad y actualizaciones de características de Azure Stack Hub. Descargue estas actualizaciones directamente desde Microsoft.

    Cada actualización tiene un tipo correspondiente: **Completo** o **Rápido**.

    Las actualizaciones **completas** actualizan los sistemas operativos del host físico de la unidad de escalado y requieren una ventana de mantenimiento mayor.

    Las actualizaciones **rápidas** están limitadas y no actualizan los sistemas operativos subyacentes del host físico.

- **Revisiones de Azure Stack Hub**. Microsoft ofrece revisiones para Azure Stack Hub que solucionan un determinado problema que suele ser de naturaleza preventiva o urgente. Todas las revisiones se publican con su correspondiente artículo de Microsoft Knowledge Base que detalla el problema, la causa y la resolución. Descargue e instale revisiones como los paquetes de actualizaciones completas normales para Azure Stack Hub. Las revisiones son acumulativas y pueden instalarse en cuestión de minutos.

- **Actualizaciones suministradas por el proveedor de hardware de OEM**. Los asociados de hardware de Azure Stack Hub son responsables del ciclo de vida de servicio de un extremo a otro (lo que incluye las instrucciones) de las actualizaciones de controladores y firmware relacionados con el hardware. Asimismo, poseen y mantienen las instrucciones de todo el software y el hardware del host de ciclo de vida del hardware. El proveedor de hardware OEM hospeda estas actualizaciones en su propio sitio de descarga.

## <a name="when-to-update"></a>Cuándo debe realizar la actualización

Los tres tipos de actualizaciones se lanzan con la siguiente cadencia:

- **Actualizaciones de software de Azure Stack Hub**. Microsoft suele lanzar paquetes de actualización de software cada mes.

- **Revisiones de Azure Stack Hub**. Las revisiones son versiones sujetas a una limitación temporal que se pueden publicar en cualquier momento.

- **Actualizaciones suministradas por el proveedor de hardware OEM**. Los proveedores de hardware de OEM lanzan sus actualizaciones en función de la necesidad.

Para seguir recibiendo soporte técnico, debe mantener su entorno de Azure Stack Hub con una versión de software de Azure Stack Hub compatible. Para más información, consulte la [Directiva de mantenimiento de Azure Stack Hub](azure-stack-update-servicing-policy.md).

## <a name="how-to-know-an-update-is-available"></a>Aviso de actualizaciones disponibles

El aviso de actualizaciones varía en función de un par de factores, como su conexión a Internet y el tipo de actualización.

- **Actualizaciones y revisiones de software de Microsoft**

    Aparecerá una alerta de actualización para las actualizaciones y revisiones de software de Microsoft en la hoja **Actualización** para las instancias de Azure Stack Hub que están conectadas a Internet. Si no aparece la hoja **Actualización**, reinicie la máquina virtual del controlador de administración de la infraestructura.

    Si su instancia no está conectada y desea recibir una notificación sobre cada versión de revisión, suscríbase a la fuente [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) o [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom).

- **Actualizaciones suministradas por el proveedor de hardware de OEM**

    Las actualizaciones de OEM dependerán de su fabricante. Tendrá que establecer un canal de comunicación con su fabricante de equipos originales para saber cuándo tiene actualizaciones que se deban aplicar. Para más información sobre los OEM y el proceso de actualización de los OEM, consulte [Aplicación de las actualizaciones del fabricante de equipos originales (OEM) de Azure Stack Hub](azure-stack-update-oem.md).

## <a name="update-processes"></a>Procesos de actualización

Una vez que sepa que tiene una actualización, aplíquela con los siguientes pasos.

![Proceso de actualización de Azure Stack Hub](./media/azure-stack-updates/azure-stack-update-process.svg)

1. **Planee la actualización**.

    Prepare Azure Stack Hub para que el proceso de actualización se realice de la forma más fluida posible a fin de que tenga un impacto mínimo para sus usuarios. Notifique a los usuarios cualquier posible interrupción del servicio y, luego, siga los pasos para preparar la instancia para la actualización. Asegúrese de seguir **TODOS** los pasos de la [lista de comprobación previa a la actualización de Azure Stack Hub](release-notes-checklist.md) para asegurarse de que ha completado los pasos previos necesarios para aplicar una actualización. Asegúrese también de programar una ventana de mantenimiento adecuada para el tipo de actualización que se va a aplicar.

2. **Cargue y prepare la actualización**.

    Para los entornos de Azure Stack Hub conectados a Internet, las actualizaciones y revisiones de software de Azure Stack Hub se importan automáticamente al sistema y se preparan para la actualización.

    En el caso de los entornos de Azure Stack Hub sin conexión a Internet y los entornos con conectividad a Internet débil o intermitente, los paquetes de actualización se importan a Azure Stack Hub Storage mediante el portal de administración de Azure Stack Hub. Para obtener más pasos para cargar y preparar la actualización, consulte [Cargar y preparar una actualización de Azure Stack Hub](azure-stack-update-prepare-package.md).

    Todas las actualizaciones de OEM se importan manualmente al entorno, independientemente de la conectividad a Internet del sistema de Azure Stack Hub. Para obtener más pasos para importar y preparar la actualización, consulte [Cargar y preparar una actualización de Azure Stack Hub](azure-stack-update-prepare-package.md).

3. **Aplique la actualización**.

    Aplique la actualización con la hoja **Actualización** en Azure Stack Hub. Durante la actualización, supervise su progreso y solucione los posibles problemas. Para más información, consulte [Aplicación de actualizaciones en Azure Stack Hub](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>El proveedor de recursos de actualización

Azure Stack Hub incluye un proveedor de recursos de actualización que controla la aplicación de las actualizaciones de software de Microsoft. Este proveedor comprueba que las actualizaciones se apliquen en todos los hosts físicos, entornos en tiempo de ejecución y aplicaciones de Service Fabric, y en todas las máquinas virtuales de la infraestructura y sus servicios asociados.

Cuando se instalan las actualizaciones, puede ver el estado general a medida que el proceso de actualización alcanza los diversos subsistemas de Azure Stack Hub (por ejemplo, hosts físicos y máquinas virtuales de la infraestructura).

## <a name="next-steps"></a>Pasos siguientes

- Para comenzar el proceso de actualización, siga los pasos que se indican en [Lista de comprobación de actividades para la actualización de Azure Stack Hub](release-notes-checklist.md).
- Para saber qué versiones de Azure Stack Hub tienen soporte técnico, consulte [Directivas de mantenimiento de Azure Stack Hub](azure-stack-servicing-policy.md).  
- Para más información sobre las actualizaciones actuales y recientes, consulte las [Notas de la versión de Azure Stack Hub](release-notes.md).
