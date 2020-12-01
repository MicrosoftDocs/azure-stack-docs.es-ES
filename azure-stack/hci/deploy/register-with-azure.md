---
title: Conexión de Azure Stack HCI a Azure
description: Registro de Azure Stack HCI en Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/23/2020
ms.openlocfilehash: d90788a6f7f267955b1c4837eef74a5980118dea
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812615"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Conexión de Azure Stack HCI a Azure

> Se aplica a: Azure Stack HCI, v20H2

Azure Stack HCl se entrega como servicio de Azure y debe registrarse en un plazo de 30 días a partir de la instalación según los Términos de los Servicios en Línea de Azure. En este tema se explica cómo registrar un clúster de Azure Stack HCI en [Azure Arc](https://azure.microsoft.com/services/azure-arc/) para supervisión, soporte técnico, facturación y servicios híbridos. Tras el registro, se crea un recurso de Azure Resource Manager para representar cada clúster local de Azure Stack HCl, lo que extiende de manera eficaz el plano de administración de Azure a Azure Stack HCl. La información se sincroniza periódicamente entre el recurso de Azure y el clúster local. 

## <a name="prerequisites-for-registration"></a>Requisitos previos para el registro

No podrá registrarse en Azure hasta que no haya creado un clúster de Azure Stack HCI. Los nodos pueden ser máquinas físicas o máquinas virtuales, pero deben tener Unified Extensible Firmware Interface (UEFI); es decir, no puede usar máquinas virtuales Hyper-V de la generación 1. El registro en Azure Arc es una funcionalidad nativa de Azure Stack HCI, por lo que no se requiere ningún agente.

### <a name="internet-access"></a>Acceso a Internet

Los nodos de Azure Stack HCI necesitan conectividad a la nube para poder conectarse a Azure. Por ejemplo, un ping saliente debe completarse correctamente:

```PowerShell
C:\> ping bing.com
```

### <a name="azure-subscription"></a>Suscripción de Azure

Si aún no tiene una cuenta de Azure, [cree una](https://azure.microsoft.com/). 

Puede usar una suscripción existente de cualquier tipo:
- Cuenta gratuita con créditos de Azure [para alumnos](https://azure.microsoft.com/free/students/) o [suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- Suscripción de [pago por uso](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) con tarjeta de crédito
- Suscripción obtenida a través de un Contrato Enterprise (EA)
- Suscripción obtenida a través del programa Proveedor de soluciones en la nube (CSP)

### <a name="azure-active-directory-permissions"></a>Permisos de Azure Active Directory

Necesitará permisos de Azure Active Directory para completar el proceso de registro. Si aún no los tiene, pida a su administrador de Azure AD que le conceda los permisos o se los delegue. Para obtener más información, consulte [Administración de registros en Azure](../manage/manage-azure-registration.md#azure-active-directory-permissions).

## <a name="register-using-powershell"></a>Registro mediante PowerShell

Use el siguiente procedimiento para registrar un clúster de Azure Stack HCI con Azure:

1. Conéctese a uno de los nodos del clúster. Para ello, abra una sesión de PowerShell y escriba el siguiente comando:

   ```PowerShell
   Enter-PSSession <server-name>
   ```

2. Instale el módulo de PowerShell para Azure Stack HCI:

   ```PowerShell
   Install-WindowsFeature RSAT-Azure-Stack-HCI
   ```

3. Instale los cmdlets necesarios. Si va a implementar Azure Stack HCl desde la imagen de la versión preliminar pública, deberá usar la versión 0.3.1 del módulo de Az.StackHCI de PowerShell:

   ```PowerShell
   Install-Module -Name Az.StackHCI -RequiredVersion 0.3.1
   ```

   Si ya ha instalado la [actualización de la versión preliminar del 23 de noviembre de 2020 (KB4586852)](../release-notes.md) en cada servidor del clúster y ahora registra el clúster en Azure, puede usar de forma segura la versión más reciente de Az.StackHCI:

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > 1. Es posible que vea un mensaje parecido al siguiente"¿Quiere que PowerShellGet se instale e importe el proveedor de NuGet ahora?", a la que debe responder Sí (S).
   > 2. Es posible que además se le pregunte "¿Estás seguro de que quieres instalar los módulos de 'PSGallery'?", a lo que debe responder Sí (S).
   > 3. Por último, podría suponer que la instalación de la totalidad del módulo de **Az** incluiría el submódulo **StackHCI**, pero no es el caso. Los submódulos en versión preliminar no se incluyen automáticamente según la convención estándar de Azure PowerShell, por lo que debe solicitar de manera explícita el submódulo **Az.StackHCI** como hemos visto anteriormente.

4. Realice el registro real:

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" [-ResourceName] [-ResourceGroupName]
   ```

   Esta sintaxis registra el clúster local (del que es miembro el servidor local), como el usuario actual, con la región de Azure y el entorno de nube predeterminados, y mediante nombres predeterminados inteligentes para el recurso y el grupo de recursos de Azure, pero puede agregar parámetros a este comando para especificar estos valores si lo desea.

   Recuerde que el usuario que ejecuta el cmdlet `Register-AzStackHCI` debe tener [permisos de Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-permissions), o el proceso de registro no se completará y, en vez de eso, se cerrará y dejará el registro pendiente del consentimiento del administrador. Una vez que se hayan concedido los permisos, solo tiene que volver a ejecutar `Register-AzStackHCI` para completar el registro.

5. Autenticación con Azure

   Para completar el proceso de registro, debe autenticarse (iniciar sesión) con su cuenta de Azure. La cuenta debe tener acceso a la suscripción de Azure que se especificó en el paso 4 anterior para que se realice el registro. Copie el código proporcionado, vaya a microsoft.com/devicelogin en otro dispositivo (como su PC o teléfono), escriba el código e inicie sesión allí. Esta es la misma experiencia que Microsoft usa para otros dispositivos con modalidades de entrada limitadas, como Xbox.

El flujo de trabajo de registro detectará cuando haya iniciado sesión y continuará con el proceso de finalización. A partir de entonces, debería poder ver el clúster en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

Ahora ya está listo para:

- [Validación del clúster](validate.md)
- [Creación de volúmenes](../manage/create-volumes.md)
