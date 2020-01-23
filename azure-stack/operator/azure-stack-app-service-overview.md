---
title: Introducción a Azure App Service en Azure Stack | Microsoft Docs
description: Introducción a Azure App Service y Azure Functions en Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: BryanLa
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: bb34fc2322294f9829a8dea02f0812a6503c0197
ms.sourcegitcommit: ce01b2cd114ca8ab5b70c6311b66c58ceb054469
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2020
ms.locfileid: "75924236"
---
# <a name="azure-app-service-and-azure-functions-on-azure-stack-hub-overview"></a>Introducción a Azure App Service y Azure Functions en Azure Stack Hub

*Se aplica a: Sistemas integrados de Azure Stack Hub y al kit de desarrollo de Azure Stack Hub*

Azure App Service en Azure Stack Hub es una oferta de plataforma como servicio (PaaS) de Microsoft Azure disponible en Azure Stack Hub. El servicio habilita a sus clientes internos o externos para crear aplicaciones de Azure Functions, API y web para cualquier plataforma o dispositivo. Pueden integrar sus aplicaciones con aplicaciones locales y automatizar sus procesos empresariales. Los operadores en la nube de Azure Stack Hub pueden ejecutar las aplicaciones de cliente en máquinas virtuales totalmente administradas, con la elección de los recursos de máquinas virtuales compartidas o máquinas virtuales dedicadas.

Azure App Service le permite automatizar procesos empresariales y hospedar las API en la nube. Como un servicio integrado único, Azure App Service le permite combinar distintos componentes, como sitios web, API REST y procesos empresariales, en una única solución.

## <a name="why-offer-azure-app-service-on-azure-stack-hub"></a>¿Por qué ofrecer Azure App Service en Azure Stack Hub?

Estas son algunas de las características y funcionalidades principales de Azure App Service:

- **Varios lenguajes y plataformas**: Azure App Service es compatible con ASP.NET, Node.js, Java, PHP y Python. También puede ejecutar Windows PowerShell y otros scripts o ejecutables en máquinas virtuales de App Service.
- **Optimización de DevOps**: configure la integración y la implementación continuas con GitHub, Git local o BitBucket. Promueva actualizaciones mediante entornos de ensayo y pruebas, y administre las aplicaciones en App Service mediante Azure PowerShell o la interfaz de la línea de comandos (CLI) multiplataforma.
- **Integración de Visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación e implementación de aplicaciones.

## <a name="app-types-in-app-service"></a>Tipos de aplicaciones en App Service

App Service ofrece varios tipos de aplicación, cada uno pensado para hospedar una carga de trabajo específica:

- [Web Apps](/azure/app-service/overview) para hospedar sitios y aplicaciones web.
- [API Apps](/azure/app-service/overview) para hospedar las API REST.
- Azure Functions para hospedar cargas de trabajo sin servidor basadas en eventos.

La palabra *aplicación* hace referencia a los recursos de hospedaje dedicados a ejecutar una carga de trabajo. Si se toma la *aplicación web* como ejemplo, probablemente está acostumbrado a pensar en una aplicación web como los recursos de proceso y el código de aplicación que juntos ofrecen funcionalidad a un explorador. En Azure App Service, una aplicación web es el recurso de proceso que Azure Stack Hub proporciona para hospedar su código de aplicación.

La aplicación puede estar compuesta de varias aplicaciones de App Service de diferentes tipos. Por ejemplo, si la aplicación se compone de un front-end web y un back-end de la API REST, se pueden realizar las operaciones siguientes:

- Implementar los dos (front-end y API) en una sola aplicación web.
- Implementar el código de front-end en una aplicación web y el código de back-end en una aplicación de API.

   [![Introducción a App Service con datos de supervisión](media/azure-stack-app-service-overview/image01.png "Introducción a App Service con datos de supervisión")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>¿Qué es un plan de App Service?

El proveedor de recursos de App Service usa el mismo código que Azure App Service, y por ello comparte algunos conceptos comunes. En App Service, el contenedor de precios para las aplicaciones se denomina *plan de App Service*. Representa el conjunto de máquinas virtuales dedicadas usadas para hospedar las aplicaciones. Dentro de una suscripción determinada, pueden haber varios planes de App Service.

En Azure, hay trabajados compartidos y dedicados. Un trabajo compartido admite el hospedaje de aplicaciones para varios inquilinos de alta densidad, y solo existe un conjunto de trabajos compartidos. Solo un inquilino usa los servidores dedicados que pueden ser de tres tamaños: pequeño, mediano y grande. Las necesidades de los clientes locales no siempre se pueden describir con estos términos. En App Service en Azure Stack Hub, los administradores de proveedores de recursos definen los niveles de trabajo que quieren que estén disponibles. Según las necesidades concretas de hospedaje, puede definir varios conjuntos de trabajos compartidos o diferentes conjuntos de trabajos dedicados. Mediante el uso de esas definiciones de nivel de trabajo, pueden definir sus propias SKU de precios.

## <a name="portal-features"></a>Características del portal


Azure App Service en Azure Stack Hub usa la misma interfaz de usuario que Azure App Service. Lo mismo puede decirse con el back-end. Sin embargo, en Azure Stack Hub algunas características están deshabilitadas. Las expectativas o los servicios específicos de Azure que requieren esas características no están disponibles actualmente en Azure Stack Hub.

## <a name="next-steps"></a>Pasos siguientes

- [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md)
- [Instalación del proveedor de recursos de Azure App Service](azure-stack-app-service-deploy.md)

También puede probar otros [servicios de plataforma como servicio (PaaS)](service-plan-offer-subscription-overview.md), como el [proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md) y el [proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md).
