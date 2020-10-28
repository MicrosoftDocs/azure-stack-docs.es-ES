---
title: Recorrido del cliente desde la compra hasta la fase posterior a la implementación de Azure Stack Hub | Microsoft Docs
description: Obtenga más información acerca de lo que cabe esperar de una implementación correcta in situ de un Centro de datos modular (MDC), desde el planeamiento hasta la etapa posterior a la implementación.
services: azure-stack
documentationcenter: ''
author: asganesh
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: 7c98b382988e0f462261bc453ebadb07d9c9a7d7
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183522"
---
# <a name="customer-journey"></a>Recorrido del cliente

En este artículo se describe el proceso completo para la integración del centro de datos de Azure Stack Hub, desde la compra hasta la fase posterior a la implementación. La integración es un proyecto colaborativo entre el cliente y Microsoft. En las secciones siguientes se tratan las distintas fases de la escala de tiempo del proyecto y los pasos específicos para los miembros del proyecto.

## <a name="introduction"></a>Introducción

En la tabla siguiente se describe lo que se puede esperar durante las distintas fases de la implementación.

|   |Proceso de pedido  |Anterior a la implementación |Integración, validación y transporte |Implementación in situ  |Después de la implementación |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  |- Señal de la entrega a una ubicación de EE. UU.<br>- Centro de datos modular (MDC) = 15 días   |Proporciona las herramientas y la documentación necesarias para recopilar los requisitos del centro de datos.  |- Valida los artefactos de configuración y comprueba los resultados de la validación.<br>- Garantiza la entrega del hardware.  |- Bastidor y pila<br>- Integración de red<br>- Implementación de Azure Stack Hub<br>- Entrega al cliente    |Registro y redifusión de Marketplace|
|Customer   |Compra de señales   |- Rellena los detalles de la red en la hoja de cálculo de implementación.<br>- Recopila certificados.<br>- Obtiene cuentas de Azure AD.<br>- Ejecuta cualquier herramienta de validación proporcionada.    |Garantiza que el sitio está preparado con los requisitos previos de red, alimentación y refrigeración.    |- Prepararse con los artefactos de configuración de la implementación.<br>- El ingeniero de red del cliente está disponible.   |     |


## <a name="order-process"></a>Proceso de pedido

Su organización trabajará con Microsoft para realizar un pedido de un número de sistemas asignado. Una vez realizado el pedido, Microsoft tendrá 15 días para enviar el dispositivo MDC a su ubicación de EE. UU. Microsoft garantizará el cumplimiento de todos los requisitos de la cadena de suministro segura. 

>[!NOTE] 
>La facturación comienza 14 días después de enviar el hardware.

Siga estos pasos en Azure Portal para crear un recurso de Azure Stack Hub.

