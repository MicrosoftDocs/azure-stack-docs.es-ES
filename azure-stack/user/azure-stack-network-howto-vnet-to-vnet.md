---
title: Conexión de dos entornos de Azure Stack Hub mediante el emparejamiento de VNET
description: Aprenda a conectar dos entornos de Azure Stack Hub mediante el emparejamiento de VNET.
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: b1d60037dd86a6de1aac73ff5a607605ceda1614
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703883"
---
# <a name="connect-two-vnets-through-peering"></a>Conexión de dos redes virtuales mediante el emparejamiento

En este artículo se describe cómo crear una conexión entre dos redes virtuales en el mismo entorno. Al configurar las conexiones, obtendrá información sobre cómo funcionan las puertas de enlace de VPN en Azure Stack Hub. Conecte dos redes virtuales en el mismo entorno de Azure Stack Hub con FortiGate de Fortinet. Este procedimiento implementa dos redes virtuales con una NVA FortiGate, una aplicación virtual de red, en cada red virtual, cada una dentro de un grupo de recursos independiente. También se detallan los cambios necesarios para configurar una VPN IPSec entre las dos redes virtuales. Repita los pasos de este artículo para cada implementación de red virtual.

## <a name="prerequisites"></a>Prerrequisitos

-   Acceso a un sistema integrado de Azure Stack Hub con capacidad disponible para implementar los requisitos de proceso, red y recursos necesarios para esta solución.

