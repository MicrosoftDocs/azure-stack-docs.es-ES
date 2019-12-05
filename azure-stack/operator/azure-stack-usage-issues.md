---
title: Problemas de conectividad de uso y errores de Azure Stack | Microsoft Docs
description: Solución de errores y problemas de uso de Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 3c7b3dcd304b684170cec8974910a42f7f86e84f
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74690084"
---
# <a name="usage-connectivity-errors"></a>Errores de conectividad de uso

Los datos de uso de Azure Stack se envían a Azure mediante el [componente *Azure Bridge*](azure-stack-usage-reporting.md) de Azure Stack. Si el puente de Azure Stack no puede conectarse al servicio de uso de Azure, aparece el siguiente error:

![error de puente de uso](media/azure-stack-usage-issues/usageerror2.png)

La ventana puede proporcionar más información sobre el error y la resolución:

![Resolución de errores](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Resolución de problemas de conectividad

Para mitigar el problema, pruebe los pasos siguientes:

- Compruebe la que configuración de red permite que Azure Bridge se conecte al servicio remoto.

- Vaya a la hoja [**Administración de regiones** > **Propiedades**](azure-stack-registration.md#verify-azure-stack-registration) para buscar el id. de suscripción de Azure usado para el registro, el grupo de recursos y el nombre del recurso de registro. Compruebe que el recurso de registro existe en el id. de suscripción de Azure correcto en Azure Portal. Para ello, vaya a **All resources** (Todos los recursos) creados con el id. de suscripción de Azure y active la casilla **Show hidden types** (Mostrar tipos ocultos). Si no encuentra el recurso de registro, siga los pasos descritos en [Renovación o cambio de registro](azure-stack-registration.md#renew-or-change-registration) para volver a registrar su instancia de Azure Stack.

  ![Portal](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Códigos de error

En esta sección se describen los códigos de errores de uso.

| Código de error                 | Problema                                                                                                                                             | Corrección                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | El puente de Azure Stack no puede enviar la solicitud al punto de conexión de servicio de uso de Azure.                                                            | Compruebe si un servidor proxy está bloqueando o interceptando el acceso al punto de conexión de servicio de uso.                                                                                                                                                                                                             |
| RequestTimedOut            | La solicitud se envió desde Azure Bridge, pero el servicio de uso de Azure no respondió dentro del período de tiempo de espera.                             | Compruebe si un servidor proxy está bloqueando o interceptando el acceso al punto de conexión de servicio de uso.                                                                                                                                                                                                                        |
| LoginError                 | No se puede realizar la autenticación con Microsoft Azure Active Directory.                                                                                                             | Asegúrese de que se pueda acceder al punto de conexión de inicio de sesión de Azure AD desde todas las VM XRP de Azure Stack.                                                                                                                                                                                                                     |
| CertificateValidationError | Azure Bridge no puede enviar la solicitud porque no puede autenticarse con el servicio de Azure.                                    | Compruebe si hay un proxy que intercepta el tráfico HTTPS entre la máquina XRP de Azure Stack y el punto de conexión de puerta de enlace de uso.                                                                                                                                                                                      |
| No autorizado               | Azure Bridge no puede insertar datos en el servicio de uso de Azure porque dicho servicio no puede realizar la autenticación del puente de Azure Stack. | Compruebe si se ha modificado el recurso de registro y, si es así, vuelva a registrar Azure Stack. <br><br> A veces, una vez problema de sincronización entre Azure Stack y Azure AD puede producir este error. En este caso, asegúrese de que las horas en las VM XRP en Azure Stack están sincronizadas con Azure AD. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

Además, es posible que deba proporcionar los archivos de registro de los componentes de Azure Bridge, WAS y WASPublic. Para ello, realice [estos pasos](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [creación de informes de datos de uso de Azure Stack en Azure](azure-stack-usage-reporting.md).
- Para revisar los mensajes de error que se desencadenen en el proceso de registro, consulte [Mensajes de error del registro de inquilinos](azure-stack-registration-errors.md).
- Obtenga más información sobre la [Infraestructura de informes de uso para proveedores de soluciones en la nube](azure-stack-csp-ref-infrastructure.md).
