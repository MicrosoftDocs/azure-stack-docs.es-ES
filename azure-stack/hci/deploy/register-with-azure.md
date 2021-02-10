---
title: Conexión de Azure Stack HCI a Azure
description: Registro de clústeres de Azure Stack HCI en Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/28/2020
ms.openlocfilehash: 17e8758dfea300f6bc3e02609877dfed8f780383
ms.sourcegitcommit: b461597917b768412036bf852c911aa9871264b2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050032"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Conexión de Azure Stack HCI a Azure

> Se aplica a: Azure Stack HCI, v20H2

Azure Stack HCl se entrega como servicio de Azure y debe registrarse en un plazo de 30 días a partir de la instalación según los Términos de los Servicios en Línea de Azure. En este tema se explica cómo registrar un clúster de Azure Stack HCI en [Azure Arc](https://azure.microsoft.com/services/azure-arc/) para supervisión, soporte técnico, facturación y servicios híbridos. Tras el registro, se crea un recurso de Azure Resource Manager para representar cada clúster local de Azure Stack HCl, lo que extiende de manera eficaz el plano de administración de Azure a Azure Stack HCl. La información se sincroniza periódicamente entre el recurso de Azure y los clústeres locales. El registro en Azure Arc es una funcionalidad nativa del sistema operativo Azure Stack HCI, por lo que no es necesario registrar ningún agente.

   > [!IMPORTANT]
   > Es necesario registrarse en Azure y el clúster no es totalmente compatible hasta que el registro esté activo. Si no registra el clúster en Azure tras la implementación, o si el clúster está registrado pero no se ha conectado a Azure durante más de 30 días, el sistema no permitirá que se creen o agreguen nuevas máquinas virtuales. Cuando esto ocurra, recibirá el siguiente error al intentar crear máquinas virtuales:
   >
   > *Error al configurar el rol de máquina virtual para "vmname". error del trabajo. Error al abrir los roles en clúster de "vmname". El servicio al que se está accediendo tiene una licencia para un número determinado de conexiones. No se pueden realizar más conexiones al servicio en este momento porque ya se ha aceptado el número máximo de conexiones.*
   >
   > La solución consiste en permitir la conectividad de salida a Azure y asegurarse de que el clúster esté registrado tal y como se describe en este tema.

## <a name="prerequisites-for-registration"></a>Requisitos previos para el registro

No podrá registrarse en Azure hasta que no haya creado un clúster de Azure Stack HCI. Para que el clúster sea compatible, los nodos de clúster deben ser servidores físicos. Se pueden usar máquinas virtuales para las pruebas, pero estas deben ser compatibles con Unified Extensible Firmware Interface (UEFI); es decir, no puede usar máquinas virtuales Hyper-V de la generación 1.

Para obtener la experiencia de registro más sencilla, haga que un administrador de Azure AD complete el registro mediante Windows Admin Center o PowerShell.

   > [!IMPORTANT]
   > Si tiene previsto registrar el clúster mediante Windows Admin Center, primero debe [registrar la puerta de enlace de Windows Admin Center](../manage/register-windows-admin-center.md) con Azure usando el mismo identificador de suscripción de Azure y el mismo identificador de inquilino que piensa usar para el registro del clúster.

### <a name="internet-access"></a>Acceso a Internet

Azure Stack HCI necesita conectarse periódicamente a la nube pública de Azure. Si la conectividad saliente está restringida por el firewall corporativo externo o un servidor proxy, estos deben configurarse para permitir el acceso saliente al puerto 443 (HTTPS) en un número limitado de direcciones IP conocidas de Azure. Para obtener información sobre cómo preparar los firewalls, consulte [Configuración de firewalls para Azure Stack HCI](../concepts/configure-firewalls.md).

   > [!NOTE]
   > El proceso de registro intenta ponerse en contacto con la Galería de PowerShell para comprobar que tiene la versión más reciente de los módulos de PowerShell necesarios, como Az y AzureAD. Aunque la Galería de PowerShell se hospeda en Azure, actualmente no tiene una etiqueta de servicio. Si no puede ejecutar el cmdlet anterior desde una máquina de administración con acceso saliente a Internet, se recomienda descargar los módulos y transferirlos manualmente a un nodo de clúster en el que se ejecutará el comando `Register-AzStackHCI`. Como alternativa, puede [instalar los módulos en un escenario sin conexión](/powershell/scripting/gallery/how-to/working-with-local-psrepositories?view=powershell-7.1#installing-powershellget-on-a-disconnected-system).

### <a name="azure-subscription-and-permissions"></a>Suscripción a Azure y permisos

Si aún no tiene una cuenta de Azure, [cree una](https://azure.microsoft.com/).

Puede usar una suscripción existente de cualquier tipo:
- Cuenta gratuita con créditos de Azure [para alumnos](https://azure.microsoft.com/free/students/) o [suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- Suscripción de [pago por uso](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) con tarjeta de crédito
- Suscripción obtenida a través de un Contrato Enterprise (EA)
- Suscripción obtenida a través del programa Proveedor de soluciones en la nube (CSP)

El usuario que registra el clúster debe tener permisos de suscripción de Azure para:

- Registrar un proveedor de recursos
- Crear/Obtener/Eliminar recursos y grupos de recursos de Azure

Si su suscripción a Azure es a través de un Contrato Enterprise o un Proveedor de servicios en la nube, la manera más fácil es pedir al administrador de la suscripción de Azure que asigne un rol integrado de "Propietario" o "Colaborador" de Azure a su suscripción. Sin embargo, algunos administradores pueden preferir una opción más restrictiva. En este caso, es posible crear un rol personalizado de Azure específico para el registro de Azure Stack HCI siguiendo estos pasos:

1. Cree un archivo json llamado **customHCIRole.json** con el siguiente contenido. Asegúrese de que cambia <subscriptionID> por el identificador de su suscripción de Azure. Para obtener el identificador de la suscripción, visite [portal.azure.com](https://portal.azure.com), vaya a Suscripciones y copie y pegue el identificador de la lista.

   ```json
   {
     "Name": "Azure Stack HCI registration role”,
     "Id": null,
     "IsCustom": true,
     "Description": "Custom Azure role to allow subscription-level access to register Azure Stack HCI",
     "Actions": [
       "Microsoft.Resources/subscriptions/resourceGroups/write",
       "Microsoft.Resources/subscriptions/resourceGroups/read",
       "Microsoft.Resources/subscriptions/resourceGroups/delete",
       "Microsoft.AzureStackHCI/register/action",
       "Microsoft.AzureStackHCI/Unregister/Action",
       "Microsoft.AzureStackHCI/clusters/*"
     ],
     "NotActions": [
     ],
   "AssignableScopes": [
       "/subscriptions/<subscriptionId>"
     ]
   }
   ```

2. Cree el rol personalizado:

   ```powershell
   New-AzRoleDefinition -InputFile <path to customHCIRole.json>
   ```

3. Asigne el rol personalizado al usuario:

   ```powershell
   $user = get-AzAdUser -DisplayName <userdisplayname>
   $role = Get-AzRoleDefinition -Name "Azure Stack HCI registration role"
   New-AzRoleAssignment -ObjectId $user.Id -RoleDefinitionId $role.Id -Scope /subscriptions/<subscriptionid>
   ```

### <a name="azure-active-directory-permissions"></a>Permisos de Azure Active Directory

También necesitará permisos adecuados de Azure Active Directory para completar el proceso de registro. Si aún no los tiene, pida a su administrador de Azure AD que le conceda el consentimiento o que le delegue los permisos. Para obtener más información, consulte [Administración de registros en Azure](../manage/manage-azure-registration.md#azure-active-directory-app-permissions).

## <a name="register-a-cluster-using-windows-admin-center"></a>Registro de un clúster mediante Windows Admin Center

La manera más sencilla de registrar el clúster de Azure Stack HCI es usar Windows Admin Center. Recuerde que el usuario debe tener [permisos de Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-app-permissions), o el proceso de registro no se completará y, en vez de eso, se cerrará y dejará el registro pendiente de la aprobación del administrador.

1. Antes de comenzar el proceso de registro, primero debe [registrar la puerta de enlace de Windows Admin Center](../manage/register-windows-admin-center.md) con Azure, si aún no lo ha hecho.

   > [!IMPORTANT]
   > Al registrar Windows Admin Center con Azure, es importante usar el mismo identificador de suscripción de Azure y el mismo identificador de inquilino que piensa usar para el registro de clúster real. Un identificador de inquilino de Azure AD representa una instancia específica de Azure AD que contiene cuentas y grupos, mientras que un identificador de suscripción de Azure representa un acuerdo para usar recursos de Azure en los cuales se generan cargos. Para buscar el identificador de inquilino, visite [portal.azure.com](https://portal.azure.com) y seleccione **Azure Active Directory**. El identificador de inquilino se mostrará en **Información del inquilino**. Para obtener el identificador de la suscripción de Azure, vaya a **Suscripciones** y copie y pegue el identificador de la lista.

2. Abra Windows Admin Center y seleccione **Configuración** en la parte inferior del menú **Herramientas** de la izquierda. A continuación, seleccione **Registro de Azure Stack HCI** en la parte inferior del menú **Configuración**. Si el clúster todavía no se ha registrado con Azure, el **estado del registro** será **No registrado**. Haga clic en el botón **Registrar** para continuar.

3. Para completar el proceso de registro, debe autenticarse (iniciar sesión) con su cuenta de Azure. La cuenta debe tener acceso a la suscripción de Azure que se especificó al registrar la puerta de enlace de Windows Admin Center para que se realice el registro. Copie el código proporcionado, vaya a microsoft.com/devicelogin en otro dispositivo (como su PC o teléfono), escriba el código e inicie sesión allí. El flujo de trabajo de registro detectará cuando haya iniciado sesión y continuará con el proceso de finalización. A partir de entonces, debería poder ver el clúster en Azure Portal.

## <a name="register-a-cluster-using-powershell"></a>Registro de un clúster con PowerShell

Use el siguiente procedimiento para registrar un clúster de Azure Stack HCI con Azure mediante un equipo de administración.

1. Instale los cmdlets necesarios en el equipo de administración. Si va a registrar un clúster implementado a partir de la imagen de disponibilidad general (GA) actual de Azure Stack HCI, solo tiene que ejecutar el siguiente comando. Si el clúster se implementó a partir de la versión preliminar pública, asegúrese de que ha aplicado la actualización de la versión preliminar del 23 de noviembre de 2020 (KB4586852) en cada servidor del clúster antes de intentar registrarse en Azure.

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Es posible que vea un mensaje parecido al siguiente: **¿Quiere que PowerShellGet se instale e importe el proveedor de NuGet ahora?** al cual debe responder **Sí** (S).
   > - Si se le pregunta de nuevo **¿Seguro que quiere instalar los módulos de "PSGallery"?** responda **Sí** (S).

2. Realice el registro con el nombre de cualquier servidor del clúster. Para obtener el identificador de la suscripción de Azure, visite [portal.azure.com](https://portal.azure.com), vaya a **Suscripciones** y copie y pegue el identificador de la lista.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1
   ```

   Esta sintaxis registra el clúster (del que es miembro Server1), como el usuario actual, con la región de Azure y el entorno de nube predeterminados, y mediante nombres predeterminados inteligentes para el recurso y el grupo de recursos de Azure. También puede agregar los parámetros `-Region`, `-ResourceName` y `-ResourceGroupName` opcionales a este comando para especificar estos valores.

   Recuerde que el usuario que ejecuta el cmdlet `Register-AzStackHCI` debe tener [permisos de Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-app-permissions), o el proceso de registro no se completará y, en vez de eso, se cerrará y dejará el registro pendiente de la aprobación del administrador. Una vez que se hayan concedido los permisos, solo tiene que volver a ejecutar `Register-AzStackHCI` para completar el registro.

3. Autenticación con Azure

   Para completar el proceso de registro, debe autenticarse (iniciar sesión) con su cuenta de Azure. La cuenta debe tener acceso a la suscripción de Azure que se especificó en el paso 2 anterior para que se realice el registro. Copie el código proporcionado, vaya a microsoft.com/devicelogin en otro dispositivo (como su PC o teléfono), escriba el código e inicie sesión allí. El flujo de trabajo de registro detectará cuando haya iniciado sesión y continuará con el proceso de finalización. A partir de entonces, debería poder ver el clúster en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

Ahora ya está listo para:

- [Validación del clúster](validate.md)

