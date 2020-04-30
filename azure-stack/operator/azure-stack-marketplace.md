---
title: Introducción al Marketplace de Azure Stack Hub
description: Una introducción al Marketplace de Azure Stack Hub y a los elementos de Marketplace.
author: sethmanheim
ms.topic: article
ms.date: 04/20/2020
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 638ffe45cef9301059fd9e9e39b74b793e2ba86a
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661341"
---
# <a name="azure-stack-hub-marketplace-overview"></a>Introducción al Marketplace de Azure Stack Hub

Marketplace de Azure Stack Hub es una colección de servicios, aplicaciones y recursos personalizados para Azure Stack Hub. Los recursos incluyen redes, máquinas virtuales (VM), almacenamiento, etc. Use Marketplace de Azure Stack Hub para crear nuevos recursos e implementar nuevas aplicaciones; o busque y seleccione los elementos que desee utilizar. Para utilizar un elemento de Marketplace, los usuarios deben suscribirse a una oferta que les conceda acceso al elemento.

Como operador de Azure Stack Hub, usted decide qué elementos quiere agregar (publicar) en Marketplace de Azure Stack Hub. Puede publicar elementos como bases de datos, App Services, etc. Esto hace que sean visibles para todos los usuarios. Puede publicar elementos personalizados creados por usted o elementos de una creciente [lista de elementos de Azure Marketplace](azure-stack-marketplace-azure-items.md). Cuando publica un elemento en Marketplace de Azure Stack Hub, los usuarios pueden verlo en menos de cinco minutos.

> [!CAUTION]  
> Todos los artefactos de los elementos de la galería, como son las imágenes y los archivos JSON, son accesibles sin autenticación después de publicarlos en Marketplace de Azure Stack Hub. Consulte otros aspectos que se deben tener en cuenta al publicar elementos en Marketplace en [Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Para abrir Marketplace, en el portal de administrador, seleccione **+ Crear un recurso**.

![Creación de un recurso en el portal de administración de Azure Stack Hub](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Elementos de Marketplace

Un elemento de Marketplace de Azure Stack Hub es un servicio, una aplicación o un recurso que los usuarios pueden descargar y usar. Todos los elementos de Marketplace de Azure Stack Hub son visibles para todos los usuarios, incluidos los elementos administrativos como planes y ofertas. Estos elementos administrativos no requieren ninguna suscripción para su visualización, pero no son funcionales para los usuarios.

Cada elemento de Marketplace tiene:

* Una plantilla Azure Resource Manager para el aprovisionamiento de recursos.
* Metadatos, como cadenas, iconos y otra documentación y material adjunto de marketing.
* Información de formato para mostrar el elemento en el portal.

Cada elemento publicado en Marketplace de Azure Stack Hub usa el formato de paquete de galería de Azure (.azpkg). Agregue recursos de implementación o de entorno en tiempo de ejecución (por ejemplo, código, archivos comprimidos con software o imágenes de VM) a Azure Stack Hub por separado, no como parte del elemento de Marketplace.

En la versión 1803 y versiones posteriores, Azure Stack Hub convierte las imágenes en archivos dispersos cuando se descargan de Azure o al cargar imágenes personalizadas. Este proceso supone más tiempo a la hora de agregar una imagen, pero ahorra espacio y acelera la implementación de esas imágenes. La conversión solo se aplica a las nuevas imágenes. Las imágenes existentes no cambian.

## <a name="next-steps"></a>Pasos siguientes

* [Descarga de elementos de Marketplace existentes desde Azure y publicación en Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)  
* [Creación y publicación de un elemento personalizado de Marketplace de Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md)
