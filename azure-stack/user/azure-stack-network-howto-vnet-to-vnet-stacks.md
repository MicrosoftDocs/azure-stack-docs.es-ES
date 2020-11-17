---
title: Establecimiento de una conexión entre redes virtuales de Azure Stack Hub con la NVA FortiGate de Fortinet
description: Aprenda a establecer una conexión entre redes virtuales en Azure Stack Hub con la NVA FortiGate de Fortinet
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: d8c20d3fe7b80a7ace90422a622c4f067f631954
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94547116"
---
# <a name="vnet-to-vnet-connectivity-between-azure-stack-hub-instances-with-fortinet-fortigate-nva"></a>Conectividad de VNet a VNet entre instancias de Azure Stack Hub con la NVA FortiGate de Fortinet

En este artículo, conectará una red virtual de un entorno de Azure Stack Hub a una red virtual de otro entorno de Azure Stack Hub mediante la NVA FortiGate de Fortinet, una aplicación virtual de red.

En el artículo se describe la limitación actual de Azure Stack Hub, que permite a los inquilinos configurar una sola conexión VPN en dos entornos. Los usuarios aprenderán a configurar una puerta de enlace personalizada en una máquina virtual Linux que permitirá varias conexiones VPN entre diferentes entornos de Azure Stack Hub. En el procedimiento de este artículo se implementan dos redes virtuales con una aplicación virtual de red FortiGate en cada una de ellas: una implementación por cada entorno de Azure Stack Hub. También se detallan los cambios necesarios para configurar una VPN IPSec entre las dos redes virtuales. Los pasos que se describen en este artículo se deben repetir en todas las redes virtuales de todas las instancias de Azure Stack Hub. 

## <a name="prerequisites"></a>Prerrequisitos

-  Acceso a un sistema integrado de Azure Stack Hub con capacidad disponible para implementar los requisitos de proceso, red y recursos necesarios para esta solución. 

    > [!NOTE]  
    > Estas instrucciones **no** funcionan con un Kit de desarrollo de Azure Stack (ASDK) debido a las limitaciones de red en ASDK. Para más información, consulte [Requisitos y consideraciones de ASDK](../asdk/asdk-deploy-considerations.md).