1. Use sus credenciales de Microsoft Azure para iniciar sesión en Azure Portal, en esta dirección URL: [https://portal.azure.com](https://portal.azure.com).
1. En el menú izquierdo, seleccione **+ Crear un recurso** . Busque y seleccione **Centro de datos modular** . Seleccione **Crear** .
1. Seleccione la suscripción que quiere usar para el dispositivo de Azure Stack Hub. Seleccione el país al que desea enviar el dispositivo físico. Seleccione **Mostrar dispositivos** .
1. Se muestra un breve formulario. Rellene el formulario y seleccione **Enviar** . Microsoft habilitará su suscripción.
1. Una vez habilitada la suscripción, debería poder continuar con la creación de recursos. En la hoja **Seleccione el tipo de dispositivo** , elija **Seleccionar** . 
1. En la pestaña **Datos básicos** , escriba o seleccione los siguientes **detalles del proyecto** .
    
    |Configuración  |Valor  |
    |---------|---------|
    |Subscription    |Este valor se rellena automáticamente según la selección anterior. La suscripción está vinculada a la cuenta de facturación. |
    |Resource group  |Cree un nuevo grupo o seleccione uno existente.   |

1. Escriba o seleccione los siguientes **detalles de la instancia** .

    |Configuración  |Valor  |
    |---------|---------|
    |Nombre   | Nombre descriptivo que identifique el recurso.<br>El nombre tiene entre 2 y 50 caracteres que contiene letras, números y guiones.<br> El nombre comienza y termina con una letra o un número.        |
    |Region     |Para obtener una lista de todas las regiones en las que está disponible el recurso de Azure Stack Hub, consulte [Productos de Azure disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Si usa Azure Government, todas las regiones de gobierno están disponibles como se muestra en las [regiones de Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Elija la ubicación más cercana a la región geográfica donde quiera implementar el dispositivo.|


1. Seleccione **Siguiente: Dirección de envío** .

    - Si ya tiene un dispositivo, seleccione el cuadro combinado **I have a Azure Stack Hub device** (Tengo un dispositivo de Azure Stack Hub).

    - Si este es el pedido del nuevo dispositivo, escriba el nombre de contacto, la empresa, la dirección para enviar el dispositivo y la información de contacto.

1. Seleccione **Siguiente: Review + create** (Revisar y crear).
1. En la pestaña **Revisar y crear** , revise la información de **Detalles de precios** , **Términos de uso** y los detalles de su recurso. Seleccione el cuadro combinado **I have reviewed the privacy terms** (He revisado los términos de privacidad).
1. Seleccione **Crear** .

Se tarda unos minutos en crear el recurso. Cuando el recurso se haya creado e implementado correctamente, recibirá una notificación. Haga clic en **Go to resource** (Ir al recurso).

Tras realizar el pedido, Microsoft lo revisa y se pone en contacto con usted (por correo electrónico) para indicarle los detalles del envío.

## <a name="pre-deployment"></a>Anterior a la implementación

Puede decidir cómo integrar Azure Stack Hub en el centro de datos. Microsoft ha publicado una [hoja de cálculo de implementación](../operator/azure-stack-deployment-worksheet.md) que le guía en la recopilación de toda la información necesaria para la correcta integración con su centro de datos. Además, se requiere un determinado conjunto de certificados en el momento de la implementación. Para ayudarle a obtener estos certificados, Microsoft le proporciona una herramienta denominada [Azure Stack Hub Readiness Checker](../operator/azure-stack-validation-report.md). Esta herramienta le ayudará a crear solicitudes de firma de certificado (CSR) para proporcionar a su entidad de certificación interna. 

>[!Important]
>Todos los requisitos previos se validan para ayudar a evitar los retrasos de implementación. La comprobación de los requisitos previos puede llevar tiempo y requerir la coordinación y recopilación de datos de distintos departamentos de la organización.

Debe elegir los siguientes elementos:

- **El modelo de conexión y el proveedor de identidades de Azure Stack Hub** . Puede implementar Azure Stack Hub, ya sea [conectado a Internet (y a Azure)](../operator/azure-stack-connected-deployment.md) o [desconectado](../operator/azure-stack-disconnected-deployment.md). Para sacar el máximo partido a Azure Stack Hub, incluidos los escenarios híbridos, es posible que quiera implementar la opción con conexión a Azure. La elección de Servicios de federación de Active Directory (AD FS) o de Azure Active Directory (Azure AD) es una decisión que se toma una sola vez y se realiza en el momento de la implementación. **Para cambiar el proveedor de identidades posteriormente es preciso volver a implementar todo el sistema.**
- **Integración de red.** La [integración de red](../operator/azure-stack-network.md) es fundamental para la implementación, funcionamiento y administración de los sistemas de Azure Stack Hub. Hay varias consideraciones que debe tener en cuenta para garantizar que la solución Azure Stack Hub es resistente y tiene una infraestructura física de alta disponibilidad compatible con sus operaciones.
- **Integración de firewall.** Se recomienda que [use un firewall](../operator/azure-stack-firewall.md) para ayudar a proteger Azure Stack Hub. Los firewalls ayudan a evitar ataques de DDOS, detectar intrusiones e inspeccionar contenidos. Sin embargo, debe tener en cuenta que se puede convertir en un cuello de botella de rendimiento para los servicios de almacenamiento de Azure.
- **Requisitos de certificado.** Es fundamental que todos los [certificados necesarios](../operator/azure-stack-pki-certs.md) estén disponibles antes de que un ingeniero llegue in situ al centro de datos para la implementación.

Una vez recopilada toda la información de requisitos previos a través de la hoja de cálculo de implementación, Microsoft se asegurará de que comprobamos que todas las herramientas de validación se han ejecutado y le ayudamos con las demás preguntas que pueda tener. 

## <a name="hardware-delivery"></a>Entrega de hardware

Microsoft trabajará con usted para asegurarse de que todo el hardware necesario llega a la ubicación estadounidense en el plazo asignado.  

Es **fundamental** que todos los datos sobre requisitos previos estén bloqueados y disponibles *antes de que llegue el ingeniero de implementación de Microsoft in situ para implementar la solución* .

- La hoja de cálculo de implementación tiene todos los datos rellenados. 
- Ya se deben haber validado todos los certificados y deben estar listos.
- Debe decidir el nombre de la región.
- Todos los parámetros de integración de red están finalizados.

>[!Tip]
>Si alguno de estos datos ha cambiado, asegúrese de trabajar con la organización interna para asegurarse de que la información se actualiza antes de la llegada del ingeniero de implementación in situ. Esto impedirá que se produzcan retrasos en el proceso de implementación.

## <a name="onsite-deployment"></a>Implementación in situ

Para implementar Azure Stack Hub, se presentará un ingeniero de implementación de Microsoft para iniciar la implementación. Es necesario que un ingeniero de red de su organización también esté disponible durante el período de la implementación in situ.

Estas comprobaciones son las que debe esperar del ingeniero que se ha desplazado al lugar durante el proceso de implementación:

- Desempaquetado e inventario de hardware
- Conexión de la alimentación y encendido de la solución
- Validación del estado del hardware físico
- Comprobará el cableado y la conectividad de borde para garantizar que la solución funciona y cumple los requisitos.
- Configuración de la solución HLH (host de ciclo de vida de hardware).
- Integración de la red del centro de datos
- Comprobación de que toda la configuración de hardware físico es correcta.
- Se asegurará de que el firmware de todos los componentes pertenece a la ultima versión que admite la solución.
- Iniciará la implementación. 

## <a name="post-deployment"></a>Posterior a la implementación

El ingeniero de implementación de Microsoft debe realizar varios pasos antes de entregar la solución al cliente. En esta fase, la validación es importante para asegurarse de que el sistema se ha implementado y está funcionando correctamente.

Acciones que debe realizar el ingeniero de implementación de Microsoft:

- Habilitación de proveedores de recursos (RP) de valor agregado.
- Ejecución de [test-azurestack](../operator/azure-stack-diagnostic-test.md).
- [Registro](../operator/azure-stack-registration-role.md) de Azure.
- [Redifusión de Marketplace](../operator/azure-stack-marketplace.md).
- Copia de seguridad de los archivos de configuración del conmutador y HLH.
- Preparación de un resumen del cliente para la implementación.
- [Comprobación de las actualizaciones](../operator/azure-stack-updates.md) para asegurarse de que el software de la solución se ha actualizado a la versión más reciente.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los [pasos para instalar y configurar el Centro de datos modular](deployment-overview.md).

