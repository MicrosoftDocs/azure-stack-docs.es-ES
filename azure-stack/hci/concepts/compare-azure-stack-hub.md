---
title: Comparación entre Azure Stack HCI y Azure Stack Hub
description: Este tema le ayudará a determinar qué solución, entre Azure Stack HCI y Azure Stack Hub, es mejor para su organización.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/04/2021
ms.openlocfilehash: 7c501650d5c9a4d2d6df66269f689d80ffd3f8e0
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570691"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Comparación entre Azure Stack HCI y Azure Stack Hub

> Se aplica a: Azure Stack HCI versión 20H2; Azure Stack Hub

A medida que la organización se transforma digitalmente, puede que se dé cuenta de que puede avanzar más rápido con el uso de los servicios de la nube pública para compilar soluciones en arquitecturas modernas y actualizar aplicaciones heredadas. Sin embargo, por motivos que incluyen obstáculos tecnológicos y normativos, las cargas de trabajo deben permanecer en el entorno local. Use esta tabla para ayudarle a determinar qué estrategia de nube híbrida de Microsoft le ofrece lo que necesita cuando lo necesita, ofreciéndole innovación para cargas de trabajo, estén donde estén.

| Azure Stack HCI | Azure Stack Hub |
| --------------- | --------------- |
| Mismas aptitudes, procesos familiares | Nuevas aptitudes, procesos innovadores |
| Conecte el centro de datos a los servicios de Azure | Los servicios de Azure en su centro de datos |

## <a name="when-to-use-azure-stack-hci"></a>Cuándo usar Azure Stack HCI

En la tabla siguiente se comparan los casos de uso para los que Azure Stack HCl es una mejor opción que Azure Stack Hub.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                         |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Use Azure Stack HCI para una superficie mínima para oficinas y sucursales remotas. Comience con solo dos servidores y redes opuestas sin conmutador para la máxima simplicidad y rentabilidad. | Azure Stack Hub requiere un mínimo de cuatro servidores y sus propios conmutadores de red. |
| Use Azure Stack HCI para la virtualización de aplicaciones empresariales clásicas como Exchange, SharePoint y SQL Server y para virtualizar los roles de Windows Server como el servidor de archivos, DNS, DHCP, IIS y Active Directory. Proporciona acceso sin restricciones a las características de Hyper-V.| Azure Stack Hub restringe la capacidad de configuración de Hyper-V y el conjunto de características para mantener la coherencia con Azure. | 
| Use Azure Stack HCI para usar la infraestructura definida por software en lugar de matrices de almacenamiento anticuadas o dispositivos de red, sin rediseñar la arquitectura principal. Hyper-V integrado, Espacios de almacenamiento directo y redes definidas mediante software (SDN) son características fáciles de administrar y accesibles directamente. | Azure Stack Hub no expone estas tecnologías de infraestructura. |

## <a name="when-to-use-azure-stack-hub"></a>Cuándo usar Azure Stack Hub

En la tabla siguiente se comparan los casos de uso para los que Azure Stack Hub es una mejor opción que Azure Stack HCI.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                          |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Azure Stack HCI no aplica de forma nativa ni permite usar varios inquilinos. | Use Azure Stack Hub para la infraestructura como servicio (IaaS) de autoservicio, con un fuerte aislamiento y un seguimiento de uso preciso, así como contracargos para varios inquilinos colocados. Ideal para proveedores de servicios y nubes privadas de empresa. Plantillas de Azure Marketplace. | 
| Azure Stack HCI no está diseñado para ejecutar servicios de plataforma como servicio (PaaS) en el entorno local, aunque incluye la capacidad de hospedar [Azure Kubernetes Service](../../aks-hci/overview.md). | Use Azure Stack Hub para desarrollar y ejecutar aplicaciones que se basan en los servicios PaaS, como Web Apps, Functions o Event Hubs en el entorno local. Estos servicios se ejecutan en Azure Stack Hub exactamente como lo hacen en Azure, lo que proporciona un entorno de desarrollo y tiempo de ejecución híbrido coherente. |
| Azure Stack HCI no incluye de forma nativa ninguna herramienta de DevOps. | Use Azure Stack Hub para modernizar la implementación de aplicaciones y el funcionamiento con prácticas de DevOps como infraestructura como código, integración continua e implementación continua (CI/CD) y funciones prácticas, como extensiones de VM coherentes con Azure. Ideal para equipos de desarrollo y DevOps. |

## <a name="next-steps"></a>Pasos siguientes

- [Comparación entre Azure Stack HCI y Windows Server 2019](compare-windows-server.md)
