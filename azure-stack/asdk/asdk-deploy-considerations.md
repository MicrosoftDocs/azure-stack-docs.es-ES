---
title: Requisitos y consideraciones de ASDK | Microsoft Docs
description: Consulte los requisitos de hardware, software y entorno para instalar el Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 05/13/2019
ms.openlocfilehash: 0e5a4e41e4650de8e3cebe7d33b9638890d5bd99
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974569"
---
# <a name="asdk-requirements-and-considerations"></a>Requisitos y consideraciones de ASDK

Antes de implementar el Kit de desarrollo de Azure Stack (ASDK), asegúrese de que el equipo host de ASDK cumple los requisitos que se describen en este artículo.

## <a name="hardware"></a>Hardware

| Componente | Mínima | Recomendado |
| --- | --- | --- |
| Unidades de disco: Sistema operativo |Un disco de sistema operativo con un mínimo de 200 GB disponibles para la partición del sistema (SSD o HDD). |Un disco del sistema operativo con un mínimo de 200 GB disponibles para la partición del sistema (SSD o HDD). |
| Unidades de disco: datos generales del kit de desarrollo<sup>*</sup>  |Cuatro discos. Cada disco proporciona un mínimo de 240 GB de capacidad (SSD o HDD). Se utilizan todos los discos disponibles. |Cuatro discos. Cada disco proporciona un mínimo de 400 GB de capacidad (SSD o HDD). Se utilizan todos los discos disponibles. |
| Proceso: CPU |Socket dual: 16 núcleos físicos (total). |Socket dual: 20 núcleos físicos (total). |
| Proceso: Memoria |192 GB de RAM. |256 GB de RAM. |
| Proceso: BIOS |Hyper-V habilitado (con compatibilidad para SLAT). |Hyper-V habilitado (con compatibilidad para SLAT). |
| Red: NIC |Certificación de Windows Server 2012 R2. No se necesitan características especializadas. | Certificación de Windows Server 2012 R2. No se necesitan características especializadas. |
| Certificación del logotipo de hardware |[Certificado para Windows Server 2012 R2](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0). |[Certificado para Windows Server 2016](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0). |

<sup>*</sup>Si tiene previsto agregar muchos de los [elementos de Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) desde Azure, necesitará más capacidad de la que se recomienda.

### <a name="hardware-notes"></a>Notas de hardware

**Configuración de la unidad de disco de datos:** todas las unidades de datos deben ser del mismo tipo (SAS, SATA o NVMe) y tener la misma capacidad. Si se usan unidades de disco SAS, las unidades de disco debe estar conectadas a través de una ruta de acceso única (no se proporciona compatibilidad con MPIO ni con rutas de acceso múltiples).

**Opciones de configuración HBA**

* (Opción preferida) HBA simple.
* HBA RAID: el adaptador debe configurarse en modo "Paso a través".
* HBA RAID: los discos deben configurarse como único disco, RAID-0.

**Combinaciones de tipos de medios y bus admitidas**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (si el tipo de soporte físico es no especificado o desconocido<sup>*</sup>)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

<sup>*</sup> Los controladores de RAID sin la funcionalidad Paso a través no reconocen el tipo de soporte físico. Estos controladores marca tanto HDD como SSD como No especificado. En ese caso, se usa una unidad SSD como almacenamiento persistente, en lugar de dispositivos de almacenamiento en caché. Por lo tanto, puede implementar ASDK en esas SSD.

**HBA de ejemplo**: LSI 9207 8i, LSI-9300-8i o LSI-9265-8i en modo Paso a través.

Están disponibles configuraciones de OEM de ejemplo.

### <a name="storage-resiliency-for-the-asdk"></a>Resistencia del almacenamiento del ASDK

Al ser un sistema de nodo único, el ASDK no está diseñado para validar la redundancia de producción de un sistema integrado de Azure Stack. Sin embargo, puede aumentar el nivel de redundancia de almacenamiento subyacente del ADSK a través de la mezcla óptima de unidades HDD y SSD. Puede implementar una configuración de espejo bidireccional, similar a una RAID1, en lugar de una configuración de resistencia simple, que es similar a una configuración RAID0. Utilice suficiente capacidad, el tipo y el número de unidades para la configuración de Espacios de almacenamiento directo subyacente.

Para usar una configuración de espejo bidireccional para la resistencia del almacenamiento:

- Necesita tener una capacidad de HDD en el sistema que sea de más de dos terabytes.
- Si no tiene ningún SSD en ASDK, necesitará al menos ocho HDD para la configuración de espejo bidireccional.
- Si dispone de varios SSD en ASDK, junto con HDD, necesitará al menos cinco HDD. Sin embargo, se recomienda seis HDD. Para seis HDD, también es aconsejable tener un mínimo de tres SSD correspondientes en el sistema para que cada disco de caché (SSD) sirva a dos unidades de capacidad (HDD).

Ejemplo de configuración de espejo bidireccional:

- Ocho HDD
- Tres SSD/seis HDD
- Cuatro SSD/ocho HDD

## <a name="operating-system"></a>Sistema operativo
|  | **Requisitos** |
| --- | --- |
| **Versión del SO.** |Windows Server 2016 o posterior. La versión del sistema operativo no es crítica antes de iniciar la implementación, ya que podrá arrancar el equipo host en el disco duro virtual que se incluye en la instalación de Azure Stack. El sistema operativo y todas las revisiones necesarias ya están integradas en la imagen. No use ninguna clave para activar las instancias de Windows Server que se usan en ASDK. |

