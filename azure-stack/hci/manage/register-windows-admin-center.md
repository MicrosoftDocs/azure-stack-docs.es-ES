---
title: Registro de Windows Admin Center con Azure
description: Registro de Windows Admin Center con Azure
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: ee6794c0798bc388bc3d9313665eb0b7917cf8eb
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868098"
---
# <a name="register-windows-admin-center-with-azure"></a>Registro de Windows Admin Center con Azure

> Se aplica a Azure Stack HCI v20H2; Windows Server 2019

Para usar los servicios de Azure con Windows Admin Center, primero debe instalar Windows Admin Center en un equipo de administración y completar un registro único.

## <a name="complete-the-registration-process-in-windows-admin-center"></a>Completar el proceso de registro en Windows Admin Center

1. Inicie Windows Admin Center y haga clic en el icono de engranaje **Configuración** de la parte superior derecha, que le llevará a la página de la cuenta. A continuación, desde el menú **Puerta de enlace** de la izquierda, seleccione **Azure** y haga clic en **Registrar**.
1. Se le proporcionará un código único. Haga clic en el botón **Copiar** situado a la derecha del código.
1. Haga clic en **Escribir el código**, que abrirá otra ventana del explorador en la que puede pegar el código que se muestra en la aplicación o el dispositivo.
1. Después de pegar el código, se le notificará que está a punto de iniciar sesión en Windows Admin Center en un servicio o dispositivo remoto. 
1. Escriba su dirección de correo electrónico o número de teléfono. Si el dispositivo se administra, se le dirigirá a la página de inicio de sesión de la organización para la autenticación. Siga las instrucciones y escriba las credenciales adecuadas.
1. Debería ver el mensaje siguiente: "Ha iniciado sesión en la aplicación de Windows Admin Center en el dispositivo". Cierre la ventana del explorador para volver a la página de registro original.
1. Conéctese a Azure Active Directory. Para hacerlo, proporcione el identificador de Azure Active Directory (inquilino). Si siguió los pasos anteriores, el campo del identificador estará ya rellenado. Deje el campo **Aplicación de Azure Active Directory** definido como **Crear nuevo** si su organización no le proporcionó un identificador existente. Si ya tiene un identificador, haga clic en **Usar existente** y aparecerá un campo vacío para que escriba el identificador que le ha proporcionado el administrador. Después de escribir el identificador, Windows Admin Center confirmará que se ha encontrado una cuenta con ese valor. Si tiene un identificador existente, pero no sabe qué es, siga [estos pasos](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) para recuperarlo.
1. Haga clic en el botón **Conectar** para conectarse a Azure. Debería ver una confirmación de que ya está conectado a Azure AD.
1. Para conceder permisos en Azure, vaya a **Permisos de aplicación** en Azure Portal. En **Otorgar consentimiento**, seleccione **Conceder consentimiento de administrador**.
1. Cierre la ventana e inicie sesión en Windows Admin Center con la cuenta de Azure.

## <a name="next-steps"></a>Pasos siguientes

Ya está listo para los pasos siguientes:

- [Uso de Azure Stack HCl con Windows Admin Center](../get-started.md)
- [Conexión a servicios híbridos de Azure](/windows-server/manage/windows-admin-center/azure/)