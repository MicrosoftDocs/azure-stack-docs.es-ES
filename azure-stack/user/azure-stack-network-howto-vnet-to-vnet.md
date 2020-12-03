---
title: Conexión de dos redes virtuales en el mismo entorno de Azure Stack
description: Aprenda a conectar dos redes virtuales en el mismo entorno de Azure Stack Hub con Fortinet FortiGate.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: b9afd01c79590092269bea8cb4e3ca293fa855fd
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525649"
---
# <a name="vnet-to-vnet-connectivity-with-fortigate"></a>Conectividad de VNet a VNet con Fortigate

En este artículo se describe cómo crear una conexión entre dos redes virtuales en el mismo entorno. Al configurar las conexiones, obtendrá información sobre cómo funcionan las puertas de enlace de VPN en Azure Stack Hub. Conecte dos redes virtuales en el mismo entorno de Azure Stack Hub con FortiGate de Fortinet. Este procedimiento implementa dos redes virtuales con una NVA FortiGate, una aplicación virtual de red, en cada red virtual, cada una dentro de un grupo de recursos independiente. También se detallan los cambios necesarios para configurar una VPN IPSec entre las dos redes virtuales. Repita los pasos de este artículo para cada implementación de red virtual.

## <a name="prerequisites"></a>Prerrequisitos

-   Acceso a un sistema integrado de Azure Stack Hub con capacidad disponible para implementar los requisitos de proceso, red y recursos necesarios para esta solución.

