---
title: Introducción a la recopilación de registros de diagnóstico de Azure Stack Hub
description: Explica la recopilación de registros de diagnóstico en Ayuda y soporte técnico de Azure Stack Hub, incluida la recopilación de registros a petición y proactiva.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 8f97ecd20e7ef8db69033268baf96060e1315751
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520636"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Introducción a la recopilación de registros de diagnóstico de Azure Stack Hub 

Azure Stack Hub es una gran colección de componentes de Windows y servicios locales de Azure que interactúan entre sí. Todos estos componentes y servicios generan su propio conjunto de registros. Para que los servicios de soporte técnico al cliente de Microsoft puedan diagnosticar los problemas de forma eficaz, hemos creado un sistema ágil de recopilación de registros de diagnóstico. 

La recopilación de registros de diagnóstico de Ayuda y soporte técnico ayuda a los operadores a recopilar rápidamente y a compartir registros de diagnóstico con los servicios de soporte técnico al cliente de Microsoft, una interfaz de usuario sencilla, que no requiere PowerShell. Los registros se recopilan aunque haya otros servicios de infraestructura inactivos.  
 
Se recomienda usar este enfoque de recopilación de registros y recurrir al [uso del punto de conexión con privilegios (PEP)](azure-stack-get-azurestacklog.md) únicamente si el portal del administrador o la hoja Ayuda y soporte técnico no están disponibles. 

>[!NOTE]
>Azure Stack Hub debe estar registrado y conectado a Internet para usar la recopilación de registros de diagnóstico. Si Azure Stack Hub no está registrado, [utilice el punto de conexión con privilegios (PEP)](azure-stack-get-azurestacklog.md) para compartir los registros. 

![Captura de pantalla de opciones de recopilación de registros de diagnóstico](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Opciones de recopilación y control de datos

La característica de recopilación de registros de diagnóstico ofrece dos opciones para enviar los registros. En la tabla siguiente se explica cada opción y cómo se controlan los datos en cada caso. 

### <a name="send-logs-proactively"></a>Envío de registros de forma proactiva

La [recopilación proactiva de registros](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) agiliza y simplifica la recopilación de registros de diagnóstico para que los clientes puedan enviar los registros a Microsoft antes de abrir una incidencia de soporte técnico. Los registros de diagnóstico se cargan de forma proactiva desde Azure Stack Hub para su análisis. Estos registros solo se recopilan cuando se genera una [alerta de mantenimiento del sistema](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts) y solo se tiene acceso a ellos desde los servicios de soporte técnico al cliente en el contexto de una incidencia de soporte técnico.


#### <a name="how-the-data-is-handled"></a>Cómo se controlan los datos

Usted acepta que Microsoft recopile periódicamente los registros relativos únicamente a las alertas de mantenimiento del sistema de Azure Stack Hub. También acepta y da su consentimiento a la carga y retención de dichos registros en una cuenta de Azure Storage administrada y controlada por Microsoft. 

Los datos se usarán solo con el fin de solucionar problemas de alertas de mantenimiento del sistema y no se usarán para marketing, publicidad ni ningún otro propósito comercial sin su consentimiento. Los datos se pueden conservar durante un máximo de 90 días y cualquier dato que Microsoft recopile se controlará de acuerdo con nuestros [procedimientos de privacidad estándar](https://privacy.microsoft.com/).

Los datos previamente recopilados con su consentimiento no se verán afectados por la revocación del permiso.

Los registros recopilados mediante la **recopilación proactiva de registros** se cargan en una cuenta de Azure Storage administrada y controlada por Microsoft. Microsoft puede acceder a estos registros en el contexto de una incidencia de soporte técnico y para mejorar el mantenimiento de Azure Stack Hub.|

### <a name="send-logs-now"></a>Envío inmediato de registros

[Send logs now](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) (Enviar registros ahora) es una opción manual para cargar los registros de diagnóstico desde Azure Stack Hub solo cuando el cliente inicia la recopilación, normalmente antes de abrir una incidencia de soporte técnico. 

Los operadores de Azure Stack pueden enviar registros de diagnóstico a petición a los servicios de soporte al cliente (CSS) de Microsoft mediante el portal del administrador o PowerShell. Si Azure Stack Hub está conectado a Azure, se recomienda usar la opción [Send logs now](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) (Enviar registros ahora) del portal del administrador, porque es la forma más sencilla de enviar los registros directamente a Microsoft. Si el portal no está disponible, en su lugar, los operadores deben [usar PowerShell para enviar los registros ahora](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Si está desconectado de Internet o solo desea guardar los registros de forma local, use el método [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para enviar los registros. En el diagrama de flujo siguiente se muestra qué opción utilizar para enviar los registros de diagnóstico en cada caso. 

![Diagrama de flujo que muestra el envío inmediato de registros a Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>Cómo se controlan los datos

Al iniciar la recopilación de registros de diagnóstico desde Azure Stack Hub, acepta y da su consentimiento para cargar esos registros y conservarlos en una cuenta de Azure Storage administrada y controlada por Microsoft. Los servicios de soporte técnico al cliente de Microsoft pueden acceder a estos registros de inmediato con la incidencia de soporte técnico sin tener que ponerse en contacto con el cliente para la recopilación de los registros. 

Los datos se usarán solo con el fin de solucionar problemas de alertas de mantenimiento del sistema y no se usarán para marketing, publicidad ni ningún otro propósito comercial sin su consentimiento. Los datos se pueden conservar durante un máximo de 90 días y cualquier dato que Microsoft recopile se controlará de acuerdo con nuestros [procedimientos de privacidad estándar](https://privacy.microsoft.com/). 

Los registros recopilados mediante la opción Send logs now (Enviar registros ahora) se cargan en un almacenamiento administrado y controlado por Microsoft. Microsoft accederá a estos registros en el contexto de una incidencia de soporte técnico y para mejorar el mantenimiento de Azure Stack Hub. 

## <a name="bandwidth-considerations"></a>Consideraciones sobre el ancho de banda

El tamaño medio de la recopilación de los registros de diagnóstico varía en función de si se ejecuta de forma proactiva o manual. El tamaño medio de la **recopilación proactiva de registros** es de aproximadamente 2 GB. El tamaño de la recopilación de la opción **Send logs now** (Enviar registros ahora) depende del número de horas que se van a recopilar.

En la tabla siguiente se enumeran las consideraciones para entornos con conexiones limitadas o de uso medido con Azure.


| Conexión de red | Impacto |
|--------------------|--------|
| Conexión de alta latencia o ancho de banda bajo | La carga de registros tardará una cantidad de tiempo ampliada en completarse | 
| Conexión compartida | La carga solo puede afectar a otras aplicaciones o usuarios que comparten la conexión de red |
| Conexión de uso medido | Puede haber un cargo adicional de su ISP por el uso adicional de la red | 

## <a name="see-also"></a>Consulte también

[Control de datos de clientes y registro de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

