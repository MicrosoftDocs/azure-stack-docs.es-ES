---
title: Uso de un adaptador de red de Azure para conectar un servidor a una instancia de Azure Virtual Network
description: En este artículo se proporcionan los requisitos y los pasos para usar el adaptador de red de Azure para conectar un servidor a una instancia de Azure Virtual Network.
ms.topic: article
author: thomasmaurer
ms.author: thmaure
ms.date: 07/14/2020
ms.openlocfilehash: 1bec00f972cd0cfb1b56aeae831dd1ba2914ab33
ms.sourcegitcommit: 2be3dd5419b0d003a9598a42541ebb1d251aea3d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2020
ms.locfileid: "86393634"
---
# <a name="use-azure-network-adapter-to-connect-a-server-to-an-azure-virtual-network"></a>Uso de un adaptador de red de Azure para conectar un servidor a una instancia de Azure Virtual Network

>Se aplica a: Windows Server 2019, Windows Server 2016 R2, Windows Server 2012

Muchas cargas de trabajo que se ejecutan en entornos locales y en entornos multinube requieren conexiones a máquinas virtuales que se ejecutan en Microsoft Azure. Para conectar un servidor a una instancia de Azure Virtual Network, tiene varias opciones, entre las que se incluyen VPN de sitio a sitio, Azure Express Route y VPN de punto a sitio.

Windows Admin Center y el adaptador de red de Azure proporcionan una experiencia en un solo clic para conectar el servidor a la red virtual con una conexión [VPN de punto a sitio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). El proceso automatiza la configuración de la puerta de enlace de red virtual y el cliente VPN local.

## <a name="when-to-use-azure-network-adapter"></a>Cuándo usar un adaptador de red de Azure
Las conexiones VPN de punto a sitio del adaptador de red de Azure resultan útiles cuando se desea establecer conexión con la red virtual desde una ubicación remota como, por ejemplo, una sucursal, tienda u otra ubicación. También puede usar el adaptador de red de Azure en lugar de una VPN de sitio a sitio si solo necesita unos pocos servidores para conectarse a una red virtual. Las conexiones del adaptador de red de Azure no requieren un dispositivo VPN ni una dirección IP pública para funcionar.

### <a name="requirements"></a>Requisitos
El uso de un adaptador de red de Azure para conectarse a una red virtual requiere lo siguiente:
- Una cuenta de Azure con al menos una suscripción activa.
- Una red virtual existente.
- Acceso a Internet para los servidores de destino que desea conectar a la red virtual de Azure.
- Una conexión de Windows Admin Center a Azure.
  Para más información, consulte [Configuración de la integración con Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).
- La versión más reciente de Windows Admin Center.
  Para más información, consulte [Windows Admin Center](https://www.microsoft.com/windows-server/windows-admin-center).

> [!NOTE]
> No es necesario instalar Windows Admin Center en el servidor que desea conectar a Azure. Sin embargo, puede hacerlo en un escenario de un solo servidor.

## <a name="add-an-azure-network-adapter-to-a-server"></a>Incorporación de un adaptador de red de Azure a un servidor
Para configurar el adaptador de red de Azure, vaya a la extensión de red de este en Windows Admin Center.

En Windows Admin Center:
1. Vaya al servidor que hospeda las máquinas virtuales que desea agregar al adaptador de red de Azure.
1. En **Herramientas**, seleccione **Redes**.
1. Seleccione **Agregar adaptador de red de Azure**.
1. En el panel **Agregar adaptador de red de Azure**, escriba la siguiente información necesaria y, a continuación, seleccione **Crear**:
    - **Suscripción**
    - **Ubicación**
    - **Virtual Network**
    - **Subred de puerta de enlace** (si no existe)
    - **SKU de puerta de enlace** (si no existe)
    - **Espacio de direcciones de cliente**

        El grupo de direcciones de cliente es un intervalo de direcciones IP privadas que usted especifica. Los clientes que se conectan de forma dinámica a través de una VPN de punto a sitio reciben una dirección IP de este intervalo. Use un intervalo de direcciones IP privadas que no se superponga a la ubicación local desde la que se va a conectar ni a la red virtual a la que desea conectarse. Se recomienda el uso de direcciones IP que se encuentren en los intervalos designados para redes privadas (10.x.x.x, 192.168.x.x o 172.16.0.0 a 172.31.255.255).

    - **Certificado de autenticación**

        Azure usa certificados para autenticar a los clientes que se conectan a una red virtual a través de una conexión VPN de punto a sitio. La información de clave pública del certificado raíz se carga en Azure. En ese momento, Azure considera que el certificado raíz es "de confianza" para conectarse desde una conexión de punto a sitio a la red virtual. Los certificados de cliente se deben generar a partir del certificado raíz de confianza e instalarse en el servidor cliente. El certificado de cliente se utiliza para autenticar al cliente cuando se inicia una conexión con la red virtual.
    
        Para más información, consulte la sección "Configuración del tipo de autenticación" de [Configuración de una conexión VPN de punto a sitio a una red virtual mediante la autenticación nativa de los certificados de Azure: Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal).

    :::image type="content" source="media/azure-network-adapter/add-azure-network-adapter.png" alt-text="Panel Agregar adaptador de red de Azure en Windows Admin Center.":::

> [!NOTE]
> Los dispositivos de red, como VPN Gateway y Application Gateway que se ejecutan en una red virtual, tienen un costo adicional. Para más información, consulte [precios de Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/).

Si no hay ninguna puerta de enlace de Azure Virtual Network, Windows Admin Center creará una automáticamente. El proceso de configuración puede tardar hasta 25 minutos. Una vez creado el adaptador de red de Azure, puede empezar a acceder a las máquinas virtuales de la red virtual directamente desde el servidor.

Si ya no necesita conectividad, en **Redes**, seleccione el adaptador de red de Azure que desea desconectar y, en el menú superior, seleccione **Desconectar** y, a continuación, en la ventana emergente de confirmación **Desconectar VPN**, seleccione **Sí**.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Azure Virtual Network, consulte también:

- [Preguntas más frecuentes (P+F) acerca de Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
