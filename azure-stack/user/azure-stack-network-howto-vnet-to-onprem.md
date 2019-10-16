---
title: Configuración de una puerta de enlace de VPN para Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo configurar una puerta de enlace de VPN para Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/03/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 722cd99a53a0c08e7b981a571726b378e54cd288
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962679"
---
# <a name="setup-vpn-gateway-for-azure-stack-using-fortigate-nva"></a>Configuración de una puerta de enlace de VPN para Azure Stack con la NVA FortiGate

En este artículo se describe cómo crear una conexión VPN a su entorno de Azure Stack. Una puerta de enlace de VPN es un tipo de puerta de enlace de red virtual que envía tráfico cifrado entre una red virtual de Azure Stack y una puerta de enlace de VPN remota. El siguiente procedimiento implementa una red virtual con una NVA FortiGate, una aplicación virtual de red, dentro de un grupo de recursos. También se proporcionan los pasos para configurar una VPN IPSec en la NVA FortiGate.

## <a name="prerequisites"></a>Requisitos previos

-  Acceso a un sistema integrado de Azure Stack con capacidad disponible para implementar los requisitos de proceso, red y recursos necesarios para esta solución. 

    > [!Note]  
    > Estas instrucciones **no** funcionan con un Kit de desarrollo de Azure Stack (ASDK) debido a las limitaciones de red en ASDK. Para más información, consulte [Requisitos y consideraciones de ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-deploy-considerations).

