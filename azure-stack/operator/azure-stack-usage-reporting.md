---
title: Notificar los datos de uso de Azure Stack Hub a Azure
titleSuffix: Azure Stack Hub
description: Aprenda a notificar los datos de uso de Azure Stack Hub a Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: ed0bcd873d6bd33017172eb3accad227e2073668
ms.sourcegitcommit: 5f53810d3c5917a3a7b816bffd1729a1c6b16d7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972642"
---
# <a name="report-azure-stack-hub-usage-data-to-azure"></a>Notificar los datos de uso de Azure Stack Hub a Azure

Los datos de uso, también denominados datos de consumo, representan la cantidad de recursos usados.

Los sistemas de varios nodos de Azure Stack Hub que usan el modelo de facturación basado en el consumo deben notificar los datos de uso a Azure para la facturación. Los operadores de Azure Stack Hub deben configurar su instancia de Azure Stack Hub para informar de los datos de uso a Azure.

> [!IMPORTANT]
> Todas las cargas de trabajo [se deben implementar en suscripciones de inquilino](#are-users-charged-for-the-infrastructure-vms) a fin de cumplir los términos de licencia de Azure Stack Hub.

Los informes de datos de uso son obligatorios para los usuarios de varios nodos de Azure Stack Hub con una licencia de modelo de pago por uso. Es opcional para los clientes con una licencia de modelo de capacidad (consulte la página [Cómo comprar](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)). Para los usuarios del Kit de desarrollo de Azure Stack (ASDK), los operadores de Azure Stack Hub pueden notificar los datos de uso y probar la característica. Sin embargo, no se cobrará a los usuarios por el uso.

![Flujo de facturación por el uso de datos en Azure Stack Hub](media/azure-stack-usage-reporting/billing-flow.png)

Los datos de uso se envían desde Azure Stack Hub a Azure mediante Azure Bridge. En Azure, el sistema commerce procesa los datos de uso y genera la factura. Cuando se genera la factura, el propietario de la suscripción de Azure puede verla y descargarla desde el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions). Para obtener información sobre cómo se concede la licencia de Azure Stack Hub, consulte el [documento de precios y empaquetado de Azure Stack Hub](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Configuración de informes de datos de uso

Para configurar los informes de datos de uso, debe [registrar la instancia de Azure Stack Hub en Azure](azure-stack-registration.md). Como parte del proceso de registro, se configura el componente Azure Bridge de Azure Stack Hub. El componente Azure Bridge es lo que conecta Azure Stack Hub a Azure. Los siguientes datos de uso se envían desde Azure Stack Hub a Azure:

- **Id. de medidor**: identificador único del recurso que se consumió.
- **Cantidad**: cantidad de uso de los recursos.
- **Ubicación**: ubicación donde está implementado el recurso actual de Azure Stack Hub.
- **URI de recurso**: URI completo del recurso para el que se notifica el uso.
- **Id. de suscripción**: identificador de suscripción del usuario de Azure Stack Hub, que es la suscripción (Azure Stack Hub) local.
- **Tiempo**: hora de inicio y finalización de los datos de uso. Hay un retraso determinado entre el momento en que se usan estos recursos en Azure Stack Hub y el momento en que se notifican los datos de uso al comercio. Azure Stack Hub agrega los datos de uso cada 24 horas y la notificación de los datos de uso a la canalización del comercio en Azure tarda unas horas más. Por lo tanto, el uso que se produce brevemente antes de medianoche puede aparecer en Azure al día siguiente.

## <a name="generate-usage-data-reporting"></a>Generación de informes de datos de uso

- Para probar los informes de datos de uso, cree algunos recursos en Azure Stack Hub. Por ejemplo, puede crear una [cuenta de almacenamiento](azure-stack-provision-storage-account.md), una [VM Windows Server](../user/azure-stack-create-vm-template.md) y una VM de Linux con SKU de nivel estándar y básico para ver cómo se informa del uso de núcleos. Los datos de uso para diferentes tipos de recursos se notifican en medidores diferentes.

- Deje que los recursos se ejecuten durante unas horas. Información de uso se recopila aproximadamente una vez cada hora. Después de recopilarse, estos datos se transmiten a Azure y se procesan en el sistema Azure commerce. Este proceso puede tardar varias horas.

## <a name="view-usage---csp-subscriptions"></a>Visualización del uso: suscripciones de CSP

Si registró Azure Stack Hub con una suscripción de CSP, puede ver los cargos y el uso de la misma manera en que se ve el consumo de Azure. El uso de Azure Stack Hub se incluye en la factura y en el archivo de conciliación, disponibles en el [Centro de partners](https://partnercenter.microsoft.com/partner/home). El archivo de reconciliación se actualiza mensualmente. Si necesita acceso a información de uso reciente de Azure Stack Hub, puede usar las API del Centro de partners.

![Visualización de datos de uso y facturación de Azure Stack Hub en el Centro de partners de Microsoft](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Visualización del uso: suscripciones al Contrato Enterprise

Si registró Azure Stack Hub con una suscripción de Contrato Enterprise, puede ver los cargos y el uso en [EA Portal](https://ea.azure.com/). El uso de Azure Stack Hub se incluye en las descargas avanzadas junto con el uso de Azure en la sección de informes de este portal.

## <a name="view-usage---other-subscriptions"></a>Visualización del uso: otras suscripciones

Si ha registrado Azure Stack Hub con cualquier otro tipo de suscripción, por ejemplo, una de pago por uso, puede ver el uso y los cargos en el Centro de cuentas de Azure. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions) como administrador de cuentas de Azure y seleccione la suscripción de Azure que usó para registrar Azure Stack Hub. Puede ver los datos de uso de Azure Stack Hub y la cantidad que se cobra por cada uno de los recursos usados, como se muestra en la siguiente imagen:

![Visualización del flujo de facturación y uso en el Centro de cuentas de Azure](media/azure-stack-usage-reporting/pricing-details.png)

En el caso de ASDK, los recursos de Azure Stack Hub no se cobran, por lo que el precio se muestra como 0,00 USD.

## <a name="which-azure-stack-hub-deployments-are-charged"></a>¿Qué implementaciones de Azure Stack Hub se cobran?

El uso de recursos es gratuito para ASDK. Para los sistemas de varios nodos de Azure Stack Hub, se cobran las máquinas virtuales de carga de trabajo, los servicios de almacenamiento y App Services.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>¿Se les cobrará a los usuarios la infraestructura de las máquinas virtuales?

No. Los datos de uso de algunas máquinas virtuales del proveedor de recursos de Azure Stack Hub se notifican a Azure, pero no se cobra por estas máquinas virtuales ni por las máquinas virtuales que se crean durante la implementación para habilitar la infraestructura de Azure Stack Hub.  

Solo se cobra a los usuarios las máquinas virtuales que se ejecuten en suscripciones de inquilinos. Todas las cargas de trabajo se deben implementar en suscripciones de inquilino a fin de cumplir los términos de licencia de Azure Stack Hub.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-hub-how-do-i-do-it"></a>Tengo una licencia de Windows Server que deseo utilizar en Azure Stack Hub, ¿cómo lo hago?

El uso de las licencias existentes impide generar medidores de uso. Las licencias de Windows Server existentes se pueden usar en Azure Stack Hub. Este proceso se describe en la sección "Uso de software existente con Azure Stack Hub" de la [Guía de licencias de Azure Stack Hub](https://go.microsoft.com/fwlink/?LinkId=851536). Para poder usar sus licencias existentes, los clientes deben implementar sus máquinas virtuales de Windows Server como se describe en el artículo sobre la [ventaja híbrida para la licencia de Windows Server](/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>¿Qué suscripción se cobra por los recursos consumidos?

La suscripción que se proporciona cuando [se registra Azure Stack Hub en Azure](azure-stack-registration.md) se cobra.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>¿Qué tipos de suscripciones se admiten para informar de los datos de uso?

En implementaciones de varios nodos de Azure Stack Hub, se admiten suscripciones de Contrato Enterprise (EA) y CSP. En el Kit de desarrollo de Azure Stack, las suscripciones con Contrato Enterprise (EA), de pago por uso, CSP y MSDN admiten la notificación de datos de uso.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>¿El informe de datos de uso funciona en las nubes soberanas?

En el Kit de desarrollo de Azure Stack, los informes de datos de uso requieren suscripciones creadas en el sistema global de Azure. No se pueden registrar suscripciones creadas en una de las nubes soberanas (nubes de Azure Government, Azure Alemania y Azure China 21Vianet) con Azure, por lo que no admiten informes de datos de uso.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-hub-match-the-report-generated-from-azure-account-center"></a>¿Por qué el uso notificado en Azure Stack Hub no coincide con el informe generado en el Centro de cuentas de Azure?

Siempre hay un retraso entre los datos de uso notificados por las API de uso de Azure Stack Hub y los datos de uso que notifica el Centro de cuentas de Azure. El retraso es el tiempo que hace falta para cargar los datos de uso de Azure Stack Hub en el comercio de Azure. Debido a este retraso, el uso que se produce brevemente antes de medianoche puede aparecer en Azure al día siguiente. Si usa la [API Usage de Azure Stack Hub](azure-stack-provider-resource-api.md) y compara los resultados con el uso del informe del portal de facturación de Azure, verá una diferencia.

## <a name="next-steps"></a>Pasos siguientes

- [API de uso de proveedor](azure-stack-provider-resource-api.md)  
- [API de uso de inquilino](azure-stack-tenant-resource-usage-api.md)
- [Preguntas más frecuentes sobre uso](azure-stack-usage-related-faq.md)
- [Administración del uso y la facturación como un proveedor de soluciones en la nube](azure-stack-add-manage-billing-as-a-csp.md)
