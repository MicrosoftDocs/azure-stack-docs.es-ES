---
title: Tutorial acerca de la integración del centro de datos de Azure Stack | Microsoft Docs
description: Más información acerca de lo que cabe esperar después de una implementación correcta in situ de Azure Stack en el centro de datos, desde la planeación a la etapa posterior a la implementación.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 57e92f877ab9516b7b4978b5a919b18dad9b60ea
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342848"
---
# <a name="azure-stack-datacenter-integration-walkthrough"></a>Tutorial acerca de la integración del centro de datos de Azure Stack

En este artículo se describe la experiencia completa del cliente de Azure Stack: desde la adquisición de un sistema integrado a una implementación correcta in situ mediante un proveedor de soluciones. Use esta información para que le sea más fácil aprender a usar esta integración y así poder garantizar sus expectativas como cliente de Azure Stack.

Asimismo, como cliente de Azure Stack, encontrará las siguientes fases:

|     |Fase de planeación|Proceso de pedido|Anterior a la implementación|Proceso de fábrica|Entrega de hardware|Implementación in situ|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Colabora con el asociado para ofrecer soporte técnico preventa.|Prepara las licencias de software y los contratos según sea necesario.|Proporciona las herramientas necesarias para recopilar los requisitos de integración del centro de datos y la documentación para el cliente.|Proporciona las compilaciones de referencia más recientes y las actualizaciones de la cadena de herramientas con una frecuencia mensual.|N/D|Microsoft proporciona soporte técnico a los ingenieros ante cualquier problema con la implementación.|
|**Asociado**|Recomienda opciones de solución según los requisitos del cliente.<br><br>Propone una prueba de concepto (POC) si es necesario.<br><br>Establece la relación comercial.<br><br>Decide el nivel de soporte técnico.|Prepara los contratos necesarios con el cliente.<br><br>Crea el pedido de compra del cliente.<br><br>Decide la escala de tiempo de entrega.<br><br>Pone en contacto al cliente con Microsoft si es necesario.|Proporciona al cliente la formación necesaria para que pueda comprender todos los requisitos previos de implementación y las opciones de integración del centro de datos.<br><br>Ayuda al cliente con la validación de los datos recopilados para garantizar la integridad y la precisión.|Aplica la última compilación de referencia validada.<br><br>Aplica la versión necesaria de Microsoft Deployment Toolkit.|Envía el hardware a la ubicación del cliente.|La implementación la controla un ingeniero in situ.<br><br>Bastidor y pila.<br><br>Implementación del host de ciclo de vida de hardware (HLH).<br><br>Implementación de Azure Stack.<br><br>Entrega al cliente.|
|**Cliente**|Describe los casos de uso previstos y especifica los requisitos.|Determina el modelo de facturación que desea usar y revisa y aprueba los contratos.|Rellene la [hoja de cálculo de implementación](azure-stack-deployment-worksheet.md) y asegúrese de que se cumplen todos los requisitos previos y de que está listo para la implementación.|N/D|Prepara el centro de datos asegurándose de que todo lo relacionado con la alimentación y la refrigeración, la conectividad de borde y el resto de requisitos de integración del centro de datos está en vigor.|Está disponible durante la implementación para proporcionar credenciales de suscripción y ayuda en caso de que surjan preguntas sobre los datos proporcionados.|
| | | | | | | |


## <a name="planning-phase"></a>Fase de planeación
La fase de planeación se produce cuando Microsoft, o el asociado de soluciones de Azure Stack, trabajan con el cliente para evaluar y comprender sus necesidades y determinar si Azure Stack es la solución adecuada:

Ellos le ayudarán a decidir sobre las siguientes cuestiones:

-   ¿Es Azure Stack la solución adecuada para su organización?

-   ¿Qué tamaño de solución necesitará?

-   ¿Qué tipo de modelo de facturación y licencias será adecuado para su organización?

-   ¿Cuáles son los requisitos de alimentación y refrigeración?

