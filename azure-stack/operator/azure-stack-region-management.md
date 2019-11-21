---
title: Administración de regiones en Azure Stack | Microsoft Docs
titleSuffix: Azure Stack
description: Introducción a la administración de regiones en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: 38004b88f43ef59448ca99c3eb2762e5ca63e89c
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802275"
---
# <a name="region-management-in-azure-stack"></a>Administración de regiones en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Azure Stack usa el concepto de *regiones*, entidades lógicas que constan de los recursos de hardware que conforman la infraestructura de Azure Stack. En la administración de regiones, puede buscar todos los recursos necesarios para usar correctamente la infraestructura de Azure Stack.

Una implementación de sistema integrada (lo que se conoce como una *nube de Azure Stack*) constituye una región única. Cada Kit de desarrollo de Azure Stack (ASDK) tiene una región, llamada **local**. Si implementa un segundo sistema integrado de Azure Stack o configura otra instancia de ASDK en otro hardware, esta nube de Azure Stack será una región diferente.

## <a name="information-available-through-the-region-management-tile"></a>Información disponible a través del icono Administración de regiones

Azure Stack tiene un conjunto de funciones de administración de regiones disponibles en la ventana **Region management** (Administración de regiones). Este icono está disponible para un nuevo operador de Azure Stack en el panel predeterminado del portal de administrador. A través de este icono, puede supervisar y actualizar su región de Azure Stack y sus componentes, que son específicos de cada región.

![Icono Administración de regiones en el portal de administración de Azure Stack](media/azure-stack-region-management/image1.png)

Si hace clic en una región en el icono **Region Management** (Administración de regiones), puede tener acceso a la siguiente información:

[![Descripción de paneles de la hoja Administración de regiones en el portal de administración de Azure Stack](media/azure-stack-region-management/regionssm.png "Hoja Administración de regiones en el portal de administración de Azure Stack")](media/azure-stack-region-management/regions.png#lightbox)

1. **Menú de recursos**: Acceda a áreas de administración de infraestructura diferentes y vea y administre recursos de usuario, como cuentas de almacenamiento y redes virtuales.

2. **Alertas**: Muestra las alertas de todo el sistema y proporciona detalles sobre cada una.

3. **Actualizaciones**: Visualice la versión actual de la infraestructura de Azure Stack, las actualizaciones disponibles y el historial de actualizaciones. También puede actualizar el sistema integrado.

4. **Proveedores de recursos**: Administre la funcionalidad para los usuarios proporcionada por los componentes necesarios para ejecutar Azure Stack. Cada proveedor de recursos viene con una experiencia administrativa. Esta experiencia puede incluir las alertas para el proveedor específico, métricas y otras funcionalidades de administración concretas para el proveedor de recursos.

5. **Roles de infraestructura**: Componentes necesarios para ejecutar Azure Stack. Se enumeran solo los roles de infraestructura que notifican alertas. Si selecciona un rol, puede ver las alertas asociadas a él y las instancias de rol en que se ejecuta.

6. **Propiedades**: El estado de registro y los detalles de su entorno en la hoja de administración de la región. El estado puede ser **Registered** (Registrado), **Not registered** (No registrado) o **Expired** (Expirado). Si el estado es registrado, también muestra el identificador de la suscripción de Azure que usó para registrar Azure Stack, junto con el grupo de recursos de registro y el nombre.

## <a name="next-steps"></a>Pasos siguientes

- [Supervisar el estado y las alertas en Azure Stack](azure-stack-monitor-health.md)
- [Administrar las actualizaciones en Azure Stack](azure-stack-updates.md)
