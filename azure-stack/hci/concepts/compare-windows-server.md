---
title: Comparación entre Azure Stack HCI y Windows Server
description: Este tema le ayudará a determinar qué solución, entre Azure Stack HCI y Windows Server, es mejor para su organización.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/21/2021
ms.openlocfilehash: c691424251d096794315880106c131ecaaea1f57
ms.sourcegitcommit: 925351b77490364b3d52746f788c4c1b93343631
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98705207"
---
# <a name="compare-azure-stack-hci-to-windows-server-2019"></a>Comparación entre Azure Stack HCI y Windows Server 2019

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se explican las diferencias principales entre Azure Stack HCI y Windows Server 2019 y se proporcionan instrucciones sobre cuándo usarlas. Microsoft admite y mantiene activamente ambos productos. Muchas organizaciones pueden optar por implementar ambas opciones, ya que están destinadas a fines diferentes y complementarios.

## <a name="when-to-use-azure-stack-hci"></a>Cuándo usar Azure Stack HCI

Azure Stack HCl es la principal plataforma de infraestructura hiperconvergida de Microsoft para la ejecución de VM o escritorios virtuales en el entorno local con conexiones a servicios híbridos de Azure. Es una manera fácil de modernizar y proteger sus centros de datos y sucursales, así como de conseguir el mejor rendimiento del sector con una baja latencia y soberanía de datos.


:::image type="content" source="media/compare-windows-server/hci-scenarios.png" alt-text="Cuándo usar Azure Stack HCI en lugar de Windows Server 2019" border="false" lightbox="media/compare-windows-server/hci-scenarios.png":::


Use Azure Stack HCI para:

- El mejor host de virtualización para modernizar la infraestructura, ya sea para las cargas de trabajo existentes en el centro de datos principal o para los requisitos emergentes de las sucursales y las ubicaciones perimetrales.
- Extensibilidad sencilla a la nube, con un flujo de innovaciones normal de la suscripción a Azure y un conjunto coherente de herramientas y experiencias.
- Todas las ventajas de la infraestructura hiperconvergida: una arquitectura de centro de centros más sencilla y consolidada con almacenamiento de alta velocidad y redes.

  >[!NOTE]
  >Dado que Azure Stack HCl se ha diseñado para usarse como un host de virtualización de Hyper-V para una arquitectura moderna e hiperconvergida, no incluye derechos de invitado. Por este motivo, Azure Stack HCI solo tiene licencia para ejecutar un número pequeño de roles de servidor directamente. Los demás roles deben ejecutarse dentro de las VM.

## <a name="when-to-use-windows-server-2019"></a>Cuándo usar Windows Server 2019

Windows Server 2019 es un sistema operativo muy versátil y multiuso, con docenas de roles y cientos de características, entre las que se incluyen los derechos de invitado. Las máquinas de Windows Server pueden estar en la nube o en un entorno local; por ejemplo, un entorno virtualizado sobre Azure Stack HCl.


:::image type="content" source="media/compare-windows-server/windows-server-scenarios.png" alt-text="Cuándo usar Windows Server 2019 en lugar de Azure Stack HCI" border="false" lightbox="media/compare-windows-server/windows-server-scenarios.png":::


Use Windows Server 2019 para lo siguiente:

- Un sistema operativo invitado dentro de máquinas virtuales (VM) o contenedores
- Como entorno en tiempo de ejecución para una aplicación Windows
- Para usar uno o más roles de servidor integrados como Active Directory, servicios de archivo, DNS, DHCP o Internet Information Services (IIS)
- Como servidor tradicional, como un controlador de dominio sin sistema operativo o una instalación de SQL Server
- Para infraestructuras tradicionales como las VM conectadas a Almacenamiento SAN de Canal de fibra

## <a name="compare-product-positioning"></a>Comparación del posicionamiento del producto

En la tabla siguiente se muestra el paquete de producto de alto nivel para Azure Stack HCI y Windows Server 2019.

