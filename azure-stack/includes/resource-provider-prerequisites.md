---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a980becc3bf749987759f90a9ab84050d033a04
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2020
ms.locfileid: "92297856"
---
Si ya ha instalado un proveedor de recursos, es probable que haya completado los siguientes requisitos previos y pueda omitir esta sección. De lo contrario, realice estos pasos antes de continuar: 

1. [Registre la instancia de Azure Stack Hub en Azure](../operator/azure-stack-registration.md), si no lo ha hecho. Este paso es necesario, ya que se va a conectar a Marketplace y a descargar ahí elementos desde Azure.

2. Si no está familiarizado con la característica **Marketplace Management** del portal de administrador de Azure Stack Hub, consulte [Descarga de elementos de Marketplace de Azure y publicación en Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md). El artículo le guía por el proceso de descarga de elementos de Azure en Marketplace de Azure Stack Hub. Abarca escenarios conectados y desconectados. Si la instancia de Azure Stack Hub está desconectada o parcialmente conectada, existen requisitos previos adicionales que hay que cumplir para completar la instalación.

3. Actualice el directorio principal de su instancia de Azure Active Directory (Azure AD). A partir de la compilación 1910, se debe registrar una nueva aplicación en el inquilino del directorio principal. Esta aplicación habilitará Azure Stack Hub para crear y registrar correctamente proveedores de recursos más recientes (como Event Hubs, IoT Hub y otros) con su inquilino de Azure AD. Se trata de una acción única que debe realizarse después de actualizar a la compilación 1910 o posterior. Si no se realiza este paso, se producirá un error en las instalaciones del proveedor de recursos de marketplace. 

   - Después de actualizar correctamente la instancia de Azure Stack Hub a 1910 o superior, siga las [instrucciones para clonar o descargar el repositorio de herramientas de Azure Stack Hub](../operator/azure-stack-powershell-download.md). 
   - A continuación, siga las instrucciones para [actualizar el directorio principal de AD de Azure Stack Hub (después de instalar actualizaciones o nuevos proveedores de recursos)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 