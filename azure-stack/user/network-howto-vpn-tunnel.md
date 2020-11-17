---
title: Configuración de un túnel de VPN de sitio a sitio en Azure Stack Hub
description: Aprenda a configurar un túnel de VPN de sitio a sitio en Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: e401e1897af63ede0a3f7dcdd924dc00df39f941
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94547082"
---
# <a name="how-to-set-up-multiple-site-to-site-vpn-tunnels-in-azure-stack-hub"></a>Configuración de túneles de VPN de sitio a sitio en Azure Stack Hub

En este artículo se muestra cómo utilizar una plantilla de Resource Manager en Azure Stack Hub para implementar la solución. La solución crea varios grupos de recursos con redes virtuales asociadas y conecta estos sistemas.

Puede encontrar las plantillas en el repositorio de GitHub [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). La plantilla se encuentra en la carpeta **rras-gre-vnet-vnet**. 

## <a name="scenarios"></a>Escenarios

![Diagrama con cinco escenarios: entre dos grupos de recursos en una suscripción única; entre dos grupos, cada uno en su propia suscripción; entre dos grupos en instancias de pila independientes; entre un grupo local y recursos locales en el entorno local; y varios túneles de VPN.](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>Creación de varios túneles de VPN

![En el diagrama se muestran dos grupos de recursos, cada uno en su propia suscripción e instancia de pila, conectados mediante VPN. Uno de ellos está conectado a recursos locales en el entorno local mediante VPN.](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  Implemente una aplicación de tres niveles: web, aplicación y base de datos.

-  Implemente las dos primeras plantillas en instancias de Azure Stack Hub independientes.

-  **WebTier** se implementará en PPE1 y **AppTier** en PPE2.

-  Connect **WebTier** y **AppTier** con un túnel IKE.

-  Conecte **AppTier** a un sistema local al que llamará **DBTier**.

## <a name="steps-to-deploy-multiple-vpns"></a>Pasos para implementar varias VPN

Este proceso tiene varios pasos. Para esta solución usará el portal de Azure Stack Hub. Sin embargo, puede usar PowerShell, la CLI de Azure u otras cadenas de herramientas de infraestructura como código para capturar las salidas y usarlas como entradas.

![En el diagrama se muestran cinco pasos para implementar túneles de VPN entre dos infraestructuras. En los dos primeros pasos se crean dos infraestructuras a partir de una plantilla. En los dos pasos posteriores se crean túneles de VPN a partir de una plantilla, y en el último paso se conectan los túneles.](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>Tutorial

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>Implementación del nivel web en instancias de Azure Stack Hub PPE1

1. Abra el portal de usuarios de Azure Stack Hub y seleccione **Crear un recurso**.

2. Seleccione **Template Deployment** (Implementación de plantillas).

    ![En el cuadro de diálogo "Panel > Nuevo" se muestran varias opciones. El botón "Template Deployment" aparece resaltado.](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3. Copie y pegue el contenido de azuredeploy.json del repositorio **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** en la ventana de la plantilla. Verá los recursos incluidos en la plantilla, seleccione **Save** (Guardar).

    ![El cuadro de diálogo "Panel > Nuevo > Implementar plantilla de solución > Editar plantilla" tiene una ventana en la que se pega el archivo azuredeploy.json.](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4. Escriba el nombre de un **grupo de recursos** y compruebe los parámetros.

    > [!Note]  
    > El espacio de direcciones de WebTier será **10.10.0.0/16** y puede ver que la ubicación del grupo de recursos es **PPE1**

    ![El cuadro de diálogo "Panel > Nuevo > Implementar plantilla de solución > Parámetros" incluye un cuadro de texto denominado "Grupo de recursos" y un botón de radio. El botón "Crear nuevo" está seleccionado, y el texto que aparece indica "WebTier".](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>Implementación de la capa de aplicación en la segunda instancia de Azure Stack Hub

Puede usar el mismo proceso que **WebTier** pero parámetros diferentes, como se muestra aquí:

> [!NOTE]  
> El espacio de direcciones de AppTier será **10.20.0.0/16** y puede ver que la ubicación del grupo de recursos es **WestUS2**.

![El cuadro de diálogo "Panel > Implementar plantilla de solución > Parámetros" incluye un cuadro de texto denominado "Grupo de recursos" y un botón de radio. El botón "Crear nuevo" está seleccionado, y el texto que aparece indica "AppTier". Se muestran otros ocho cuadros de texto resaltados que incluyen parámetros de plantilla.](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Revisión de las implementaciones para el nivel de web y el nivel de aplicación, y captura de las salidas

1. Revise que la implementación se haya completado correctamente. Seleccione **Salidas**.

    ![La opción Salidas aparece resaltada en el cuadro de diálogo "Panel > Microsoft.Template - Overview (Microsoft.Template: información general)". El mensaje indica "Your deployment is complete" (La implementación se ha completado), seguido de una lista de recursos para el grupo WebTier, cada uno con el nombre, el tipo, el estado y un vínculo a los detalles.](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

1. Copie los cuatro primeros valores en la aplicación Bloc de notas.

    ![El cuadro de diálogo es "Panel > Microsoft.Template - Outputs (Microsoft.Template: salidas)". Se muestran resaltados los cuatro cuadros de texto que se van a copiar desde la implementación de nivel web: LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY y LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

1. Repita el procedimiento para la implementación de **AppTier**.

    ![La opción Salidas aparece resaltada en el cuadro de diálogo "Panel > Microsoft.Template - Overview (Microsoft.Template: información general)". El mensaje indica "Your deployment is complete" (La implementación se ha completado), seguido de una lista de recursos para el grupo AppTier, cada uno con el nombre, el tipo, el estado y un vínculo a los detalles.](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

    ![El cuadro de diálogo es "Panel > Microsoft.Template - Outputs (Microsoft.Template: salidas)". Se muestran resaltados los cuatro cuadros de texto que se van a copiar desde la implementación de nivel de aplicación: LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY y LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Creación de un túnel desde el nivel de web al nivel de aplicación

1. Abra el portal de usuarios de Azure Stack Hub y seleccione **Crear un recurso**.

2. Seleccione **template deployment** (implementación de plantillas).

3. Pegue el contenido de **azuredeploy.tunnel.ike.json**.

4. Seleccione **Edit parameters** (Editar parámetros).

![El cuadro de diálogo "Panel > Nuevo > Implementar plantilla de solución > Parámetros" incluye un cuadro de texto denominado "Grupo de recursos" y un botón de radio. El botón "Usar existente" está seleccionado, y el texto que aparece indica "WebTier". Se muestran otros cuatro cuadros de texto resaltados que incluyen parámetros de plantilla.](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Creación de un túnel desde el nivel de aplicación al nivel de web

1. Abra el portal de usuarios de Azure Stack Hub y seleccione **Crear un recurso**.

2. Seleccione **Implementación de plantillas**.

3. Pegue el contenido de **azuredeploy.tunnel.ike.json**.

4. Seleccione **Edit parameters** (Editar parámetros).

![El cuadro de diálogo "Panel > Nuevo > Implementar plantilla de solución > Parámetros" incluye un cuadro de texto denominado "Grupo de recursos" y un botón de radio. El botón "Usar existente" está seleccionado, y el texto que aparece indica "AppTier". Se muestran otros cuatro cuadros de texto resaltados que incluyen parámetros de plantilla.](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Visualización de la implementación del túnel

Si ve la salida de la extensión del script personalizado, puede ver el túnel que se está creando y debería mostrar el estado. Verá que un lado muestra **connecting** (conectando) mientras espera a que el otro lado esté listo y que el otro lado mostrará **connected** (conectado) una vez implementado.

![El cuadro de diálogo es "Panel > Grupos de recursos > WebTier > WebTier-RRAS - Extensions (RRAS de WebTier: extensiones)". Se muestran dos extensiones en la lista: CustomScriptExtension (resaltada) e InstallRRAS. Ambas tienen el estado Aprovisionamiento realizado correctamente.](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![El cuadro de diálogo es "Panel > Grupos de recursos > WebTier > WebTier-RRAS - Extensions (RRAS de WebTier: extensiones) > CustomScriptExtension". El valor del campo ESTADO DETALLADO es "Aprovisionamiento realizado correctamente".](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![Las salidas de las extensiones de script personalizado para AppTier y WebTier se muestran en ventanas de la aplicación Bloc de notas en paralelo. AppTier muestra el estado de túnel Conectando. WebTier muestra el estado Conectado.](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Solución de problemas en la máquina virtual de RRAS

1. Cambie la regla de Escritorio remoto (RDP) de **Denegar** a **Permitir**.

1. Conéctese al sistema con un cliente de Escritorio remoto (RDP) con las credenciales que estableció durante la implementación.

1. Abra PowerShell con un símbolo del sistema con privilegios elevados y ejecute `get-VpnS2SInterface`.

    ![La ventana de PowerShell muestra la ejecución del comando Get-VpnS2SInterface, que muestra los detalles de la interfaz de sitio a sitio. El valor de ConnectionState es Conectado.](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

1. Use los cmdlets **RemoteAccess** para administrar el sistema.

    ![Hay una ventana de cmd.exe que se encuentra en ejecución en el nivel de aplicación y muestra la salida del comando ipconfig. Dos ventanas se muestran como superposiciones: una ventana de conexión RDP que se conecta al nivel web y una ventana de Seguridad de Windows que se conecta al nivel web.](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-an-on-premises-vm-db-tier"></a>Instalación de RRAS en un nivel de base de datos de máquina virtual local

1. El destino es una imagen de Windows 2016.

1. Si copia el script de `Add-Site2SiteIKE.ps1` del repositorio y lo ejecuta localmente, el script instala **WindowsFeature** y **RemoteAccess**.

    > [!NOTE]
    > Dependiendo de su entorno, es posible que tenga que reiniciar el sistema.

    Como referencia, consulte la configuración de red de la máquina local.

    ![Se muestran una ventana de cmd.exe con la salida del comando ipconfig y una ventana del Centro de redes y recursos compartidos.](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

1. Ejecute el script agregando los parámetros de **Output** (Salida) registrados en la implementación de la plantilla de AppTier.

    ![El script Add-Site2SiteIKE.psa se ejecuta en una ventana de PowerShell y se muestra la salida.](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

1. El túnel ya está configurado y espera la conexión con AppTier.

### <a name="configure-app-tier-to-db-tier"></a>Configuración del nivel de aplicación en el nivel de base de datos

1. Abra el portal de usuarios de Azure Stack Hub y seleccione **Crear un recurso**.

2. Seleccione **Implementación de plantillas**.

3. Pegue el contenido de **azuredeploy.tunnel.ike.json**.

4. Seleccione **Edit parameters** (Editar parámetros).

    ![El cuadro de diálogo "Panel > Nuevo > Implementar plantilla de solución > Parámetros" incluye un cuadro de texto denominado "Grupo de recursos" y un botón de radio. El botón "Usar existente" está seleccionado, y el texto que aparece indica "AppTier". Se muestran otros tres cuadros de texto resaltados que incluyen parámetros de plantilla.](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5. Compruebe que AppTier está seleccionado y que la red interna remota está establecida en 10.99.0.1.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Confirmación del túnel entre el nivel de aplicación y el nivel de base de datos

1. Para comprobar el túnel sin iniciar sesión en la máquina virtual, ejecute una extensión de script personalizado.

2. Vaya a la máquina virtual de RRAS (AppTier).

3. Seleccione **Extensions** (Extensiones) y **ejecute la extensión del script personalizado**.

4. Vaya al directorio scripts del repositorio **azure-intelligent-edge-patterns/rras-vnet-vpntunnel**. Seleccione **Get-VPNS2SInterfaceStatus.ps1**.

    ![El cuadro de diálogo es "Panel > Grupos de recursos > AppTier > AppTier-RRAS - Extensions (RRAS de AppTier: extensiones) > CustomScriptExtension". El valor del campo ESTADO DETALLADO es "Aprovisionamiento realizado correctamente". Los detalles se encuentran en una superposición de ventana del Bloc de notas.](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5. Si habilita RDP e inicia sesión, abra PowerShell y ejecute `get-vpns2sinterface`, puede ver que el túnel está conectado.

    **DBTier**

    ![En DBTier, la ventana de PowerShell muestra la ejecución del comando Get-VpnS2SInterface, que muestra los detalles de la interfaz de sitio a sitio. El valor de ConnectionState es Conectado.](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![En AppTier, la ventana de PowerShell muestra la ejecución del comando Get-VpnS2SInterface, que muestra los detalles de la interfaz de sitio a sitio. El valor de ConnectionState es Conectado para dos destinos.](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!NOTE]  
    > Se puede probar RDP tanto desde una máquina hasta la segunda como desde la segunda hasta la primera.

    > [!NOTE]  
    > Para implementar esta solución en un entorno local, necesitará implementar rutas de la red remota de Azure Stack Hub en la infraestructura que cambia o, como mínimo, en máquinas virtuales concretas.

### <a name="deploying-a-gre-tunnel"></a>Implementación de un túnel GRE

Para esta plantilla, este tutorial ha usado la [plantilla de IKE](network-howto-vpn-tunnel-ipsec.md). Sin embargo, también se puede implementar un [túnel GRE](network-howto-vpn-tunnel-gre.md). Este túnel ofrece un mayor rendimiento.

El proceso es casi idéntico. Sin embargo, cuando implemente la plantilla de túnel en la infraestructura existente, deberá usar las salidas del otro sistema para las tres primeras entradas. Deberá conocer el valor de **LOCALTUNNELGATEWAY** para el grupo de recursos en el que va a realizar la implementación, en lugar del grupo de recursos al que intenta conectarse.

![El cuadro de diálogo "Panel > Nuevo > Implementar plantilla de solución > Parámetros" incluye un cuadro de texto denominado "Grupo de recursos" y un botón de radio. El botón "Usar existente" está seleccionado, y el texto que aparece indica "AppTier". Otros cuatro cuadros de texto resaltados con datos de salidas de WebTier.](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md)  
[Creación de un túnel de VPN mediante GRE](network-howto-vpn-tunnel-gre.md)  
[Creación de un túnel de VPN mediante IPSEC](network-howto-vpn-tunnel-ipsec.md)