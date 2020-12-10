---
title: Credenciales del controlador integrado de acceso remoto de Dell
description: Se explica cómo actualizar las credenciales para el controlador integrado de acceso remoto de Dell.
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 04/28/2020
ms.reviewer: justinha
ms.lastreviewed: 04/28/2020
ms.openlocfilehash: 2c6e7f0aa0e6e03af061083259234e49275a83de
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941354"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller"></a>Actualización de credenciales para el controlador integrado de acceso remoto de Dell

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