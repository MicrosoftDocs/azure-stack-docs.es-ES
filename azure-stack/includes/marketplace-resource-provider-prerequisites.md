---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e1e2c3c6c3d1149b2cd034c2f5e4abfdf26e712f
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424611"
---
Si ya ha instalado un proveedor de recursos desde Marketplace de Azure Stack Hub, es probable que haya completado los requisitos previos comunes y pueda omitir esta sección. De lo contrario, complete primero los siguientes requisitos previos: 

1. [Registre la instancia de Azure Stack Hub en Azure](../operator/azure-stack-registration.md), si no lo ha hecho. Este paso es necesario, ya que se va a conectar a Marketplace y a descargar ahí elementos desde Azure.

2. Si no está familiarizado con la característica **Marketplace Management** del portal de administrador de Azure Stack Hub, consulte [Descarga de elementos de Marketplace de Azure y publicación en Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md). El artículo le guía por el proceso de descarga de elementos de Azure en Marketplace de Azure Stack Hub. Abarca escenarios conectados y desconectados. Si la instancia de Azure Stack Hub está desconectada o parcialmente conectada, existen requisitos previos adicionales que hay que cumplir para completar la instalación.

3. Actualice el directorio principal de su instancia de Azure Active Directory (Azure AD). A partir de la compilación 1910, se debe usar la nueva aplicación del proveedor de recursos de implementación (DRP) para registrar el inquilino del directorio principal. Esta aplicación permitirá que DRP cree y registre correctamente los proveedores de recursos. Si no se realiza este paso, se producirá un error en la instalación del proveedor de recursos. 

   - Después de actualizar correctamente la instancia de Azure Stack Hub a 1910 o superior, siga las [instrucciones para clonar o descargar el repositorio de herramientas de Azure Stack Hub](../operator/azure-stack-powershell-download.md). 
   - A continuación, siga las instrucciones para [actualizar el directorio principal de AD de Azure Stack Hub (después de instalar actualizaciones o nuevos proveedores de recursos)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 