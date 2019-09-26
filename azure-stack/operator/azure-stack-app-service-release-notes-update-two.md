---
title: Notas de la versión de la actualización 2 de App Service en Azure Stack | Microsoft Docs
description: Obtenga información acerca de las mejoras, las correcciones y los problemas conocidos de la actualización 2 para App Service en Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: f427a31001f8f486fd231af7e59ef2bb30592661
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974805"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Notas de la versión de la actualización 2 de App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Estas notas de la versión describen las mejoras, las correcciones y los problemas conocidos de la actualización 2 de Azure App Service en Azure Stack. Los problemas conocidos se dividen tres secciones: los problemas directamente relacionados con la implementación, los problemas relacionados con el proceso de actualización y los problemas relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1804 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar Azure App Service 1.2.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 2 de App Service en Azure Stack es **72.0.13698.10**.

### <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> Las nuevas implementaciones de Azure App Service en Azure Stack requieren ahora un [certificado comodín con tres firmantes](azure-stack-app-service-before-you-get-started.md#get-certificates) debido a las mejoras en la forma en que se trata el inicio de sesión único para Kudu en Azure App Service. El nuevo firmante es **\*.sso.appservice.\<región\>.\<nombreDominio\>.\<extensión\>**

Consulte [Requisitos previos para implementar App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 2 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK del portal de Azure Stack.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.11612**.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
  - Se agregó .Net Framework 4.7.1
  - Versiones de **Node.JS** agregadas:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Versiones de **NPM** agregadas:
    - 5.6.0
  - Se actualizaron los componentes .Net Core para que fueran coherentes con Azure App Service en la nube pública.
  - Kudu actualizado

- Característica de intercambio automático de ranuras de implementación habilitada: [Configuración del intercambio automático](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap).

- Característica de prueba de producción habilitada: [Introducción a Testing in Production](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

- Azure Functions Proxies habilitado: [Uso de Azure Functions Proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

- Se agregó compatibilidad de la experiencia de usuario con la extensión de administración de App Service para:
  - Rotación de secretos
  - Rotación de certificados
  - Rotación de credenciales de sistema
  - Rotación de cadenas de conexión

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada.

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Vaya a WorkersNsg en el portal del administrador y agregue una regla de seguridad de salida con las siguientes propiedades:

* Origen: Any
* Intervalo de puertos de origen: *
* Destino: Direcciones IP
* Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
* Intervalo de puertos de destino: 445
* Protocolo: TCP
* Acción: Allow
* Prioridad: 700
* Nombre: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

Consulte la documentación de las [notas de la versión de Azure Stack 1804](azure-stack-update-1903.md)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Requisitos previos para implementar App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
