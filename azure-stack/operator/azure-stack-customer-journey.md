---
title: Tutorial acerca de la integración de Azure Stack Hub en el centro de datos
description: Aprenda qué cabe esperar para realizar una implementación correcta in situ de Azure Stack Hub en el centro de datos, desde el planeamiento a la etapa posterior a la implementación.
author: ihenkel
ms.topic: article
ms.date: 11/07/2019
ms.author: inhenkel
ms.reviewer: asganesh
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 6711483384ba9985ced7396d19e025e0ed3240c6
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882510"
---
# <a name="azure-stack-hub-datacenter-integration-walkthrough"></a>Tutorial acerca de la integración de Azure Stack Hub en el centro de datos

En este artículo se describe el proceso completo para la integración de Azure Stack Hub en el centro de datos, desde la compra hasta el soporte técnico posterior a la implementación. La integración es un proyecto colaborativo entre el cliente, un proveedor de soluciones y Microsoft. Haga clic en las siguientes pestañas para ver los pasos específicos que debe dar cada miembro del proyecto y consulte las secciones siguientes para obtener un resumen de las distintas fases de la escala de tiempo del proyecto. 

# <a name="customertabcustomer"></a>[Cliente](#tab/customer)

1. Describir los requisitos y casos de uso
1. Determinar el modelo de facturación
1. Revisar y aprobar contratos
1. Completar la [hoja de cálculo de implementación](azure-stack-deployment-worksheet.md)
1. Asegurarse de que se cumplen los requisitos previos de implementación
1. Preparar el centro de datos 
1. Proporcionar información de suscripción durante la implementación
1. Resolver cualquier pregunta sobre los datos proporcionados

# <a name="partnertabpartner"></a>[Asociado](#tab/partner)

1. Recomendar soluciones opcionales según los requisitos del cliente
1. Proponer una prueba de concepto (POC) 
1. Decidir el nivel de soporte técnico
1. Preparar los contratos con el cliente
1. Crear el pedido de compra del cliente
1. Decidir la programación de entrega
1. Poner en contacto al cliente con Microsoft 
1. Entrenar al cliente sobre la implementación 
1. Ayudar al cliente a validar los datos recopilados
1. Instalar y validar la compilación de línea de base y el kit de herramientas de implementación de Microsoft
1. Enviar el hardware a la ubicación del cliente
1. Proporcionar ingeniería in situ
1. Bastidor y pila
1. Implementar el host de ciclo de vida de hardware (HLH) 
1. Implementar Azure Stack Hub
1. Entregar al cliente

# <a name="microsofttabmicro"></a>[Microsoft](#tab/micro)

1. Colaborar con el asociado en el soporte técnico preventa
2. Preparar las licencias de software y los contratos según sea necesario
3. Proporcionar herramientas para recopilar los requisitos de integración del centro de datos
4. Proporcionar compilaciones de línea de base mensuales y actualizaciones de la cadena de herramientas
5. Microsoft proporciona soporte técnico a los ingenieros ante cualquier problema con la implementación

---

## <a name="planning"></a>Planificación
Microsoft o un asociado de soluciones de Azure Stack Hub ayudará a evaluar sus objetivos. Ellos le ayudarán a responder a las siguientes preguntas:

-   ¿Es Azure Stack Hub la solución adecuada para su organización?
-   ¿Qué tipo de modelo de facturación y licencias será adecuado para su organización?
-   ¿Qué tamaño de solución necesitará?
-   ¿Cuáles son los requisitos de alimentación y refrigeración?

