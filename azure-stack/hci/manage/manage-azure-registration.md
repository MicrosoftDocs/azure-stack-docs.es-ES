---
title: Administración del registro de Azure para Azure Stack HCl
description: Administración del registro de Azure para Azure Stack HCI, descripción del estado del registro y anulación del registro del clúster cuando esté listo para la retirada.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: a187730ed43c6c4a57bbe2d1f81d39085d8b94a1
ms.sourcegitcommit: b461597917b768412036bf852c911aa9871264b2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050100"
---
# <a name="manage-azure-registration"></a>Administración del registro de Azure

> Se aplica a Azure Stack HCI v20H2

Una vez creado el clúster de Azure Stack HCI, debe [registrar el clúster con Azure Arc](../deploy/register-with-azure.md). Una vez registrado el clúster, se sincroniza periódicamente la información entre el clúster local y la nube. En este tema se explica cómo entender el estado del registro, otorgar permisos de Azure Active Directory y anular el registro del clúster cuando esté listo para la retirada.

## <a name="understanding-registration-status-using-windows-admin-center"></a>Descripción del estado del registro mediante Windows Admin Center

Al conectarse a un clúster mediante Windows Admin Center, aparecerá el panel que muestra el estado de la conexión de Azure. **Conectado** significa que el clúster ya está registrado con Azure y se ha sincronizado correctamente con la nube en el último día.

   :::image type="content" source="media/manage-azure-registration/registration-status.png" alt-text="El panel de Windows Admin Center mostrará siempre el estado de la conexión del clúster" lightbox="media/manage-azure-registration/registration-status.png":::

Para más información, seleccione **Configuración** en la parte inferior del menú **Herramientas** de la izquierda y, a continuación, seleccione **Registro de Azure Stack HCl**.

   :::image type="content" source="media/manage-azure-registration/azure-stack-hci-registration.png" alt-text="Seleccione Configuración > Herramientas > Registro de Azure Stack HCI para más información" lightbox="media/manage-azure-registration/azure-stack-hci-registration.png":::.

## <a name="understanding-registration-status-using-powershell"></a>Descripción del estado del registro mediante PowerShell

Para comprender el estado de registro mediante Windows PowerShell, use el cmdlet de PowerShell `Get-AzureStackHCI` y las propiedades `ClusterStatus`, `RegistrationStatus` y `ConnectionStatus`. Por ejemplo, después de instalar el sistema operativo de Azure Stack HCI, antes de unirse a un clúster o crearlo, la propiedad `ClusterStatus` muestra el estado "todavía no":

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Estado de registro de Azure antes de la creación del clúster":::

Una vez creado el clúster, solo `RegistrationStatus` muestra el estado "todavía no":

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Estado de registro de Azure después de la creación del clúster":::

Azure Stack HCl necesita registrarse en un plazo de 30 días a partir de la instalación según los Términos de los Servicios en Línea de Azure. Si no está agrupado después de 30 días, `ClusterStatus` mostrará `OutOfPolicy` y, si no se registra después de 30 días, `RegistrationStatus` mostrará `OutOfPolicy`.

Una vez registrado el clúster, puede ver `ConnectionStatus` y la hora `LastConnected` que, normalmente, es el último día, a menos que el clúster se desconecte temporalmente de Internet. Un clúster de Azure Stack HCl puede funcionar completamente sin conexión durante un máximo de 30 días consecutivos.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Estado de registro de Azure después del registro":::

Si se supera ese período máximo, `ConnectionStatus` mostrará `OutOfPolicy`.

## <a name="azure-active-directory-app-permissions"></a>Permisos de aplicación de Azure Active Directory

Además de crear un recurso de Azure en la suscripción, el registro de Azure Stack HCI crea una identidad de aplicación, conceptualmente similar a un usuario, en su inquilino de Azure Active Directory. La identidad de la aplicación hereda el nombre del clúster. Esta identidad actúa en nombre del servicio en la nube de Azure Stack HCI, según corresponda, en la suscripción.

Si el usuario que registra el clúster es un administrador de Azure Active Directory o si se le han delegado permisos suficientes, todo esto sucede automáticamente y no se requiere ninguna acción adicional. Si no es así, puede ser necesaria la aprobación del administrador de Azure Active Directory para completar el registro. El administrador puede conceder consentimiento explícitamente a la aplicación o puede delegar permisos para que pueda conceder consentimiento a la aplicación:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Diagrama de identidad y permisos de Azure Active Directory" border="false":::

