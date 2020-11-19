---
title: Diferencias entre Azure global, Azure Stack Hub y Azure Stack HCI
titleSuffix: Azure Stack Hub
description: Conozca las diferencias entre Azure global, Azure Stack Hub y Azure Stack HCI.
author: justinha
ms.topic: overview
ms.date: 07/10/2020
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 4b6fe9434d7fbc0f62de4c22241300ad20170af2
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674503"
---
# <a name="differences-between-global-azure-azure-stack-hub-and-azure-stack-hci"></a>Diferencias entre Azure global, Azure Stack Hub y Azure Stack HCI

Microsoft proporciona Azure y la familia de servicios de Azure Stack Hub en un ecosistema de Azure. Utilice el mismo modelo de aplicación, portales de autoservicio y API con Azure Resource Manager para brindar funcionalidades basadas en la nube si su empresa usa Azure global o recursos locales.

En este artículo se describen las diferencias entre las funcionalidades de Azure global, Azure Stack Hub y Azure Stack HCI. También proporciona recomendaciones para escenarios comunes para ayudarle a elegir la mejor opción para la entrega de servicios en la nube de Microsoft para su organización.

![Información general del ecosistema de Azure](./media/compare-azure-azure-stack/azure-family-updated.png)

## <a name="global-azure"></a>Azure global

Microsoft Azure es conjunto en constante expansión de servicios en la nube para ayudar a su organización a satisfacer sus necesidades comerciales. Le otorga la libertad de crear, administrar e implementar aplicaciones en una red mundial enorme con sus herramientas y plataformas favoritas.

Azure global ofrece más de 100 servicios disponibles en 54 regiones de todo el mundo. Para la lista más reciente de servicios de Azure global, consulte los [*Productos disponibles por región*](https://azure.microsoft.com/regions/services). Los servicios disponibles en Azure se enumeran por categoría y también por disponibilidad general o en versión preliminar.

Para más información acerca de los servicios de Azure global, consulte [Introducción a Azure](/azure/?panel=get-started1&pivot=get-started).

## <a name="azure-stack-hub"></a>Azure Stack Hub

Azure Stack Hub es una extensión de Azure que incorpora a entornos locales la agilidad y la innovación de la informática en la nube. Implementado en local, Azure Stack Hub puede utilizarse para proporcionar servicios coherentes con Azure, ya sea conectados a Internet (y a Azure) o en entornos desconectados sin conexión a Internet. Azure Stack Hub usa las mismas tecnologías subyacentes que Azure global, entre las cuales se incluyen los componentes básicos de la infraestructura como servicio (IaaS), del software como servicio (SaaS) y las funcionalidades de la plataforma como servicio (PaaS) opcionales. Estas son algunas de ellas:

- Máquinas virtuales de Azure para Windows y Linux
- Aplicaciones web y funciones de Azure
- Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Contenedores
- Herramientas de administración (planes, ofertas, RBAC, etc.)

Las funcionalidades de PaaS de Azure Stack Hub son opcionales porque Azure Stack Hub no lo opera Microsoft, sino nuestros clientes. Esto significa que puede ofrecer cualquier servicio de PaaS que desee a los usuarios finales si está preparado para apartar la infraestructura y los procesos subyacentes de estos usuarios. Sin embargo, Azure Stack Hub incluye varios proveedores de servicios de PaaS opcionales, como App Service, bases de datos SQL y bases de datos MySQL. Estos se entregan como proveedores de recursos, multiinquilino, listos, actualizados con el tiempo con actualizaciones estándar de Azure Stack Hub y visibles en el portal de Azure Stack Hub; además, se integran bien con Azure Stack Hub.

Además de los proveedores de recursos descritos anteriormente, hay servicios de PaaS adicionales disponibles que se han probado, como [soluciones basadas en plantillas de Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) que se ejecutan en IaaS. Como operador de Azure Stack Hub puede ofrecerlos como servicios de PaaS a los usuarios, junto con:

- Service Fabric
- Kubernetes Container Service
- Cadena de bloques Ethereum
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Casos de uso de ejemplo para Azure Stack Hub

- Creación de modelos financieros
- Datos clínicos y notificaciones
- Análisis de dispositivos IoT
- Optimización del surtido comercial
- Optimización de la cadena de suministro
- IoT industrial
- Mantenimiento predictivo
- Ciudad inteligente
- Participación ciudadana

Para más información sobre Azure Stack Hub, consulte [¿Qué es Azure Stack Hub?](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI

[Azure Stack HCI](../hci/overview.md) es un clúster hiperconvergido que usa hardware validado para ejecutar cargas de trabajo de Windows y Linux virtualizadas en el entorno local y que se conecta fácilmente a Azure para realizar operaciones de copia de seguridad, recuperación y supervisión basadas en la nube. En la actualidad, en Windows Server 2019, Azure Stack HCI se entrega como un servicio de Azure con un modelo de licencias basado en suscripciones y funcionalidades híbridas integradas. Aunque Azure Stack HCI se basa en los mismos componentes principales de sistema operativo que Windows Server, se trata de una línea de productos completamente nueva que tiene el objetivo de convertirse en el mejor host de virtualización.

Azure Stack HCI usa hardware de un fabricante de equipos originales asociado validado por Microsoft para garantizar un rendimiento y una confiabilidad óptimos. Las soluciones incluyen compatibilidad con tecnologías como las unidades de NVMe, la memoria persistente y la red de acceso directo a memoria remota (RDMA).

### <a name="example-use-cases-for-azure-stack-hci"></a>Casos de uso de ejemplo para Azure Stack HCI

- Sistemas de oficina remota o sucursal
- Consolidación del centro de datos
- Infraestructura de escritorio virtual
- Infraestructura crítica para la empresa
- Almacenamiento de bajo costo
- Alta disponibilidad y recuperación ante desastres en la nube
- Virtualización de aplicaciones empresariales como SQL Server

Visite el [sitio web de Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci/) para ver las más de 70 soluciones de Azure Stack HCI actualmente disponibles de los asociados de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

[Aspectos básicos de administración de Azure Stack Hub](azure-stack-manage-basics.md)

[Inicio rápido: Uso del portal de administración de Azure Stack Hub](azure-stack-manage-portals.md)
