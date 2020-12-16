---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: 003f6801209d5d5ab1c9c4bd1df0fa47578004ee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935175"
---
### <a name="error-thrown-when-installing-the-az-modules"></a>Error al instalar los módulos Az

- Aplicable a: Este problema se aplica a la versión 2002 y versiones posteriores.
- Causa: Al instalar el módulo, se produce un error. El mensaje de error dice: `Register-PacakgeSource : A parameter cannot be found that matches parameter name. 'PackageManagementProvider'.` O bien, puede que incluya el texto siguiente: `PackageManagement\Install-Package : Cannot convert value "2.0.1-preview" to type "System.Version". Error: "Input string was not in a correct format."`
- Corrección: En la misma sesión, ejecute el siguiente cmdlet:  
    `Install-Module PowershellGet -MinimumumVersion 2.3.0 -Force`  
Cierre la sesión e inicie una nueva sesión de PowerShell con privilegios elevados.
- Repetición: Comunes