| **Atributo**    | **Azure Stack HCI** | **Windows Server 2019** |
| ---------------- | ------------------- | ----------------------- |
| Tipo de producto     | Servicio en la nube que incluye un sistema operativo y mucho más. | Sistema operativo |
| Información legal            | Incluido en el Contrato de cliente de Microsoft o en el Contrato Online Subscription. | Tiene su propio contrato de licencia para el usuario final. |
| Licencias        | Se factura en la suscripción a Azure. | Tiene su propia licencia de pago. |
| Soporte técnico          | Incluido en el Soporte técnico de Azure | Puede estar incluido en diferentes contratos de soporte técnico, incluido el soporte técnico Premier de Microsoft. |
| Dónde obtenerla  | Se descarga desde [Azure.com/HCI](https://azure.com/hci) o viene preinstalado en sistemas integrados. | Centro de evaluación o centro de servicios de licencias por volumen de Microsoft. |
| Se ejecuta en VM.      | Solo para fines de evaluación; diseñado como sistema operativo host. | Sí, en la nube o en el entorno local. |
| Hardware         | Se ejecuta en una o más de las 200 soluciones previamente validadas del [Catálogo de Azure Stack HCI](https://hcicatalog.azurewebsites.net) | Se ejecuta en cualquier hardware con el logotipo "Certificado para Windows Server 2019" |
| Directiva de ciclo de vida | Siempre al día con las características más recientes | Elija entre los [canales de servicio de Windows Server](/windows-server/get-started-19/servicing-channels-19): Canal de mantenimiento a largo plazo (LTSC) y canal semianual (SAC) |

## <a name="compare-technical-features"></a>Comparar características técnicas

En la tabla siguiente se comparan las características técnicas de Azure Stack HCI y Windows Server 2019.

| **Atributo** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Hyper-V de núcleo | Sí | Sí |
| Espacios de almacenamiento directo de núcleo | Sí | Sí |
| SDN de núcleo | Sí | Sí |
| Clúster extendido para la recuperación ante desastres | Sí | - |
| Reparaciones de Espacios de almacenamiento de 4 a 5 veces más rápidas | Sí | - |
| Actualizaciones de firmware y controlador integradas | Sí (solo sistemas integrados) | - |
| Implementación guiada | Sí | - |

## <a name="compare-management-options"></a>Comparación de opciones de administración

En la tabla siguiente se comparan las opciones de administración para Azure Stack HCI y Windows Server 2019. Ambos productos se han diseñado para la administración remota y se pueden administrar con muchas de las mismas herramientas.

| **Atributo** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Experiencia de escritorio | - | Sí |
| Windows Admin Center | Sí | Sí |
| Microsoft System Center | Sí (se vende por separado) | Sí (se vende por separado) |
| Azure Portal | Sí (de forma nativa) | Requiere el agente de Arc |
| Herramientas de terceros | Sí | Sí |

## <a name="compare-product-pricing"></a>Comparación de precios de producto

En la tabla siguiente se comparan los precios de los productos Azure Stack HCI y Windows Server 2019. Para obtener más información, consulte [Precios de Azure Stack HCI](https://azure.microsoft.com/pricing/details/azure-stack/hci/).

| **Atributo** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Tipo de precio | Servicio de suscripción | Varía: suele ser una licencia de uso único |
| Estructura de precios | Por núcleo, por mes | Varía: normalmente, por núcleo |
| Precio | 10 USD por núcleo al mes | Consulte [Precios y licencias para Windows Server 2019](https://www.microsoft.com/windows-server/pricing) |
| Período de evaluación/prueba | Evaluación gratuita de 30 días después del registro | Copia de evaluación de 180 días |
| Canales | Contrato Enterprise, proveedor de servicios en la nube o directo | Contrato Enterprise/licencias por volumen, OEM, contrato de licencia de proveedor de servicios (SPLA) |

## <a name="next-steps"></a>Pasos siguientes

- [Comparación entre Azure Stack HCI y Azure Stack Hub](compare-azure-stack-hub.md)
