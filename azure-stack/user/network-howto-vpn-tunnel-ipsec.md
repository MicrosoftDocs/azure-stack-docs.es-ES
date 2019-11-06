---
title: Creación de un túnel de VPN con IPSEC en Azure Stack | Microsoft Docs
description: Aprenda a crear un túnel de VPN con IPSEC en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 762d3f631823d1acb8445148a164a18605fa3762
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73168232"
---
# <a name="how-to-create-a-vpn-tunnel-using-ipsec--in-azure-stack"></a>Creación de un túnel de VPN con IPSEC en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar la plantilla de Resource Manager de Azure Stack en esta solución para conectar dos redes virtuales de Azure Stack en el mismo entorno. [No puede conectar redes virtuales de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences) con la puerta de enlace de red virtual integrada. Por ahora, debe usar aplicaciones virtuales de red (NVA) para crear un túnel de VPN entre dos redes virtuales de Azure Stack. La plantilla de la solución implementa dos máquinas virtuales Windows Server 2016 con RRAS instalado. La solución configura los dos servidores RRAS para usar un túnel IKEv2 de S2SVPN entre las dos redes virtuales. Se crean las reglas adecuadas de grupo de seguridad de red y de UDR para permitir el enrutamiento entre las subredes de cada red virtual designada como **interna**. 

Esta solución es la base que le permitirá crear túneles de VPN no solo dentro de la instancia de Azure Stack, sino también entre instancias de Azure Stack y otros recursos, como las redes locales con el uso de túneles de VPN de sitio a sitio de RRAS de Windows.

Puede encontrar las plantillas en el repositorio de GitHub [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). La plantilla se encuentra en la carpeta **rras-gre-vnet-vnet**. 

![texto alternativo](./media/azure-stack-network-howto-vpn-tunnel-ipsec/overview.png)

## <a name="requirements"></a>Requisitos

- ASDK o el sistema integrado de Azure Stack con las últimas actualizaciones aplicadas. 
- Elementos de Marketplace requeridos para Azure Stack:
    -  Windows Server 2016 Datacenter o Windows Server 2019 Datacenter (se recomienda la última compilación)
    -  Custom Script Extension

## <a name="things-to-consider"></a>Aspectos que se deben tener en cuenta:

- Se aplica un grupo de seguridad de red a la subred del túnel de la plantilla.  Se recomienda proteger la subred interna en cada red virtual con un grupo de seguridad de red adicional.
- Se aplica una regla de denegación de RDP al grupo de seguridad de red del túnel y debe establecerse en permitir si tiene previsto acceder a las máquinas virtuales mediante la dirección IP pública.
- Esta solución no tiene en cuenta la resolución de DNS.
- La combinación de nombre de la red virtual y el nombre de la máquina virtual debe tener menos de 15 caracteres.
- Esta plantilla está diseñada para tener los nombres de red virtual personalizados para VNet1 y VNet2.
- Esta plantilla usa BYOL de Windows.
- Al eliminar el grupo de recursos, actualmente en (1907), tiene que separar manualmente el grupo de seguridad de red de la subred del túnel para asegurarse de que se completa el grupo de recursos de eliminación.
- Esta plantilla utiliza una máquina virtual DS3v2.  El servicio RRAS instala y ejecuta el servidor de SQL Server interno de Windows.  Esto puede producir problemas de memoria si el tamaño de la máquina virtual es demasiado pequeño.  Valide el rendimiento antes de reducir el tamaño de la máquina virtual.
- Esta no es una solución de alta disponibilidad.  Si necesita una solución de más alta disponibilidad, puede agregar una segunda máquina virtual. Tendrá que cambiar manualmente la ruta de la tabla de rutas a la dirección IP interna de la interfaz secundaria.  También debe configurar los túneles múltiples para la conexión cruzada.

## <a name="optional"></a>Opcional

- Puede usar su propia cuenta de almacenamiento de blobs y el token de SAS con los parámetros _artifactsLocation y _artifactsLocationSasToken.
- Hay dos salidas en esta plantilla INTERNALSUBNETREFVNET1 y INTERNALSUBNETREFVNET2, que son los identificadores de recursos para las subredes internas, si desea usarlas en un patrón de implementación de estilo de canalización.

Esta plantilla proporciona valores predeterminados para la nomenclatura de redes virtuales y el direccionamiento IP.  Requiere una contraseña para el administrador (rrasadmin) y también ofrece la posibilidad de usar su propio blob de almacenamiento con el token de SAS.  Tenga cuidado de mantener estos valores dentro de los intervalos legales, ya que puede producirse un error en la implementación.  El paquete de DSC de PowerShell se ejecuta en cada máquina virtual de RRAS e instala el enrutamiento y todos los servicios y características dependientes necesarios.  Este DSC se puede personalizar aún más si fuera necesario.  La extensión de script personalizado ejecuta el siguiente script y Add-Site2SiteIKE.ps1 configura el túnel de VPNS2S entre los dos servidores RRAS con una clave compartida.  Puede ver la salida detallada de la extensión de script personalizado para ver los resultados de la configuración del túnel de VPN.

![texto alternativo](./media/azure-stack-network-howto-vpn-tunnel-ipsec/s2svpntunnel.png)

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack](azure-stack-network-differences.md)  
[Configuración de un túnel de VPN de sitio a sitio múltiple](network-howto-vpn-tunnel.md)  
[Creación de un túnel de VPN mediante GRE](network-howto-vpn-tunnel-gre.md)