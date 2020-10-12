---
title: Cambio de la configuración de privacidad
description: En este tema se proporcionan instrucciones sobre cómo cambiar la configuración de privacidad en el sistema operativo Azure Stack HCI o en Windows Server.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 8055e02b702fb5e585b5570171c6dabb247b623f
ms.sourcegitcommit: 79be77eb2bebf314d956258fc9bc14b2014be190
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745168"
---
# <a name="change-privacy-settings-on-individual-servers"></a>Cambio de la configuración de privacidad en servidores individuales

>Se aplica a: Azure Stack HCI, versión 20H2, Windows Server 2019 y Windows Server 2016

Aquí se indica cómo cambiar la configuración de privacidad de un servidor que se ejecuta en el sistema operativo Azure Stack HCI o en Windows Server. Para administrar la configuración de privacidad con la directiva de grupo en varios servidores a la vez o en toda su empresa, consulte [Administración de los datos de diagnóstico de la empresa](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data).

## <a name="azure-stack-hci-or-server-core"></a>Azure Stack HCI o Server Core
Si el servidor utiliza el sistema operativo Azure Stack HCI o Windows Server con la opción de instalación Server Core, siga estos pasos:
1. Conéctese a un servidor del clúster de Azure Stack HCI mediante Escritorio remoto, un controlador de administración remota (sin periféricos ni BMC) con un KVM, o mediante el uso de un teclado y un monitor. 
1. Si se conecta a un servidor que ejecuta Azure Stack HCI, se abrirá automáticamente la herramienta de configuración del servidor (Sconfig). Si se conecta a un servidor que ejecuta Windows Server con Server Core, en el símbolo del sistema, escriba `Sconfig`.
1. En la indicación **Enter a number to select an option:** (Escriba un nombre para seleccionar una opción), escriba **10** y presione ENTRAR.
1. En el mensaje de confirmación **Change Telemetry** (Cambiar datos de telemetría), seleccione **Yes** (Sí) para mostrar las siguientes opciones:

    Configuración de telemetría disponible: **1 Security** (1 Seguridad), **2 Basic** (2 Básico), **3 Enhanced** (3 Mejorado), **4 Full** (4 Completo)

    >[!NOTE]
    > La configuración predeterminada para Azure Stack HCI es **1 Security** (1 Seguridad).

1. En la indicación **Enter new telemetry setting:** (Especifique la nueva configuración de telemetría), escriba la opción que quiera y presione ENTRAR.

## <a name="full-desktop"></a>Escritorio completo
Si el servidor ejecuta Windows Server y tiene la opción de instalación de escritorio completo, siga estos pasos:
1. Conéctese al panel de Administrador del servidor de Windows Server.

    Puede conectarse localmente mediante un teclado y un monitor, o mediante un controlador de administración remota (sin periféricos ni BMC) o un escritorio remoto. 

1. En Administrador de servidores, en **Panel**, seleccione **Servidor local**.
1. En la página **Propiedades** del servidor, junto a **Comentarios y diagnósticos**, seleccione **Configuración**.

    En la página de **Configuración**, aparecerán los valores **Frecuencia de comentarios** y **Datos de diagnóstico y uso**. 
 
1. Expanda el valor **Datos de diagnóstico y uso** para seleccionar una de las siguientes opciones:
    - **Datos de diagnóstico obligatorios**
    - **Mejorado**.
    - **Datos de diagnóstico opcionales**

    >[!NOTE]
    > En la página **Configuración**, si aparece el aviso **Tu organización administra algunas opciones de configuración**, es posible que el valor **Datos de diagnóstico y uso** no esté disponible.

## <a name="next-steps"></a>Pasos siguientes
Para administrar la configuración de privacidad con la directiva de grupo en varios servidores a la vez o en toda su empresa, consulte:
-   [Administración de los datos de diagnóstico de la empresa](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)
