---
title: Implementación del sistema operativo de Azure Stack HCI
description: Aprenda a implementar el sistema operativo de Azure Stack HCI y, después, use Windows Admin Center para conectarse a sus servidores. Aprenda a crear un clúster de servidores y averigüe cómo obtener las actualizaciones y el firmware más recientes de Windows para los servidores.
author: JohnCobb1
ms.author: v-johcob
ms.topic: tutorial
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: cf34506f5fbeec1c6e0531eda231219ae2949b59
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572609"
---
# <a name="deploy-the-azure-stack-hci-operating-system"></a>Implementación del sistema operativo de Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2

Después de realizar los pasos que se indican en [Antes de implementar Azure Stack HCI](before-you-start.md#install-windows-admin-center), el primer paso para la implementación es [descargar Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) e instalarlo en cada servidor que desee agrupar en el clúster. En este artículo se describen diferentes formas de implementar el sistema operativo y de utilizar Windows Admin Center para conectarse a los servidores.

Después de implementar el sistema operativo, está listo para usar las instrucciones relacionadas con la creación de un clúster de servidores y la obtención de las actualizaciones más recientes de Windows y del firmware de los servidores, tal y como se indica en [Creación de un clúster de Azure Stack HCI](create-cluster.md).

## <a name="prerequisites"></a>Requisitos previos

- Windows Admin Center configurado en un sistema que pueda acceder a los servidores que desea agrupar en un clúster, tal como se indica en [Antes de implementar Azure Stack HCI](before-you-start.md#install-windows-admin-center).
- Una solución de Azure Stack HCI que proporciona hardware validado por Microsoft de su proveedor de hardware preferido, el sistema operativo Azure Stack HCI y los servicios de Azure, como se detalla en [Soluciones de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

## <a name="deployment-preparation"></a>Preparación de la implementación

Una vez que haya adquirido el hardware del servidor para la solución de Azure Stack HCI, es el momento de instalarlo en el bastidor y conectarlo. Siga los pasos que se indican a continuación para preparar el hardware del servidor para la implementación del sistema operativo.

1. Instale en el bastidor todos los nodos de servidor que desee usar en el clúster de servidores.
1. Conecte los nodos de servidor a los conmutadores de red.
1. Configure el BIOS o Unified Extensible Firmware Interface (UEFI) de los servidores según lo recomendado por el proveedor de hardware de Azure Stack HCI para maximizar el rendimiento y la confiabilidad.

## <a name="operating-system-deployment-options"></a>Opciones de implementación del sistema operativo

Puede implementar el sistema operativo Azure Stack HCI de la misma manera que implementa otros sistemas operativos de Microsoft:

- Preinstalación del fabricante del servidor.
- Implementación desatendida con un archivo de respuesta.
- System Center Virtual Machine Manager (VMM).
- Implementación de red.
- Implementación manual mediante la conexión de un teclado y un monitor directamente al hardware del servidor en el centro de datos, o mediante la conexión de un dispositivo de hardware KVM.

### <a name="server-manufacturer-preinstallation"></a>Preinstalación del fabricante del servidor

Para la implementación empresarial del sistema operativo Azure Stack HCI, se recomienda el hardware de una solución de sistema integrado de Azure Stack HCI del asociado de hardware que prefiera. El hardware de la solución viene con el sistema operativo preinstalado y admite el uso de Windows Admin Center para implementar y actualizar los controladores y el firmware del fabricante del hardware.

El hardware de la solución incluye entre 2 y 16 nodos, y Microsoft y los proveedores asociados lo prueban y validan. Para buscar el hardware de la solución de Azure Stack HCI de su proveedor de hardware preferido, consulte el [Catálogo de Azure Stack HCI](https://www.microsoft.com/cloud-platform/azure-stack-hci-catalog).

### <a name="headless-deployment"></a>Implementación desatendida

Puede usar un archivo de respuesta para realizar una implementación desatendida del sistema operativo. El archivo de respuesta utiliza un formato XML para definir los valores de configuración durante una instalación desatendida del sistema operativo.

Para esta opción de implementación, puede usar el Administrador de imágenes de sistema para crear un archivo de respuesta unattend.xml para implementar el sistema operativo en los servidores. El Administrador de imágenes de sistema crea el archivo de respuesta "unattend" mediante una herramienta gráfica con secciones de componentes para definir las "respuestas" a las preguntas de configuración y, a continuación, garantizar el formato y la sintaxis correctos en el archivo.
La herramienta Administrador de imágenes de sistema está disponible en Windows Assessment and Deployment Kit (Windows ADK). Primeros pasos: [Descargue e instale Windows ADK](/windows-hardware/get-started/adk-install).

### <a name="system-center-virtual-machine-manager-vmm-deployment"></a>Implementación de System Center Virtual Machine Manager (VMM)

System Center Virtual Machine Manager (VMM) forma parte del conjunto de pruebas de System Center. Puede usar VMM para implementar el sistema operativo Azure Stack HCI en hardware sin sistema operativo, así como para agrupar los servidores en un clúster. Para más información sobre VMM, consulte [Requisitos del sistema para System Center Virtual Machine Manager](/system-center/vmm/system-requirements).

Para más información acerca del uso de VMM para realizar una implementación del sistema operativo en equipos sin este, consulte [Aprovisionamiento de un host o clúster de Hyper-V de equipos sin sistema operativo](/system-center/vmm/hyper-v-bare-metal).

### <a name="network-deployment"></a>Implementación de red

Otra opción consiste en instalar el sistema operativo Azure Stack HCI a través de la red mediante los [Servicios de implementación de Windows](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831764(v=ws.11)).

### <a name="manual-deployment"></a>Implementación manual

Para implementar manualmente el sistema operativo Azure Stack HCI en la unidad de sistema de cada servidor que se va a agrupar, instale el sistema operativo mediante el método que prefiera como, por ejemplo, mediante el arranque desde un DVD o unidad USB. Complete el proceso de instalación mediante la herramienta de configuración de servidores (Sconfig) para preparar los servidores para la agrupación en clústeres. Para más información sobre la herramienta, consulte [Configuración de una instalación básica de servidores con Sconfig](/windows-server/get-started/sconfig-on-ws2016).

Para instalar manualmente el sistema operativo Azure Stack HCI:
1. Inicie el asistente para la instalación de Azure Stack HCI en la unidad de sistema del servidor en el que desea instalar el sistema operativo.
1. Elija el idioma de instalación o acepte la configuración de idioma predeterminada, seleccione **Next** (Siguiente) y, a continuación, en la siguiente página del asistente, seleccione **Install now** (Instalar ahora).

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-language.png" alt-text="Página de idiomas del asistente para la instalación de Azure Stack HCI.":::

1. En la página de avisos y términos de licencia aplicables, consulte los términos de licencia, seleccione la casilla **I accept the license terms** (Acepto los términos de licencia) y, a continuación, haga clic en **Next** (Siguiente).
1. En la página que le pregunta qué tipo de instalación desea, seleccione **Custom: Install the newer version of Azure Stack HCI only (advanced) (Instalar solo la versión más reciente de Azure Stack HCI [avanzada])** .

    > [!NOTE]
    > En esta versión del sistema operativo no se admiten las instalaciones de actualizaciones.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-which-type.png" alt-text="Página de idiomas del asistente para la instalación de Azure Stack HCI.":::

1. En la página que le pregunta dónde desea instalar Azure Stack HCI, confirme la ubicación de la unidad en la que desea instalar el sistema operativo o actualícela y, a continuación, seleccione **Next** (Siguiente).

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-where.png" alt-text="Página de idiomas del asistente para la instalación de Azure Stack HCI.":::

1. Aparece la página de instalación de Azure Stack HCI que muestra el estado del proceso.

    :::image type="content" source="../media/operating-system/azure-stack-hci-installing.png" alt-text="Página de idiomas del asistente para la instalación de Azure Stack HCI.":::

    > [!NOTE]
    > El proceso de instalación reinicia el sistema operativo dos veces para completar el proceso y muestra avisos sobre el inicio de los servicios antes de abrir un símbolo del sistema del administrador.

1. En el símbolo del sistema del administrador, seleccione **OK** (Aceptar) para cambiar la contraseña del usuario antes de iniciar sesión en el sistema operativo y presione Entrar.

    :::image type="content" source="../media/operating-system/azure-stack-hci-change-admin-password.png" alt-text="Página de idiomas del asistente para la instalación de Azure Stack HCI.":::

1. En el aviso Enter new credential for Administrator (Escribir nueva credencial para el administrador), escriba una nueva contraseña, escríbala de nuevo para confirmarla y, a continuación, presione Entrar.
1. En el mensaje de confirmación Your password has been changed (Se ha cambiado la contraseña), presione Entrar.

    :::image type="content" source="../media/operating-system/azure-stack-hci-admin-password-changed.png" alt-text="Página de idiomas del asistente para la instalación de Azure Stack HCI.":::

Ahora está listo para usar la herramienta de configuración de servidores (Sconfig) para realizar tareas importantes. Para usar Sconfig, inicie sesión en el servidor que ejecuta el sistema operativo Azure Stack HCI. Esto se podría realizar en el lugar mediante un teclado y un monitor, o mediante un controlador de administración remota (sin periféricos ni BMC) o un escritorio remoto. La herramienta Sconfig se abre automáticamente al iniciar sesión en el servidor.

:::image type="content" source="../media/operating-system/azure-stack-hci-sconfig-screen.png" alt-text="Página de idiomas del asistente para la instalación de Azure Stack HCI." lightbox="../media/operating-system/azure-stack-hci-sconfig-screen.png":::

En la página principal de la herramienta Sconfig, puede realizar las siguientes tareas de configuración inicial:
- Configure las redes o confirme que la red se configuró automáticamente mediante el protocolo de configuración dinámica de host (DHCP).
- Cambie el nombre del servidor si el nombre generado automáticamente no es adecuado.
- Conecte el servidor a un dominio de Active Directory.
- Agregue la cuenta de usuario del dominio o el grupo de dominio designado a los administradores locales.
- Habilite el acceso a Administración remota de Windows (WinRM) si tiene pensado administrar el servidor desde fuera de la subred local y ha decidido no unirse al dominio todavía. (Las reglas de firewall predeterminadas permiten la administración de la subred local y de cualquier subred dentro de los servicios de dominio de Active Directory).

Después de configurar el sistema operativo según sea necesario con Sconfig en cada servidor, está listo para usar el asistente para la creación de clústeres en Windows Admin Center para agrupar los servidores en clústeres.

## <a name="next-steps"></a>Pasos siguientes

Para realizar la siguiente tarea de administración relacionada con este artículo, consulte:
> [!div class="nextstepaction"]
> [Creación de un clúster de Azure Stack HCI](../deploy/create-cluster.md)
