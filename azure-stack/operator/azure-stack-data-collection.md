---
title: Control de datos de clientes y registro de Azure Stack Hub
description: Conozca cómo Azure Stack Hub recopila información y datos de clientes.
author: JustinHall
ms.topic: article
ms.date: 06/10/2019
ms.author: justinha
ms.reviewer: chengwei
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: cf61e4950752f6c41b204c7754b9c19b5307709e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700262"
---
# <a name="azure-stack-hub-log-and-customer-data-handling"></a>Control de datos de clientes y registro de Azure Stack Hub 

En la medida en que Microsoft es un procesador o un subprocesador de datos personales en relación con Azure Stack Hub, Microsoft se compromete ante todos los clientes, a partir del 25 de mayo de 2018, a cumplir los siguientes compromisos:

- La disposición "Procesamiento de los datos personales, RGPD" de la sección "Términos de protección de los datos" de los [Términos de los servicios en línea](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).
- Los términos del Reglamento general de protección de datos de la Unión Europea en el apartado 4 de los [Términos de los servicios en línea](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Como Azure Stack Hub reside en los centros de datos del cliente, Microsoft únicamente controla los datos que se comparten con Microsoft mediante [diagnósticos](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs), [telemetría](azure-stack-telemetry.md) y [facturación](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Controles de acceso de datos 
Los empleados de Microsoft, que se asignan para investigar un caso de soporte técnico específico, recibirán acceso de solo lectura a los datos cifrados. Los empleados de Microsoft también tienen acceso a las herramientas que se usan para eliminar los datos si es necesario. Todo el acceso a los datos de los clientes se audita y se registra.  

Controles de acceso de datos:
- Los datos solo se conservan durante un máximo de 90 días después de cerrar el caso.
- El cliente siempre tiene la opción de eliminar los datos en cualquier momento durante ese período de 90 días.
- Los empleados de Microsoft tienen acceso a los datos de cada caso individual y solo cuando es necesario para ayudar a resolver el problema de soporte técnico.
- En el caso de que Microsoft deba compartir los datos del cliente con los socios OEM, el consentimiento del cliente es obligatorio.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>¿Qué controles de solicitudes del titular de los datos tienen los clientes?
Microsoft admite la eliminación de datos bajo demanda del cliente. Los clientes pueden solicitar que uno de nuestros ingenieros de soporte técnico elimine todos sus registros para un caso determinado en cualquier momento, antes de que los datos se borren permanentemente.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>¿Microsoft notifica a los clientes la eliminación de los datos?
En el caso de la acción de eliminación automática de datos (90 días después del cierre del caso), no nos ponemos en contacto con los clientes de manera proactiva para informarles sobre la eliminación.

Para la acción de eliminación de datos a petición, los ingenieros de soporte técnico de Microsoft tienen acceso a la herramienta que les permite eliminar los datos a petición. Pueden proporcionar una confirmación en el teléfono del cliente cuando la acción haya terminado.

## <a name="diagnostic-data"></a>Datos de diagnóstico
Como parte del proceso de soporte técnico, los operadores de Azure Stack Hub pueden [compartir los registros de diagnóstico](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) con los equipos de soporte técnico e ingeniería de Azure Stack Hub para ayudar a la solución de problemas.

Microsoft proporciona una herramienta y un script para que los clientes recopilen y carguen los archivos de registro de diagnóstico solicitados. Una vez recopilados, los archivos de registro se transfieren a través de una conexión cifrada protegida por HTTPS a Microsoft. Como HTTPS proporciona el cifrado a través de la conexión, no se necesita ninguna contraseña para el cifrado en tránsito. Una vez que se reciben, los registros se cifran y se almacenan hasta que se eliminen automáticamente 90 días después de cerrar el caso de soporte técnico.

## <a name="telemetry-data"></a>Datos de telemetría
La [telemetría de Azure Stack Hub](azure-stack-telemetry.md) carga automáticamente datos del sistema en Microsoft mediante la Experiencia del usuario conectado. Los operadores de Azure Stack Hub disponen de controles para personalizar características y configuraciones de privacidad en cualquier momento.

Microsoft no tiene la intención de recopilar información confidencial, como números de tarjetas de crédito, nombres de usuario y contraseñas, direcciones de correo electrónico, etc. Si determinamos que, por accidente, se ha recibido información confidencial, la eliminamos.

## <a name="billing-data"></a>Datos de facturación
La [facturación de Azure Stack Hub](azure-stack-usage-reporting.md) hace uso del sistema global de facturación y uso de Azure y, por lo tanto, está en consonancia con las pautas de cumplimiento de Microsoft.

Los operadores de Azure Stack Hub pueden configurar Azure Stack Hub para reenviar la información de uso a Azure con fines de facturación. Esta configuración es necesaria para los clientes de los sistemas integrados de Azure Stack Hub que eligen el modelo de facturación de pago por uso. Los informes de uso se controlan de forma independiente de la telemetría y no son necesarios para los clientes de los sistemas integrados que eligen el modelo de capacidad ni para los usuarios del Kit de desarrollo de Azure Stack. En estos escenarios, los informes de uso se pueden desactivar [mediante el script de registro](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Pasos siguientes 
[Más información acerca de la seguridad de Azure Stack](azure-stack-security-foundations.md) 
