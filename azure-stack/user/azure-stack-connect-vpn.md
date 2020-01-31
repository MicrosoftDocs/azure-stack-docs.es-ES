---
title: Conexión de Azure Stack Hub a Azure mediante VPN
description: Cómo conectar redes virtuales en Azure Stack Hub a redes virtuales en Azure mediante VPN.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: c0a03b453a574b04e6b4a8654e3375d552acc5f5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883912"
---
# <a name="connect-azure-stack-hub-to-azure-using-vpn"></a>Conexión de Azure Stack Hub a Azure mediante VPN

En este artículo se describe cómo crear una VPN de sitio a sitio para conectar una red virtual en Azure Stack Hub a una red virtual en Azure.

## <a name="before-you-begin"></a>Antes de empezar

Para completar esta configuración de conexión, asegúrese de tener los elementos siguientes antes de empezar:

* Una implementación de sistemas integrados (varios nodos) de Azure Stack Hub que esté conectada directamente a Internet. Al intervalo de direcciones IP públicas externas se debe poder acceder directamente desde Internet público.
* Una suscripción válida a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita de Azure aquí](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Diagrama de conexión de VPN

La ilustración siguiente muestra el aspecto que debería tener la configuración de la conexión cuando haya terminado:

![Configuración de una conexión VPN de sitio a sitio](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Valores de ejemplo de configuración de la red

La tabla de ejemplos de configuración de la red muestra los valores que se usan para los ejemplos de este artículo. Puede usar estos valores o hacer referencia a ellos para comprender mejor los ejemplos de este artículo:

|   |Azure Stack Hub|Azure|
|---------|---------|---------|
|Nombre de la red virtual     |Azs-VNet|AzureVNet |
|Espacio de direcciones de red virtual |10.1.0.0/16|10.100.0.0/16|
|Nombre de subred     |FrontEnd|FrontEnd|
|Intervalo de direcciones de subred|10.1.0.0/24 |10.100.0.0/24 |
|Subred de puerta de enlace     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Creación de recursos de red en Azure

En primer lugar, cree los recursos de red para Azure. Las instrucciones siguientes muestran cómo crear los recursos mediante [Azure Portal](https://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Creación de la red virtual y la subred de la máquina virtual (VM)

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con su cuenta de Azure.

2. En el portal de usuario, seleccione **+ Crear un recurso**.
3. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
4. Seleccione **Red virtual**.
5. Use la información de la tabla de configuración de red para identificar los valores de los campos **Nombre**, **Espacio de direcciones**, **Nombre de subred** e **Intervalo de direcciones de subred** de Azure.
6. Para **Grupo de recursos**, cree un nuevo grupo de recursos o, si ya tiene uno, seleccione **Usar existente**.
7. Seleccione la **Ubicación** de su VNet.  Si utiliza los valores del ejemplo, seleccione **Este de EE. UU.** o utilice otra ubicación.
8. Seleccione **Anclar al panel**.
9. Seleccione **Crear**.

### <a name="create-the-gateway-subnet"></a>Creación de la subred de la puerta de enlace

1. Abra el recurso de red virtual que acaba de crear (**AzureVNet**) desde el panel.
2. En la sección **Configuración**, seleccione **Subredes**.
3. Seleccione **Subred de puerta de enlace** para agregar una subred de puerta de enlace a la red virtual.
4. El nombre de la subred se establece como **GatewaySubnet** de forma predeterminada.

   >[!IMPORTANT]
   >Las subredes de puerta de enlace son especiales y tienen que tener este nombre específico para funcionar correctamente.

5. En el campo **Intervalo de direcciones**, compruebe que la dirección sea **10.100.1.0/24**.
6. Seleccione **Aceptar** para crear la subred de puerta de enlace.

### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual

1. En Azure Portal, seleccione **+ Crear un recurso**.

2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. Seleccione **Puerta de enlace de red virtual** en la lista de recursos de red.
4. En el campo **Nombre** escriba **Azure-GW**.
5. Seleccione **Red virtual** para elegir una red virtual. A continuación, seleccione **AzureVnet** en la lista.
6. Seleccione **Dirección IP pública**. Cuando se abra la sección **Elegir dirección IP pública**, seleccione **Crear nuevo**.
7. En el campo **Nombre**, escriba **Azure-GW-PiP** y seleccione **Aceptar**.
8. Compruebe que la **Suscripción** y la **Ubicación** son correctas. Puede anclar el recurso en el panel. Seleccione **Crear**.

### <a name="create-the-local-network-gateway-resource"></a>Creación del recurso de puerta de enlace de red local

1. En Azure Portal, seleccione **+ Crear un recurso**.

2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. En la lista de recursos, seleccione **Puerta de enlace de red local**.
4. En el campo **Nombre** escriba **Azs-GW**.
5. En el campo **Dirección IP**, escriba la dirección IP pública de la puerta de enlace de red virtual de Azure Stack Hub que se indicó anteriormente en la tabla de configuración de red.
6. En el campo **Espacio de direcciones**, en Azure Stack Hub, escriba el espacio de direcciones **10.1.0.0/24** y **10.1.1.0/24** para **AzureVNet**.
7. Compruebe que la **Suscripción**, **Grupo de recursos** y **Ubicación** son correctos y seleccione **Crear**.

## <a name="create-the-connection"></a>Creación de la conexión

1. En el portal de usuario, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. Seleccione **Conexión** en la lista de recursos.
4. En la sección de configuración **Básico**, para **Tipo de conexión**, elija **Sitio a sitio (IPSec)** .
5. Seleccione la **Suscripción**, **Grupo de recursos** y **Ubicación** y, a continuación, seleccione **Aceptar**.
6. En la sección **Configuración**, seleccione **Puerta de enlace de red virtual** y, a continuación, seleccione **Azure-GW**.
7. Seleccione **Puerta de enlace de red local** y, a continuación, seleccione **Azs-GW**.
8. En **Nombre de la conexión**, escriba **Azure-Azs**.
9. En **Clave compartida (PSK)** , escriba **12345** y, después, seleccione **Aceptar**.

   >[!NOTE]
   >Si usa otro valor diferente para la clave compartida, recuerde que tiene que coincidir con el valor de la clave compartida que creó en el otro extremo de la conexión.

10. Revise la sección **Resumen** y, a continuación, seleccione **Aceptar**.

## <a name="create-a-vm"></a>Crear una VM

Cree una máquina virtual en Azure ahora y colóquela en la subred de máquina virtual de la red virtual.

1. En Azure Portal, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Compute**.
3. En la lista de imágenes de máquina virtual, seleccione la imagen **Windows Server 2016 Datacenter Eval**.
4. En la sección **Conceptos básicos**, para **Nombre**, escriba **AzureVM**.
5. Escriba un nombre de usuario y una contraseña válidos. Usará esta cuenta para iniciar sesión en la máquina virtual una vez creada.
6. Proporcione una **Suscripción**, **Grupo de recursos** y **Ubicación** y, a continuación, seleccione **Aceptar**.
7. En la sección **Tamaño**, seleccione un tamaño de máquina virtual para esta instancia y, a continuación, seleccione **Seleccionar**.
8. En la sección **Configuración**, puede usar la configuración predeterminada. Antes de seleccionar **Aceptar**, confirme que:

   * La red virtual **AzureVnet** está seleccionada.
   * La subred está establecida en **10.100.0.0/24**.

   Seleccione **Aceptar**.

9. Revise la configuración de la sección **Resumen** y seleccione **Aceptar**.

## <a name="create-the-network-resources-in-azure-stack-hub"></a>Creación de recursos de red en Azure Stack Hub

Después, cree los recursos de red en Azure Stack Hub.

### <a name="sign-in-as-a-user"></a>Inicio de sesión como usuario

Un administrador de servicios puede iniciar sesión como usuario para probar los planes, ofertas y suscripciones que pueden usar sus usuarios. Si aún no tiene una, [cree una cuenta de usuario](../operator/azure-stack-add-new-user-aad.md) antes de iniciar sesión.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Creación de la red virtual y una subred de máquina virtual

1. Use una cuenta de usuario para iniciar sesión en el portal de usuario.
2. En el portal de usuario, seleccione **+ Crear un recurso**.

    ![Creación de una nueva red virtual](media/azure-stack-connect-vpn/image3.png)

3. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
4. Seleccione **Red virtual**.
5. Para **Nombre**, **Espacio de direcciones**, **Nombre de subred** e **Intervalo de direcciones de subred**, utilice los valores de la tabla de configuración de red.
6. En **Suscripción** aparece la suscripción que creó anteriormente.
7. Para **Grupo de recursos**, puede crear un grupo de recursos o, si ya tiene uno, seleccione **Usar existente**.
8. Compruebe la ubicación predeterminada.
9. Seleccione **Anclar al panel**.
10. Seleccione **Crear**.

### <a name="create-the-gateway-subnet"></a>Creación de la subred de la puerta de enlace

1. Abra el panel, abra el recurso de red virtual Azs-VNet que acaba de crear.
2. En la sección **Configuración**, seleccione **Subredes**.
3. Seleccione **Subred de puerta de enlace** para agregar una subred de puerta de enlace a la red virtual.

    ![Agregación de subred de puerta de enlace](media/azure-stack-connect-vpn/image4.png)

4. El nombre de la subred se establece en **GatewaySubnet** de forma predeterminada. Para que las subredes de puerta de enlace funcione correctamente, deben utilizar el nombre **GatewaySubnet**.
5. En **Intervalo de direcciones**, compruebe que la dirección sea **10.1.1.0/24**.
6. Seleccione **Aceptar** para crear la subred de puerta de enlace.

### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual

1. En el portal de Azure Stack Hub, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. Seleccione **Puerta de enlace de red virtual** en la lista de recursos de red.
4. En **Nombre**, escriba **Azs-GW**.
5. Seleccione el elemento **Red virtual** para elegir una red virtual. Seleccione **Azs-VNet** en la lista.
6. Seleccione el elemento de menú **Dirección IP pública**. Cuando se abra la sección **Elegir dirección IP pública**, seleccione **Crear nuevo**.
7. En **Nombre**, escriba **Azs-GW-PiP** y seleccione **Aceptar**.
8. De forma predeterminada, en **Tipo de VPN** está seleccionado **Basada en rutas**. Mantenga el tipo de VPN **Basada en enrutamiento**.

9. Compruebe que la **Suscripción** y la **Ubicación** son correctas. Puede anclar el recurso en el panel. Seleccione **Crear**.

### <a name="create-the-local-network-gateway"></a>Creación de la puerta de enlace de red local

El concepto de una *puerta de enlace de red local* en Azure Stack Hub es un algo diferente del de en una implementación de Azure.

En una implementación de Azure, una puerta de enlace de red local representa un dispositivo físico local (en la ubicación del usuario) que se conecta a una puerta de enlace de red virtual en Azure. Sin embargo, en Azure Stack Hub, ambos extremos de la conexión son puertas de enlace de red virtual.

Una descripción más genérica es que el recurso de la puerta de enlace de red local siempre indica la puerta de enlace remota en el otro extremo de la conexión.

### <a name="create-the-local-network-gateway-resource"></a>Creación del recurso de puerta de enlace de red local

1. Inicie sesión en el portal de Azure Stack Hub.
2. En el portal de usuario, seleccione **+ Crear un recurso**.
3. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
4. En la lista de recursos, seleccione **Puerta de enlace de red local**.
5. En el campo **Nombre** escriba **Azure-GW**.
6. En el campo **dirección IP**, escriba la dirección IP pública para la puerta de enlace de red virtual de Azure **Azure-GW-PiP**. Esta dirección aparece antes en la tabla de configuración de red.
7. En el campo **Espacio de direcciones**, para el espacio de direcciones de la red virtual de Azure que creó, escriba **10.100.0.0/24** y **10.100.1.0/24**.

8. Compruebe que los valores de **Suscripción**, **Grupo de recursos** y **Ubicación** son correctos y seleccione **Crear**.

### <a name="create-the-connection"></a>Creación de la conexión

1. En el portal de usuario, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Redes**.
3. Seleccione **Conexión** en la lista de recursos.
4. En la sección de configuración **Básico**, elija **Sitio a sitio (IPSec)** como **Tipo de conexión**.
5. Seleccione la **Suscripción**, **Grupo de recursos** y **Ubicación** y, a continuación, seleccione **Aceptar**.
6. En la sección **Configuración**, seleccione **Puerta de enlace de red virtual** y, a continuación, seleccione **Azs-GW**.
7. Seleccione **Puerta de enlace de red local** y, a continuación, seleccione **Azure-GW**.
8. En **Nombre de la conexión**, escriba **Azs-Azure**.
9. En **Clave compartida (PSK)** , escriba **12345** y, a continuación, seleccione **Aceptar**.

10. En la sección **Resumen**, seleccione **Aceptar**.

### <a name="create-a-vm"></a>Crear una VM

Para comprobar la conexión VPN, cree dos máquinas virtuales: una en Azure y otra en Azure Stack Hub. Después de crear estas máquinas virtuales, puede usarlas para enviar y recibir datos a través del túnel VPN.

1. En Azure Portal, seleccione **+ Crear un recurso**.
2. Vaya a **Marketplace** y, a continuación, seleccione **Compute**.
3. En la lista de imágenes de máquina virtual, seleccione la imagen **Windows Server 2016 Datacenter Eval**.
4. En la sección **Conceptos básicos**, en **Nombre**, escriba **Azs-VM**.
5. Escriba un nombre de usuario y una contraseña válidos. Usará esta cuenta para iniciar sesión en la máquina virtual una vez creada.
6. Proporcione una **Suscripción**, **Grupo de recursos** y **Ubicación** y, a continuación, seleccione **Aceptar**.
7. En la sección **Tamaño**, para esta instancia, seleccione un tamaño de máquina virtual y, a continuación, seleccione **Seleccionar**.
8. En la sección **Configuración**, acepte los valores predeterminados. Asegúrese de que la red virtual **Azs-VNet** esté seleccionada. Compruebe que la subred esté establecida en **10.1.0.0/24**. Después, seleccione **Aceptar**.

9. En la sección **Resumen**, revise la configuración y luego seleccione **Aceptar**.

## <a name="test-the-connection"></a>Comprobación de la conexión

Después de que se establece la conexión de sitio a sitio, debe comprobar que puede hacer que los datos fluyan en ambas direcciones. La forma más fácil de probar la conexión es realizar una prueba del ping:

* Inicie sesión en la máquina virtual que creó en Azure Stack Hub y haga ping en la máquina virtual de Azure.
* Inicie sesión en la máquina virtual que creó en Azure y haga ping en la máquina virtual de Azure Stack Hub.

>[!NOTE]
>Para asegurarse de que envía el tráfico a través de la conexión de sitio a sitio, haga ping a la dirección IP directa (DIP) de la máquina virtual en la subred remota, no a la IP virtual.

### <a name="sign-in-to-the-user-vm-in-azure-stack-hub"></a>Inicio de sesión en la máquina virtual de usuario en Azure Stack Hub

1. Inicie sesión en el portal de Azure Stack Hub.
2. En la barra de navegación izquierda, seleccione **Máquinas virtuales**.
3. Encuentre la máquina **Azs-VM** que creó anteriormente en la lista de máquinas virtuales y selecciónela.
4. En la sección de la máquina virtual, seleccione **Conectar** y, después, abra el archivo Azs-VM.rdp.

     ![Botón Conectar](media/azure-stack-connect-vpn/image17.png)

5. Inicie sesión con la cuenta que configuró al crear la máquina virtual.
6. Abra un símbolo del sistema de Windows PowerShell con privilegios elevados.
7. Escriba **ipconfig/all**.
8. En la salida, busque la **dirección IPv4** y, a continuación, guarde la dirección para su uso posterior. Esta es la dirección a la que hará ping desde Azure. En el entorno de ejemplo, la dirección es **10.1.0.4**, pero en su entorno puede ser diferente. Debe estar dentro de la subred **10.1.0.0/24** que se creó anteriormente.
9. Para crear una regla de firewall que permita que la máquina virtual responda a los pings, ejecute el siguiente comando de PowerShell:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Inicio de sesión en la máquina virtual del inquilino en Azure

1. Inicie sesión en Azure Portal.
2. En la barra de navegación izquierda, seleccione **Máquinas virtuales**.
3. Encuentre la máquina **Azure-VM** que creó anteriormente en la lista de máquinas virtuales y selecciónela.
4. En la sección de la máquina virtual, seleccione **Conectar**.
5. Inicie sesión con la cuenta que configuró al crear la máquina virtual.
6. Abra una ventana de **Windows PowerShell** con privilegios elevados.
7. Escriba **ipconfig/all**.
8. Debería ver una dirección IPv4 que se encuentre dentro de **10.100.0.0/24**. En el entorno de ejemplo, la dirección es **10.100.0.4**, pero en su entorno puede ser diferente.
9. Para crear una regla de firewall que permita que la máquina virtual responda a los pings, ejecute el siguiente comando de PowerShell:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. Desde la máquina virtual de Azure, haga ping a la máquina virtual de Azure Stack Hub, a través del túnel. Para ello, haga ping a la DIP que registró de Azs-VM. En el entorno de ejemplo la dirección es **10.1.0.4**, pero asegúrese de hacer ping a la dirección que haya anotado en su laboratorio. Debe ver un resultado con un aspecto como la captura de pantalla siguiente:

    ![Ping correcto](media/azure-stack-connect-vpn/image19b.png)

11. Una respuesta de la máquina virtual remota indica que la prueba ha dado un resultado correcto. Puede cerrar la ventana de la máquina virtual.

También debe hacer pruebas más rigurosas de la transferencia de datos; por ejemplo, copiar archivos de distintos tamaños en ambas direcciones.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visualización de las estadísticas de transferencia de datos a través de la conexión de puerta de enlace

Si desea saber qué cantidad de datos pasa mediante la conexión de sitio a sitio, esta información está disponible en la sección **Conexión**. Esta prueba también es otra forma de comprobar que el ping que acaba de enviar ha ido a través de la conexión VPN.

1. Mientras tiene iniciada la sesión en la máquina virtual del usuario en Azure Stack Hub, use la cuenta de usuario para iniciar sesión en el portal de usuarios.
2. Vaya a **Todos los recursos** y, a continuación, seleccione la conexión **Azs-Azure**. Aparece **Conexiones**.
3. En la sección **Conexión**, aparecen las estadísticas de **Datos de entrada** y **Datos de salida**. En la siguiente captura de pantalla, los números grandes se atribuyen a transferencias de archivos adicionales. Debería ver algunos valores distintos de cero.

    ![Datos de entrada y salida](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de aplicaciones en Azure y Azure Stack Hub](azure-stack-solution-pipeline.md)
