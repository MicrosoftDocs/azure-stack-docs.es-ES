---
title: Requisitos previos para instalar Event Hubs en Azure Stack Hub
description: Obtenga información sobre los requisitos previos necesarios antes de instalar el proveedor de recursos de Event Hubs en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: b4286074828a0cea911ed6ccd977b6b0690e7eb1
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562977"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>Requisitos previos para la instalación de Event Hubs en Azure Stack Hub

Deben completarse los siguientes requisitos previos para poder instalar Event Hubs en Azure Stack Hub. **Pueden ser necesarios varios días o semanas** para completar todos los pasos.

> [!IMPORTANT]
> En estos requisitos previos, se da por hecho que ya ha implementado al menos un sistema integrado Azure Stack Hub de cuatro nodos. No se admite el proveedor de recursos de Event Hubs en el Kit de desarrollo de Azure Stack (ASDK).

> [!IMPORTANT]
> Event Hubs necesita la versión de compilación de Azure Stack Hub 2005 o posterior. Tenga en cuenta que las compilaciones de Azure Stack Hub son incrementales. Por ejemplo, si tiene instalada la versión 1910, primero debe actualizar a la versión [2002](./release-notes.md?view=azs-2002&preserve-view=true#2002-build-reference) y luego a la 2005. Es decir, no puede omitir las compilaciones intermedias.

## <a name="common-prerequisites"></a>Requisitos previos comunes

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>Requisitos previos de Event Hubs

1. Adquiera certificados SSL de infraestructura de clave pública (PKI) para Event Hubs. El nombre alternativo del sujeto (SAN) debe cumplir el siguiente patrón de nomenclatura: `CN=*.eventhub.<region>.<fqdn>`. Se puede especificar el nombre del sujeto, pero Event Hubs no lo usa al administrar los certificados. Solo se usa el nombre alternativo del sujeto. Consulte [Requisitos de certificados PKI](azure-stack-pki-certs.md) para obtener una lista completa de los requisitos detallados.  

   ![certificado de ejemplo](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **Los archivos PFX deben estar protegidos por contraseña**. La contraseña se solicitará más adelante durante la instalación.

2. Asegúrese de consultar [Validación de certificados PKI](azure-stack-validate-pki-certs.md). En el artículo se muestra cómo preparar y validar los certificados que se usan para el proveedor de recursos de Event Hubs. 

## <a name="next-steps"></a>Pasos siguientes

A continuación, [instale el proveedor de recursos de Event Hubs](event-hubs-rp-install.md).
