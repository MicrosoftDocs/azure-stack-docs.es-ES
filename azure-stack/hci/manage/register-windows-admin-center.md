---
title: Registro de Windows Admin Center con Azure
description: Registro de la puerta de enlace de Windows Admin Center con Azure
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: d98cb4f0531903d27d97d9fb055c7ae2db35a65e
ms.sourcegitcommit: b461597917b768412036bf852c911aa9871264b2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050066"
---
# <a name="register-windows-admin-center-with-azure"></a>Registro de Windows Admin Center con Azure

> Se aplica a Azure Stack HCI v20H2; Windows Server 2019

Para usar los servicios de Azure con Windows Admin Center, primero debe instalar Windows Admin Center en un equipo de administración y completar un registro único de la puerta de enlace de Windows Admin Center. Se trata de un requisito previo para registrar el clúster con Azure y debe realizarse en el mismo equipo de administración que tenga previsto usar para completar el proceso de [registro del clúster](../deploy/register-with-azure.md) mediante el mismo identificador de suscripción y de inquilino de Azure.

## <a name="complete-the-gateway-registration-process-using-windows-admin-center"></a>Completar el proceso de registro de puerta de enlace con Windows Admin Center

1. Inicie Windows Admin Center y haga clic en el icono de engranaje **Configuración** de la parte superior derecha, que le llevará a la página de la cuenta. A continuación, desde el menú **Puerta de enlace** de la izquierda, seleccione **Azure** y haga clic en **Registrar**.

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="Seleccione Configuración > Puerta de enlace > Azure y haga clic en Registrar." lightbox="media/register-wac/register-wac.png":::

2. Se le proporcionará un código único. Haga clic en el botón **Copiar** situado a la derecha del código. Haga clic en **Escribir el código**, que abrirá otra ventana del explorador en la que puede pegar el código que se muestra en la aplicación o el dispositivo.

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="Copie el código único, haga clic en Escribir el código y péguelo en el cuadro de diálogo." lightbox="media/register-wac/enter-code.png":::

3. Después de pegar el código, se le notificará que está a punto de iniciar sesión en Windows Admin Center en un servicio o dispositivo remoto. Escriba su dirección de correo electrónico o número de teléfono. Si el dispositivo se administra, se le dirigirá a la página de inicio de sesión de la organización para la autenticación. Siga las instrucciones y escriba las credenciales adecuadas.

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="Iniciar sesión en Windows Admin Center con el correo electrónico o el número de teléfono" lightbox="media/register-wac/sign-in.png":::

   Debería ver el mensaje siguiente: "Ha iniciado sesión en la aplicación de Windows Admin Center en el dispositivo". Cierre la ventana del explorador para volver a la página de registro original.

4. Conéctese a Azure Active Directory. Para hacerlo, proporcione el identificador de Azure Active Directory (inquilino). Si ya tiene un identificador de inquilino de Azure y ha seguido los pasos anteriores, el campo ID puede aparecer rellenado previamente. Deje el campo **Aplicación de Azure Active Directory** definido como **Crear nuevo** si su organización no le proporcionó un identificador existente. Si ya tiene un identificador, haga clic en **Usar existente** y aparecerá un campo vacío para que escriba el identificador que le ha proporcionado el administrador. Después de escribir el identificador, Windows Admin Center confirmará que se ha encontrado una cuenta con ese valor. Si tiene un identificador existente, pero no sabe qué es, siga [estos pasos](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) para recuperarlo.

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="Conéctese a Azure Active Directory. Para hacerlo, proporcione el identificador de Azure Active Directory (inquilino) o cree uno nuevo." lightbox="media/register-wac/connect-to-aad.png":::

5. Haga clic en el botón **Conectar** para conectarse a Azure. Debería ver una confirmación de que ya está conectado a Azure AD.

6. Para conceder permisos en Azure, vaya a **Permisos de aplicación** en Azure Portal. En **Otorgar consentimiento**, seleccione **Conceder consentimiento de administrador**.

7. Cierre la ventana e inicie sesión en Windows Admin Center con la cuenta de Azure.

## <a name="next-steps"></a>Pasos siguientes

Ahora ya está listo para:

- [Conexión de Azure Stack HCl a Azure](../deploy/register-with-azure.md)
- [Uso de Azure Stack HCl con Windows Admin Center](../get-started.md)
- [Conexión a servicios híbridos de Azure](/windows-server/manage/windows-admin-center/azure/)