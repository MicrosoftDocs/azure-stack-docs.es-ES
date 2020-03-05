---
title: Administración de regiones en Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Introducción a la administración de regiones en Azure Stack Hub
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: 69cdd8a672cd3431906d949c1a038cc1dbdb9641
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698018"
---
# <a name="region-management-in-azure-stack-hub"></a>Administración de regiones en Azure Stack Hub

Azure Stack Hub usa el concepto de *regiones*, que son entidades lógicas que constan de los recursos de hardware que conforman la infraestructura de Azure Stack Hub. En la administración de regiones, puede buscar todos los recursos necesarios para usar correctamente la infraestructura de Azure Stack Hub.

Una implementación del sistema integrado (lo que se conoce como una *nube de Azure Stack Hub*) constituye una región única. Cada Kit de desarrollo de Azure Stack (ASDK) tiene una región, llamada **local**. Si implementa un segundo sistema integrado de Azure Stack Hub o configura otra instancia del ASDK en otro hardware, esta nube de Azure Stack Hub será una región diferente.

## <a name="information-available-through-the-region-management-tile"></a>Información disponible a través del icono Administración de regiones

Azure Stack Hub incluye un conjunto de funcionalidades de administración de regiones disponibles en el icono **Region management** (Administración de regiones). Este icono está disponible para los operadores de Azure Stack Hub en el panel predeterminado del portal del administrador. Mediante él, puede supervisar y actualizar la región de Azure Stack Hub y sus componentes, que son específicos de cada región.

![Icono de administración de regiones en el portal del administrador de Azure Stack Hub](media/azure-stack-region-management/image1.png)

Si selecciona una región en el icono **Region Management** (Administración de regiones), puede acceder a la siguiente información:

[![Descripción de los paneles de la hoja de administración de regiones del portal del administrador de Azure Stack Hub](media/azure-stack-region-management/regionssm.png "Hoja de administración de regiones del portal del administrador de Azure Stack Hub")](media/azure-stack-region-management/regions.png#lightbox)

1. **Menú de recursos**: Acceda a áreas de administración de infraestructura diferentes y vea y administre recursos de usuario, como cuentas de almacenamiento y redes virtuales.

2. **Alertas**: Muestra las alertas de todo el sistema y proporciona detalles sobre cada una.

3. **Actualizaciones**: Consulte la versión actual de la infraestructura de Azure Stack Hub, las actualizaciones disponibles y el historial de actualizaciones. También puede actualizar el sistema integrado.

4. **Proveedores de recursos**: administran la funcionalidad de los usuarios que se ofrece mediante los componentes necesarios para ejecutar Azure Stack Hub. Cada proveedor de recursos viene con una experiencia administrativa. Esta experiencia puede incluir las alertas para el proveedor específico, métricas y otras funcionalidades de administración concretas para el proveedor de recursos.

5. **Roles de infraestructura**: los componentes necesarios para ejecutar Azure Stack Hub. Se enumeran solo los roles de infraestructura que notifican alertas. Si selecciona un rol, puede ver las alertas asociadas a él y las instancias de rol en que se ejecuta.

6. **Propiedades**: El estado de registro y los detalles de su entorno en la hoja de administración de la región. El estado puede ser **Registered** (Registrado), **Not registered** (No registrado) o **Expired** (Expirado). Si el estado es registrado, también se muestra el identificador de la suscripción de Azure que usó para registrar Azure Stack Hub, junto con el grupo de recursos de registro y el nombre.

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión de estado y alertas en Azure Stack Hub](azure-stack-monitor-health.md)
- [Administrar las actualizaciones en Azure Stack Hub](azure-stack-updates.md)
