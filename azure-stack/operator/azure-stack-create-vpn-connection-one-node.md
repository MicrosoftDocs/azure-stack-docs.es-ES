---
title: Creación de una conexión VPN de sitio a sitio entre dos redes virtuales en diferentes entornos de ASDK
description: Tutorial para operadores en la nube sobre creación de una conexión VPN de sitio a sitio entre dos entornos del Kit de desarrollo de Azure Stack (ASDK) de un solo nodo.
author: justinha
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 01/22/2020
ms.openlocfilehash: 84e32744a2a337f9a6c64c558195de02e3a7db92
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489171"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-asdk-environments"></a>Creación de una conexión VPN de sitio a sitio entre dos redes virtuales en diferentes entornos de ASDK

## <a name="overview"></a>Información general

En este artículo se describe cómo crear una conexión VPN de sitio a sitio entre dos redes virtuales en dos entornos independientes del Kit de desarrollo de Azure Stack (ASDK). Al configurar las conexiones, aprenderá cómo funcionan las puertas de enlace de VPN en Azure Stack Hub.

### <a name="connection"></a>Conexión

En la ilustración siguiente se muestra el aspecto que debería tener la configuración de la conexión cuando haya terminado.

![Configuración de una conexión VPN de sitio a sitio](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Antes de empezar

Para completar esta configuración de conexión, asegúrese de tener los elementos siguientes antes de empezar:

* Dos servidores y otros requisitos previos que cumplen con los requisitos de hardware del ASDK, tal como se describe en [Inicio rápido: Evaluación del Kit de desarrollo de Azure Stack](../asdk/asdk-download.md).
* El paquete de implementación de [ASDK](https://azure.microsoft.com/overview/azure-stack/try/).

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Implementación de los entornos del Kit de desarrollo de Azure Stack

Para completar la configuración de conexión, debe implementar dos entornos del ASDK.

> [!NOTE]
> Siga las [instrucciones de implementación](../asdk/asdk-install.md) para cada ASDK que implemente. En este artículo, los entornos de ASDK se llaman **POC1** y **POC2**.

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Preparación de una oferta en POC1 y POC2

En POC1 y POC2, prepare una oferta para que un usuario pueda suscribirse a la oferta e implementar las máquinas virtuales. Para más información sobre cómo crear una oferta, consulte [Máquinas virtuales disponibles para los usuarios de Azure Stack Hub](./tutorial-offer-services.md?view=azs-2002).

## <a name="review-and-complete-the-network-configuration-table"></a>Revisión y realización de la tabla de configuración de red

En la tabla siguiente se resume la configuración de red para ambos entornos de ASDK. Utilice el procedimiento que aparece después de la tabla para agregar la dirección de BGPNAT externa específica de la red.

### <a name="network-configuration-table"></a>Tabla de configuración de red

|   |POC1|POC2|
|---------|---------|---------|
|Nombre de la red virtual     |VNET-01|VNET-02 |
|Espacio de direcciones de red virtual |10.0.10.0/23|10.0.20.0/23|
|Nombre de subred     |Subnet-01|Subnet-02|
|Intervalo de direcciones de subred|10.0.10.0/24 |10.0.20.0/24 |
|Subred de puerta de enlace     |10.0.11.0/24|10.0.21.0/24|
|Dirección BGPNAT externa     |         |         |

> [!NOTE]
> Las direcciones IP BGPNAT externas en el entorno de ejemplo son 10.16.167.195 para POC1 y 10.16.169.131 para POC2. Utilice el procedimiento siguiente para determinar las direcciones IP BGPNAT externas de los hosts del ASDK y, a continuación, agréguelos a la tabla de configuración de red anterior.

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obtención de la dirección IP del adaptador externo de la máquina virtual de NAT

1. Inicie sesión en la máquina física de Azure Stack Hub para POC1.
2. Abra PowerShell como administrador y ejecute el siguiente cmdlet:

   ```powershell
   Get-NetNatExternalAddress
   ```

3. Agregue la dirección IP a la tabla de configuración de red que aparece en la sección anterior.
4. Repita este procedimiento en POC2.

## <a name="create-the-network-resources-in-poc1"></a>Creación de los recursos de red en POC1

Ahora puede crear los recursos de red de POC1 que se necesitan para configurar las puertas de enlace. Las instrucciones siguientes describen cómo crear los recursos mediante el portal de usuarios de Azure Stack Hub. También puede usar código de PowerShell para crear los recursos.

![Flujo de trabajo para crear recursos](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Inicio de sesión como un inquilino

Un administrador de servicios puede iniciar sesión como un inquilino para probar los planes, ofertas y suscripciones que pueden usar sus inquilinos. Si aún no tiene una, [cree una cuenta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sesión.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creación de la red virtual y la subred de máquina virtual

1. Use una cuenta de inquilino para iniciar sesión en el portal de usuario.
2. En el portal de usuario, seleccione **+ Crear un recurso**.
3. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
4. Seleccione **Red virtual**.
5. Para **Nombre**, **Espacio de direcciones**, **Nombre de subred** e **Intervalo de direcciones de subred**, utilice los valores que aparecen anteriormente en la tabla de configuración de red.
6. En **Suscripción** aparece la suscripción que creó anteriormente.
7. Para **Grupo de recursos**, puede crear un grupo de recursos o, si ya tiene uno, seleccione **Usar existente**.
8. Compruebe la ubicación predeterminada.
9. Seleccione **Anclar al panel**.
10. Seleccione **Crear**.

### <a name="create-the-gateway-subnet"></a>Creación de la subred de la puerta de enlace

1. En el panel, abra el recurso de red virtual VNET-01 que creó anteriormente.
2. En la hoja **Configuración**, seleccione **Subredes**.
3. Seleccione **Subred de puerta de enlace** para agregar una subred de puerta de enlace a la red virtual.

    ![Agregación de subred de puerta de enlace](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. El nombre de la subred se establece en **GatewaySubnet** de forma predeterminada. Las subredes de puerta de enlace son especiales. Para que funcione correctamente, deben utilizar el nombre **GatewaySubnet**.
5. En **Intervalo de direcciones**, compruebe que la dirección es **10.0.11.0/24**.
6. Seleccione **Aceptar** para crear la subred de puerta de enlace.

### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual

1. En Azure Portal, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. Seleccione **Puerta de enlace de red virtual** en la lista de recursos de red.
4. En **Nombre**, escriba **GW1**.
5. Seleccione el elemento **Red virtual** para elegir una red virtual. Seleccione **VNET-01** en la lista.
6. Seleccione el elemento de menú **Dirección IP pública**. Cuando la ventana **Elegir dirección IP pública** se abra, seleccione **Crear nuevo**.
7. En **Nombre**, escriba **GW1-PiP** y, a continuación, seleccione **Aceptar**.
8. De forma predeterminada, para **Tipo de VPN**, está seleccionado **Basada en enrutamiento**. Mantenga el tipo de VPN **Basada en enrutamiento**.
9. Compruebe que la **Suscripción** y la **Ubicación** son correctas. Puede anclar el recurso en el panel. Seleccione **Crear**.

### <a name="create-the-local-network-gateway"></a>Creación de la puerta de enlace de red local

La implementación de una *puerta de enlace de red local* en esta implementación de evaluación de Azure Stack Hub es ligeramente diferente de la de una implementación real de Azure.

En una implementación de Azure, una puerta de enlace de red local representa un dispositivo físico local (en el inquilino) que se usa para conectar a una puerta de enlace de red virtual en Azure. En esta implementación de evaluación de Azure Stack Hub, ambos extremos de la conexión son puertas de enlace de red virtual.

Una manera de ver esto de forma más genérica es que el recurso de la puerta de enlace de red local siempre indica la puerta de enlace remota en el otro extremo de la conexión. Debido al modo en que se diseñó el Kit de desarrollo de Azure Stack, debe proporcionar la dirección IP del adaptador de red externo en la máquina virtual de traducción de direcciones de red (NAT) del otro entorno del ASDK como la dirección IP pública de la puerta de enlace de red local. A continuación, cree asignaciones de NAT en la máquina virtual de NAT para asegurarse de que ambos extremos están conectados correctamente.

### <a name="create-the-local-network-gateway-resource"></a>Creación del recurso de puerta de enlace de red local

1. Inicie sesión en la máquina física de Azure Stack Hub para POC1.
2. En el portal de usuario, seleccione **+ Crear un recurso**.
3. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
4. En la lista de recursos, seleccione **Puerta de enlace de red local**.
5. En **Nombre**, escriba **POC2-GW**.
6. En **Dirección IP**, escriba la dirección BGPNAT externa de POC2. Esta dirección aparece antes en la tabla de configuración de red.
7. En **Espacio de direcciones**, para el espacio de direcciones de la red virtual de POC2 que se va a crear, escriba **10.0.20.0/23**.
8. Compruebe que los valores de **Suscripción**, **Grupo de recursos** y **Ubicación** son correctos y seleccione **Crear**.

### <a name="create-the-connection"></a>Creación de la conexión

1. En el portal de usuario, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. Seleccione **Conexión** en la lista de recursos.
4. En la hoja de configuración **Básico**, elija **Sitio a sitio (IPSec)** como **Tipo de conexión**.
5. Seleccione la **Suscripción**, **Grupo de recursos** y **Ubicación** y, a continuación, seleccione **Aceptar**.
6. En la hoja **Configuración**, seleccione **Puerta de enlace de red virtual** y, a continuación, seleccione **GW1**.
7. Seleccione **Puerta de enlace de red local** y, a continuación, seleccione **POC2-GW**.
8. En **Nombre de la conexión**, escriba **POC1-POC2**.
9. En **Clave compartida (PSK)** , escriba **12345** y, a continuación, seleccione **Aceptar**.
10. En la hoja **Resumen**, seleccione **Aceptar**.

### <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Para validar los datos que se desplazan a través de la conexión VPN, es necesario que las máquinas virtuales envíen y reciban datos en cada ASDK. Cree una máquina virtual en POC1 ahora y colóquela en la subred de máquina virtual en la red virtual:

1. En Azure Portal, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Compute**.
3. En la lista de imágenes de máquina virtual, seleccione la imagen **Windows Server 2016 Datacenter Eval**.
4. En la hoja **Básico**, en **Nombre**, escriba **VM01**.
5. Escriba un nombre de usuario válido y una contraseña. Usará esta cuenta para iniciar sesión en la máquina virtual una vez creada.
6. Proporcione una **Suscripción**, **Grupo de recursos** y **Ubicación** y, a continuación, seleccione **Aceptar**.
7. En la hoja **Tamaño**, para esta instancia, seleccione un tamaño de máquina virtual y, a continuación, seleccione **Seleccionar**.
8. En la hoja **Configuración**, acepte los valores predeterminados. Asegúrese de que la red virtual **VNET-01** está seleccionada. Compruebe que la subred está establecida en **10.0.10.0/24**. Después, seleccione **Aceptar**.
9. En la hoja **Resumen**, revise la configuración y, a continuación, seleccione **Aceptar**.

## <a name="create-the-network-resources-in-poc2"></a>Creación de los recursos de red en POC2

El siguiente paso es crear los recursos de red para POC2. Las instrucciones siguientes muestran cómo crear los recursos mediante el portal de usuario.

### <a name="sign-in-as-a-tenant-again"></a>Nuevo inicio de sesión como un inquilino

Un administrador de servicios puede iniciar sesión como un inquilino para probar los planes, ofertas y suscripciones que pueden usar sus inquilinos. Si aún no tiene una, [cree una cuenta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sesión.

### <a name="create-virtual-network-and-vm-subnet"></a>Creación de la red virtual y la subred de máquina virtual

1. Inicie sesión con una cuenta de inquilino.
2. En el portal de usuario, seleccione **+ Crear un recurso**.
3. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
4. Seleccione **Red virtual**.
5. Use la información que apareció anteriormente en la tabla de configuración de red para identificar los valores de los campos **Nombre**, **Espacio de direcciones**, **Nombre de subred** e **Intervalo de direcciones de subred** de POC2.
6. En **Suscripción** aparece la suscripción que creó anteriormente.
7. Para **Grupo de recursos**, cree un nuevo grupo de recursos o, si ya tiene uno, seleccione **Usar existente**.
8. Compruebe la **Ubicación** predeterminada.
9. Seleccione **Anclar al panel**.
10. Seleccione **Crear**.

### <a name="create-gateway-subnet"></a>Creación de una subred de puerta de enlace

1. Abra el recurso de red virtual que acaba de crear (**VNET-02**) desde el panel.
2. En la hoja **Configuración**, seleccione **Subredes**.
3. Seleccione **Subred de puerta de enlace** para agregar una subred de puerta de enlace a la red virtual.
4. El nombre de la subred se establece como **GatewaySubnet** de forma predeterminada. Las subredes de puerta de enlace son especiales y tienen que tener este nombre específico para funcionar correctamente.
5. En el campo **Intervalo de direcciones**, compruebe que la dirección es **10.0.21.0/24**.
6. Seleccione **Aceptar** para crear la subred de puerta de enlace.

### <a name="create-virtual-network-gateway"></a>Creación de puerta de enlace de red virtual

1. En Azure Portal, seleccione **+ Crear un recurso**.  
2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. Seleccione **Puerta de enlace de red virtual** en la lista de recursos de red.
4. En **Nombre**, escriba **GW2**.
5. Seleccione **Red virtual** para elegir una red virtual. A continuación, seleccione **VNET-02** en la lista.
6. Seleccione **Dirección IP pública**. Cuando la hoja **Elegir dirección IP pública** se abra, seleccione **Crear nuevo**.
7. En **Nombre**, escriba **GW2-PiP** y, a continuación, seleccione **Aceptar**.
8. De forma predeterminada, en **Tipo de VPN** está seleccionado **Basada en rutas**. Mantenga el tipo de VPN **Basada en enrutamiento**.
9. Compruebe que la **Suscripción** y la **Ubicación** son correctas. Puede anclar el recurso en el panel. Seleccione **Crear**.

### <a name="create-local-network-gateway-resource"></a>Creación del recurso de puerta de enlace de red local

1. En el portal de usuario de POC2, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. En la lista de recursos, seleccione **Puerta de enlace de red local**.
4. En **Nombre**, escriba **POC1-GW**.
5. En **Dirección IP**, escriba la dirección BGPNAT externa de POC1 que se indicó anteriormente en la tabla de configuración de red.
6. En **Espacio de direcciones**, de POC1, escriba el espacio de direcciones **10.0.10.0/23** de **VNET-01**.
7. Compruebe que la **Suscripción**, **Grupo de recursos** y **Ubicación** son correctos y seleccione **Crear**.

## <a name="create-connection"></a>Crear conexión

1. En el portal de usuario, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. Seleccione **Conexión** en la lista de recursos.
4. En la hoja de configuración **Básico**, para **Tipo de conexión**, elija **Sitio a sitio (IPSec)** .
5. Seleccione la **Suscripción**, **Grupo de recursos** y **Ubicación** y, a continuación, seleccione **Aceptar**.
6. En la hoja **Configuración**, seleccione **Puerta de enlace de red virtual** y, a continuación, seleccione **GW2**.
7. Seleccione **Puerta de enlace de red local** y, a continuación, seleccione **POC1-GW**.
8. En **Nombre de la conexión**, escriba **POC2-POC1**.
9. En **Clave compartida (PSK)** , escriba **12345**. Si elige un valor diferente, recuerde que tiene que coincidir con el valor de la clave compartida que creó en POC1. Seleccione **Aceptar**.
10. Revise la hoja **Resumen** y, a continuación, seleccione **Aceptar**.

## <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Cree una máquina virtual en POC2 ahora y colóquela en la subred de máquina virtual de la red virtual:

1. En Azure Portal, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Compute**.
3. En la lista de imágenes de máquina virtual, seleccione la imagen **Windows Server 2016 Datacenter Eval**.
4. En la hoja **Básico**, en **Nombre**, escriba **VM02**.
5. Escriba un nombre de usuario válido y una contraseña. Usará esta cuenta para iniciar sesión en la máquina virtual una vez creada.
6. Proporcione una **Suscripción**, **Grupo de recursos** y **Ubicación** y, a continuación, seleccione **Aceptar**.
7. En la hoja **Tamaño**, seleccione un tamaño de máquina virtual para esta instancia y, a continuación, seleccione **Seleccionar**.
8. En la hoja **Configuración**, acepte los valores predeterminados. Asegúrese de que la red virtual **VNET-02** está seleccionada y compruebe que la subred está establecida en **10.0.20.0/24**. Seleccione **Aceptar**.
9. En la hoja **Resumen**, revise la configuración y, a continuación, seleccione **Aceptar**.

## <a name="configure-the-nat-vm-on-each-asdk-for-gateway-traversal"></a>Configuración de la máquina virtual de NAT en cada ASDK para el cruce seguro de puerta de enlace

Dado que el ASDK es independiente y aislado de la red en la que se implementa el host físico, la red VIP *externa* a la que están conectadas las puertas de enlace no es realmente externa. En su lugar, la red de VIP está oculta detrás de un enrutador que realiza la traducción de direcciones de red.

El enrutador es una máquina virtual Windows Server llamada **AzS-bgpnat01**, que ejecuta el rol de Enrutamiento y servicios de acceso remoto (RRAS) en la infraestructura del ASDK. Tiene que configurar NAT en la máquina virtual AzS-bgpnat01 para permitir la conexión de VPN de sitio a sitio para conectarse en ambos extremos.

Para configurar la conexión VPN, debe crear una ruta de asignación de NAT estática que asigne la interfaz externa en la máquina virtual BGPNAT a la dirección VIP del grupo de puerta de enlace de perímetro. Se necesita una ruta de asignación de NAT estática para cada puerto en una conexión VPN.

> [!NOTE]
> Esta configuración es necesaria solo para los entornos de ASDK.

### <a name="configure-the-nat"></a>Configuración de la NAT

> [!IMPORTANT]
> Debe realizar este procedimiento para ambos entornos del ASDK.

1. Determine la **Dirección IP interna** a utilizar en el siguiente script de PowerShell. Abra la puerta de enlace de red virtual (GW1 y GW2). En la hoja **Información general**, guarde el valor de **Dirección IP pública** para su uso posterior.

   ![Dirección IP interna](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. Inicie sesión en la máquina física de Azure Stack Hub para POC1.
3. Copie y edite el siguiente script de PowerShell. Para configurar la NAT en cada Kit de desarrollo de Azure Stack, ejecute el script en un equipo con Windows PowerShell ISE con privilegios elevados. En el script, agregue valores a los marcadores de posición `External BGPNAT address` y `Internal IP address`:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Repita este procedimiento en POC2.

## <a name="test-the-connection"></a>Comprobación de la conexión

Ahora que se ha establecido la conexión de sitio a sitio, debe comprobar que puede hacer que fluya tráfico a través de ella. Para validar, inicie sesión en una de las máquinas virtuales que creó en el entorno del ASDK. A continuación, haga ping a la máquina virtual que creó en el otro entorno.

Para confirmar que el tráfico se envía a través de la conexión de sitio a sitio, asegúrese de que hace ping a la dirección IP directa (DIP) de la máquina virtual en la subred remota, no a la dirección VIP. Para ello, busque la dirección DIP en el otro extremo de la conexión. Guarde la dirección para su uso posterior.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Inicio de sesión en la máquina virtual del inquilino en POC1

1. Inicie sesión en la máquina física de Azure Stack Hub para POC1 e inicie sesión en el portal de usuarios con una cuenta de inquilino.
2. En la barra de navegación de la izquierda, seleccione **Compute**.
3. Encuentre la máquina **VM01** que creó anteriormente en la lista de máquinas virtuales y selecciónela.
4. En la hoja de la máquina virtual, haga clic en **Conectar** y, a continuación, abra el archivo VM01.rdp.

     ![Botón Conectar](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Inicie sesión con la cuenta que configuró al crear la máquina virtual.
6. Abra una ventana de **Windows PowerShell** con privilegios elevados.
7. Escriba **ipconfig /all**.
8. En la salida, busque la **dirección IPv4** y, a continuación, guarde la dirección para su uso posterior. Esta es la dirección a la que hará ping desde POC2. En el entorno de ejemplo, la dirección es **10.0.10.4**, pero en su entorno puede ser diferente. Debe estar dentro de la subred **10.0.10.0/24** que se creó anteriormente.
9. Para crear una regla de firewall que permita que la máquina virtual responda a los pings, ejecute el siguiente comando de PowerShell:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Inicio de sesión en la máquina virtual del inquilino en POC2

1. Inicie sesión en la máquina física de Azure Stack Hub para POC2 e inicie sesión en el portal de usuarios con una cuenta de inquilino.
2. En la barra de navegación de la izquierda, haga clic en **Compute**.
3. Encuentre la máquina **VM02** que creó anteriormente en la lista de máquinas virtuales y selecciónela.
4. En la hoja de la máquina virtual, haga clic en **Conectar**.
5. Inicie sesión con la cuenta que configuró al crear la máquina virtual.
6. Abra una ventana de **Windows PowerShell** con privilegios elevados.
7. Escriba **ipconfig /all**.
8. Aparece una dirección IPv4 que se encuentra dentro del rango **10.0.20.0/24**. En el entorno de ejemplo, la dirección es **10.0.20.4**, pero en su entorno puede ser diferente.
9. Para crear una regla de firewall que permita que la máquina virtual responda a los pings, ejecute el siguiente comando de PowerShell:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. Desde la máquina virtual de POC2, haga ping a la máquina virtual de POC1 a través del túnel. Para ello, haga ping a la dirección DIP que anotó de VM01. En el entorno de ejemplo la dirección es **10.0.10.4**, pero asegúrese de hacer ping a la dirección que haya anotado en su laboratorio. Debe ver un resultado similar al ejemplo siguiente:

    ![Ping correcto](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Una respuesta de la máquina virtual remota indica que la prueba ha dado un resultado correcto. Puede cerrar la ventana de la máquina virtual. O puede intentar realizar alguna otra transferencia de datos, como una copia de archivo, para probar la conexión.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visualización de las estadísticas de transferencia de datos a través de la conexión de puerta de enlace

Si desea saber qué cantidad de datos pasa a través de la conexión de sitio a sitio, esta información está disponible en la hoja **Conexión**. Esta prueba también es otra forma de comprobar que el ping que acaba de enviar ha ido a través de la conexión VPN.

1. Mientras ha iniciado sesión en la máquina virtual del inquilino en POC2, use la cuenta de inquilino para iniciar sesión en el portal de usuario.
2. Vaya a **Todos los recursos** y, a continuación, seleccione la conexión **POC2-POC1**. Aparece **Conexiones**.
3. En la ventana **Conexión**, aparecen las estadísticas de **Datos de entrada** y **Datos de salida**. En la siguiente captura de pantalla, los números grandes se atribuyen a transferencias de archivos adicionales. Debería ver algunos valores distintos de cero.

    ![Datos de entrada y salida](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
