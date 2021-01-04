---
title: Comparación entre Azure Stack HCI, Azure Stack Hub y Windows Server
description: Este tema le ayudará a determinar qué solución, entre Azure Stack HCI, Azure Stack Hub y Windows Server, es adecuada para su organización.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/11/2020
ms.openlocfilehash: bd1798ddfcf0897eeaedaeaae0cfb02eac1aaa1e
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2020
ms.locfileid: "97365163"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub-and-windows-server"></a>Comparación entre Azure Stack HCI, Azure Stack Hub y Windows Server

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019, y Azure Stack Hub

Muchos clientes se preguntan qué solución es más adecuada para sus necesidades, si Windows Server, Azure Stack HCI o Azure Stack Hub. La tabla siguiente le ayuda a determinar cuál es la más adecuada para su organización. 

## <a name="compare-azure-stack-hci-to-windows-server"></a>Comparación entre Azure Stack HCI y Windows Server

Windows Server y Azure Stack HCI proporcionan la misma experiencia de usuario de alta calidad con una hoja de ruta de nuevas versiones.

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Mejor invitado y servidor tradicional | Mejor host de virtualización para un centro de datos definido por software, incluido Espacios de almacenamiento directo |
| Se ejecuta en cualquier parte, con un modelo de licencias de software tradicional | Se ejecuta en el hardware de su proveedor preferido, pero se proporciona como un servicio de Azure y se factura a su cuenta de Azure |
| Dos opciones de instalación: Server con experiencia de escritorio o Server Core | Basado en un Server Core ligeramente personalizado |

### <a name="when-to-use-windows-server"></a>Cuándo usar Windows Server

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server es un sistema operativo muy versátil y multiuso, con docenas de roles y cientos de características, entre las que se incluyen los derechos de invitado. | Azure Stack HCI no incluye derechos de invitado y está pensado para usarse en una arquitectura hiperconvergida moderna. |
| Use Windows Server para ejecutar máquinas virtuales o para instalaciones sin sistema operativo que engloben todos los roles de servidor tradicionales, como Active Directory, servicios de archivo, DNS, DHCP, Internet Information Services (IIS), host/invitado de contenedor, SQL Server, Exchange Server, Servicio de protección de host, etc. | Diseñado como host de virtualización de Hyper-V, Azure Stack HCI solo tiene licencia para ejecutar un número pequeño de roles de servidor directamente. Los demás roles deben ejecutarse dentro de las máquinas virtuales. |

### <a name="when-to-use-azure-stack-hci"></a>Cuándo usar Azure Stack HCI

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server se puede ejecutar de forma local o en la nube, pero no es una oferta completa hiperconvergida.| Use Azure Stack HCI para ejecutar máquinas virtuales de forma local y, opcionalmente, extenderlas en dos sitios y con conexiones a los servicios híbridos de Azure. Es una manera fácil de modernizar y proteger sus centros de datos y sucursales, conseguir el mejor rendimiento del sector para las bases de datos de SQL Server y ejecutar escritorios virtuales locales para obtener una baja latencia y soberanía de datos.|
| Windows Server es una herramienta multiuso para todos los roles de Windows Server, virtualizados o no. | Use Azure Stack HCI para la virtualización de aplicaciones empresariales clásicas como Exchange, SharePoint y SQL Server y para virtualizar los roles de Windows Server como el servidor de archivos, DNS, DHCP, IIS y AD. Incluye acceso sin restricciones a todas las características de Hyper-V como, por ejemplo, a las máquinas virtuales blindadas.|
| Muchas implementaciones de Windows Server se ejecutan en hardware antiguo. | Use Azure Stack HCI para usar la infraestructura definida por software en lugar de matrices de almacenamiento anticuadas o dispositivos de red, sin rediseñar la arquitectura principal. Hyper-V integrado, Espacios de almacenamiento directo y redes definidas mediante software (SDN) son características fáciles de administrar y accesibles directamente. Ejecute aplicaciones en máquinas virtuales Windows o Linux.|

## <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Comparación entre Azure Stack HCI y Azure Stack Hub

A medida que la organización se transforma digitalmente, puede que se dé cuenta de que puede avanzar más rápido con el uso de los servicios de la nube pública para compilar soluciones en arquitecturas modernas y actualizar aplicaciones heredadas. Sin embargo, por motivos que incluyen obstáculos tecnológicos y normativos, las cargas de trabajo deben permanecer en el entorno local. Use esta tabla para ayudarle a determinar qué estrategia de nube híbrida de Microsoft le ofrece lo que necesita cuando lo necesita, ofreciéndole innovación para cargas de trabajo, estén donde estén.

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Nuevas aptitudes, procesos innovadores | Mismas aptitudes, procesos familiares |
| Los servicios de Azure en su centro de datos | Conecte el centro de datos a los servicios de Azure |

### <a name="when-to-use-azure-stack-hub"></a>Cuándo usar Azure Stack Hub

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Use Azure Stack Hub para la infraestructura como servicio (IaaS) de autoservicio, con un fuerte aislamiento y un seguimiento de uso preciso, así como contracargos para varios inquilinos colocados. Ideal para proveedores de servicios y nubes privadas de empresa. Plantillas de Azure Marketplace. | Azure Stack HCI no aplica de forma nativa ni permite usar varios inquilinos. |
| Use Azure Stack Hub para desarrollar y ejecutar aplicaciones que se basan en los servicios de plataforma como servicio (PaaS), como Web Apps, Functions o Event Hubs en el entorno local. Estos servicios se ejecutan en Azure Stack Hub exactamente como lo hacen en Azure, lo que proporciona un entorno de desarrollo y tiempo de ejecución híbrido coherente. | Azure Stack HCI no ejecuta servicios de PaaS en el entorno local. |
| Use Azure Stack Hub para modernizar la implementación de aplicaciones y el funcionamiento con prácticas de DevOps como infraestructura como código, integración continua e implementación continua (CI/CD) y funciones prácticas, como extensiones de VM coherentes con Azure. Ideal para equipos de desarrollo y DevOps. | Azure Stack HCI no incluye de forma nativa ninguna herramienta de DevOps. |

### <a name="when-to-use-azure-stack-hci"></a>Cuándo usar Azure Stack HCI

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack Hub requiere un mínimo de cuatro nodos y sus propios conmutadores de red. | Use Azure Stack HCI para una superficie mínima para oficinas y sucursales remotas. Comience con solo 2 nodos de servidor y redes inmutables de salida a entrada para la máxima simplicidad y rentabilidad. Las ofertas de hardware empiezan a partir de 4 unidades y 64 GB de memoria, por debajo de 10 000 USD por nodo. |
| Azure Stack Hub restringe la capacidad de configuración de Hyper V y el conjunto de características para mantener la coherencia con Azure. | Use Azure Stack HCI para la virtualización básica de Hyper-V para aplicaciones empresariales clásicas como Exchange, SharePoint y SQL Server y para virtualizar los roles de Windows Server como el servidor de archivos, DNS, DHCP, IIS y AD. Acceso sin restricciones a todas las características de Hyper-V, como las VM blindadas.|
| Azure Stack Hub no expone estas tecnologías de infraestructura. | Use Azure Stack HCI para usar la infraestructura definida por software en lugar de matrices de almacenamiento anticuadas o dispositivos de red, sin rediseñar la arquitectura principal. Hyper-V integrado, Espacios de almacenamiento directo y redes definidas mediante software (SDN) son características fáciles de administrar y accesibles directamente. |

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es Azure Stack HCI?](../overview.md)
