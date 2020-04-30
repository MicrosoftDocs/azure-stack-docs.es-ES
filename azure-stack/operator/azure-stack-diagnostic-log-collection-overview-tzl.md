---
title: Introducción a la recopilación de registros de diagnóstico de Azure Stack Hub
description: Explica la recopilación de registros de diagnóstico en Ayuda y soporte técnico de Azure Stack Hub, incluida la recopilación de registros a petición y proactiva.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: b1c1048a8ad8bdb8d16d2e86c82febc8c74b03af
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/17/2020
ms.locfileid: "81624948"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Introducción a la recopilación de registros de diagnóstico de Azure Stack Hub 

::: moniker range=">= azs-2002"

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

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Recopilación de registros de varios sistemas de Azure Stack Hub

Configure un contenedor de blobs para todas las unidades de escalado de Azure Stack Hub de las que quiera recopilar registros. Para más información sobre cómo configurar el contenedor de blobs, consulte [Configuración de la recopilación de registros de diagnóstico automática de Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Como procedimiento recomendado, guarde solo los registros de diagnóstico de la misma unidad de escalado de Azure Stack Hub en un solo contenedor de blobs. 

## <a name="retention-policy"></a>Directiva de retención

Cree una [regla de administración del ciclo de vida](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) de Azure Blob Storage para administrar la directiva de retención de registros. Recomendamos que los registros de diagnóstico se retengan durante 30 días. Para crear una regla de administración del ciclo de vida en Azure Storage, inicie sesión en Azure Portal, haga clic en **Cuentas de almacenamiento** y en el contenedor de blobs y, en **Blob service**, haga clic en **Administración del ciclo de vida**.

![Captura de pantalla que muestra la administración del ciclo de vida en Azure Portal](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>Expiración del token de SAS

Establezca la expiración de la dirección URL de SAS en dos años. Si alguna vez renueva sus claves de cuenta, asegúrese de regenerar la dirección URL de SAS. Debe administrar el token de SAS según los procedimientos recomendados. Para obtener más información, consulte [Procedimientos recomendados al usar SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).


## <a name="bandwidth-consumption"></a>Consumo de ancho de banda

El tamaño medio de la recopilación de registros de diagnóstico varía en función de si esta es a petición o automática. 

Para la recopilación de registros a petición, el tamaño de la recopilación de registros depende de cuántas horas se recopilan. Puede elegir cualquier ventana deslizante de entre 1 y 4 horas de los últimos siete días. 

Al habilitarse la recopilación de registros de diagnóstico automática, el servicio se supervisa para alertas críticas. 
Una vez que se genera una alerta crítica y persiste durante unos 30 minutos, el servicio recopila y carga los registros adecuados. 
El tamaño medio de esta recopilación de registros es aproximadamente 2 GB. 
En caso de que se produzca un error de revisiones y actualizaciones, la recopilación de registros automática solo se iniciará si se genera una alerta crítica y persiste durante unos 30 minutos. Recomendamos que siga las [instrucciones sobre la supervisión de las revisiones y actualizaciones](azure-stack-updates.md).
La supervisión de alertas, la recopilación de registros y la carga son transparentes para el usuario. 



En un sistema correcto, no se recopilarán registros en absoluto. 
En un sistema incorrecto, la recopilación de registros puede ejecutarse dos o tres veces en un día, pero normalmente se ejecuta solo una vez. 
Como máximo, podría ejecutarse hasta diez veces en un día en el peor de los escenarios.  

La siguiente tabla puede ayudar a los entornos con conexiones limitadas a Azure a tener en cuenta el impacto de la habilitación de la recopilación de registros automática.

| Conexión de red | Impacto |
|--------------------|--------|
| Conexión de alta latencia o ancho de banda bajo | La carga de registros tardará una cantidad de tiempo ampliada en completarse | 
| Conexión compartida | La carga solo puede afectar a otras aplicaciones o usuarios que comparten la conexión de red |
| Conexión de uso medido | Puede haber un cargo adicional de su ISP por el uso adicional de la red |


## <a name="managing-costs"></a>Administración de los costos

Los [cargos de Azure Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) Storage dependen de cuántos datos se guardan cada mes y de otros factores como la redundancia de datos. 
Si no tiene una cuenta de almacenamiento existente, puede iniciar sesión en Azure Portal, hacer clic en **Cuentas de almacenamiento** y seguir los pasos para [crear una dirección URL de SAS del contenedor de blobs de Azure](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).

Como procedimiento recomendado, cree una [directiva de administración del ciclo de vida](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) de Azure Blob Storage para minimizar los costos de almacenamiento en curso. Para más información sobre cómo configurar la cuenta de almacenamiento, consulte [Configuración de la recopilación de registros de diagnóstico automática de Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).

::: moniker-end

## <a name="see-also"></a>Consulte también

[Control de datos de clientes y registro de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

