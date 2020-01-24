---
title: Conexión de dos entornos de Azure Stack Hub mediante el emparejamiento de VNET | Microsoft Docs
description: Aprenda a conectar dos entornos de Azure Stack Hub mediante el emparejamiento de VNET.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: bf855be0ec6fc63be4ab816dac4dde170b24fdad
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536086"
---
# <a name="vnet-peering-in-azure-stack-hub-with-vms"></a>Emparejamiento de VNET en Azure Stack Hub con máquinas virtuales

Puede conectar dos redes virtuales de Azure Stack Hub entre sí en el mismo entorno de Azure Stack Hub. Actualmente no es posible conectar redes virtuales de Azure Stack Hub mediante la [puerta de enlace de red virtual](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences)integrada. Debe usar aplicaciones NVA para crear un túnel de VPN entre dos redes virtuales de Azure Stack Hub. En las referencias de las plantillas de este artículo, se implementan dos máquinas virtuales con Windows Server 2016 con RRAS instalado. Se configuran los dos servidores RRAS para implementar un túnel IKEv2 de S2SVPN entre dos redes virtuales. Se crean las reglas de NSG y UDR para que pueda realizarse el enrutamiento entre las subredes de las redes virtuales designadas como **internas**. 

Este patrón de implementación es la base que permitirá crear túneles de VPN no solo en una instancia de Azure Stack Hub, sino también entre instancias de Azure Stack Hub y otros recursos, como las redes locales con el uso de túneles de VPN de sitio a sitio de RRAS de Windows. 

Puede encontrar las plantillas en el repositorio de GitHub [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns
). La plantilla se encuentra en la carpeta **S2SVPNTunnel**.

![texto alternativo](./media/azure-stack-network-howto-vnet-peering/overview.png)

## <a name="requirements"></a>Requisitos

- Una implementación en la que se han aplicado las actualizaciones más recientes. 
- Elementos de Marketplace requeridos para Azure Stack Hub:
    -  Windows Server 2016 Datacenter (se recomienda la última compilación)
    -  Custom Script Extension

## <a name="things-to-consider"></a>Aspectos que se deben tener en cuenta:

- Se aplica un grupo de seguridad de red a la subred del túnel de la plantilla. Se recomienda proteger la subred interna en cada red virtual con un grupo de seguridad de red adicional.
- Se aplica una regla de denegación de RDP al grupo de seguridad de red del túnel y debe establecerse en permitir si tiene previsto acceder a las máquinas virtuales mediante la dirección IP pública.
- Esta solución no tiene en cuenta la resolución de DNS.
- La combinación de nombre de red virtual y nombre de máquina virtual debe tener menos de 15 caracteres.
- Esta plantilla está diseñada para tener los nombres de red virtual personalizados para VNet1 y VNet2.
- Esta plantilla usa BYOL de Windows.
- Al eliminar el grupo de recursos, actualmente en (1907), tiene que separar manualmente el grupo de seguridad de red de la subred del túnel para asegurarse de que se completa el grupo de recursos de eliminación.
- Esta plantilla utiliza una máquina virtual DS3v2. El servicio RRAS instala y ejecuta el servidor de SQL Server interno de Windows. Esto puede producir problemas de memoria si el tamaño de la máquina virtual es demasiado pequeño. Valide el rendimiento antes de reducir el tamaño de la máquina virtual.
- Esta no es una solución de alta disponibilidad. Si necesita una solución de más alta disponibilidad, puede agregar una segunda máquina virtual. Tendrá que cambiar manualmente la ruta de la tabla de rutas a la dirección IP interna de la interfaz secundaria. También debe configurar los túneles múltiples para la conexión cruzada.

## <a name="options"></a>Opciones

- Puede usar su propia cuenta de almacenamiento de blobs y el token de SAS con los parámetros _artifactsLocation y _artifactsLocationSasToken.
- Hay dos salidas en esta plantilla INTERNALSUBNETREFVNET1 y INTERNALSUBNETREFVNET2, que son los identificadores de recursos para las subredes internas, si desea usarlas en un patrón de implementación de estilo de canalización.

La plantilla proporciona valores predeterminados para la nomenclatura de redes virtuales y el direccionamiento IP. Requiere una contraseña para el administrador (rrasadmin) y también ofrece la posibilidad de usar su propio blob de almacenamiento con el token de SAS. Tenga cuidado de mantener estos valores dentro de los intervalos legales, ya que puede producirse un error en la implementación. El paquete de DSC de PowerShell se ejecuta en cada máquina virtual de RRAS e instala el enrutamiento y todos los servicios y características dependientes necesarios. Este DSC se puede personalizar aún más si fuera necesario. La extensión de script personalizado ejecuta el siguiente script y `Add-Site2Site.ps1` configura el túnel VPNS2S entre los dos servidores RRAS con una clave compartida. Puede ver la salida detallada de la extensión de script personalizado para ver los resultados de la configuración del túnel de VPN.

![texto alternativo](./media/azure-stack-network-howto-vnet-peering/s2svpntunnels2.png)

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md)  