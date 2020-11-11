---
title: Requisitos previos para la instalación de IoT Hub en Azure Stack Hub
description: Obtenga información sobre los requisitos previos necesarios antes de instalar el proveedor de recursos de IoT Hub en Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: f194ef78a31722a05742b14b312ea4aad58d3ed3
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050403"
---
# <a name="prerequisites-for-installing-iot-hub-on-azure-stack-hub"></a>Requisitos previos para la instalación de IoT Hub en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Deben completarse los siguientes requisitos previos para poder instalar IoT Hub en Azure Stack Hub. **Pueden ser necesarios varios días o semanas** para completar todos los pasos.

> [!IMPORTANT]
> En estos requisitos previos, se da por hecho que ya ha implementado al menos un sistema integrado de Azure Stack Hub de cuatro nodos, con un **número de compilación 1.2005.6.53** o superior. No se admite el proveedor de recursos de IoT Hub en el Kit de desarrollo de Azure Stack Hub.

## <a name="common-prerequisites"></a>Requisitos previos comunes

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="dependency-prerequisites"></a>Requisitos previos de dependencias

1. Descargue e [instale Event Hubs](event-hubs-rp-install.md) desde Marketplace. La implementación de Event Hubs debe realizarse ANTES de que se inicie la implementación de IoT Hub.
2. Para una descarga e instalación más rápidas de IoT Hub, descargue los siguientes elementos dependientes de Marketplace antes de descargar el paquete de IoT Hub. De lo contrario, la implementación de IoT Hub intentará descargar los paquetes dependientes:
    * Custom Script Extension
    * Configuración de estado deseado de PowerShell
    * Licencia gratuita: SQL Server 2016 SP2 Express en Windows Server 2016
    * Extensión IaaS de SQL
    * Proveedor de recursos del complemento de Azure Stack para Windows Server
3. Espere al menos 10 minutos después de la instalación correcta de Event Hubs, antes de continuar con la implementación de IoT Hub.

## <a name="certificate-requirements"></a>Requisitos de certificados

1. Adquiera un certificado TLS/SSL de infraestructura de clave pública (PKI) para Event Hubs. El nombre alternativo del sujeto (SAN) debe cumplir el siguiente patrón de nomenclatura: `CN=*.mgmtiothub.<region>.<fqdn>`.

   Se puede especificar el nombre del sujeto, pero IoT Hub no lo usa al administrar los certificados. Solo se usa el nombre alternativo del sujeto. Consulte [Requisitos de certificados PKI](azure-stack-pki-certs.md) para obtener una lista completa de los requisitos detallados.

   ![Ejemplo de certificado de IoT Hub](media\iot-hub-rp-prerequisites\certificate.png)

2. Asegúrese de consultar [Validación de certificados PKI](azure-stack-validate-pki-certs.md). En el artículo se muestra cómo preparar y validar los certificados que se usan para el proveedor de recursos de IoT Hub. 

## <a name="dns-configuration-requirements"></a>Requisitos de configuración de DNS
 
Para que IoT Hub funcione correctamente en la red en Azure Stack Hub, el administrador de red debe configurar el DNS. Busque la configuración de reenvío condicional de DNS en la herramienta de administración de DNS y agregue una regla de reenvío condicional para permitir el tráfico para: `<region>.cloudapp.<externaldomainname>`. Por ejemplo, `ussouth.cloudapp.contoso.com`.

## <a name="next-steps"></a>Pasos siguientes

A continuación, instale el proveedor de recursos de IoT Hub en la [instancia con conexión de Azure Stack](iot-hub-rp-install.md).