-  Una solución de aplicación virtual de red (NVA) descargada y publicada en Marketplace de Azure Stack Hub. Una NVA controla el flujo del tráfico de red desde una red perimetral a otras redes o subredes. En este procedimiento se usa la [Solución de máquina virtual única del firewall de próxima generación FortiGate de Fortinet](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

-  Al menos dos archivos de licencia de FortiGate disponibles para activar la aplicación virtual de red FortiGate. Para obtener información sobre cómo obtener estas licencias, consulte el artículo de la biblioteca de documentos de Fortinet [Registro y descarga de la licencia](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    En este procedimiento se usa la [implementación de máquina virtual única de FortiGate](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Puede encontrar pasos sobre cómo conectar la aplicación virtual de red FortiGate de la red virtual de Azure Stack Hub a la red local.

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

> [!NOTE]
> \* Elija otro conjunto de espacios de direcciones y prefijos de subred si el anterior se superpone de cualquier manera con el entorno de la red local, incluido el grupo de VIP de cualquiera de los entornos de Azure Stack Hub. Asegúrese también de que los intervalos de direcciones no se superponen entre sí.

## <a name="deploy-the-fortigate-ngfw"></a>Implementación de FortiGate NGFW

1.  Abra el portal de usuarios de Azure Stack Hub.

2.  Seleccione **Crear un recurso** y busque `FortiGate`.

    ![En la lista de resultados de la búsqueda se muestra FortiGate NGFW - Single VM Deployment (FortiGate NGFW: implementación de una sola máquina virtual).](./media/azure-stack-network-howto-vnet-to-onprem/image6a.png)

3.  Seleccione **FortiGate NGFW** y seleccione **Crear**.

4.  Complete la sección **Básico** con los parámetros de la tabla [Parámetros de implementación](#deployment-parameters).

    ![La pantalla de datos básicos incluye los valores de los parámetros de implementación especificados en los cuadros de lista y texto.](./media/azure-stack-network-howto-vnet-to-onprem/image7a.png)

5.  Seleccione **Aceptar**.

6.  Proporcione la red virtual, las subredes y los detalles del tamaño de máquina virtual según la tabla de [Parámetros de implementación](#deployment-parameters).

    > [!Warning] 
    > Si la red local se superpone con el intervalo IP `172.16.0.0/16`, debe seleccionar y configurar un intervalo de red y subredes diferentes. Si desea usar distintos nombres e intervalos que los de la tabla de [Parámetros de implementación](#deployment-parameters), use parámetros que **no** entren en conflicto con la red local. Tenga cuidado al establecer el intervalo de direcciones IP de la red virtual y los intervalos de subred dentro de la red virtual. No querrá que el intervalo se superponga con los intervalos IP que existen en la red local.

7.  Seleccione **Aceptar**.

8.  Configure la dirección IP pública para la aplicación virtual de red FortiGate:

    ![En el cuadro de diálogo Asignación de IP se muestran los valores forti1-publicip1 para "Nombre de dirección IP pública" y Estático para "Tipo de dirección IP pública".](./media/azure-stack-network-howto-vnet-to-onprem/image8a.png)

9.  Seleccione **Aceptar**. Y, a continuación, seleccione **Aceptar**.

10.  Seleccione **Crear**.

La implementación tardará unos 10 minutos.

## <a name="configure-routes-udrs-for-each-vnet"></a>Configuración de rutas (UDR) para cada red virtual

Realice estos pasos para ambas implementaciones, forti1-rg1 y forti2-rg1.

1. Abra el portal de usuarios de Azure Stack Hub.

1. Seleccione Resource groups (Grupos de recursos). Escriba `forti1-rg1` en el filtro y haga doble clic en el grupo de recursos forti1-rg1.

    ![Se muestran diez recursos para el grupo de recursos forti1-rg1.](./media/azure-stack-network-howto-vnet-to-onprem/image9a.png)

1. Seleccione el recurso **forti1-forti1-InsideSubnet-routes-xxxx**.

1. Seleccione **Routes** (Rutas) en **Settings** (Configuración).

    ![El botón Rutas está seleccionado en el cuadro de diálogo Configuración.](./media/azure-stack-network-howto-vnet-to-onprem/image10a.png)

1. Elimine la ruta **to-Internet**.

    ![La única ruta que aparece en la lista es to-Internet y está seleccionada. Hay un botón de eliminación.](./media/azure-stack-network-howto-vnet-to-onprem/image11a.png)

1. Seleccione *Sí*.

1. Seleccione **Add** (Agregar) para agregar una nueva ruta.

1. Asigne a la ruta el nombre `to-onprem`.

1. Escriba el intervalo de red IP que define el intervalo de red de la red local a la que se conectará la VPN.

1. Seleccione **Virtual appliance** (Aplicación virtual) para **Next hop type** (Tipo del próximo salto) y `172.16.1.4`. Utilice su intervalo de direcciones IP si usa un intervalo IP diferente.

    ![En el cuadro de diálogo Agregar ruta se muestran los cuatro valores que se seleccionaron y especificaron en los cuadros de texto.](./media/azure-stack-network-howto-vnet-to-onprem/image12a.png)

1. Seleccione **Guardar**.

Necesitará un archivo de licencia válido de Fortinet para activar cada NVA FortiGate. Las NVA **no** funcionarán hasta que haya activado cada NVA. Para más información sobre cómo obtener un archivo de licencia y los pasos para activar la NVA, consulte el artículo de la biblioteca de documentos de Fortinet [Registro y descarga de la licencia](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Tendrá que adquirir dos archivos de licencia: uno para cada NVA.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Creación de una VPN IPSec entre las dos NVA

Una vez que se han activado las NVA, siga estos pasos para crear una VPN IPSec entre las dos NVA.

Siga los pasos que se indican a continuación para la NVA forti1 y la NVA forti2:

1. Para obtener la dirección IP pública asignada, vaya a la página de información general de la máquina virtual fortiX:

    ![En la página de información general de la máquina virtual forti1 se muestran los valores de forti1, como el grupo de recursos y el estado.](./media/azure-stack-network-howto-vnet-to-vnet/image13a.png)

1. Copie la dirección IP asignada, abra un explorador y pegue la dirección en la barra de direcciones. El explorador puede avisarle de que el certificado de seguridad no es de confianza. Continúe de todos modos.

1. Escriba el nombre de usuario administrativo y la contraseña de FortiGate que proporcionó durante la implementación.

    ![El cuadro de diálogo de inicio de sesión tiene cuadros de texto para especificar el usuario y la contraseña, además de un botón de inicio de sesión.](./media/azure-stack-network-howto-vnet-to-vnet/image14a.png)

1. Seleccione **System** > **Firmware** (Sistema > Firmware).

1. Seleccione la casilla que muestra el firmware más reciente; por ejemplo, `FortiOS v6.2.0 build0866`.

    ![El cuadro de diálogo Firmware tiene el identificador de firmware "FortiOS v6.2.0 build0866", un vínculo a las notas de la versión y dos botones: "Backup config and upgrade" (Realizar copia de seguridad de la configuración) y "Actualizar".](./media/azure-stack-network-howto-vnet-to-vnet/image15a.png)

1. Seleccione **Backup config and upgrade** > **Continue** (Realizar copia de seguridad de la configuración y actualizar > Continuar).

1. La NVA actualiza su firmware a la compilación más reciente y se reinicia. El proceso tarda unos cinco minutos. Vuelva a iniciar sesión en la consola web de FortiGate.

1. Haga clic en **VPN** > **IPSec Wizard** (VPN > Asistente de IPSec).

1. Escriba un nombre para la VPN, por ejemplo, `conn1`, en **VPN Creation Wizard** (Asistente para la creación de VPN).

1. Seleccione **This site is behind NAT** (Este sitio está detrás de un sistema NAT).

    ![En la captura de pantalla de VPN Creation Wizard (Asistente para creación de VPN), esto se muestra en el primer paso, VPN Setup (Configuración de VPN). Aparecen seleccionados los valores siguientes: "Sitio a sitio" en Tipo de plantilla, "FortiGate" en Remote Device Type (Tipo de dispositivo remoto) y "This site is behind NAT" (Este sitio está detrás de NAT) en Configuración de NAT.](./media/azure-stack-network-howto-vnet-to-vnet/image16a.png)

1. Seleccione **Next** (Siguiente).

1. Escriba la dirección IP remota del dispositivo VPN local al que se va a conectar.

1. Seleccione **port1** en **Outgoing Interface** (Interfaz de salida).

1. Seleccione **Pre-shared Key** (Clave compartida previamente) y escriba (y anote) una clave compartida previamente. 

    > [!NOTE]  
    > Necesitará esta clave para configurar la conexión en el dispositivo VPN local, es decir, deben coincidir *exactamente*.

    ![En la captura de pantalla de VPN Creation Wizard (Asistente para creación de VPN), esto se muestra en el segundo paso, Autenticación, y los valores seleccionados aparecen resaltados.](./media/azure-stack-network-howto-vnet-to-vnet/image17a.png)

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

    ![En la captura de pantalla de VPN Creation Wizard (Asistente para creación de VPN), esto se muestra en el tercer paso, Policy & Routing (Directiva y enrutamiento). Se muestran los valores seleccionados y especificados.](./media/azure-stack-network-howto-vnet-to-vnet/image18a.png)

1. Seleccione **Crear**

1. Seleccione **Network** > **Interfaces** (Red > Interfaces).

    ![En la lista de interfaces se muestran dos: port1 (se ha configurado) y port2 (sin configurar). Hay botones para crear, editar y eliminar interfaces.](./media/azure-stack-network-howto-vnet-to-vnet/image19a.png)

1. Haga doble clic en **port2**.

1. Elija **LAN** en la lista **Role** (Rol) y **DHCP** para el modo de direccionamiento.

1. Seleccione **Aceptar**.

Repita los pasos para la otra NVA.

## <a name="bring-up-all-phase-2-selectors"></a>Visualización de todos los selectores de la fase 2 

Una vez que se haya completado lo anterior para *ambas* NVA:

1.  En la consola web de FortiGate para forti2, seleccione **Monitor** > **IPsec Monitor** (Monitor > Monitor IPsec). 

    ![El monitor de la conexión VPN conn1 figura en la lista. Se muestra como desconectado, al igual que el selector de fase 2 correspondiente.](./media/azure-stack-network-howto-vnet-to-vnet/image20a.png)

2.  Resalte `conn1` y seleccione **Bring Up** > **All Phase 2 Selectors** (Mostrar > Todos los selectores de la fase 2).

    ![El monitor y el selector de fase 2 aparecen como activos.](./media/azure-stack-network-howto-vnet-to-vnet/image21a.png)

## <a name="test-and-validate-connectivity"></a>Prueba y validación de la conectividad

Ahora debería poder enrutar la comunicación entre cada red virtual a través de las NVA FortiGate. Para validar la conexión, cree una máquina virtual de Azure Stack Hub en la subred InsideSubnet de cada red virtual. La creación de una máquina virtual de Azure Stack Hub se puede realizar mediante el portal, la CLI o PowerShell. Al crear las máquinas virtuales:

-   Las máquinas virtuales de Azure Stack Hub se colocan en la subred **InsideSubnet** de cada red virtual.

-   **No** aplique ningún NSG a la máquina virtual en el momento de la creación (es decir, elimine el NSG que se agrega de forma predeterminada si se crea la máquina virtual desde el portal).

-   Asegúrese de que las reglas de firewall de las máquinas virtuales permiten la comunicación que va a usar para probar la conectividad. Con fines de prueba, se recomienda deshabilitar el firewall completamente dentro del sistema operativo, si es posible.

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md)  
[Oferta de una solución de red en Azure Stack Hub con FortiGate de Fortinet](../operator/azure-stack-network-solutions-enable.md)  