-  Una solución de aplicación virtual de red (NVA) descargada y publicada en Marketplace de Azure Stack Hub. Una NVA controla el flujo del tráfico de red desde una red perimetral a otras redes o subredes. En este procedimiento se usa la [Solución de máquina virtual única del firewall de próxima generación FortiGate de Fortinet](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

-  Al menos dos archivos de licencia de FortiGate disponibles para activar la aplicación virtual de red FortiGate. Para obtener información sobre cómo obtener estas licencias, consulte el artículo de la biblioteca de documentos de Fortinet [Registro y descarga de la licencia](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    En este procedimiento se usa la [implementación de máquina virtual única de FortiGate](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Puede encontrar pasos sobre cómo conectar la aplicación virtual de red FortiGate de la red virtual de Azure Stack Hub a la red local.

    Para más información sobre cómo implementar la solución FortiGate en una configuración activo-pasivo (alta disponibilidad), consulte los detalles en el artículo de la biblioteca de documentos de Fortinet [Alta disponibilidad para máquina virtual de FortiGate en Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

## <a name="deployment-parameters"></a>Parámetros de implementación

En la tabla siguiente se resumen los parámetros que se usan en estas implementaciones como referencia:

### <a name="deployment-one-forti1"></a>Implementación uno: Forti1

| Nombre de la instancia de FortiGate | Forti1 |
|-----------------------------------|---------------------------|
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

### <a name="deployment-two-forti2"></a>Implementación dos: Forti2

| Nombre de la instancia de FortiGate | Forti2 |
|-----------------------------------|---------------------------|
| Licencia/versión de BYOL | 6.0.3 |
| Nombre de usuario administrativo de FortiGate | fortiadmin |
| Nombre del grupo de recursos | forti2-rg1 |
| Nombre de la red virtual | forti2vnet1 |
| Espacio de direcciones de la red virtual | 172.17.0.0/16* |
| Nombre de subred de la red virtual pública | forti2-PublicFacingSubnet |
| Prefijo de dirección de la red virtual pública | 172.17.0.0/24* |
| Nombre de subred en la red virtual | Forti2-InsideSubnet |
| Prefijo de subred en la red virtual | 172.17.1.0/24* |
| Tamaño de máquina virtual de la aplicación virtual de red FortiGate | F2s_v2 estándar |
| Nombre de la dirección IP pública | Forti2-publicip1 |
| Tipo de dirección IP pública | estática |

> [!Note]
> \* Elija otro conjunto de espacios de direcciones y prefijos de subred si el anterior se superpone de cualquier manera con el entorno de la red local, incluido el grupo de VIP de cualquiera de los entornos de Azure Stack Hub. Asegúrese también de que los intervalos de direcciones no se superponen entre sí.

## <a name="deploy-the-fortigate-ngfw"></a>Implementación de FortiGate NGFW

1.  Abra el portal de usuarios de Azure Stack Hub.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

2.  Seleccione **Crear un recurso** y busque `FortiGate`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

3.  Seleccione **FortiGate NGFW** y seleccione **Crear**.

4.  Complete la sección **Básico** con los parámetros de la tabla [Parámetros de implementación](#deployment-parameters).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

5.  Seleccione **Aceptar**.

6.  Proporcione la red virtual, las subredes y los detalles del tamaño de máquina virtual según la tabla de [Parámetros de implementación](#deployment-parameters).

    > [!Warning] 
    > Si la red local se superpone con el intervalo IP `172.16.0.0/16`, debe seleccionar y configurar un intervalo de red y subredes diferentes. Si desea usar distintos nombres e intervalos que los de la tabla de [Parámetros de implementación](#deployment-parameters), use parámetros que **no** entren en conflicto con la red local. Tenga cuidado al establecer el intervalo de direcciones IP de la red virtual y los intervalos de subred dentro de la red virtual. No querrá que el intervalo se superponga con los intervalos IP que existen en la red local.

7.  Seleccione **Aceptar**.

8.  Configure la dirección IP pública para la aplicación virtual de red FortiGate:

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

9.  Seleccione **Aceptar**. Y, a continuación, seleccione **Aceptar**.

10.  Seleccione **Crear**.

La implementación tardará unos 10 minutos.

## <a name="configure-routes-udrs-for-each-vnet"></a>Configuración de rutas (UDR) para cada red virtual

Realice estos pasos para ambas implementaciones, forti1-rg1 y forti2-rg1.

1. Abra el portal de usuarios de Azure Stack Hub.

2. Seleccione Resource groups (Grupos de recursos). Escriba `forti1-rg1` en el filtro y haga doble clic en el grupo de recursos forti1-rg1.

    ![resource group](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Seleccione el recurso **forti1-forti1-InsideSubnet-routes-xxxx**.

3. Seleccione **Routes** (Rutas) en **Settings** (Configuración).

    ![Rutas](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Elimine la ruta **to-Internet**.

    ![to-Internet](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Seleccione *Sí*.

6. Seleccione **Add** (Agregar) para agregar una nueva ruta.

7. Asigne a la ruta el nombre `to-onprem`.

8. Escriba el intervalo de red IP que define el intervalo de red de la red local a la que se conectará la VPN.

9. Seleccione **Virtual appliance** (Aplicación virtual) para **Next hop type** (Tipo del próximo salto) y `172.16.1.4`. Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

    ![Tipo de próximo salto](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Seleccione **Guardar**.

Necesitará un archivo de licencia válido de Fortinet para activar cada NVA FortiGate. Las NVA **no** funcionarán hasta que haya activado cada NVA. Para más información sobre cómo obtener un archivo de licencia y los pasos para activar la NVA, consulte el artículo de la biblioteca de documentos de Fortinet [Registro y descarga de la licencia](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Tendrá que adquirir dos archivos de licencia: uno para cada NVA.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Creación de una VPN IPSec entre las dos NVA

Una vez que se han activado las NVA, siga estos pasos para crear una VPN IPSec entre las dos NVA.

Siga los pasos que se indican a continuación para la NVA forti1 y la NVA forti2:

1.  Para obtener la dirección IP pública asignada, vaya a la página de información general de la máquina virtual fortiX:

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image13.png)

2.  Copie la dirección IP asignada, abra un explorador y pegue la dirección en la barra de direcciones. El explorador puede avisarle de que el certificado de seguridad no es de confianza. Continúe de todos modos.

4.  Escriba el nombre de usuario administrativo y la contraseña de FortiGate que proporcionó durante la implementación.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image14.png)

5.  Seleccione **System** > **Firmware** (Sistema > Firmware).

6.  Seleccione la casilla que muestra el firmware más reciente; por ejemplo, `FortiOS v6.2.0 build0866`.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image15.png)

7.  Seleccione **Backup config and upgrade** > **Continue** (Realizar copia de seguridad de la configuración y actualizar > Continuar).

8.  La NVA actualiza su firmware a la compilación más reciente y se reinicia. El proceso tarda unos cinco minutos. Vuelva a iniciar sesión en la consola web de FortiGate.

10.  Haga clic en **VPN** > **IPSec Wizard** (VPN > Asistente de IPSec).

11. Escriba un nombre para la VPN, por ejemplo, `conn1`, en **VPN Creation Wizard** (Asistente para la creación de VPN).

12. Seleccione **This site is behind NAT** (Este sitio está detrás de un sistema NAT).

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image16.png)

13. Seleccione **Next** (Siguiente).

14. Escriba la dirección IP remota del dispositivo VPN local al que se va a conectar.

15. Seleccione **port1** en **Outgoing Interface** (Interfaz de salida).

16. Seleccione **Pre-shared Key** (Clave compartida previamente) y escriba (y anote) una clave compartida previamente. 

    > [!Note]  
    > Necesitará esta clave para configurar la conexión en el dispositivo VPN local, es decir, deben coincidir *exactamente*.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image17.png)

17. Seleccione **Next** (Siguiente).

18. Seleccione **port2** en **Local Interface** (Interfaz local).

19. Escriba el intervalo de la subred local:
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

20. Escriba las subredes remotas adecuadas que representan la red local a la que se conectará a través del dispositivo VPN local.
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image18.png)

21. Seleccione **Crear**

22. Seleccione **Network** > **Interfaces** (Red > Interfaces).

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image19.png)

23. Haga doble clic en **port2**.

24. Elija **LAN** en la lista **Role** (Rol) y **DHCP** para el modo de direccionamiento.

25. Seleccione **Aceptar**.

Repita los pasos para la otra NVA.

## <a name="bring-up-all-phase-2-selectors"></a>Visualización de todos los selectores de la fase 2 

Una vez que se haya completado lo anterior para *ambas* NVA:

1.  En la consola web de FortiGate para forti2, seleccione **Monitor** > **IPsec Monitor** (Monitor > Monitor IPsec). 

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  Resalte `conn1` y seleccione **Bring Up** > **All Phase 2 Selectors** (Mostrar > Todos los selectores de la fase 2).

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)

## <a name="test-and-validate-connectivity"></a>Prueba y validación de la conectividad

Ahora debería poder enrutar la comunicación entre cada red virtual a través de las NVA FortiGate. Para validar la conexión, cree una máquina virtual de Azure Stack Hub en la subred InsideSubnet de cada red virtual. La creación de una máquina virtual de Azure Stack Hub se puede realizar mediante el portal, la CLI o PowerShell. Al crear las máquinas virtuales:

-   Las máquinas virtuales de Azure Stack Hub se colocan en la subred **InsideSubnet** de cada red virtual.

-   **No** aplique ningún NSG a la máquina virtual en el momento de la creación (es decir, elimine el NSG que se agrega de forma predeterminada si se crea la máquina virtual desde el portal).

-   Asegúrese de que las reglas de firewall de las máquinas virtuales permiten la comunicación que va a usar para probar la conectividad. Con fines de prueba, se recomienda deshabilitar el firewall completamente dentro del sistema operativo, si es posible.

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md)  
[Oferta de una solución de red en Azure Stack Hub con FortiGate de Fortinet](../operator/azure-stack-network-solutions-enable.md)  