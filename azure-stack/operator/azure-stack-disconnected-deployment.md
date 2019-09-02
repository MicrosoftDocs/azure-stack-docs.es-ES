---
title: Decisiones de implementación desconectada de Azure en sistemas integrados de Azure Stack | Microsoft Docs
description: Determine qué decisiones de planeamiento hay que tomar en las implementaciones de Azure Stack multinodo con conexión a Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 11bf3bd4cc670d45fc4a4c9d1421fc0c25440726
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118675"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Decisiones de planeamiento de implementación desconectada de Azure en sistemas integrados de Azure Stack
Una vez que haya decidido [cómo va a integrar Azure Stack en el entorno de nube híbrida](azure-stack-connection-models.md), puede concluir sus decisiones de implementación de Azure Stack.

Puede implementar y usar Azure Stack sin conexión a Internet. Sin embargo, este tipo de implementación tiene la limitación de que solo se puede usar un almacén de identidades de AD FS y el modelo de facturación por capacidad. Dado que la arquitectura multiempresa requiere usar Azure Active Directory (Azure AD), esta arquitectura multiempresa no se admite para realizar implementaciones sin conexión. 

Elija esta opción si:
- Las opciones de seguridad u otras restricciones requieren que implemente Azure Stack en un entorno que no esté conectado a Internet.
- Desea bloquear datos (incluidos los datos de uso) para que no se envíen a Azure.
- Desea usar Azure Stack solo como una solución de nube privada que se implemente en la intranet corporativa y no tiene interés alguno en escenarios híbridos.

> [!TIP]
> En ocasiones, este tipo de entorno también se conoce como *escenario submarino*.

Una implementación sin conexión no significa que posteriormente no sea posible conectar una instancia de Azure Stack a Azure en escenarios de máquinas virtuales de inquilino híbrido. Esto significa que no tiene conectividad a Azure durante la implementación o que no quiere usar Azure AD como almacén de identidades.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Características con limitaciones o que no están disponible en implementaciones desconectadas 
Azure Stack se ha diseñado para funcionar mejor cuando se conecta a Azure, por lo que es importante tener en cuenta que hay algunas funcionalidades y características que tienen limitaciones o que no están disponibles en el modo sin conexión. 

|Característica|Impacto en modo desconectado|
|-----|-----|
|Implementación de máquina virtual con extensión DSC para configurar la máquina virtual después de la implementación|Limitación: la extensión DSC busca en Internet el WMF más reciente.|
|Implementación de máquina virtual con extensión Docker para ejecutar comandos de Docker|Limitación: Docker buscará en Internet la versión más reciente y se producirá un error en esta comprobación.|
|Vínculos de documentación en el Portal de Azure Stack|No disponible: los vínculos como Enviar comentarios, Ayuda, Inicio rápido, etc., que utilizan una dirección URL de Internet, no funcionarán.|
|Corrección o mitigación de alertas que hace referencia a una guía de corrección en línea|No disponible: no funcionará ningún vínculo de corrección de alertas que utilice una dirección URL de Internet.|
|Marketplace: la capacidad para seleccionar y agregar paquetes de la galería directamente desde Azure Marketplace.|Limitación: al implementar Azure Stack en un modo sin conexión (sin conectividad a Internet), no se pueden descargar elementos de Marketplace mediante el portal de Azure Stack. Sin embargo, puede usar la [herramienta de redifusión de Marketplace](azure-stack-download-azure-marketplace-item.md) para descargar los elementos de Marketplace en un equipo que tenga conectividad a Internet y transferirlos después a su entorno de Azure Stack.|
|Uso de cuentas de federación de Azure Active Directory para administrar una implementación de Azure Stack|No disponible: esta característica requiere conectividad con Azure. En su lugar se debe usar AD FS con una instancia local de Active Directory.|
|Servicios de aplicaciones|Limitación: las aplicaciones web pueden requerir acceso a Internet para contenidos actualizados.|
|Interfaz de línea de comandos (CLI)|Limitación: la CLI ha reducido la funcionalidad en lo que se refiere a la autenticación y el aprovisionamiento de entidades de servicio.|
|Visual Studio - Cloud discovery|Limitación: Cloud Discovery detectará nubes diferentes o no funcionará.|
|Visual Studio - AD FS|Limitación: solo Visual Studio Enterprise y Visual Studio Code admiten la autenticación de AD FS.
Telemetría|No disponible: tanto los datos de telemetría de Azure Stack como todos los paquetes de la galería de terceros que dependan de los datos de telemetría.|
|Certificados|No disponible: se requiere conectividad a Internet para los servicios Lista de revocación de certificados (CRL) y Protocolo de estado de certificados en línea (OSCP) en el contexto de HTTPS.|
|Key-Vault|Limitación: un caso de uso común de Key Vault es que una aplicación lea los secretos en el runtime. Para ello, la aplicación necesita a una entidad de servicio en el directorio. En Azure Active Directory, a los usuarios normales (no administradores) se les permite, de manera predeterminada, agregar entidades de servicio. En AD (mediante AD FS) no se les permite. Esto supone un obstáculo para la experiencia global, porque para agregar cualquier aplicación siempre es preciso recurrir a un administrador de directorio.| 

## <a name="learn-more"></a>Más información
- Para obtener información acerca de los casos de uso, las compras, los asociados y los distribuidores de hardware de OEM, consulte la página del producto de [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Para información sobre el mapa de ruta y la disponibilidad geográfica de los sistemas integrados de Azure Stack, consulte las notas del producto: [Azure Stack: una extensión de Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Para más información acerca de los paquetes y precios de Microsoft Azure Stack [descargue el archivo .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Pasos siguientes
[Integración de la red del centro de datos](azure-stack-network.md)
