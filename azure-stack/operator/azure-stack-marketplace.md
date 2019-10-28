---
title: Introducción a Azure Stack Marketplace | Microsoft Docs
description: Información general de Azure Stack Marketplace y elementos de Marketplace.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: da8fdf20446e5ed60271e236f44939d439118b4b
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534098"
---
# <a name="azure-stack-marketplace-overview"></a>Información general de Azure Stack Marketplace

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Azure Stack Marketplace es una colección de servicios, aplicaciones y recursos personalizados para Azure Stack. Los recursos incluyen redes, máquinas virtuales (VM), almacenamiento, etc. Use Azure Stack Marketplace para crear nuevos recursos e implementar nuevas aplicaciones; o busque y seleccione los elementos que desee utilizar. Para utilizar un elemento de Marketplace, los usuarios deben suscribirse a una oferta que les conceda acceso al elemento.

Como operador de Azure Stack, usted decide qué elementos quiere agregar (publicar) en Azure Stack Marketplace. Puede publicar elementos como bases de datos, App Services, etc. Esto hace que sean visibles para todos los usuarios. Puede publicar elementos personalizados creados por usted o elementos de una creciente [lista de elementos de Azure Marketplace](azure-stack-marketplace-azure-items.md). Al publicar un elemento en Azure Stack Marketplace, los usuarios pueden verlo en un plazo de cinco minutos.

> [!CAUTION]  
> Todos los artefactos de los elementos de la galería, como son las imágenes y los archivos JSON, son accesibles sin autenticación después de publicarlos en Azure Stack Marketplace. Consulte otros aspectos que se deben tener en cuenta al publicar elementos en Marketplace en [Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Para abrir Marketplace, en el portal de administrador, seleccione **+ Crear un recurso**.

![Crear un recurso en el portal de administración de Azure Stack](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Elementos de Marketplace

Un elemento de Azure Stack Marketplace es un servicio, una aplicación o un recurso que los usuarios pueden descargar y usar. Todos los elementos de Marketplace para Azure Stack son visibles para todos los usuarios, incluidos los elementos administrativos como planes y ofertas. Estos elementos administrativos no requieren ninguna suscripción para su visualización, pero no son funcionales para los usuarios.

Cada elemento de Marketplace tiene:

* Una plantilla Azure Resource Manager para el aprovisionamiento de recursos.
* Metadatos, como cadenas, iconos y otra documentación y material adjunto de marketing.
* Información de formato para mostrar el elemento en el portal.

Cada elemento publicado en Azure Stack Marketplace usa el formato de paquete de galería de Azure (.azpkg). Agregue recursos de implementación o de entorno en tiempo de ejecución (por ejemplo, código, archivos comprimidos con software o imágenes de VM) a Azure Stack por separado, no como parte del elemento de Marketplace.

En la versión 1803 y versiones posteriores, Azure Stack convierte las imágenes en archivos dispersos cuando se descargan de Azure o al cargar imágenes personalizadas. Este proceso supone más tiempo a la hora de agregar una imagen, pero ahorra espacio y acelera la implementación de esas imágenes. La conversión solo se aplica a las nuevas imágenes. Las imágenes existentes no cambian.

## <a name="next-steps"></a>Pasos siguientes

* [Descarga de elementos de Marketplace existentes desde Azure y publicación en Azure Stack](azure-stack-download-azure-marketplace-item.md)  
* [Creación y publicación de un elemento personalizado de Marketplace de Azure Stack](azure-stack-create-and-publish-marketplace-item.md)
