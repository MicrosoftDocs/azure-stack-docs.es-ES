---
title: Ayuda y soporte técnico de Azure Stack Hub
description: Obtenga soporte técnico para Microsoft Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: db1a7919987803682ff0a9df84565fa7aef47620
ms.sourcegitcommit: 30ea43f486895828710297967270cb5b8d6a1a18
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2020
ms.locfileid: "93415307"
---
# <a name="azure-stack-hub-help-and-support"></a>Ayuda y soporte técnico de Azure Stack Hub

::: moniker range=">= azs-2002"

Los operadores de Azure Stack Hub pueden usar **Ayuda y soporte técnico** para recopilar los registros de diagnóstico y enviarlos a Microsoft para solucionar problemas. A la opción **Ayuda y soporte técnico** del portal de Azure Stack Hub se puede acceder desde el portal del administrador. Tiene recursos para ayudar a los operadores a obtener información sobre Azure Stack, comprobar sus opciones de soporte técnico y obtener ayuda experta.  

![Acceso a la ayuda y soporte técnico en el portal del administrador de Azure Stack Hub](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Recursos de ayuda

Los operadores pueden usar **Ayuda y soporte técnico** para más información sobre Azure Stack Hub, comprobar sus opciones de soporte técnico y obtener ayuda experta.

### <a name="things-to-try-first"></a>Cosas para probar primero

En la parte superior de **Ayuda y soporte técnico** hay opciones que debería probar primero, como leer la información relativa a los nuevos conceptos, aprender cómo funciona la facturación o ver qué opciones de soporte técnico están disponibles.

![Compatibilidad con el autoservicio en Azure Stack Hub](media/azure-stack-help-and-support/get-support-tiles.png)

- **Documentación** La [documentación del operador de Azure Stack Hub](index.yml) incluye conceptos, temas y tutoriales de procedimientos que muestran cómo ofrecer servicios de Azure Stack Hub. Estos servicios incluyen máquinas virtuales, bases de datos SQL, aplicaciones web, etc.

- **Más información sobre la facturación**. Obtenga sugerencias sobre [el uso y la facturación](azure-stack-billing-and-chargeback.md).

- **Opciones de soporte técnico**. Los operadores de Azure Stack Hub pueden elegir entre una gama de [opciones de soporte técnico de Azure](./azure-stack-manage-basics.md) que puede satisfacer las necesidades de cualquier empresa.

### <a name="get-expert-help"></a>Obtención de ayuda experta

En el caso de un sistema integrado, hay un proceso de escalación y resolución coordinado entre Microsoft y nuestros asociados de hardware de fabricante de equipos originales (OEM).

Si hay algún problema en los servicios en la nube, el soporte técnico se ofrece a través de Soporte técnico de Microsoft. Puede seleccionar **Ayuda** (signo de interrogación) en la esquina superior derecha del portal de administración y, después, seleccionar **Ayuda y soporte técnico** para abrir la **información general de la ayuda y soporte técnico** , y enviar una nueva solicitud de soporte técnico. La creación de una solicitud de soporte técnico preseleccionará el servicio Azure Stack Hub. Se recomienda que los clientes utilicen esta experiencia para enviar incidencias de soporte técnico en lugar de utilizar la versión global de Azure Portal.

Si surge algún problema de implementación, aplicación de revisiones y actualizaciones, hardware (incluidas las unidades reemplazables en campo) y cualquier software específico de una marca de hardware (como el software que se ejecuta en el host del ciclo de vida del hardware) en primer lugar póngase en contacto con el proveedor de hardware OEM. Para todo lo demás, póngase en contacto con Soporte técnico de Microsoft.

![Obtención de ayuda experta para sistemas integrados](media/azure-stack-help-and-support/get-support-integrated.png)

En el caso del Kit de desarrollo de Azure Stack, puede realizar las preguntas relacionadas con el soporte técnico en el [foro de MSDN de Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Seleccione **Ayuda** (signo de interrogación) en la esquina superior derecha del portal de administración y, después, seleccione **Ayuda y soporte técnico** para abrir la **información general de la ayuda y el soporte técnico** , que tiene un vínculo al foro. Los foros de MSDN se supervisan periódicamente. Dado que ASDK es un entorno de evaluación, Soporte técnico de Microsoft no ofrece soporte técnico oficial.

También se puede acceder a los foros de MSDN para tratar un problema o para realizar cursos en línea y mejorar sus conocimientos.

![Obtener ayuda experta para Azure Stack Hub](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Póngase al día con Azure Stack Hub

Este conjunto de tutoriales se puede personalizar en función de si se ejecutan en ASDK o en sistemas integrados de forma que se pueda poner rápidamente en marcha con su entorno.

![Obtener tutoriales de soporte técnico para Azure Stack Hub](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Recopilación de registros de diagnóstico

Los registros de diagnóstico se pueden enviar a Microsoft de dos formas:

- [Envío de registros de forma proactiva](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively): si está habilitada, ciertas alertas de estado desencadenan la recopilación de registros.
- [Envío inmediato de registros](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now) puede elegir manualmente una ventana deslizante específica como período de tiempo de la recopilación de registros.

![Captura de pantalla que muestra cómo iniciar la recopilación de registros de diagnóstico.](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>Recopilación de registros de diagnóstico

A partir de la versión 1907, hay dos nuevas formas de recopilar registros en **Ayuda y soporte técnico** :

- **Recopilación automática** : si está habilitada, ciertas alertas de estado desencadenan la recopilación de registros.
- **Recopilar registros ahora** : puede elegir una ventana deslizante de entre 1 y 4 horas de los últimos siete días.

![Opciones de recopilación de registros de diagnóstico](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Los sistemas integrados pueden compartir los registros de diagnóstico con Soporte técnico de Microsoft. Dado que el Kit de desarrollo de Azure Stack (ASDK) es un entorno de evaluación, no es compatible con Soporte técnico de Microsoft. Para más información, consulte [Información general sobre la recopilación de registros de diagnóstico de Azure Stack Hub](azure-stack-diagnostic-log-collection-overview.md).

## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Ayuda y soporte técnico para las versiones anteriores de Azure Stack Hub (anteriores a la versión 1905)

Las versiones anteriores de Azure Stack Hub también tienen un vínculo a la **ayuda y soporte técnico** que redirige a la [documentación del operador de Azure Stack Hub](./index.yml).

![Obtención de tutoriales de soporte técnico](media/azure-stack-help-and-support/get-support-previous.png)

Si hay algún problema en los servicios en la nube, el soporte técnico se ofrece a través de Soporte técnico de Microsoft. Puede seleccionar **Ayuda** (signo de interrogación) en la esquina superior derecha del portal de administración, luego **Ayuda y soporte técnico** y, finalmente, **Nueva solicitud de soporte técnico** para enviar directamente una nueva solicitud de soporte técnico con Soporte técnico de Microsoft.

En el caso de un sistema integrado, hay un proceso de escalación y resolución coordinadas entre Microsoft y nuestros asociados OEM. Si hay algún problema en los servicios en la nube, el soporte técnico se ofrece a través de Soporte técnico de Microsoft.

Si surge algún problema en la implementación, aplicación de revisiones y actualizaciones, hardware (incluidas las unidades reemplazables en campo) y cualquier software específico de una marca de hardware, como el software que se ejecuta en el host del ciclo de vida del hardware, en primer lugar póngase en contacto con el proveedor de hardware OEM. Para todo lo demás, póngase en contacto con Soporte técnico de Microsoft.

Para ASDK, puede dirigir las preguntas relacionadas con el soporte técnico a los [foros de MSDN de Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Seleccione **Ayuda** (signo de interrogación) en la esquina superior derecha del portal de administración y, después, **Nueva solicitud de soporte técnico** para obtener ayuda de los expertos de la comunidad de Azure Stack Hub. Dado que ASDK es un entorno de evaluación, Soporte técnico de Microsoft no ofrece soporte técnico oficial.

::: moniker-end

## <a name="next-steps"></a>Pasos siguientes

- Información sobre la [recopilación de registros de diagnóstico](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002).
- Aprenda a [buscar el identificador de su nube](azure-stack-find-cloud-id.md).
- Aprenda a [solucionar problemas de Azure Stack Hub](azure-stack-troubleshooting.md).