Use [Azure Stack Hub Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para investigar y analizar la mejor capacidad y configuración del hardware para sus necesidades. 

## <a name="ordering"></a>Pedido
La organización se compromete a comprar Azure Stack Hub, firma los contratos y pedidos de compra, y proporciona los datos de los requisitos de integración al proveedor de soluciones.

## <a name="pre-deployment"></a>Anterior a la implementación
Puede decidir cómo integrar Azure Stack Hub en el centro de datos. Microsoft ha colaborado con proveedores de soluciones para publicar una [hoja de cálculo de implementación](azure-stack-deployment-worksheet.md) para ayudarle a recopilar la información necesaria.
El artículo sobre [consideraciones generales de integración del centro de datos](azure-stack-datacenter-integration.md) proporciona información que le ayudará a completar esta plantilla, conocida como hoja de cálculo de implementación.

> [!IMPORTANT]
> Se analizarán todos los requisitos previos antes de encargar la solución para evitar retrasos en la implementación. La comprobación de los requisitos previos puede llevar tiempo y requerir la coordinación y recopilación de datos de distintos departamentos de la organización. 

Debe elegir los siguientes elementos:

- **El modelo de conexión y el proveedor de identidades de Azure Stack Hub**. Puede implementar Azure Stack Hub, ya sea [conectado a Internet (y a Azure) o desconectado](azure-stack-connection-models.md). Para sacar el máximo partido a Azure Stack Hub, incluidos los escenarios híbridos, es posible que quiera implementar la opción con conexión a Azure. La elección de Servicios de federación de Active Directory (AD FS) o de Azure Active Directory (Azure AD) es una decisión que se toma una sola vez y se realiza en el momento de la implementación. **Para cambiar el proveedor de identidades posteriormente es preciso volver a implementar todo el sistema**.

- **Modelo de licencia**. Las opciones de modelos de licencia entre los que puede elegir dependen del tipo de implementación que tenga. Su elección del proveedor de identidades no repercute en las máquinas virtuales del inquilino, en el sistema de identidades ni en las cuentas que utilizan.
    - Los clientes que optan por una[implementación sin conexión](azure-stack-disconnected-deployment.md) solo tienen una opción: la facturación basada en la capacidad.

    - Los clientes que optan por una [implementación con conexión](azure-stack-connected-deployment.md) pueden elegir entre la facturación basada en la capacidad o el pago por uso. La facturación basada en capacidad requiere una suscripción de Azure con Contrato Enterprise (EA) para el registro. Esto es necesario para el registro, el cual proporciona la disponibilidad de los elementos en Azure Marketplace mediante una suscripción de Azure.

- **Integración de red**. La [integración de red](azure-stack-network.md) es fundamental para la implementación, funcionamiento y administración de los sistemas de Azure Stack Hub. Hay varias consideraciones que debe tener en cuenta para garantizar que la solución Azure Stack Hub es resistente y tiene una infraestructura física de alta disponibilidad compatible con sus operaciones.

- **Integración de firewall**. Se recomienda que [use un firewall](azure-stack-firewall.md) para ayudar a proteger Azure Stack Hub. Los firewalls ayudan a evitar ataques de DDOS, detectar intrusiones e inspeccionar contenidos. Sin embargo, debe tener en cuenta que se puede convertir en un cuello de botella de rendimiento para los servicios de almacenamiento de Azure.

- **Requisitos de certificados**. Es fundamental que todos los [certificados necesarios](azure-stack-pki-certs.md) estén disponibles *antes* de que un ingeniero llegue in situ al centro de datos para la implementación.

Una vez recopilada toda la información sobre los requisitos previos mediante la hoja de cálculo de implementación, el proveedor de soluciones comenzará el proceso de fábrica según los datos recopilados para garantizar una correcta integración de Azure Stack Hub en el centro de datos.

## <a name="hardware-delivery"></a>Entrega de hardware 
El proveedor de soluciones colaborará con usted a la hora de programar la llegada de la solución a sus instalaciones. Una vez recibida y colocada, deberá programar una hora con el proveedor de soluciones para que un ingeniero se desplace in situ para llevar a cabo la implementación de Azure Stack Hub.

Es **fundamental** que todos los datos sobre requisitos previos estén bloqueados y disponibles *antes de que llegue el ingeniero in situ para implementar la solución*.

-   Ya se deben haber adquirido todos los certificados y deben estar listos.

-   Debe decidir el nombre de la región.

-   Todos los parámetros de integración de la red deben estar finalizados y deben coincidir con los que ha compartido con el proveedor de soluciones.

> [!TIP]
> Si algo de esta información ha cambiado, asegúrese de comunicar el cambio al proveedor de soluciones antes de programar la implementación real.

## <a name="onsite-deployment"></a>Implementación in situ 
Para implementar Azure Stack Hub debe estar presente in situ un ingeniero de parte de su proveedor de soluciones de hardware para iniciar la implementación. Para garantizar una implementación correcta, asegúrese de que la información que proporcionó mediante la hoja de cálculo de implementación no ha cambiado.

Estas comprobaciones son las que debe esperar del ingeniero que se ha desplazado al lugar durante el proceso de implementación:

- Comprobará el cableado y la conectividad de borde para garantizar que la solución funciona y cumple los requisitos.
- Configurará la solución HLH (Host de ciclo de vida de hardware), si la tuviera.
- Comprobará y se asegurará de que toda la configuración de BMC, BIOS y de red es correcta.
- Se asegurará de que el firmware de todos los componentes pertenece a la ultima versión que admite la solución.
- Iniciará la implementación.

> [!NOTE]
> El ingeniero desplazado a las instalaciones puede tardar una semana laboral en completar el procedimiento de implementación.

## <a name="post-deployment"></a>Posterior a la implementación 
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

- Si la implementación se ha completado mediante [AD FS](azure-stack-integrate-identity.md), la marca de Azure Stack Hub se deberá integrar con la propia marca de AD FS del cliente.

  > [!NOTE]
  > Este paso es responsabilidad del cliente, aunque el asociado puede ofrecer sus servicios para hacerlo.

- Integración con un sistema de supervisión existente del asociado respectivo.

  -   [System Center Operations Manager Integration](azure-stack-integrate-monitor.md) admite también funcionalidades de administración de flotas.

  -   [Integración de Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios).

## <a name="schedule"></a>Programación

![Escala de tiempo general para la implementación in situ de Azure Stack Hub](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Soporte técnico
Azure Stack Hub ofrece una experiencia de soporte técnico integrado y coherente con Azure que cubre todo el ciclo de vida del sistema. Para recibir soporte técnico para los sistemas integrados de Azure Stack Hub, los clientes necesitan dos contratos: uno con Microsoft (o con el proveedor de la solución en la nube) en el caso del soporte técnico para los servicios de Azure, y otro con el proveedor del hardware para el soporte técnico del sistema. La experiencia de soporte técnico integrado proporciona un escalado y resolución coordinados que hace que los clientes obtengan una experiencia de soporte coherente sin importar a quién llaman primero. Para los clientes que ya disponen de soporte Premier, Azure Estándar/ProDirect o Partner con Microsoft, se incluye el soporte técnico del software de Azure Stack Hub.

La experiencia de soporte técnico integrado hace uso de un mecanismo de intercambio de casos para realizar una transferencia bidireccional de casos de soporte técnico y actualizaciones de casos entre Microsoft y el asociado de hardware. Microsoft Azure Stack Hub sigue la [directiva de ciclo de vida actual](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre las [consideraciones generales de integración del centro de datos](azure-stack-datacenter-integration.md).
