---
title: Configuración de la conectividad de nube híbrida con Azure y Azure Stack Hub
description: Aprenda a configurar la conectividad de nube híbrida con Azure y Azure Stack Hub.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 88355951ea5cffc147ef9644a6e35b78bb80a02c
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640050"
---
# <a name="configure-hybrid-cloud-connectivity-using-azure-and-azure-stack-hub"></a>Configuración de la conectividad de nube híbrida con Azure y Azure Stack Hub

*Se aplica a: Sistemas integrados de Azure Stack Hub y el kit de desarrollo de Azure Stack Hub*

Puede acceder a recursos con seguridad en Azure global y Azure Stack Hub mediante el patrón de conectividad híbrida.

En esta solución, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Mantener datos en el entorno loca para cumplir los requisitos de privacidad o normativos, pero conservar el acceso a los recursos de Azure global.
> - Mantener un sistema heredado mientras se usan recursos e implementaciones de aplicaciones escaladas en la nube en Azure global.

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub es una extensión de Azure. Azure Stack Hub aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En el artículo [Consideraciones de diseño para aplicaciones híbridas](overview-app-design-considerations.md) se examinan los pilares de la calidad de software (selección de ubicación, escalabilidad, disponibilidad, resistencia, manejabilidad y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.


## <a name="prerequisites"></a>Requisitos previos

Se requieren algunos componentes para crear una implementación de conectividad híbrida. Algunos de estos componentes tardarán en prepararse, por lo que tendrá que planear en consecuencia.

**Azure Stack Hub**

Un asociado de hardware u OEM de Azure puede implementar una instancia de Azure Stack Hub de producción y todos los usuarios pueden implementar un kit de desarrollo de Azure Stack Hub (ASDK).

**Componentes de Azure Stack Hub**

Un operador de Azure Stack Hub debe implementar la instancia de App Service, crear planes y ofertas, crear una suscripción de inquilino y agregar la imagen de Windows Server 2016. Si ya tiene estos componentes, asegúrese de que cumplen los requisitos antes de empezar esta solución.

En esta solución de ejemplo se da por supuesto que tiene algunos conocimientos básicos de Azure y Azure Stack Hub. Para más información antes de iniciar la solución, lea los siguientes artículos:

 - [Introducción a Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Conceptos clave de Azure Stack Hub](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
 - Cree una [aplicación web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) en Azure. Tome nota de la dirección URL de la aplicación web, porque la necesitará en la solución.

### <a name="azure-stack-hub"></a>Azure Stack Hub

 - Use su instancia de Azure Stack Hub de producción o implemente el kit de desarrollo de Azure Stack Hub desde https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >La implementación del ASDK puede tardar hasta 7 horas, así que planéela en consecuencia.

 - Implemente los servicios PaaS de [App Service](../operator/azure-stack-app-service-deploy.md) en Azure Stack Hub.
 - [Cree planes y ofertas](../operator/service-plan-offer-subscription-overview.md) en el entorno de Azure Stack Hub.
 - [Cree una suscripción de inquilino](../operator/azure-stack-subscribe-plan-provision-vm.md) dentro del entorno de Azure Stack Hub.

### <a name="before-you-begin"></a>Antes de empezar

Compruebe que se cumplen los siguientes criterios antes de empezar a configurar la conectividad de nube híbrida:

 - Necesita una dirección IPv4 pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de una NAT (traducción de direcciones de red).
 - Todos los recursos se implementan en la misma región o ubicación.

#### <a name="solution-example-values"></a>Valores de ejemplo de la solución

En los ejemplos de esta solución se usan los valores siguientes. Puede usar estos valores para crear un entorno de prueba o consultarlos para comprender mejor los ejemplos. Para más información sobre la configuración de puertas de enlace de VPN en general, consulte [Acerca de la configuración de VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Especificaciones de conexión:

 - **Tipo de VPN**: basado en rutas
 - **Tipo de conexión**: de sitio a sitio (IPsec)
 - **Tipo de puerta de enlace**: VPN
 - **Nombre de conexión de Azure**: Azure-Gateway-AzureStack-S2SGateway (el portal rellenará automáticamente este valor)
 - **Nombre de conexión de Azure Stack Hub**: Azure-Gateway-AzureStack-S2SGateway (el portal rellenará automáticamente este valor)
 - **Clave compartida**: cualquiera compatible con el hardware de VPN, con valores coincidentes en ambos lados de la conexión.
 - **Suscripción**: cualquier suscripción preferida.
 - **Grupo de recursos**: Test-Infra

Direcciones IP de red y subred:

| Conexión de Azure o Azure Stack Hub | NOMBRE | Subred | Dirección IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Red virtual de Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Red virtual de Azure Stack Hub | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Puerta de enlace de red virtual de Azure | Puerta de enlace de Azure |  |  |
| Puerta de enlace de red virtual de Azure Stack Hub | Puerta de enlace de AzureStack |  |  |
| IP pública de Azure | GatewayPublicIP de Azure |  | Se determina durante la creación |
| Dirección IP pública de Azure Stack Hub | AzureStack-GatewayPublicIP |  | Se determina durante la creación |
| Puerta de enlace de red local de Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valor de dirección IP pública de Azure Stack Hub |
| Puerta de enlace de red local de Azure Stack Hub | S2SGateway de Azure<br>10.100.102.0/23 |  | Valor de dirección IP pública de Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack-hub"></a>Creación de una red virtual en Azure global y Azure Stack Hub

Para crear una red virtual mediante el portal, use los pasos siguientes. Puede utilizar estos [valores de ejemplo](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) si usa este artículo solo como una solución. Si usa este artículo para configurar un entorno de producción, sustituya los valores de ejemplo por sus propios valores.

> [!IMPORTANT]
> Debe asegurarse de que no haya ninguna superposición de direcciones IP en los espacios de direcciones de red virtual de Azure o Azure Stack Hub.

Para crear una red virtual en Azure:

1. Utilice un explorador para conectarse a [Azure Portal](https://portal.azure.com/) e inicie sesión con su cuenta de Azure.
2. Seleccione **Crear un recurso**. En el campo **Buscar en el Marketplace**, introduzca "Red virtual". Seleccione **Red virtual** en los resultados.
3. En la lista **Seleccionar un modelo de implementación**, seleccione **Resource Manager** y haga clic en **Crear**.
4. En **Crear red virtual**, configure los valores correspondientes. Los nombres de los campos obligatorios van precedidos de un asterisco rojo.  Al escribir un valor válido, el asterisco se cambia a una marca de verificación verde.

Para crear una red virtual en Azure Stack Hub:

* Repita los pasos anteriores (1-4) con el **portal de inquilino** de Azure Stack Hub.

## <a name="add-a-gateway-subnet"></a>Adición de una subred de puerta de enlace

Antes de conectar la red virtual a una puerta de enlace, es preciso crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. Los servicios de puerta de enlace usan las direcciones IP especificadas en la subred de puerta de enlace.

En [Azure Portal](https://portal.azure.com/), navegue a la red virtual de Resource Manager donde desea crear una puerta de enlace de red virtual.

1. Seleccione la red virtual para abrir la página **Red virtual**.
2. En **CONFIGURACIÓN**, seleccione **Subredes**.
3. En la página **Subredes**, haga clic en **+Subred de puerta de enlace** para abrir la página **Agregar subred**.

    ![Agregación de subred de puerta de enlace](media/solution-deployment-guide-connectivity/image4.png)

4. El **nombre** de la subred se rellena automáticamente con el valor "GatewaySubnet". Este valor es necesario para que Azure reconozca que se trata de subred de una puerta de enlace.
5. Cambie los valores del **intervalo de direcciones** que se proporcionan para que coincidan con los requisitos de configuración y, después, seleccione **Aceptar**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Creación de una puerta de enlace de red virtual en Azure y Azure Stack.

Para crear una puerta de enlace de red virtual en Azure, use los pasos siguientes.

1. En el lado izquierdo de la página del portal, seleccione **+** y escriba "Puerta de enlace de red virtual" en el cuadro de búsqueda.
2. En **Resultados**, haga clic en **Puerta de enlace de red virtual**.
3. En **Puerta de enlace de red virtual**, seleccione **Crear** para abrir la página **Crear puerta de enlace de red virtual**.
4. En **Crear puerta de enlace de red virtual**, especifique los valores para la puerta de enlace de red, tal como se muestra en los **valores de ejemplo del tutorial**, y los siguientes valores adicionales:

   - **SKU**: básica.
   - **Red virtual**: seleccione la red virtual que ha creado antes. La subred de puerta de enlace que ha creado se selecciona automáticamente.
   - **Primera configuración de IP**:  IP pública de la puerta de enlace.
     - Seleccione **Crear configuración de IP de puerta de enlace**, que le lleva a la página **Elegir dirección IP pública**.
     - Seleccione **+Crear nueva** para abrir la página **Crear dirección IP pública**.
     - Escriba un **nombre** para la dirección IP pública. Deje el valor de SKU en **Básica** y seleccione **Aceptar** para guardar los cambios.

       > [!Note]
       > Actualmente, VPN Gateway solo admite la asignación de direcciones IP públicas dinámicas. Sin embargo, esto no significa que la dirección IP cambie después de que se haya asignado a la puerta de enlace VPN. La única vez que la dirección IP pública cambia es cuando la puerta de enlace se elimina y se vuelve a crear. El cambio de tamaño, el restablecimiento u otras actualizaciones u operaciones de mantenimiento interno realizadas en la puerta de enlace VPN no cambia la dirección IP.

4. Compruebe la configuración de la puerta de enlace.
5. Seleccione **Crear** para crear la puerta de enlace VPN. Se validará la configuración de la puerta de enlace y verá el icono "Implementando la puerta de enlace de red virtual" en el panel.

   >[!Note]
   >La creación de una puerta de enlace puede tardar hasta 45 minutos. Es posible que tenga que actualizar la página de portal para ver el estado completado.

    Una vez creada la puerta de enlace, puede ver la dirección IP que se le ha asignado consultando la red virtual en el portal. La puerta de enlace aparece como un dispositivo conectado. Para más información acerca de la puerta de enlace, seleccione el dispositivo.

6. Repita los pasos anteriores (1-5) en la implementación de Azure Stack Hub.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack-hub"></a>Creación de la puerta de enlace de red virtual local de Azure y Azure Stack Hub

La puerta de enlace de red local suele hacer referencia a la ubicación local. Asignará al sitio un nombre al que puedan hacer referencia Azure o Azure Stack Hub y, luego, especificará lo siguiente:

- La dirección IP del dispositivo VPN local para el que va a crear una conexión.
- Los prefijos de dirección IP que se enrutarán mediante la puerta de enlace VPN al dispositivo VPN. Los prefijos de dirección que especifique son los prefijos que se encuentran en la red local.

  >[!Note]
  >Si la red local cambia o necesita cambiar la dirección IP pública del dispositivo VPN, puede actualizar fácilmente los valores más adelante.

1. En el portal, seleccione **+Crear un recurso**.
2. En el cuadro de búsqueda, escriba **Puerta de enlace de red local** y, luego, seleccione **Entrar** para realizar la búsqueda. Se mostrará una lista de resultados.
3. Seleccione **Puerta de enlace de red local** y, después, **Crear** para abrir la página **Crear puerta de enlace de red local**.
4. En **Crear puerta de enlace de red local**, especifique los valores de la puerta de enlace de red local mediante nuestros **valores de ejemplo del tutorial**. Incluya los siguientes valores adicionales.

    - **Dirección IP**: dirección IP pública del dispositivo VPN al que quiere que se conecte Azure o Azure Stack Hub. Especifique una dirección IP pública válida que no esté detrás de un NAT, para que Azure pueda llegar a la dirección. Si no tiene la dirección IP en este momento, puede usar un valor del ejemplo como marcador de posición, pero deberá volver y reemplazar el marcador de posición por la dirección IP pública del dispositivo VPN. Azure no puede conectarse al dispositivo hasta que proporcione una dirección válida.
    - **Espacio de direcciones**: intervalo de direcciones de la red que representa esta red local. Puede agregar varios intervalos de espacios de direcciones. Asegúrese de que los intervalos que especifique no se superpongan con los de otras redes a las que quiera conectarse. Azure enrutará el intervalo de direcciones que especifique a la dirección IP del dispositivo VPN local. Utilice sus propios valores si quiere conectarse a su sitio local, no a un valor de ejemplo.
    - **Configurar BGP**: usar solo al configurar BGP. En caso contrario, no seleccione esta opción.
    - **Suscripción**: compruebe que se muestra la suscripción correcta.
    - **Grupo de recursos**: seleccione el grupo de recursos que quiere usar. Puede crear un grupo de recursos nuevo o seleccionar uno ya creado.
    - **Ubicación**: seleccione la ubicación en la que se creará este objeto. Puede seleccionar la misma ubicación en la que reside la red virtual, pero no es obligatorio.
5. Cuando termine de especificar los valores requeridos, seleccione **Crear** para crear la puerta de enlace de red local.
6. Repita estos pasos (1-5) en la implementación de Azure Stack Hub.

## <a name="configure-your-connection"></a>Configuración de la conexión

Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. El dispositivo VPN que configure se denomina conexión. Para configurar la conexión, necesita:

- Una clave compartida. Esta clave es la misma clave compartida que se especifica al crear la conexión VPN de sitio a sitio. En estos ejemplos se utiliza una clave compartida básica. Se recomienda que genere y utilice una clave más compleja.
- La dirección IP pública de la puerta de enlace de red virtual. Puede ver la dirección IP pública mediante Azure Portal, PowerShell o la CLI. Para buscar la dirección IP pública de la puerta de enlace de VPN mediante Azure Portal, vaya a las puertas de enlace de red virtual y seleccione el nombre de su puerta de enlace.

Utilice los pasos siguientes para crear la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN local.

1. En Azure Portal, haga clic en **+Crear un recurso**.
2. Busque **conexiones**.
3. En **Resultados**, seleccione **Conexiones**.
4. En **Conexión**, seleccione **Crear**.
5. En **Crear conexión**, configure las siguientes opciones:

    - **Tipo de conexión**: seleccione De sitio a sitio (IPsec).
    - **Grupo de recursos**: seleccione el grupo de recursos de prueba.
    - **Puerta de enlace de red virtual**: seleccione la puerta de enlace de red virtual que ha creado.
    - **Puerta de enlace de red local**: seleccione la puerta de enlace de red local que ha creado.
    - **Nombre de la conexión**: este nombre se rellena automáticamente con los valores de las dos puertas de enlace.
    - **Clave compartida**: este valor debe ser el mismo que el que usa para el dispositivo VPN local. En el ejemplo del tutorial se usa "abc123", pero debería usar algo más complejo. Lo importante es que este valor DEBE ser el mismo que el que se especificó al configurar el dispositivo VPN.
    - Los valores de **Suscripción**, **Grupo de recursos** y **Ubicación** son fijos.

6. Haga clic en **Aceptar** para crear la conexión.

La conexión se puede ver en la página **Conexiones** de la puerta de enlace de red virtual. El estado pasará de *Desconocido* a *Conectando* y luego a *Correcto*.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
