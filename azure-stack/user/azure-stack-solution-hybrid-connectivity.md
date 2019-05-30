---
title: Configuración de la conectividad de nube híbrida con Azure y Azure Stack | Microsoft Docs
description: Aprenda a configurar la conectividad de nube híbrida con Azure y Azure Stack
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 00f7c3b990e9930571ace8adec9a33d0c9105be6
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252035"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutorial: Configuración de la conectividad de nube híbrida con Azure y Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede acceder a recursos con seguridad en Azure global y Azure Stack mediante el patrón de conectividad híbrida.

En este tutorial, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Mantener datos localmente para cumplir los requisitos de seguridad o normativos, pero tendrá acceso a los recursos de Azure global.
> - Mantener un sistema heredado mientras se usan recursos e implementaciones de aplicaciones escaladas en la nube en Azure global.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack es una extensión de Azure. Azure Stack aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En las notas del producto [Consideraciones de diseño para aplicaciones híbridas](https://aka.ms/hybrid-cloud-applications-pillars) se revisan los pilares de la calidad de software (selección de ubicación, escalabilidad, disponibilidad, resistencia, capacidad de administración y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.


## <a name="prerequisites"></a>Requisitos previos

Se requieren algunos componentes para crear una implementación de conectividad híbrida. Algunos de estos componentes tardarán tiempo en prepararse, por lo que tendrá que planear en consecuencia.

**Azure Stack**

Un asociado de hardware u OEM de Azure puede implementar una instancia de Azure Stack de producción y todos los usuarios pueden implementar un Kit de desarrollo de Azure Stack (ASDK).

**Componentes de Azure Stack**

Un operador de Azure Stack debe implementar la instancia de App Service, crear planes y ofertas, crear una suscripción de inquilino y agregar la imagen de Windows Server 2016. Si ya tiene algunos de estos componentes, asegúrese de que cumplen los requisitos antes de empezar este tutorial.

En este tutorial se da por supuesto que tiene algunos conocimientos básicos de Azure y Azure Stack. Para más información antes de iniciar el tutorial, lea los siguientes artículos:

 - [Introducción a Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Conceptos clave de Azure Stack](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - Si no tiene una suscripción a Azure, cree una  [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
 - Cree una  [aplicación web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) en Azure. Tome nota de la dirección URL de la aplicación web, porque la necesitará en el tutorial.

### <a name="azure-stack"></a>Azure Stack

 - Use su instancia de Azure Stack de producción o implemente el Kit de desarrollo de Azure Stack desde https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >La implementación del ASDK puede tardar hasta 7 horas, así que planéela en consecuencia.

 - Implemente servicios PaaS de  [App Service](../operator/azure-stack-app-service-deploy.md)  en Azure Stack.
 - [Cree planes y ofertas](../operator/azure-stack-plan-offer-quota-overview.md) en el entorno de Azure Stack.
 - [Cree una suscripción de inquilino](../operator/azure-stack-subscribe-plan-provision-vm.md) dentro del entorno de Azure Stack.

### <a name="before-you-begin"></a>Antes de empezar

Compruebe que se cumplen los siguientes criterios antes de empezar a configurar la conectividad de nube híbrida:

 - Necesita una dirección IPv4 pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
 - Todos los recursos se implementan en la misma región o ubicación.

#### <a name="tutorial-example-values"></a>Valores de ejemplo del tutorial

Los ejemplos de este tutorial utilizan los valores siguientes. Puede usar estos valores para crear un entorno de prueba o consultarlos para comprender mejor los ejemplos. Para más información acerca de la configuración de VPN Gateway en general, consulte  [Acerca de la configuración de VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Especificaciones de conexión:

 - **Tipo de VPN**: basada en rutas
 - **Tipo de conexión**: de sitio a sitio (IPsec)
 - **Tipo de puerta de enlace**: VPN
 - **Nombre de conexión de Azure**: Azure-Gateway-AzureStack-S2SGateway (el portal rellenará automáticamente este valor)
 - **Nombre de conexión de Azure Stack**: Azure-Gateway-AzureStack-S2SGateway (el portal rellenará automáticamente este valor)
 - **Clave compartida**: cualquiera compatible con el hardware de VPN, con valores coincidentes en ambos lados de la conexión
 - **Suscripción**: la suscripción preferida
 - **Grupo de recursos**: Test-Infra

Direcciones IP de red y subred:

| Conexión de Azure/Azure Stack | NOMBRE | Subred | Dirección IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Red virtual de Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Red virtual de Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Puerta de enlace de red virtual de Azure | Puerta de enlace de Azure |  |  |
| Puerta de enlace de red virtual de Azure Stack | Puerta de enlace de AzureStack |  |  |
| IP pública de Azure | GatewayPublicIP de Azure |  | Se determina durante la creación |
| Dirección IP pública de Azure Stack | AzureStack-GatewayPublicIP |  | Se determina durante la creación |
| Puerta de enlace de red local de Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valor de dirección IP pública de Azure Stack |
| Puerta de enlace de red local de Azure Stack | S2SGateway de Azure<br>10.100.102.0/23 |  | Valor de dirección IP pública de Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Creación de una red virtual en Azure global y Azure Stack

Para crear una red virtual mediante el portal, use los pasos siguientes. Puede utilizar estos  [valores de ejemplo](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)  si usa este artículo como tutorial. Sin embargo, si utiliza este artículo para configurar un entorno de producción, sustituya los valores de ejemplo por sus propios valores.

> [!IMPORTANT]
> Debe asegurarse de que no hay ninguna superposición de direcciones IP en los espacios de direcciones de red virtual de Azure o Azure Stack.

Para crear una red virtual en Azure:

1. Utilice un explorador para conectarse a [Azure Portal](https://portal.azure.com/)  e inicie sesión con su cuenta de Azure.
2. Seleccione  **Crear un recurso**. En el campo  **Buscar en Marketplace** , escriba `virtual network`. Busque  **Red virtual**  en la lista de resultados y, seleccione  **Red virtual**.
3. En la lista **Seleccionar un modelo de implementación** , seleccione  **Resource Manager** y seleccione  **Crear**.
4. En **Crear red virtual**, configure los valores correspondientes. Los nombres de los campos obligatorios van precedidos de un asterisco rojo.  Al escribir un valor válido, el asterisco se cambia a una marca de verificación verde.

Para crear una red virtual en Azure Stack:

* Repita los pasos anteriores (1-4) con el **portal de inquilino** de Azure Stack.

## <a name="add-a-gateway-subnet"></a>Adición de una subred de puerta de enlace

Antes de conectar la red virtual a una puerta de enlace, es preciso crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. Los servicios de puerta de enlace usan las direcciones IP especificadas en la subred de puerta de enlace.

En  [Azure Portal](https://portal.azure.com/), vaya a la red virtual de Resource Manager en la que desea crear una puerta de enlace de red virtual.

1. Seleccione la red virtual para abrir la página **Red virtual**.
2. En **CONFIGURACIÓN**, seleccione **Subredes**.
3. En la página  **Subredes** , haga clic en  **+Subred de puerta de enlace**  para abrir la página  **Agregar subred**.

    ![Agregación de subred de puerta de enlace](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. El  **nombre**  de la subred se rellena automáticamente con el valor "GatewaySubnet". Este valor es necesario para que Azure reconozca que se trata de subred de una puerta de enlace.
5. Cambie los valores de **Intervalo de direcciones** que se proporcionan para que coincidan con los requisitos de configuración y seleccione  **Aceptar**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Creación de una puerta de enlace de red virtual en Azure y Azure Stack.

Para crear una puerta de enlace de red virtual en Azure, use los pasos siguientes.

1. En el lado izquierdo de la página del portal, seleccione **+**   y escriba "puerta de enlace de red virtual" en el cuadro de búsqueda.
2. En **Resultados**, seleccione **Puerta de enlace de red virtual**.
3. En **Puerta de enlace de red virtual**, seleccione  **Crear** para abrir la página  **Crear puerta de enlace de red virtual** .
4. En **Crear puerta de enlace de red virtual**, especifique los valores para la puerta de enlace de red, tal como se muestra en los **valores de ejemplo del tutorial**, y los siguientes valores adicionales:

   - **SKU**: básica.
   - **Red virtual**: seleccione la red virtual que ha creado antes. La subred de puerta de enlace que ha creado se selecciona automáticamente.
   - **Primera configuración de IP**:  es la IP pública de la puerta de enlace.
     - Seleccione **Crear configuración de IP de puerta de enlace**, que le lleva a la página **Elegir dirección IP pública**.
     - Seleccione **+Crear nueva** para abrir la página **Crear dirección IP pública**.
     - Escriba un **nombre** para la dirección IP pública. Deje el valor de SKU en **Básica** y seleccione **Aceptar** para guardar los cambios.

       > [!Note]
       > Actualmente, VPN Gateway solo admite la asignación de direcciones IP públicas dinámicas. Sin embargo, esto no significa que la dirección IP cambie después de que se haya asignado a la puerta de enlace VPN. La única vez que la dirección IP pública cambia es cuando la puerta de enlace se elimina y se vuelve a crear. El cambio de tamaño, el restablecimiento u otras actualizaciones u operaciones de mantenimiento interno realizadas en la puerta de enlace VPN no cambia la dirección IP.

4. Compruebe la configuración de la puerta de enlace.
5. Seleccione  **Crear** para crear la puerta de enlace de la VPN. Se validará la configuración de la puerta de enlace y verá el icono "Implementando la puerta de enlace de red virtual" en el panel.

   >[!Note]
   >La creación de una puerta de enlace puede tardar hasta 45 minutos. Es posible que tenga que actualizar la página de portal para ver el estado completado.

    Una vez creada la puerta de enlace, puede ver la dirección IP que se le ha asignado consultando la red virtual en el portal. La puerta de enlace aparece como un dispositivo conectado. Para más información acerca de la puerta de enlace, seleccione el dispositivo.

6. Repita los pasos anteriores (1-5) en la implementación de Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Creación de la puerta de enlace de red virtual local de Azure y Azure Stack

La puerta de enlace de red local suele hacer referencia a la ubicación local. Asigne un nombre al sitio al que se puedan referir Azure o Azure Stack y, a continuación, especifique lo siguiente:

- La dirección IP del dispositivo VPN local para el que va a crear una conexión.
- Los prefijos de dirección IP que se enrutarán mediante la puerta de enlace VPN al dispositivo VPN. Los prefijos de dirección que especifique son los prefijos que se encuentran en la red local.

  >[!Note]
  >Si la red local cambia o necesita cambiar la dirección IP pública del dispositivo VPN, puede actualizar fácilmente los valores más adelante.

1. En el portal, seleccione  **+Crear un recurso**.
2. En el cuadro de búsqueda, escriba  **Puerta de enlace de red local** y presione  **Entrar**  para realizar la búsqueda. Se devolverá una lista de resultados.
3. Seleccione  **Puerta de enlace de red local** y, después, **Crear**  Se abrirá la página  **Crear puerta de enlace de red local** .
4. En **Crear puerta de enlace de red local**, especifique los valores de la puerta de enlace de red local mediante nuestros **valores de ejemplo del tutorial**. Incluya los siguientes valores adicionales.

    - **Dirección IP**: es la dirección IP pública del dispositivo VPN al que desea que Azure o Azure Stack se conecten. Especifique una dirección IP pública válida que no esté detrás de un NAT, para que Azure pueda llegar a la dirección. Si no tiene la dirección IP en este momento, puede usar un valor del ejemplo como marcador de posición, pero deberá volver y reemplazar el marcador de posición por la dirección IP pública del dispositivo VPN. Azure no puede conectarse al dispositivo hasta que proporcione una dirección válida.
    - **Espacio de direcciones**: es el intervalo de direcciones de la red que representa esta red local. Puede agregar varios intervalos de espacios de direcciones. Asegúrese de que los intervalos que especifique no se superpongan con los de otras redes a las que quiera conectarse. Azure enrutará el intervalo de direcciones que especifique a la dirección IP del dispositivo VPN local. Utilice sus propios valores si quiere conectarse a su sitio local, no a un valor de ejemplo.
    - **Configurar BGP**: usar solo al configurar BGP. En caso contrario, no seleccione esta opción.
    - **Suscripción**: compruebe que se muestra la suscripción correcta.
    - **Grupo de recursos**: seleccione el grupo de recursos que quiere usar. Puede crear un grupo de recursos nuevo o seleccionar uno ya creado.
    - **Ubicación**: seleccione la ubicación en la que se creará este objeto. Puede seleccionar la misma ubicación en la que reside la red virtual, pero no es obligatorio.
5. Cuando termine de especificar los valores requeridos, seleccione **Crear**  para crear la puerta de enlace de red local.
6. Repita estos pasos (1-5) en la implementación de Azure Stack.

## <a name="configure-your-connection"></a>Configuración de la conexión

Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. El dispositivo VPN que configure se denomina conexión. Para configurar la conexión, necesita:

- Una clave compartida. Se trata de la misma clave compartida que se especifica al crear la conexión VPN de sitio a sitio. En estos ejemplos se utiliza una clave compartida básica. Se recomienda que genere y utilice una clave más compleja.
- La dirección IP pública de la puerta de enlace de red virtual. Puede ver la dirección IP pública mediante Azure Portal, PowerShell o la CLI. Para buscar la dirección IP pública de la puerta de enlace de VPN mediante Azure Portal, vaya a las puertas de enlace de red virtual y seleccione el nombre de su puerta de enlace.

Utilice los pasos siguientes para crear la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN local.

1. En Azure Portal, seleccione  **+ Crear un recurso**.
2. Busque **conexiones**.
3. En **Resultados**, seleccione  **Conexiones**.
4. En **Conexión**, seleccione **Crear**.
5. En **Crear conexión**, configure las siguientes opciones:

    - **Tipo de conexión**: seleccione De sitio a sitio (IPsec).
    - **Grupo de recursos**: seleccione el grupo de recursos de prueba.
    - **Puerta de enlace de red virtual**: seleccione la puerta de enlace de red virtual que ha creado.
    - **Puerta de enlace de red local**: seleccione la puerta de enlace de red local que ha creado.
    - **Nombre de la conexión**: este valor se rellena automáticamente con los valores de las dos puertas de enlace.
    - **Clave compartida**: este valor debe ser el mismo que el que usa para el dispositivo VPN local. En el ejemplo del tutorial se usa "abc123", pero puede (y debería) utilizar algo más complejo. Lo importante es que este valor DEBE ser el mismo que el que se especificó al configurar el dispositivo VPN.
    - Los valores de **Suscripción**, **Grupo de recursos** y **Ubicación** son fijos.

6. Haga clic en **Aceptar** para crear la conexión.

La conexión se puede ver en la página  **Conexiones**  de la puerta de enlace de red virtual. El estado pasará de  *Desconocido*  a  *Conectando* y luego a  *Correcto*.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