> [!TIP]
> Después de instalar el sistema operativo, puede usar el [Comprobador de implementación para Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar que el hardware cumple todos los requisitos.

## <a name="account-requirements"></a>Requisitos de cuenta
Normalmente, se implementa ASDK con conectividad a Internet, lo que permite conectarse a Microsoft Azure. En este caso, debe configurar una cuenta de Azure Active Directory (Azure AD) para implementar ASDK.

Si el entorno no está conectado a Internet o no quiere usar Azure AD, puede implementar Azure Stack mediante el uso de los Servicios de federación de Active Directory (AD FS). ASDK incluye sus propias instancias de AD FS y Active Directory Domain Services. Si implementa mediante el uso de esta opción, no tiene que configurar cuentas de antemano.

> [!NOTE]
> Si implementa mediante la opción de AD FS, debe volver a implementar Azure Stack para cambiar a Azure AD.

### <a name="azure-active-directory-accounts"></a>Cuentas de Azure Active Directory
Para implementar Azure Stack mediante una cuenta de Azure AD, debe preparar una cuenta de Azure AD antes de ejecutar el script de PowerShell de implementación. Esta cuenta se convierte en el administrador global para el inquilino de Azure AD. Se usa para aprovisionar y delegar aplicaciones y entidades de servicio para todos los servicios de Azure Stack que interactúan con Azure AD y Graph API. También se utiliza como el propietario de la suscripción de proveedor predeterminada (que puede cambiar más adelante). Puede iniciar sesión en el portal de administradores del sistema de Azure Stack mediante esta cuenta.

1. Cree una cuenta de Azure AD en la que sea el administrador de directorios de al menos una instancia de Azure AD. Si ya tiene una, puede usarla. En caso contrario, puede crearla de forma gratuita en [https://azure.microsoft.com/free/](https://azure.microsoft.com/free/) (en China, visite <https://go.microsoft.com/fwlink/?LinkID=717821> ). Si tiene previsto más adelante [registrar Azure Stack en Azure](asdk-register.md), también debe tener una suscripción en la cuenta recién creada.
   
    Guarde dichas credenciales para usarlas como administrador del servicio. Esta cuenta puede configurar y administrar recursos en la nube, cuentas de usuario, planes de inquilinos, cuotas y precios. En el portal, pueden crear nubes de sitios web, nubes privadas de VM, crear planes y administrar suscripciones de usuario.
1. Cree al menos una cuenta de usuario de prueba en Azure AD para que pueda iniciar sesión en ASDK como un inquilino.
   
   | **Cuenta de Azure Active Directory** | **¿Admitida?** |
   | --- | --- |
   | Cuenta profesional o educativa con una suscripción de Azure global válida |Sí |
   | Cuenta de Microsoft con una suscripción de Azure global válida |Sí |
   | Cuenta profesional o educativa con una suscripción de Azure China válida |Sí |
   | Cuenta profesional o educativa con una suscripción de US Government Azure válida |Sí |

Después de la implementación, no se necesita el permiso de administrador global de Azure AD. Sin embargo, algunas operaciones pueden requerir la credencial de administrador global. Los ejemplos de tales operaciones incluyen un script de instalación del proveedor de recursos o una nueva característica que necesite la concesión de un permiso. Puede restablecer temporalmente los permisos de administrador global de la cuenta o usar una cuenta de administrador global independiente que sea propietaria de la *suscripción del proveedor predeterminada*.

## <a name="network"></a>Red
### <a name="switch"></a>Switch
Un puerto disponible en un conmutador para la máquina de ASDK.  

La máquina de ASDK admite la conexión a un puerto de acceso de conmutador o puerto de tronco. No se requieren características especializadas en el conmutador. Si está usando un puerto de tronco o necesita configurar un identificador de VLAN, tendrá que proporcionar el identificador de VLAN como parámetro de implementación.

### <a name="subnet"></a>Subnet
No conecte el equipo de ASDK a las subredes siguientes:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Estas subredes están reservadas para las redes internas dentro del entorno de ASDK.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Se admite solo IPv4. No se pueden crear redes IPv6.

### <a name="dhcp"></a>DHCP
Asegúrese de que hay un servidor DHCP disponible en la red a la que se conecta la tarjeta NIC. Si DHCP no está disponible, debe preparar una red IPv4 estática adicional además de la que usa el host. Debe proporcionar esa dirección IP y la puerta de enlace como parámetro de implementación.

### <a name="internet-access"></a>Acceso a Internet
Azure Stack necesita acceso a Internet, ya sea directamente o a través de un proxy transparente. Azure Stack no admite la configuración de un proxy web para habilitar el acceso a Internet. Tanto la dirección IP del host como la nueva dirección IP asignada a AzS-BGPNAT01 (mediante DHCP o la dirección IP estática) deben poder acceder a Internet. Se usan los puertos 80 y 443 en los dominios graph.windows.net y login.microsoftonline.com.


## <a name="next-steps"></a>Pasos siguientes

- [Descarga del paquete de implementación de ASDK](asdk-download.md).
- Para más información acerca de Espacios de almacenamiento directo, consulte [Información general de Espacios de almacenamiento directos](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

