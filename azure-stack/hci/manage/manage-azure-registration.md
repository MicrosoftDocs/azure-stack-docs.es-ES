---
title: Administración del registro de Azure para Azure Stack HCl
description: Cómo administrar el registro de Azure para Azure Stack HCl y comprender el estado de registro mediante PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 696ef552dcf49f31fb613a22393617e653f7e10d
ms.sourcegitcommit: eb91a28a19a74f799b093ae2a705f7f6e4c5cd49
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2020
ms.locfileid: "87436463"
---
# <a name="manage-azure-registration"></a>Administración del registro de Azure

> Se aplica a Azure Stack HCI v20H2

Una vez creado el clúster de Azure Stack HCI, debe [registrar el clúster con Azure Arc](../deploy/register-with-azure.md). Una vez registrado el clúster, se sincroniza periódicamente la información entre el clúster local y la nube. En este tema se explica cómo entender el estado del registro y anular el registro del clúster cuando esté listo para la retirada.

## <a name="understanding-registration-status"></a>Reconocimiento del estado del registro

Para comprender el estado de registro, use el cmdlet de PowerShell `Get-AzureStackHCI` y las propiedades `ClusterStatus`, `RegistrationStatus` y `ConnectionStatus`. Por ejemplo, después de instalar el sistema operativo de Azure Stack HCI, antes de unirse a un clúster o crearlo, la propiedad `ClusterStatus` muestra el estado "todavía no":

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Estado de registro de Azure antes de la creación del clúster":::

Una vez creado el clúster, solo `RegistrationStatus` muestra el estado "todavía no":

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Estado de registro de Azure después de la creación del clúster":::

Azure Stack HCl necesita registrarse en un plazo de 30 días a partir de la instalación según los Términos de los Servicios en Línea de Azure. Si no está agrupado después de 30 días, `ClusterStatus` mostrará `OutOfPolicy` y, si no se registra después de 30 días, `RegistrationStatus` mostrará `OutOfPolicy`.

Una vez registrado el clúster, puede ver `ConnectionStatus` y la hora `LastConnected` que, normalmente, es el último día, a menos que el clúster se desconecte temporalmente de Internet. Un clúster de Azure Stack HCl puede funcionar completamente sin conexión durante un máximo de 30 días consecutivos.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Estado de registro de Azure después del registro":::

Si se supera ese período máximo, `ConnectionStatus` mostrará `OutOfPolicy`.

## <a name="azure-active-directory-permissions"></a>Permisos de Azure Active Directory

Además de crear un recurso de Azure en la suscripción, el registro de Azure Stack HCI crea una identidad de aplicación, conceptualmente similar a un usuario, en su inquilino de Azure Active Directory. La identidad de la aplicación hereda el nombre del clúster. Esta identidad actúa en nombre del servicio en la nube de Azure Stack HCI, según corresponda, en la suscripción.

Si el usuario que ejecuta `Register-AzureStackHCI` es un administrador de Azure Active Directory o si se le han delegado permisos suficientes, todo esto sucede automáticamente y no se requiere ninguna acción adicional. Si no es así, puede ser necesaria la aprobación del administrador de Azure Active Directory para completar el registro. El administrador puede conceder consentimiento explícitamente a la aplicación o puede delegar permisos para que pueda conceder consentimiento a la aplicación:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Diagrama de identidad y permisos de Azure Active Directory" border="false":::

Para dar su consentimiento, abra portal.azure.com e inicie sesión con una cuenta de Azure que tenga permisos suficientes en Azure Active Directory. Vaya a **Azure Active Directory** y, después, a **Registros de aplicaciones**. Seleccione la identidad de la aplicación llamada como el clúster y desplácese a **Permisos de API**.

La aplicación requiere dos permisos:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Solicitar la aprobación del administrador de Azure Active Directory podría llevar cierto tiempo, por lo que el cmdlet `Register-AzureStackHCI` saldrá y dejará el registro en el estado "pendiente de consentimiento del administrador"; es decir, parcialmente completado. Una vez concedido el consentimiento, simplemente, vuelva a ejecutar `Register-AzureStackHCI` para completar el registro.

## <a name="unregister-azure-stack-hci-with-azure"></a>Anulación del registro de Azure Stack HCI con Azure

Cuando esté listo para retirar su clúster de Azure Stack HCI, use el cmdlet `Unregister-AzStackHCI` para anular el registro. Esto detiene toda la funcionalidad de supervisión, soporte técnico y facturación mediante Azure Arc. Se eliminan el recurso de Azure que representa el clúster y la identidad de la aplicación de Azure Active Directory, pero el grupo de recursos, no, ya que puede contener otros recursos no relacionados.

Si ejecuta el cmdlet `Unregister-AzStackHCI` en un nodo de clúster, use esta sintaxis y especifique el identificador de la suscripción de Azure así como el nombre del recurso del clúster de Azure Stack HCI cuyo registro desea anular:

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Se le pedirá que visite microsoft.com/devicelogin en otro dispositivo (como su PC o teléfono), escriba el código e inicie sesión allí para autenticarse con Azure.

Si ejecuta el cmdlet desde un equipo de administración, también deberá especificar el nombre de un servidor del clúster:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Aparecerá una ventana interactiva de inicio de sesión de Azure. Los mensajes exactos que verá variarán en función de la configuración de seguridad (por ejemplo, la autenticación en dos fases). Siga las indicaciones para iniciar sesión.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Conexión de Azure Stack HCl a Azure](../deploy/register-with-azure.md)
- [Supervisión de Azure Stack HCI con Azure Monitor](azure-monitor.md)
