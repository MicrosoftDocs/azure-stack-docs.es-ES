---
title: Rotación de secretos para el proveedor de recursos de IoT Hub en Azure Stack Hub
description: Aprenda a rotar secretos para el proveedor de recursos de IoT Hub en Azure Stack Hub
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/15/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/15/2020
ms.openlocfilehash: d693b73794b71ec32816f2e79370e2f836b00e7b
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517565"
---
# <a name="how-to-rotate-secrets-for-iot-hub-on-azure-stack-hub"></a>Rotación de secretos para IoT Hub en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

En este artículo se muestra cómo rotar los secretos que utiliza el proveedor de recursos de IoT Hub.

## <a name="overview-and-prerequisites"></a>Introducción y requisitos previos

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Preparación de un nuevo certificado TLS

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Girar secretos

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Solución de problemas

La rotación de secretos debe completarse correctamente sin errores. Si experimenta alguna de las siguientes condiciones en el portal de administración, [abra una solicitud de soporte técnico](azure-stack-manage-basics.md#where-to-get-support) para obtener ayuda:

   - Problemas de autenticación, incluidos los problemas de conexión con el proveedor de recursos de IoT Hub.
   - No se puede actualizar el proveedor de recursos ni editar los parámetros de configuración.
   - No aparecen las métricas de uso.
   - No se generan las facturas.
   - Las copias de seguridad no se realizan.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo rotar los secretos de la infraestructura de Azure Stack Hub, visite [Rotación de secretos en Azure Stack Hub](azure-stack-rotate-secrets.md).