-  Una solución de aplicación virtual de red (NVA) descargada y publicada en Marketplace de Azure Stack Hub. Una NVA controla el flujo del tráfico de red desde una red perimetral a otras redes o subredes. En este procedimiento se usa la [Solución de máquina virtual única del firewall de próxima generación FortiGate de Fortinet](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

-  Al menos dos archivos de licencia de FortiGate disponibles para activar la aplicación virtual de red FortiGate. Para obtener información sobre cómo obtener estas licencias, consulte el artículo de la biblioteca de documentos de Fortinet [Registro y descarga de la licencia](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    En este procedimiento se usa la [implementación de máquina virtual única de FortiGate](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Puede encontrar pasos sobre cómo conectar la aplicación virtual de red FortiGate de la red virtual de Azure Stack Hub a la red local.

    Para más información sobre cómo implementar la solución FortiGate en una configuración activo-pasivo (HA), consulte el artículo de la biblioteca de documentos de Fortinet [Alta disponibilidad para máquina virtual de FortiGate en Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

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

> [!NOTE]
> \* Elija otro conjunto de espacios de direcciones y prefijos de subred si el anterior se superpone de cualquier manera con el entorno de la red local, incluido el grupo de VIP de cualquiera de los entornos de Azure Stack Hub. Asegúrese también de que los intervalos de direcciones no se superponen entre sí.**

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Implementación de los elementos de Marketplace de FortiGate NGFW

Repita estos pasos en los dos entornos de Azure Stack Hub. 

1. Abra el portal de usuarios de Azure Stack Hub. Asegúrese de usar credenciales que tengan al menos derechos de Colaborador en una suscripción.

1. Seleccione **Crear un recurso** y busque `FortiGate`.

    ![En la captura de pantalla se muestra una sola línea de resultados correspondiente a la búsqueda de "fortigate". El nombre del elemento encontrado es "FortiGate NGFW - Single VM Deployment (BYOL)" (FortiGate NGFW: implementación de una sola máquina virtual [BYOL]).](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image6.png)

1. Seleccione **FortiGate NGFW** y seleccione **Crear**.

1. Complete la sección **Básico** con los parámetros de la tabla [Parámetros de implementación](#deployment-parameters).

    El formulario debería contener la siguiente información:

    ![Los cuadros de texto (como Nombre de instancia y Licencia BYOL) del cuadro de diálogo de datos básicos aparecen rellenados con valores de la tabla de implementación.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image7.png)

1. Seleccione **Aceptar**.

1. Proporcione la red virtual, las subredes y los detalles del tamaño de máquina virtual según los [Parámetros de implementación](#deployment-parameters).

    Si desea usar distintos nombres e intervalos, tenga cuidado de no usar parámetros que entren en conflicto con los recursos de la red virtual y FortiGate en el otro entorno de Azure Stack Hub. Esto es especialmente cierto cuando se establece el intervalo de direcciones IP de la red virtual y los intervalos de subred dentro de la red virtual. Compruebe que no se superponen con los intervalos IP de la otra red virtual que cree.

1. Seleccione **Aceptar**.

1. Configure la dirección IP pública que se usará para la aplicación virtual de red FortiGate:

    ![En el cuadro de texto "Nombre de dirección IP pública", se muestra el valor "forti1-publicip1" (de la tabla de implementación).](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image8.png)

1. Seleccione **Aceptar** y, a continuación, seleccione **Aceptar**.

1. Seleccione **Crear**.

La implementación tardará unos 10 minutos. Ahora puede repetir los pasos necesarios para crear la otra implementación de la aplicación virtual de red FortiGate y de la red virtual en el otro entorno de Azure Stack Hub.

## <a name="configure-routes-udrs-for-each-vnet"></a>Configuración de rutas (UDR) para cada red virtual

Realice estos pasos para ambas implementaciones, forti1-rg1 y forti2-rg1.

1. Vaya al grupo de recursos forti1-rg1 en el portal de Azure Stack Hub.

    ![Esta es una captura de pantalla de la lista de recursos correspondiente al grupo de recursos forti1-rg1.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image9.png)

2. Seleccione el recurso "forti1-forti1-InsideSubnet-Routes-xxxx".

3. Seleccione **Routes** (Rutas) en **Settings** (Configuración).

    ![En la captura de pantalla se muestra el elemento Rutas resaltado en Configuración.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image10.png)

4. Elimine la ruta **to-Internet**.

    ![En la captura de pantalla se muestra la ruta to-Internet resaltada. Hay un botón de eliminación.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image11.png)

5. Seleccione **Sí**.

6. Seleccione **Agregar**.

7. En **Ruta, asigne los nombres** `to-forti1` o `to-forti2`. Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

8. Especifique:
    - forti1: `172.17.0.0/16`  
    - forti2: `172.16.0.0/16`  

    Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

9. Seleccione **Virtual appliance** (Aplicación virtual) para **Next hop type** (Tipo del próximo salto).
    - forti1: `172.16.1.4`  
    - forti2: `172.17.0.4`  

    Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

    ![El cuadro de diálogo Editar ruta para to-forti2 tiene cuadros de texto con valores. El "Prefijo de dirección" es 172.17.0.0/16, el "Tipo de próximo salto" es "Aplicación virtual" y la "Dirección de próximo salto" es 172.16.1.4.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image12.png)

10. Seleccione **Guardar**.

Repita los pasos para cada ruta **InsideSubnet** para cada grupo de recursos.

## <a name="activate-the-fortigate-nvas-and-configure-an-ipsec-vpn-connection-on-each-nva"></a>Activación de las aplicaciones virtuales de red FortiGate y configuración de una conexión VPN IPSec en cada NVA

 Necesitará un archivo de licencia válido de Fortinet para activar cada NVA FortiGate. Las NVA **no** funcionarán hasta que haya activado cada NVA. Para más información sobre cómo obtener un archivo de licencia y los pasos para activar la NVA, consulte el artículo de la biblioteca de documentos de Fortinet [Registro y descarga de la licencia](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Tendrá que adquirir dos archivos de licencia: uno para cada NVA.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Creación de una VPN IPSec entre las dos NVA

Una vez que se han activado las NVA, siga estos pasos para crear una VPN IPSec entre las dos NVA.

Siga los pasos que se indican a continuación para la NVA forti1 y la NVA forti2:

1. Para obtener la dirección IP pública asignada, vaya a la página de información general de la máquina virtual fortiX:

    ![En la página de información general de forti1 se muestran el grupo de recursos, el estado, entre otros datos.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image13.png)

1. Copie la dirección IP asignada, abra un explorador y pegue la dirección en la barra de direcciones. El explorador puede avisarle de que el certificado de seguridad no es de confianza. Continúe de todos modos.

1. Escriba el nombre de usuario administrativo y la contraseña de FortiGate que proporcionó durante la implementación.

    ![La imagen corresponde a la pantalla de inicio de sesión, que incluye un botón de inicio de sesión y os cuadros de texto para especificar el nombre de usuario y la contraseña.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image14.png)

1. Seleccione **System** > **Firmware** (Sistema > Firmware).

1. Seleccione la casilla que muestra el firmware más reciente; por ejemplo, `FortiOS v6.2.0 build0866`.

    ![En la captura de pantalla del firmware "FortiOS v6.2.0 build0866" se proporciona un vínculo a las notas de la versión y dos botones: "Backup config and upgrade" (Realizar copia de seguridad de la configuración) y "Actualizar".](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image15.png)

1. Seleccione **Backup config and upgrade** (Realizar copia de seguridad de la configuración y actualizar) y continúe cuando se le solicite.

1. La NVA actualiza su firmware a la compilación más reciente y se reinicia. El proceso tarda unos cinco minutos. Vuelva a iniciar sesión en la consola web de FortiGate.

1. Haga clic en **VPN** > **IPSec Wizard** (VPN > Asistente de IPSec).

1. Escriba un nombre para la VPN, por ejemplo, `conn1`, en **VPN Creation Wizard** (Asistente para la creación de VPN).

1. Seleccione **This site is behind NAT** (Este sitio está detrás de un sistema NAT).

    ![En la captura de pantalla de VPN Creation Wizard (Asistente para creación de VPN), esto se muestra en el primer paso, VPN Setup (Configuración de VPN). Aparecen seleccionados los valores siguientes: "Sitio a sitio" en Tipo de plantilla, "FortiGate" en Remote Device Type (Tipo de dispositivo remoto) y "This site is behind NAT" (Este sitio está detrás de NAT) en Configuración de NAT.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image16.png)

1. Seleccione **Next** (Siguiente).

1. Escriba la dirección IP remota del dispositivo VPN local al que se va a conectar.

1. Seleccione **port1** en **Outgoing Interface** (Interfaz de salida).

1. Seleccione **Pre-shared Key** (Clave compartida previamente) y escriba (y anote) una clave compartida previamente. 

    > [!NOTE]  
    > Necesitará esta clave para configurar la conexión en el dispositivo VPN local, es decir, deben coincidir *exactamente*.

    ![En la captura de pantalla de VPN Creation Wizard (Asistente para creación de VPN), esto se muestra en el segundo paso, Autenticación, y los valores seleccionados aparecen resaltados.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image17.png)

1. Seleccione **Next** (Siguiente).

1. Seleccione **port2** en **Local Interface** (Interfaz local).

1. Escriba el intervalo de la subred local:
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

1. Escriba las subredes remotas adecuadas que representan la red local a la que se conectará a través del dispositivo VPN local.
    - forti1: 172.16.0.0/16
    - forti2: 172.17.0.0/16

    Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

    ![En la captura de pantalla de VPN Creation Wizard (Asistente para creación de VPN), esto se muestra en el tercer paso, Policy & Routing (Directiva y enrutamiento), con los valores seleccionados y especificados.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image18.png)

1. Seleccione **Crear**

1. Seleccione **Network** > **Interfaces** (Red > Interfaces).  

    ![En la lista de interfaces se muestran dos: port1 (se ha configurado) y port2 (sin configurar). Hay botones para crear, editar y eliminar interfaces.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image19.png)

1. Haga doble clic en **port2**.

1. Elija **LAN** en la lista **Role** (Rol) y **DHCP** para el modo de direccionamiento.

1. Seleccione **Aceptar**.

Repita los pasos para la otra NVA.


## <a name="bring-up-all-phase-2-selectors"></a>Visualización de todos los selectores de la fase 2 

Una vez que se haya completado lo anterior para **ambas** NVA:

1.  En la consola web de FortiGate para forti2, seleccione **Monitor** > **IPsec Monitor** (Monitor > Monitor IPsec). 

    ![El monitor de la conexión VPN conn1 figura en la lista. Se muestra como desconectado, al igual que el selector de fase 2 correspondiente.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image20.png)

2.  Resalte `conn1` y seleccione **Bring Up** > **All Phase 2 Selectors** (Mostrar > Todos los selectores de la fase 2).

    ![El monitor y el selector de fase 2 aparecen como activos.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image21.png)


## <a name="test-and-validate-connectivity"></a>Prueba y validación de la conectividad

Ahora debería poder enrutar la comunicación entre cada red virtual a través de las NVA FortiGate. Para validar la conexión, cree una máquina virtual de Azure Stack Hub en la subred InsideSubnet de cada red virtual. La creación de una máquina virtual de Azure Stack Hub se puede realizar mediante el portal, la CLI o PowerShell. Al crear las máquinas virtuales:

-   Las máquinas virtuales de Azure Stack Hub se colocan en la subred **InsideSubnet** de cada red virtual.

-   **No** aplique ningún grupo de seguridad de red a la máquina virtual en el momento de la creación (es decir, elimine el grupo de seguridad de red que se agrega de forma predeterminada si se crea la máquina virtual desde el portal).

-   Asegúrese de que las reglas de firewall de la máquina virtual permiten la comunicación que va a usar para probar la conectividad. Con fines de prueba, se recomienda deshabilitar el firewall completamente dentro del sistema operativo, si es posible.

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md)  
[Oferta de una solución de red en Azure Stack Hub con FortiGate de Fortinet](../operator/azure-stack-network-solutions-enable.md)  