-  Acceso a un dispositivo VPN en la red local que hospeda el sistema integrado de Azure Stack. El dispositivo debe crear un túnel IPSec que cumpla los parámetros descritos en [Parámetros de implementación](#deployment-parameters).

-  Una solución de aplicación virtual de red (NVA) disponible en Marketplace de Azure Stack. Una NVA controla el flujo del tráfico de red desde una red perimetral a otras redes o subredes. En este procedimiento se usa la [Solución de máquina virtual única del firewall de próxima generación FortiGate de Fortinet](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

    > [!Note]  
    > Si no tiene **Fortinet FortiGate-VM For Azure BYOL** y **FortiGate NGFW - Single VM Deployment (BYOL)** disponibles en Marketplace de Azure Stack, póngase en contacto con su operador de la nube.

-  Necesitará al menos dos archivos de licencia de FortiGate disponibles para activar la aplicación virtual de red FortiGate. Para obtener información sobre cómo adquirir estas licencias, consulte el artículo de la biblioteca de documentos de Fortinet [Registro y descarga de la licencia](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    En este procedimiento se usa la [implementación de máquina virtual única de FortiGate](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Puede encontrar pasos sobre cómo conectar la aplicación virtual de red FortiGate de la red virtual de Azure Stack a la red local.

    Para más información sobre cómo implementar la solución FortiGate en una configuración activo-pasivo (alta disponibilidad), consulte los detalles en el artículo de la biblioteca de documentos de Fortinet [Alta disponibilidad para máquina virtual de FortiGate en Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

## <a name="deployment-parameters"></a>Parámetros de implementación

En la tabla siguiente se resumen los parámetros que se usan en estas implementaciones como referencia.

| Parámetro | Valor |
|-----------------------------------|---------------------------|
| Nombre de la instancia de FortiGate | forti1 |
| Licencia/versión de BYOL | 6.0.3 |
| Nombre de usuario administrativo de FortiGate | fortiadmin |
| Nombre del grupo de recursos | forti1-rg1 |
| Nombre de la red virtual | forti1vnet1 |
| Espacio de direcciones de la red virtual | 172.16.0.0/16* |
| Nombre de subred de la red virtual pública | forti1-PublicFacingSubnet |
| Prefijo de dirección de la red virtual pública | 172.16.0.0/24* |
| Nombre de subred en la red virtual | forti1-InsideSubnet |
| Prefijo de subred en la red virtual | 172.16.1.0/24* |
| Tamaño de máquina virtual de la aplicación virtual de red FortiGate | F2s_v2 estándar |
| Nombre de la dirección IP pública | forti1-publicip1 |
| Tipo de dirección IP pública | estática |

> [!Note]
> \* Elija un espacio de direcciones y prefijos de subred diferentes si `172.16.0.0/16` se superpone con la red local o el grupo de VIP de Azure Stack.

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Implementación de los elementos de Marketplace de FortiGate NGFW

1. Abra el portal de usuario de Azure Stack.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

1. Seleccione **Crear un recurso** y busque `FortiGate`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

2. Seleccione **FortiGate NGFW** y seleccione **Crear**.

3. Complete la sección **Básico** con los parámetros de la tabla [Parámetros de implementación](#deployment-parameters).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

1. Seleccione **Aceptar**.

2. Proporcione la red virtual, las subredes y los detalles del tamaño de máquina virtual según la tabla de [Parámetros de implementación](#deployment-parameters).

    > [!Warning] 
    > Si la red local se superpone con el intervalo IP `172.16.0.0/16`, debe seleccionar y configurar un intervalo de red y subredes diferentes. Si desea usar distintos nombres e intervalos que los de la tabla de [Parámetros de implementación](#deployment-parameters), use parámetros que **no** entren en conflicto con la red local. Tenga cuidado al establecer el intervalo de direcciones IP de la red virtual y los intervalos de subred dentro de la red virtual. No querrá que el intervalo se superponga con los intervalos IP que existen en la red local.

3. Seleccione **Aceptar**.

4. Configure la dirección IP pública para la aplicación virtual de red FortiGate:

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

5. Seleccione **Aceptar**. Y, a continuación, seleccione **Aceptar**.

6. Seleccione **Crear**.

    La implementación tardará unos 10 minutos.

## <a name="configure-routes-udr-for-the-vnet"></a>Configuración de rutas (UDR) para la red virtual

1. Abra el portal de usuario de Azure Stack.

2. Seleccione Resource groups (Grupos de recursos). Escriba `forti1-rg1` en el filtro y haga doble clic en el grupo de recursos forti1-rg1.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Seleccione el recurso "forti1-forti1-InsideSubnet-Routes-xxxx".

3. Seleccione **Routes** (Rutas) en **Settings** (Configuración).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Elimine la ruta **to-Internet**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Seleccione *Sí*.

6. Seleccione **Add** (Agregar) para agregar una nueva ruta.

7. Asigne a la ruta el nombre `to-onprem`.

8. Escriba el intervalo de red IP que define el intervalo de red de la red local a la que se conectará la VPN.

9. Seleccione **Virtual appliance** (Aplicación virtual) para **Next hop type** (Tipo del próximo salto) y `172.16.1.4`. Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Seleccione **Guardar**.

## <a name="activate-the-fortigate-nva"></a>Activación de la NVA FortiGate

Active la NVA FortiGate y configure una conexión VPN IPSec en cada NVA.

Necesitará un archivo de licencia válido de Fortinet para activar cada NVA FortiGate. Las NVA **no** funcionarán hasta que haya activado cada NVA. Para más información sobre cómo obtener un archivo de licencia y los pasos para activar la NVA, consulte el artículo de la biblioteca de documentos de Fortinet [Registro y descarga de la licencia](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Una vez que haya activado las NVA, cree un túnel VPN IPSec en la NVA.

1. Abra el portal de usuario de Azure Stack.

2. Seleccione Resource groups (Grupos de recursos). Escriba `forti1` en el filtro y haga doble clic en el grupo de recursos forti1.

3. Haga doble clic en la máquina virtual **forti1** en la lista de tipos de recursos de la hoja del grupo de recursos.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image13.png)

4. Copie la dirección IP asignada, abra un explorador y pegue la dirección IP en la barra de direcciones. El sitio puede desencadenar una advertencia que indica que el certificado de seguridad no es de confianza. Continúe de todos modos.

5. Escriba el nombre de usuario administrativo y la contraseña de FortiGate que proporcionó durante la implementación.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image14.png)

6. Seleccione **System** > **Firmware** (Sistema > Firmware).

7. Seleccione la casilla que muestra el firmware más reciente; por ejemplo, `FortiOS v6.2.0 build0866`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image15.png)

8. Seleccione **Backup config and upgrade** > **Continue** (Realizar copia de seguridad de la configuración y actualizar > Continuar).

9. La NVA actualiza su firmware a la compilación más reciente y se reinicia. El proceso tarda unos cinco minutos. Vuelva a iniciar sesión en la consola web de FortiGate.

10. Haga clic en **VPN** > **IPSec Wizard** (VPN > Asistente de IPSec).

11. Escriba un nombre para la VPN, por ejemplo, `conn1`, en **VPN Creation Wizard** (Asistente para la creación de VPN).

12. Seleccione **This site is behind NAT** (Este sitio está detrás de un sistema NAT).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image16.png)

13. Seleccione **Next** (Siguiente).

14. Escriba la dirección IP remota del dispositivo VPN local al que se va a conectar.

15. Seleccione **port1** en **Outgoing Interface** (Interfaz de salida).

16. Seleccione **Pre-shared Key** (Clave compartida previamente) y escriba (y anote) una clave compartida previamente. 

    > [!Note]  
    > Necesitará esta clave para configurar la conexión en el dispositivo VPN local, es decir, deben coincidir *exactamente*.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image17.png)

17. Seleccione **Next** (Siguiente).

18. Seleccione **port2** en **Local Interface** (Interfaz local).

19. Escriba el intervalo de la subred local:
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

20. Escriba las subredes remotas adecuadas que representan la red local a la que se conectará a través del dispositivo VPN local.

    [](./media/azure-stack-network-howto-vnet-to-onprem/image18.png)

21. Seleccione **Crear**

22. Seleccione **Network** > **Interfaces** (Red > Interfaces).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image19.png)

23. Haga doble clic en **port2**.

24. Elija **LAN** en la lista **Role** (Rol) y **DHCP** para el modo de direccionamiento.

25. Seleccione **Aceptar**.

## <a name="configure-the-on-premises-vpn"></a>Configuración de la VPN local

El dispositivo VPN local se debe configurar para crear el túnel VPN IPSec. En la tabla siguiente se proporcionan los parámetros necesarios para configurar el dispositivo VPN local. Para obtener información sobre cómo configurar el dispositivo VPN local, consulte la documentación del dispositivo.

| Parámetro | Valor |
| --- | --- |
| Dirección IP de la puerta de enlace remota | Dirección IP pública asignada a forti1: consulte [Activación de la NVA FortiGate](#activate-the-fortigate-nva). |
| Red IP remota | 172.16.0.0/16 (si usa el intervalo de direcciones IP de estas instrucciones para la red virtual). |
| Método de autenticación = clave compartida previamente (PSK) | Del paso 16.
| Versión de IKE | 1 |
| Modo de IKE | Principal (protección de identificador) |
| Algoritmos de propuesta de la fase 1 | AES128-SHA256, AES256-SHA256, AES128-SHA1, AES256-SHA1 |
| Grupos Diffe-Hellman | 14, 5 |

## <a name="create-the-vpn-tunnel"></a>Creación del túnel VPN

Una vez que el dispositivo VPN local está configurado correctamente, se puede establecer el túnel VPN.

En la NVA FortiGate:

1. En la consola web de FortiGate para forti1, vaya a **Monitor** > **IPsec Monitor** (Monitor > Monitor IPsec).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image20.png)

2. Resalte **conn1** y seleccione **Bring Up** > **All Phase 2 Selectors** (Mostrar > Todos los selectores de la fase 2).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image21.png)

## <a name="test-and-validate-connectivity"></a>Prueba y validación de la conectividad

Puede enrutar entre la red de la red virtual y la red local mediante el dispositivo VPN local.

Para validar la conexión:

1. Cree una máquina virtual en las redes virtuales de Azure Stack y un sistema en la red local. Puede seguir las instrucciones para crear una máquina virtual en [Guía de inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).

2. Al crear la máquina virtual de Azure Stack y preparar el sistema local, compruebe lo siguiente:

-  La máquina virtual de Azure Stack se coloca en la subred **InsideSubnet** de la red virtual.

-  El sistema local se coloca en la red local dentro del intervalo de direcciones IP definido, tal y como se define en la configuración de IPSec. Asegúrese también de que la dirección IP de la interfaz local del dispositivo VPN local se proporciona al sistema local como una ruta que puede acceder a la red de la red virtual de Azure Stack, por ejemplo, `172.16.0.0/16`.

-  **No** aplique ningún NSG a la máquina virtual de Azure Stack en el momento de la creación. Es posible que tenga que eliminar el NSG que se agrega de forma predeterminada si se crea la máquina virtual desde el portal.

-  Asegúrese de que el sistema operativo del sistema local y el sistema operativo de la máquina virtual de Azure Stack no tienen reglas de firewall de sistema operativo que prohíban la comunicación que se va a usar para probar la conectividad. Con fines de prueba, se recomienda deshabilitar el firewall completamente dentro del sistema operativo de ambos sistemas.

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack](azure-stack-network-differences.md)  
[Oferta de una solución de red en Azure Stack con FortiGate de Fortinet](../operator/azure-stack-network-solutions-enable.md)  