Para dar su consentimiento, abra [portal.azure.com](https://portal.azure.com) e inicie sesión con una cuenta de Azure que tenga permisos suficientes en Azure Active Directory. Vaya a **Azure Active Directory** y, después, a **Registros de aplicaciones**. Seleccione la identidad de la aplicación llamada como el clúster y desplácese a **Permisos de API**.

Para la versión de disponibilidad general de Azure Stack HCI, la aplicación requiere los permisos siguientes, los cuales son diferentes de los permisos de aplicación requeridos en la versión preliminar pública:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

En el caso de la versión preliminar pública, los permisos de la aplicación fueron (estos están ahora en desuso):

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Solicitar la aprobación del administrador de Azure Active Directory podría llevar cierto tiempo, por lo que el cmdlet `Register-AzStackHCI` saldrá y dejará el registro en el estado "pendiente de consentimiento del administrador"; es decir, parcialmente completado. Una vez concedido el consentimiento, simplemente, vuelva a ejecutar `Register-AzStackHCI` para completar el registro.

## <a name="azure-active-directory-user-permissions"></a>Permisos de usuario de Azure Active Directory

El usuario que ejecuta Register-AzStackHCI necesita permisos de Azure AD para:

- Crear/obtener/establecer/quitar aplicaciones de Azure AD (New/Get/Set/Remove-AzureADApplication)
- Crear/obtener una entidad de servicio de Azure AD (New/Get-New-AzureADServicePrincipal)
- Administrar secretos de aplicación de AD (New/Get/Remove-AzureADApplicationKeyCredential)
- Otorgar consentimiento para usar permisos de aplicación específicos (New/Get/Remove AzureADServiceAppRoleAssignments)

Esto se puede lograr de tres formas distintas.

### <a name="option-1-allow-any-user-to-register-applications"></a>Opción 1: Permitir a cualquier usuario registrar aplicaciones

En Azure Active Directory, vaya a **Configuración de usuario > Registros de aplicaciones**. En **Los usuarios pueden registrar aplicaciones**, seleccione **Sí**.

Esto permitirá a todos los usuarios registrar aplicaciones. Sin embargo, el usuario seguirá necesitando que el administrador de Azure AD conceda su consentimiento durante el registro del clúster. Tenga en cuenta que se trata de una configuración en el nivel de inquilino, por lo que es posible que no sea adecuada para clientes empresariales de gran tamaño.

### <a name="option-2-assign-cloud-application-administration-role"></a>Opción 2: Asignar rol Administrador de aplicaciones en la nube

Asigne el rol integrado "Administrador de aplicaciones en la nube" de Azure AD al usuario. Esto permitirá al usuario registrar clústeres o anular el registro de estos sin necesidad de consentimiento adicional del administrador de AD.

### <a name="option-3-create-a-custom-ad-role-and-consent-policy"></a>Opción 3: Crear un rol y una directiva de consentimiento de AD personalizados

La opción más restrictiva es crear un rol de AD personalizado con una directiva de consentimiento personalizada que delegue el consentimiento del administrador de todo el inquilino de los permisos necesarios en el servicio Azure Stack HCI. Cuando se asigna este rol personalizado, los usuarios pueden registrarse y conceder su consentimiento sin necesidad de ningún consentimiento adicional del administrador de AD.

   > [!NOTE]
   > Esta opción requiere una licencia de Azure AD Premium y usa roles de AD personalizados y características de directiva de consentimiento personalizadas que se encuentran actualmente en versión preliminar pública.

   1. Conéctese a Azure AD:
   
      ```powershell
      Connect-AzureAD
      ```

   2. Cree una directiva de consentimiento personalizada:

      ```powershell
      New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
      ```

   3. Agregue una condición que incluya los permisos de aplicación necesarios para el servicio Azure Stack HCI con el identificador de aplicación 1322e676-dee7-41ee-A874-ac923822781c. Tenga en cuenta que los siguientes permisos son para la versión de disponibilidad general de Azure Stack HCI y no funcionarán con la versión preliminar pública a menos que haya aplicado la [actualización de la versión preliminar del 23 de noviembre de 2020(KB4586852)](https://support.microsoft.com/help/4595086/azure-stack-hci-release-notes-overview) a todos los servidores del clúster y haya descargado la versión 0.4.1 o posterior del módulo Az.StackHCI.
   
      ```powershell
      New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
      ```

   4. Conceda permisos para permitir el registro de Azure Stack HCI teniendo en cuenta la directiva de consentimiento personalizada creada en el paso 2:
   
      ```powershell
      $displayName = "Azure Stack HCI Registration Administrator "
      $description = "Custom AD role to allow registering Azure Stack HCI "
      $templateId = (New-Guid).Guid
      $allowedResourceAction =
      @(
             "microsoft.directory/applications/createAsOwner",
             "microsoft.directory/applications/delete",
             "microsoft.directory/applications/standard/read",
             "microsoft.directory/applications/credentials/update",
             "microsoft.directory/applications/permissions/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/read",
             "microsoft.directory/servicePrincipals/appRoleAssignments/read",
             "microsoft.directory/servicePrincipals/createAsOwner",
             "microsoft.directory/servicePrincipals/credentials/update",
             "microsoft.directory/servicePrincipals/permissions/update",
             "microsoft.directory/servicePrincipals/standard/read",
             "microsoft.directory/servicePrincipals/managePermissionGrantsForAll.AzSHCI-registration-consent-policy"
      )
      $rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
      ```

   5. Cree el nuevo rol personalizado de AD:

      ```powershell
      $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
      ```

   6. Asigne el nuevo rol personalizado de AD al usuario que va a registrar el clúster de Azure Stack HCI con Azure mediante [estas instrucciones](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context).

## <a name="unregister-azure-stack-hci-using-windows-admin-center"></a>Anulación del registro de Azure Stack HCI mediante Windows Admin Center

Cuando esté listo para retirar el clúster de Azure Stack HCI, solo tiene que conectarse al clúster mediante Windows Admin Center y seleccionar **Configuración** en la parte inferior del menú **Herramientas** de la izquierda. A continuación, seleccione **Registro de Azure Stack HCI** y haga clic en el botón **Anular registro**. El proceso de anulación del registro limpia automáticamente el recurso de Azure que representa el clúster, el grupo de recursos de Azure (si el grupo se creó durante el registro y no contiene ningún otro recurso) y la identidad de la aplicación de Azure AD. Esto detiene toda la funcionalidad de supervisión, soporte técnico y facturación mediante Azure Arc.

   > [!NOTE]
   > La anulación del registro de un clúster de Azure Stack HCI requiere un administrador de Azure Active Directory u otro usuario al que se hayan delegado permisos suficientes. Consulte [Permisos de usuario de Azure Active Directory](#azure-active-directory-user-permissions).

## <a name="unregister-azure-stack-hci-using-powershell"></a>Anulación del registro de Azure Stack HCI mediante PowerShell

También puede usar el cmdlet `Unregister-AzStackHCI` para anular el registro de un clúster de Azure Stack HCI. Puede ejecutar el cmdlet en un nodo de clúster o en un equipo de administración.

Puede que tenga que instalar la última versión del módulo `Az.StackHCI`. Si se le pregunta **¿Seguro que quiere instalar los módulos de "PSGallery"?** responda **Sí** (S).

```PowerShell
Install-Module -Name Az.StackHCI
```

### <a name="unregister-from-a-cluster-node"></a>Anulación del registro desde un nodo de clúster

Si ejecuta el cmdlet `Unregister-AzStackHCI` en un servidor del clúster, use esta sintaxis y especifique el identificador de la suscripción de Azure así como el nombre del recurso del clúster de Azure Stack HCI cuyo registro desea anular:

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Se le pedirá que visite microsoft.com/devicelogin en otro dispositivo (como su PC o teléfono), escriba el código e inicie sesión allí para autenticarse con Azure.

### <a name="unregister-from-a-management-pc"></a>Anulación del registro desde un equipo de administración

Si ejecuta el cmdlet desde un equipo de administración, también deberá especificar el nombre de un servidor del clúster:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Aparecerá una ventana interactiva de inicio de sesión de Azure. Los mensajes exactos que verá variarán en función de la configuración de seguridad (por ejemplo, la autenticación en dos fases). Siga las indicaciones para iniciar sesión.

## <a name="cleaning-up-after-a-cluster-that-was-not-properly-unregistered"></a>Limpieza después de un clúster cuya anulación del registro no se efectuó correctamente

Si un usuario destruye un clúster de Azure Stack HCI sin anular su registro, por ejemplo, mediante la creación de una nueva imagen de los servidores host o la eliminación de los nodos del clúster virtual, los artefactos se dejarán en Azure. Pasan desapercibidos y no afectan a la facturación ni usan recursos, pero pueden acumularse en Azure Portal. Para limpiarlos, puede eliminarlos manualmente.

Para eliminar el recurso de Azure Stack HCI, vaya a su página en Azure Portal y seleccione **Eliminar** en la barra de acciones de la parte superior. Escriba el nombre del recurso para confirmar la eliminación y luego seleccione **Eliminar**. Para eliminar la identidad de la aplicación de Azure AD, vaya a **Azure AD**, después a **Registros de aplicaciones** y la encontrará en **Todas las aplicaciones**. Seleccione **Eliminar** y confirme.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Conexión de Azure Stack HCl a Azure](../deploy/register-with-azure.md)
