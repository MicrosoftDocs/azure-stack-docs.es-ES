---
title: Implementación de una infraestructura de SDN mediante SDN Express
description: Aprenda a implementar una infraestructura de SDN mediante SDN Express
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e367602252207a673316caf3482d7805bff02ba8
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647817"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Implementación de una infraestructura de SDN mediante SDN Express

> Se aplica a Azure Stack HCI, versión 20H2

En este tema, implementará una infraestructura completa de red definida por software (SDN) mediante scripts de PowerShell de SDN Express. La infraestructura puede incluir un controlador de red (NC), un equilibrador de carga de software (SLB) y una puerta de enlace (GW), todo ello con alta disponibilidad (HA).  Los scripts admiten una implementación por fases, en la que puede implementar solo el componente del controlador de red para lograr un conjunto básico de funcionalidades con requisitos de red mínimos.

También puede implementar una infraestructura de SDN mediante System Center Virtual Machine Manager (VMM). Para obtener más información, consulte [Administrar recursos de SDN en el tejido de VMM](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar una implementación de SDN, planee y configure la infraestructura de red física y del host. Consulte los artículos siguientes:

- [Requisitos de la red física](../concepts/physical-network-requirements.md)
- [Requisitos de la red del host](../concepts/host-network-requirements.md)
- [Creación de un clúster mediante Windows Admin Center](../deploy/create-cluster.md)
- [Creación de un clúster mediante Windows PowerShell](../deploy/create-cluster-powershell.md)
- [Planeamiento de una infraestructura de red definida por software](../concepts/plan-software-defined-networking-infrastructure.md)

No es necesario implementar todos los componentes de SDN. Vea la sección [Implementación por fases](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) de [Planeamiento de una infraestructura de red definida por software](../concepts/plan-software-defined-networking-infrastructure.md) para determinar qué componentes de infraestructura necesita y, a continuación, ejecute los scripts según corresponda.

Asegúrese de que todos los servidores host tengan el sistema operativo Azure Stack HCI instalado. Para más información, consulte [Implementación del sistema operativo de Azure Stack HCI](../deploy/operating-system.md).

## <a name="requirements"></a>Requisitos

Se deben cumplir los requisitos siguientes para una implementación correcta de SDN:

- Todos los servidores host deben tener habilitado Hyper-V
- Todos los servidores host deben estar unidos a Active Directory
- Se debe crear un conmutador virtual
- La red física debe estar configurada para las subredes y VLAN definidas en el archivo de configuración
- El archivo VHDX especificado en el archivo de configuración debe ser accesible desde el equipo de implementación donde se ejecuta el script de SDN Express

## <a name="create-the-vdx-file"></a>Creación del archivo VDX

SDN usa un archivo VHDX que contiene el sistema operativo Azure Stack HCI como origen para crear las máquinas virtuales de SDN. La versión del sistema operativo del archivo VHDX debe coincidir con la versión que usan los hosts de Hyper-V de Azure Stack HCI. Todos los componentes de la infraestructura de SDN usan este archivo VHDX.

Si ha descargado e instalado el sistema operativo Azure Stack HCI desde una imagen ISO, puede crear el archivo VHDX con la utilidad [Convert-WindowsImage ](https://www.powershellgallery.com/packages/Convert-WindowsImage/10.0).

A continuación se muestra un ejemplo mediante `Convert-WindowsImage`:

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>Descarga del repositorio de GitHub

Los archivos de script de SDN Express están activos en GitHub. El primer paso consiste en obtener los archivos y carpetas necesarios en el equipo de implementación.

1. Vaya al repositorio de GitHub [SDN de Microsoft](https://github.com/microsoft/SDN).

1. En el repositorio, expanda la lista desplegable **Code** (Código) y, a continuación, elija **Clone** (Clonar) o **Download ZIP** (Descargar archivo ZIP) para descargar los archivos de SDN en el equipo de implementación designado.

    > [!NOTE]
    > El equipo de implementación designado debe ejecutar Windows Server 2016 o versiones posteriores.

1. Extraiga el archivo ZIP y copie la carpeta `SDNExpress` en la carpeta `C:\` del equipo de implementación.

## <a name="edit-the-configuration-file"></a>Editar el archivo de configuración

El archivo de datos de configuración de `MultiNodeSampleConfig.psd1` de PowerShell contiene todos los parámetros y la configuración necesarios para el script de SDN Express como entrada para los distintos parámetros y valores de configuración. Este archivo tiene información específica acerca de lo que se debe rellenar en función de si va a implementar solo la controladora de red, o también la puerta de enlace y el equilibrador de carga de software. Para obtener información detallada, consulte el tema [Planeamiento de una infraestructura de red definida por software](../concepts/plan-software-defined-networking-infrastructure.md).

Vaya a la carpeta `C:\SDNExpress\scripts` y abra el archivo `MultiNodeSampleConfig.psd1` en el editor de texto que prefiera. Cambie los valores de los parámetros específicos para que se adapten a la infraestructura e implementación:

### <a name="general-settings-and-parameters"></a>Configuración general y parámetros

SDN usa en general la configuración y los parámetros para todas las implementaciones:

- **VHDPath**: ruta de acceso del archivo VHD usada por todas las máquinas virtuales de infraestructura de SDN (NC, SLB, GW)
- **VHDFile** : nombre del archivo VHD usado por todas las máquinas virtuales de infraestructura de SDN
- **VMLocation**: ruta de acceso de archivo de las máquinas virtuales de infraestructura de SDN
- **JoinDomain**: dominio al que se unen las máquinas virtuales de infraestructura de SDN
- **SDNMacPoolStart**: comienzo del grupo de direcciones MAC de las máquinas virtuales de la carga de trabajo de cliente
- **SDNMacPoolEnd**: final del grupo de direcciones MAC de las máquinas virtuales de la carga de trabajo de cliente
- **ManagementSubnet**: subred de la red de administración usada por el NC para administrar los componentes de hosts de Hyper-V, SLB y GW
- **ManagementGateway**: dirección de la puerta de enlace de la red de administración
- **ManagementDNS**: servidor DNS de la red de administración
- **ManagementVLANID**: identificador de VLAN de la red de administración
- **DomainJoinUsername**: nombre de usuario del administrador
- **LocalAdminDomainUser**: contraseña del administrador
- **RestName**: nombre DNS que usan los clientes de administración (como Windows Admin Center) para comunicarse con el NC
- **HyperVHosts**: servidores host que se administrarán mediante el controlador de red
- **NCUsername**: nombre de usuario de la cuenta del controlador de red
- **ProductKey**: clave de producto de las máquinas virtuales de infraestructura de SDN
- **SwitchName**: solo es necesario si existe más de un conmutador virtual en los hosts de Hyper-V
- **VMMemory**: memoria (en GB) asignada a las máquinas virtuales de infraestructura. El valor predeterminado es 4 GB.
- **VMProcessorCount**: número de procesadores asignados a las máquinas virtuales de infraestructura. El valor predeterminado es 8.
- **Locale**: si no se especifica, se usa la configuración regional del equipo de implementación.
- **TimeZone**: si no se especifica, se usa la zona horaria local del equipo de implementación.

Las contraseñas se pueden incluir opcionalmente si se almacenan cifradas como cadenas seguras con codificación de texto.  Las contraseñas solo se usarán si los scripts de SDN Express se ejecutan en el mismo equipo en el que se cifraron las contraseñas; de lo contrario, pedirá estas contraseñas:

- **DomainJoinSecurePassword**: para la cuenta de dominio
- **LocalAdminSecurePassword**: para la cuenta de administrador local
- **NCSecurePassword**: para la cuenta del controlador de red

> [!NOTE]
> El script de SDN Express no admite el direccionamiento DHCP. Asegúrese de que las direcciones MAC de todas las máquinas virtuales de la infraestructura de SDN estén fuera del intervalo del parámetro `SDNMACPool`.

### <a name="network-controller-vm-section"></a>Sección de máquinas virtuales del controlador de red

La sección `NCs = @()` se usa para las máquinas virtuales del controlador de red. Asegúrese de que la dirección MAC de cada máquina virtual del NC esté fuera del intervalo `SDNMACPool` indicado en la configuración general:

- **ComputerName**: nombre de la máquina virtual del NC
- **HostName**: nombre de host del servidor donde se encuentra la máquina virtual del NC
- **ManagementIP**: dirección IP de la red de administración de la máquina virtual del NC
- **MACAddress**: dirección MAC de la máquina virtual del NC

### <a name="software-load-balancer-vm-section"></a>Sección de máquinas virtuales del equilibrador de carga de software

La sección `Muxes = @()` se usa para las máquinas virtuales del SLB. Asegúrese de que la dirección MAC de cada máquina virtual del SLB esté fuera del intervalo `SDNMACPool` indicado en la configuración general. Deje esta sección vacía (`Muxes = @()`) si no implementa el componente del SLB:

- **ComputerName**: nombre de la máquina virtual del SLB
- **HostName**: nombre de host del servidor donde se encuentra la máquina virtual del SLB
- **ManagementIP**: dirección IP de la red de administración de la máquina virtual del SLB
- **MACAddress**: dirección MAC de la máquina virtual del SLB
- **PAIPAddress**: dirección IP de la red del proveedor (PA) de la máquina virtual del SLB
- **PAMACAddress**: dirección MAC de la red del proveedor (PA) de la máquina virtual del SLB

### <a name="gateway-vm-section"></a>Sección de máquinas virtuales de la puerta de enlace

La sección `Gateways = @()` se usa para las máquinas virtuales de la puerta de enlace. Asegúrese de que la dirección MAC de cada máquina virtual de la puerta de enlace esté fuera del intervalo `SDNMACPool` indicado en la configuración general. Deje esta sección vacía (`Gateways = @()`) si no implementa el componente de la puerta de enlace:

- **ComputerName**: nombre de la máquina virtual de la puerta de enlace
- **HostName**: nombre de host del servidor donde se encuentra la máquina virtual de la puerta de enlace
- **ManagementIP**: dirección IP de la red de administración de la máquina virtual de la puerta de enlace
- **MACAddress**: dirección MAC de la máquina virtual de la puerta de enlace
- **FrontEndIp**: dirección IP de front-end de la red del proveedor de la máquina virtual de la puerta de enlace
- **FrontEndMac**: dirección MAC de front-end de la red del proveedor de la máquina virtual de la puerta de enlace
- **BackEndMac**: dirección MAC de back-end de la red del proveedor de la máquina virtual de la puerta de enlace

### <a name="additional-settings-for-slb-and-gateway"></a>Configuración adicional para el SLB y la puerta de enlace

Las máquinas virtuales de la puerta de enlace y el SLB usan los siguientes parámetros adicionales. Deje estos valores en blanco si no va a implementar máquinas virtuales de SLB o de puerta de enlace:

- **SDNASN**: número de sistema autónomo (ASN) utilizado por SDN para el emparejamiento con los conmutadores de red
- **RouterASN**: ASN del enrutador de la puerta de enlace
- **RouterIPAddress**: dirección IP del enrutador de la puerta de enlace
- **PrivateVIPSubnet**: dirección IP virtual (VIP) de la subred privada
- **PublicVIPSubnet**: dirección IP virtual (VIP) de la subred pública

Los parámetros adicionales siguientes solo se usan en las máquinas virtuales de puerta de enlace. Deje estos valores en blanco si no va a implementar máquinas virtuales de puerta de enlace:

- **PoolName**: nombre de grupo usado por todas las máquinas virtuales de puerta de enlace
- **GRESubnet**: VIP de la subred para GRE (si se usan conexiones GRE)
- **Capacity**: capacidad en Kbps de cada máquina virtual de puerta de enlace del grupo

### <a name="settings-for-tenant-overlay-networks"></a>Configuración de redes de superposición de inquilinos

Los siguientes parámetros se usan si va a implementar y administrar redes virtualizadas de superposición para los inquilinos. Si por el contrario usa el controlador de red para administrar redes VLAN tradicionales, estos valores se pueden dejar en blanco.

- **PASubnet**: subred de la red de direcciones del proveedor (PA)
- **PAVLANID**: identificador de VLAN de la red de PA
- **PAGateway**: dirección IP de la puerta de enlace de la red de PA
- **PAPoolStart**: dirección IP inicial del grupo de la red de PA
- **PAPoolEnd**: dirección IP final del grupo de la red de PA

## <a name="run-the-deployment-script"></a>Ejecutar el script de implementación

El script de SDN Express implementa la infraestructura de SDN que ha especificado. Una vez completado el script, la infraestructura de SDN está lista para su uso en las implementaciones de cargas de trabajo de máquinas virtuales.

1. Revise el archivo `README.md` para obtener información de última hora sobre cómo ejecutar el script de implementación.  

1. Ejecute el siguiente comando desde una cuenta de usuario con credenciales administrativas para los servidores host del clúster:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Una vez creadas las máquinas virtuales del NC, configure las actualizaciones dinámicas de DNS para el nombre del clúster del controlador de red en el servidor DNS. Para más información, consulte [Cómo configurar actualizaciones dinámicas de DNS en Windows Server 2003](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-dns-dynamic-updates-windows-server-2003).

## <a name="next-steps"></a>Pasos siguientes

Administración de las máquinas virtuales. Para obtener más información, consulte el artículo [Administración de máquinas virtuales](../manage/vm.md).
