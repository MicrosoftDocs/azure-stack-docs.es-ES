---
title: Conexión de Azure Stack HCI a Azure
description: Registro de Azure Stack HCI en Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/22/2020
ms.openlocfilehash: 9b8e7e211f6c2ce21f0b00ed2a3b972418f9f9bd
ms.sourcegitcommit: 16ff77f7157e5b04a8cd401b095f7b71f51d5a11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86949617"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Conexión de Azure Stack HCI a Azure

> Se aplica a Azure Stack HCI v20H2; Windows Server 2019

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

Instale el módulo de PowerShell para Azure Stack HCl; para ello, ejecute el siguiente comando de PowerShell en uno o varios servidores de un clúster que ejecute el sistema operativo de Azure Stack HCI:

```PowerShell
Install-WindowsFeature RSAT-Azure-Stack-HCI -ComputerName Server1
```

Instale los cmdlets necesarios en el equipo de administración o en un nodo de clúster:

```PowerShell
Install-Module Az.StackHCI
```
   > [!NOTE]
   > 1. Es posible que vea un mensaje parecido al siguiente"¿Quiere que PowerShellGet se instale e importe el proveedor de NuGet ahora?", a la que debe responder Sí (S).
   > 2. Es posible que además se le pregunte "¿Estás seguro de que quieres instalar los módulos de 'PSGallery'?", a lo que debe responder Sí (S).
   > 3. Por último, podría suponer que la instalación de la totalidad del módulo de **Az** incluiría el submódulo **StackHCI** y que será correcto a largo plazo. Sin embargo, según la convención estándar de Azure PowerShell, los submódulos en versión preliminar no se incluyen automáticamente; en su lugar, debe especificarlos de manera explícita. Por lo tanto, por ahora debe solicitar explícitamente **Az.StackHCI** como se mostró anteriormente.

Regístrese (esto le mostrará el inicio de sesión de Azure):

```PowerShell
Register-AzStackHCI  -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" [-ResourceName] [-ResourceGroupName] [-ComputerName –Credential]
```

La sintaxis mínima requiere solo su identificador de suscripción de Azure. Recuerde que el usuario que ejecuta el comando anterior debe tener permisos de Azure Active Directory, o el proceso de registro no se completará.

   > [!NOTE]
   > Si, tras el registro, recibe un error similar al mensaje siguiente, **intente volver a registrarse en 24 a 48 horas**. La integración de Azure todavía está en proceso de implementación entre regiones. Aún puede continuar con la evaluación, y no se verá afectada ninguna funcionalidad. Solo tiene que asegurarse de volver y registrarse más tarde.
   >
   > `Register-AzStackHCI : Azure Stack HCI is not yet available in region <regionName>`

## <a name="authenticate-with-azure"></a>Autenticación con Azure
Una vez que se han instalado las dependencias y se han validado los parámetros, debe autenticarse (iniciar sesión) con su cuenta de Azure. La cuenta debe tener acceso a la suscripción de Azure que se especificó para que continúe el registro.

Si ejecutó Register-AzStackHCI de forma remota desde un sistema operativo con experiencia de escritorio, como Windows 10, aparecerá una ventana interactiva de inicio de sesión de Azure. Los mensajes exactos que verá variarán en función de la configuración de seguridad (por ejemplo, la autenticación en dos fases). Siga las indicaciones para iniciar sesión.

Si ejecutó Register-AzStackHCI localmente en el sistema operativo de Azure Stack HCI, que no puede representar una ventana interactiva de inicio de sesión de Azure, se le pedirá que visite microsoft.com/devicelogin en otro dispositivo (como su PC o teléfono), escriba el código e inicie sesión en él. Esta es la misma experiencia que Microsoft usa para otros dispositivos con modalidades de entrada limitadas, como Xbox.

El flujo de trabajo de registro detectará cuando haya iniciado sesión y continuará con el proceso de finalización. A partir de entonces, debería poder ver el clúster en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

Ahora ya está listo para:

- [Validación del clúster](validate.md)
- [Creación de volúmenes](../manage/create-volumes.md)
