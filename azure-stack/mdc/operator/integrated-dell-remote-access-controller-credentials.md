---
title: 'Credenciales del controlador integrado de acceso remoto de Dell: MDC'
description: Obtenga información sobre cómo actualizar las credenciales del controlador integrado de acceso remoto de Dell (iDRAC) en un Centro de datos modular (MDC).
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/27/2020
ms.reviewer: justinha
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: d0d2f522038d0788d4942db68b36e5bb21884619
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910744"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller---modular-data-center-mdc"></a>Actualización de credenciales para el controlador integrado de acceso remoto de Dell: Centro de datos modular (MDC)

En esta sección se describe cómo cambiar las credenciales del controlador integrado de acceso remoto de Dell (iDRAC). 

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar el procedimiento: 

- Use el Escritorio remoto para conectarse a la máquina virtual de administración. 
- Asegúrese de que tiene las nuevas credenciales para la cuenta o las cuentas. 
 
## <a name="update-the-idrac-credentials"></a>Actualización de las credenciales de iDRAC

Para actualizar las credenciales de iDRAC para todos los servidores PowerEdge (HLH y nodos de unidad de escalado) del entorno:

1. En un explorador web, inicie sesión en https://<iDRAC_IP>. 
1. Vaya a **iDRAC Settings** > **Users** (Configuración de iDRAC > Usuarios). 
1. Seleccione el usuario que desea modificar y, a continuación, haga clic en **Edit** (Editar). 
1. En la ventana **Edit User** (Editar usuario), escriba la nueva contraseña en **Password** (Contraseña) y **Confirm Password** (Confirmar contraseña), tal como se muestra en la ilustración siguiente: 

   ![Captura de pantalla que muestra información del usuario](../operator/media/idrac-credentials/enter-user.png)

1. Haga clic en **Save** (Guardar) y luego en **OK** (Aceptar). 

## <a name="next-steps"></a>Pasos siguientes

[Cambio de secretos en Azure Stack Hub](../../operator/azure-stack-rotate-secrets.md)