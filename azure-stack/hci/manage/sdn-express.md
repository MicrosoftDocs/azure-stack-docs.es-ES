---
title: Implementación de una infraestructura de SDN mediante SDN Express
description: Aprenda a implementar una infraestructura de SDN mediante SDN Express
author: v-dasis
ms.topic: how-to
ms.date: 02/01/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b8431b7e2cf2cad3d238386619839a760b722042
ms.sourcegitcommit: d74f6d8630783de49667956f39cac67e1968a89d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99473198"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Implementación de una infraestructura de SDN mediante SDN Express

> Se aplica a Azure Stack HCI, versión 20H2

En este tema, implementará una infraestructura completa de red definida por software (SDN) mediante scripts de PowerShell de SDN Express. La infraestructura puede incluir una controladora de red, un equilibrador de carga de software (SLB) y una puerta de enlace, todo ello con alta disponibilidad.  Los scripts admiten una implementación por fases, en la que solo se puede implementar la controladora de red para lograr un conjunto básico de funcionalidades con requisitos de red mínimos. 

También puede implementar una infraestructura de SDN mediante System Center Virtual Machine Manager (VMM). Para obtener más información, consulte [Administrar recursos de SDN en el tejido de VMM](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar la implementación de SDN, planee y configure la infraestructura de red física y del host. Consulte los artículos siguientes:

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

SDN usa un archivo VHDX que contiene el sistema operativo Azure Stack HCI como origen para crear las máquinas virtuales de SDN. La versión del sistema operativo en VHDX debe coincidir con la versión que usan los hosts de Hyper-V.

Si ha descargado e instalado el sistema operativo Azure Stack HCI desde una imagen ISO, puede crear este VHDX con la utilidad `Convert-WindowsImage`, tal y como se describe en el [Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f).

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

1. Vaya al repositorio de [SDN Express](https://github.com/microsoft/SDN) en GitHub.

1. Descargue los archivos del repositorio en el equipo de implementación designado. Seleccione **Clone** (Clonar) o **Download ZIP** (Descargar ZIP).

    > [!NOTE]
    > El equipo de implementación designado debe ejecutar Windows Server 2016 o versiones posteriores.

1. Extraiga el archivo ZIP y copie la carpeta `SDNExpress` en la carpeta `C:\` del equipo de implementación.

## <a name="edit-the-configuration-file"></a>Editar el archivo de configuración

El archivo de datos de configuración de `MultiNodeSampleConfig.psd1` de PowerShell contiene todos los parámetros y la configuración necesarios para el script de SDN Express como entrada para los distintos parámetros y valores de configuración. Este archivo tiene información específica acerca de lo que se debe rellenar en función de si va a implementar solo la controladora de red, o también la puerta de enlace y el equilibrador de carga de software.

Para obtener información detallada, consulte el tema [Planeamiento de una infraestructura de red definida por software](../concepts/plan-software-defined-networking-infrastructure.md).

Empecemos:

1. Vaya a la carpeta `C:\SDNExpress\scripts`.

1. Abra el archivo `MultiNodeSampleConfig.psd1` en el editor de texto que prefiera.

1. Cambiar los valores de parámetros específicos para que se adapten a la infraestructura.

## <a name="run-the-deployment-script"></a>Ejecutar el script de implementación

El script de SDN Express implementa la infraestructura de SDN. Una vez completado el script, la infraestructura está lista para usarse en las implementaciones de cargas de trabajo.

1. Revise el archivo `README.md` para obtener información de última hora sobre cómo ejecutar el script de implementación.  

1. Ejecute el siguiente comando desde una cuenta de usuario con credenciales administrativas para los servidores host del clúster:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Una vez creadas las máquinas virtuales de la controladora de red, configure las actualizaciones dinámicas de DNS para el nombre de clúster Network Controller en el servidor DNS. Para más información, consulte el paso 3 de [Requisitos de implementación de Controladora de red](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Pasos siguientes

Administración de las máquinas virtuales. Para más información, consulte [Administración de máquinas virtuales](../manage/vm.md).