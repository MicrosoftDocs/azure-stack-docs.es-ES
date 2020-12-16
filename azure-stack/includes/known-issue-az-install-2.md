---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: e456f17c75b129a3f45c64b9b55e75d21ecb2973
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935162"
---
### <a name="when-installing-az-module-falsely-throws-admin-rights-required-error"></a>Cuando la instalación del módulo Az produce de forma incorrecta un error que indica que se requieren derechos de administrador

- Aplicable a: Este problema se aplica a la versión 2002 y versiones posteriores.
- Causa: Al instalar el módulo desde un símbolo del sistema con privilegios elevados, se produce un error. El error indica: `Administrator rights required`.
- Corrección: Cierre la sesión e inicie una nueva sesión de PowerShell con privilegios elevados. Asegúrese de que no hay ningún módulo Az existente. Se ha cargado el módulo de cuentas en la sesión.
- Repetición: Comunes