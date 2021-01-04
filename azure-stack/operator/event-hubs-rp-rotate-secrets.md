---
title: Rotación de secretos para el proveedor de recursos de Event Hubs en Azure Stack Hub
description: Obtenga información sobre cómo rotar secretos para el proveedor de recursos de Event Hubs en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/15/2020
ms.reviewer: jfggdl
ms.lastreviewed: 10/15/2020
ms.openlocfilehash: fe238576814d1aae5d7469e9438f8921f912f0ea
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2020
ms.locfileid: "97349471"
---
# <a name="how-to-rotate-secrets-for-event-hubs-on-azure-stack-hub"></a>Rotación de secretos para Event Hubs en Azure Stack Hub

En este artículo se muestra cómo rotar los secretos que utiliza el proveedor de recursos de Event Hubs.

## <a name="overview-and-prerequisites"></a>Introducción y requisitos previos

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Preparación de un nuevo certificado TLS

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Girar secretos

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Solución de problemas

La rotación de secretos debe completarse correctamente sin errores. Si experimenta alguna de las siguientes condiciones en el portal de administración, [abra una solicitud de soporte técnico](azure-stack-manage-basics.md#where-to-get-support) para obtener ayuda:

   - Problemas de autenticación, incluidos los problemas de conexión con el proveedor de recursos de Event Hubs.
   - No se puede actualizar el proveedor de recursos ni editar los parámetros de configuración.
   - No aparecen las métricas de uso.
   - No se generan las facturas.
   - Las copias de seguridad no se realizan.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo rotar los secretos de la infraestructura de Azure Stack Hub, visite [Rotación de secretos en Azure Stack Hub](azure-stack-rotate-secrets.md).