Para asegurarse de que la solución de hardware es la que mejor se adapta a sus necesidades, se puede usar [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para ayudar, durante la planeación previa a la adquisición, a determinar la capacidad y la configuración adecuadas para esta solución.

La hoja de cálculo *no* está pensada para que sustituya a su propio proceso de investigación y análisis de las soluciones de hardware que mejor se adapten a sus necesidades. Al planear una implementación de Azure Stack, debe revisar también las [consideraciones generales de integración del centro de datos](azure-stack-datacenter-integration.md) para sistemas integrados en Azure Stack.

## <a name="order-process-phase"></a>Fase del proceso de pedido
En esta etapa, ya se deben haber respondido muchas de sus preguntas con respecto a la viabilidad. Ahora que ya está listo para confirmar la adquisición de Azure Stack y después de firmar todos los contratos y pedidos de compra necesarios, se le pedirá que proporcione datos sobre los requisitos de integración al proveedor de soluciones.

## <a name="pre-deployment-phase"></a>Fase anterior a la implementación
Durante esta fase, deberá decidir cómo desea integrar Azure Stack en su centro de datos. Para facilitar este proceso, Microsoft ha elaborado una plantilla de requisitos para ayudarle a recopilar la información necesaria para planear una implementación de un sistema integrado dentro de su entorno. Esta plantilla de requisitos se realizó en colaboración con los proveedores de soluciones.

El artículo sobre [consideraciones generales de integración del centro de datos](azure-stack-datacenter-integration.md) proporciona información que le ayudará a completar esta plantilla, conocida como hoja de cálculo de implementación.

> [!IMPORTANT]
> Durante esta etapa es importante que se investigue y se tomen decisiones sobre la información contenida en los requisitos previos antes de adquirir la solución. Tenga en cuenta que este paso necesita bastante tiempo y requiere la coordinación y la obtención de datos de varias disciplinas dentro de su organización. Esto puede dar lugar a información incorrecta o incompleta en una implementación que dure más tiempo. 

En la fase anterior a la implementación deberá decidir sobre los siguientes elementos:

- **El modelo de conexión y el proveedor de identidades de Azure Stack**. Puede implementar Azure Stack, ya sea [con conexión a Internet (y a Azure) o sin conexión](azure-stack-connection-models.md). Para sacar el máximo partido a Azure Stack, incluidos los escenarios híbridos, es posible que quiera implementar la opción con conexión a Azure. La elección de Servicios de federación de Active Directory (AD FS) o de Azure Active Directory (Azure AD) es una decisión que se toma una sola vez y se realiza en el momento de la implementación. **Para cambiar el proveedor de identidades posteriormente es preciso volver a implementar todo el sistema**.

- **Modelo de licencia**. Las opciones de modelos de licencia entre los que puede elegir dependen del tipo de implementación que tenga. Su elección del proveedor de identidades no repercute en las máquinas virtuales del inquilino, en el sistema de identidades ni en las cuentas que utilizan.
    - Los clientes que optan por una[implementación sin conexión](azure-stack-disconnected-deployment.md) solo tienen una opción: la facturación basada en la capacidad.

    - Los clientes que optan por una [implementación con conexión](azure-stack-connected-deployment.md) pueden elegir entre la facturación basada en la capacidad o el pago por uso. La facturación basada en capacidad requiere una suscripción de Azure con Contrato Enterprise (EA) para el registro. Esto es necesario para el registro, el cual proporciona la disponibilidad de los elementos en Azure Marketplace mediante una suscripción de Azure.

- **Integración de red**. La [integración de red](azure-stack-network.md) es fundamental para la implementación, funcionamiento y administración de los sistemas de Azure Stack. Hay varias consideraciones que debe tener en cuenta para garantizar que la solución Azure Stack es resistente y tiene una infraestructura física de alta disponibilidad compatible con sus operaciones.

- **Integración de firewall**. Se recomienda que [use un firewall](azure-stack-firewall.md) para ayudar a proteger Azure Stack. Los firewalls ayudan a evitar ataques de DDOS, detectar intrusiones e inspeccionar contenidos. Sin embargo, debe tener en cuenta que se puede convertir en un cuello de botella de rendimiento para los servicios de almacenamiento de Azure.


- **Requisitos de certificados**. Es fundamental que todos los [certificados necesarios](azure-stack-pki-certs.md) estén disponibles *antes* de que un ingeniero llegue in situ al centro de datos para la implementación.

Una vez recopilada toda la información sobre los requisitos previos mediante la hoja de cálculo de implementación, el proveedor de soluciones comenzará el proceso de fábrica según los datos recopilados para garantizar una correcta integración de Azure Stack en su centro de datos.

## <a name="hardware-delivery-phase"></a>Fase de entrega de hardware
El proveedor de soluciones colaborará con usted a la hora de programar la llegada de la solución a sus instalaciones. Una vez recibida y colocada, deberá programar una hora con el proveedor de soluciones para que un ingeniero se desplace in situ para llevar a cabo la implementación de Azure Stack.

Es **fundamental** que todos los datos sobre requisitos previos estén bloqueados y disponibles *antes de que llegue el ingeniero in situ para implementar la solución*.

-   Ya se deben haber adquirido todos los certificados y deben estar listos.

-   Debe decidir el nombre de la región.

-   Todos los parámetros de integración de la red deben estar finalizados y deben coincidir con los que ha compartido con el proveedor de soluciones.

> [!TIP]
> Si algo de esta información ha cambiado, asegúrese de comunicar el cambio al proveedor de soluciones antes de programar la implementación real.

## <a name="onsite-deployment-phase"></a>Fase de implementación in situ
Para implementar Azure Stack debe estar presente in situ un ingeniero de parte de su proveedor de soluciones de hardware para iniciar la implementación. Para garantizar una implementación correcta, asegúrese de que la información que proporcionó mediante la hoja de cálculo de implementación no ha cambiado.

Estas comprobaciones son las que debe esperar del ingeniero que se ha desplazado al lugar durante el proceso de implementación:

- Comprobará el cableado y la conectividad de borde para garantizar que la solución funciona y cumple los requisitos.
- Configurará la solución HLH (Host de ciclo de vida de hardware), si la tuviera.
- Comprobará y se asegurará de que toda la configuración de BMC, BIOS y de red es correcta.
- Se asegurará de que el firmware de todos los componentes pertenece a la ultima versión que admite la solución.
- Iniciará la implementación.

> [!NOTE]
> El ingeniero desplazado a las instalaciones puede tardar una semana laboral en completar el procedimiento de implementación.

## <a name="post-deployment-phase"></a>Fase posterior a la implementación
El asociado debe realizar varios pasos antes de entregar la solución al cliente en la fase posterior a la integración. En esta fase, la validación es importante para asegurarse de que el sistema se ha implementado y está funcionando correctamente. 

Las acciones que debe realizar el asociado de OEM son:

- [Ejecución de test-azurestack](azure-stack-diagnostic-test.md).

- [Registro de Azure](azure-stack-registration.md).

- [Redifusión de Marketplace](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items).

- Copia de seguridad de los archivos de configuración del conmutador y HLH.

- Eliminación de DVM.

- Preparación de un resumen del cliente para la implementación.

- [Comprobación de las actualizaciones para asegurarse de que el software de la solución se ha actualizado a la versión más reciente](./azure-stack-updates.md).

Hay varios pasos que son obligatorios u opcionales según el tipo de instalación.

- Si la implementación se ha completado mediante [AD FS](azure-stack-integrate-identity.md), la marca de Azure Stack se deberá integrar con la propia instancia de AD FS del cliente.

  > [!NOTE]
  > Este paso es responsabilidad del cliente, aunque el asociado puede ofrecer sus servicios para hacerlo.

- Integración con un sistema de supervisión existente del asociado respectivo.

  -   [System Center Operations Manager Integration](azure-stack-integrate-monitor.md) admite también funcionalidades de administración de flotas.

  -   [Integración de Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios).

## <a name="overall-timeline"></a>Escala de tiempo completa

![Escala de tiempo general para la implementación in situ de Azure Stack](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Soporte técnico
Azure Stack ofrece una experiencia de soporte técnico integrado y coherente que cubre todo el ciclo de vida del sistema. Para recibir soporte técnico para los sistemas integrados de Azure Stack, los clientes necesitan dos contratos: uno con Microsoft (o con el proveedor de la solución en la nube) en el caso del soporte técnico para los servicios de Azure, y otro con el proveedor del hardware para el soporte técnico del sistema. La experiencia de soporte técnico integrado proporciona un escalado y resolución coordinados que hace que los clientes obtengan una experiencia de soporte coherente sin importar a quién llaman primero. Para los clientes que ya disponen de soporte Premier, Azure Estándar/ProDirect o Partner con Microsoft, se incluye el soporte técnico del software de Azure Stack.

La experiencia de soporte técnico integrado hace uso de un mecanismo de intercambio de casos para realizar una transferencia bidireccional de casos de soporte técnico y actualizaciones de casos entre Microsoft y el asociado de hardware. Microsoft Azure Stack sigue la [directiva de ciclo de vida actual](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre las [consideraciones generales de integración del centro de datos](azure-stack-datacenter-integration.md).
