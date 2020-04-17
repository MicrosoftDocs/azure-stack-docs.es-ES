---
title: Ayuda y soporte técnico para Microsoft Azure Stack Hub
description: Obtenga soporte técnico para Microsoft Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: d4d5fbd3979d7f30354cb3716e3277b895ddac78
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520618"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Ayuda y soporte técnico para Microsoft Azure Stack Hub

Los operadores de Azure Stack Hub pueden usar **Ayuda y soporte técnico** para recopilar los registros de diagnóstico y enviarlos a Microsoft para solucionar problemas. Se puede acceder a **Ayuda y soporte técnico** en el portal de Azure Stack Hub desde el portal del administrador. Tiene recursos para ayudar a los operadores a obtener información sobre Azure Stack, comprobar sus opciones de soporte técnico y obtener ayuda experta.  

![Captura de pantalla sobre cómo acceder a Ayuda y soporte técnico en el portal del administrador](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Recursos de ayuda 

Los operadores pueden usar **Ayuda y soporte técnico** para más información sobre Azure Stack Hub, comprobar sus opciones de soporte técnico y obtener ayuda experta. 

### <a name="things-to-try-first"></a>Cosas para probar primero

En la parte superior de **Help + support** (Ayuda y soporte técnico) hay vínculos a cosas que puede probar primero, como leer sobre un nuevo concepto, entender cómo funciona la facturación o ver qué opciones de soporte están disponibles. 

![Soporte mediante autoservicio](media/azure-stack-help-and-support/get-support-tiles.png)

- **Documentación** La [documentación del operador de Azure Stack Hub](index.yml) incluye conceptos, temas y tutoriales de procedimientos que muestran cómo ofrecer servicios de Azure Stack Hub, como máquinas virtuales, bases de datos SQL, aplicaciones web, etc. 

- **Más información sobre la facturación**. Obtenga sugerencias sobre [el uso y la facturación](azure-stack-billing-and-chargeback.md).

- **Opciones de soporte técnico**. Los operadores de Azure Stack Hub pueden elegir entre una gama de [opciones de soporte técnico de Azure](https://aka.ms/azstacksupport) que puede satisfacer las necesidades de cualquier empresa. 


### <a name="get-expert-help"></a>Obtención de ayuda experta 

En el caso de un sistema integrado, hay un proceso de escalamiento y resolución coordinado entre Microsoft y nuestros partners de hardware de fabricante de equipos originales (OEM).

Si hay algún problema en los servicios en la nube, el soporte técnico se ofrece a través de los servicios de soporte al cliente de Microsoft (CSS). Haga clic en el icono de **ayuda** (signo de interrogación) en la esquina superior derecha del portal de administración y, después, haga clic en **Help + support** (Ayuda y soporte técnico) para abrir la **información general de la ayuda y el soporte técnico** y envíe una nueva solicitud de soporte técnico. La creación de una solicitud de soporte técnico preseleccionará el servicio Azure Stack Hub. Se recomienda que los clientes utilicen esta experiencia para enviar incidencias de soporte técnico en lugar de utilizar la versión global de Azure Portal. 

Si surge algún problema en la implementación, aplicación de revisiones y actualizaciones, hardware (incluidas las unidades reemplazables en campo) y cualquier software específico de una marca de hardware, como el software que se ejecuta en el host del ciclo de vida del hardware, en primer lugar póngase en contacto con su proveedor de hardware OEM. Para todo lo demás, póngase en contacto con el servicio de asistencia al cliente (CSS) de Microsoft.

![Obtención de ayuda experta para sistemas integrados](media/azure-stack-help-and-support/get-support-integrated.png)

Para ASDK, puede dirigir las preguntas relacionadas con el soporte técnico a los [foros de MSDN de Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

Haga clic en **Ayuda** (signo de interrogación) en la esquina superior derecha del portal de administración y, después, haga clic en **Help and Support** (Ayuda y soporte técnico) para abrir la **información general de la ayuda y el soporte técnico**, que tiene un vínculo al foro. Los foros de MSDN se supervisan periódicamente.  
Dado que el kit de desarrollo es un entorno de evaluación, el servicio de asistencia al cliente (CSS) de Microsoft no ofrece soporte técnico oficial.

También se puede acceder a los foros de MSDN para tratar un problema o para realizar cursos en línea y mejorar sus conocimientos. 

![Obtención de ayuda experta](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Póngase al día con Azure Stack Hub

Este conjunto de tutoriales se puede personalizar en función de si se ejecutan en ASDK o en sistemas integrados de forma que se pueda poner rápidamente en marcha con su entorno. 

![Obtención de tutoriales de soporte técnico](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Recopilación de registros de diagnóstico

Hay dos formas de enviar los registros de diagnóstico a Microsoft: 

- [Envío de registros de forma proactiva](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md): si está habilitada, las alertas de estado específicas desencadenan la recopilación de registros. 
- [Envío inmediato de registros](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) puede elegir manualmente una ventana deslizante específica para el período de tiempo de la recopilación de registros

![Captura de pantalla de opciones de recopilación de registros de diagnóstico](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [recopilación de registros de diagnóstico](azure-stack-diagnostic-log-collection-overview-tzl.md)
- Obtenga información sobre cómo [buscar el identificador de nube](azure-stack-find-cloud-id.md)
- Más información sobre la [solución de problemas de Azure Stack Hub](azure-stack-troubleshooting.md)
