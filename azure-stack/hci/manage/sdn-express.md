---
title: Implementación de una infraestructura de SDN mediante SDN Express
description: Aprenda a implementar una infraestructura de SDN mediante SDN Express
author: v-dasis
ms.topic: how-to
ms.date: 01/22/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: ec4c348242910eaf78831b59659bd5943f9cb854
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2021
ms.locfileid: "98782022"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Implementación de una infraestructura de SDN mediante SDN Express

> Se aplica a Azure Stack HCI, versión 20H2

En este tema, implementará una infraestructura completa de red definida por software (SDN) mediante scripts de PowerShell de SDN Express. La infraestructura incluye una controladora de red, un equilibrador de carga de software (SLB) y una puerta de enlace, todo ello de alta disponibilidad.  

Los scripts admiten una implementación por fases, en la que solo se puede implementar la controladora de red para lograr un conjunto básico de funcionalidades con requisitos de red mínimos. También puede implementar Controladora de red mediante el asistente de creación del clústeres de Windows Admin Center. Para implementar otros componentes de SDN, como el SLB y la puerta de enlace, debe usar los scripts de SDN Express.

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

## <a name="run-the-sdn-express-scripts"></a>Ejecución de los scripts de SDN Express

1. Vaya al repositorio de [SDN Express](https://github.com/microsoft/SDN) en GitHub.

1. Descargue los archivos del repositorio en el equipo de implementación designado. Seleccione **Clone** (Clonar) o **Download ZIP** (Descargar ZIP).

    > [!NOTE]
    > El equipo de implementación designado debe ejecutar Windows Server 2016 o versiones posteriores.

1. Extraiga el archivo ZIP y copie la carpeta `SDNExpress` en la carpeta `C:\` del equipo de implementación.

1. Vaya a la carpeta `C:\SDNExpress\scripts`.

1. Ejecute el archivo de script `SDNExpress.ps1`. Este script usa un archivo de implementación (PSD) de PowerShell como entrada para los distintos valores de configuración. Consulte el archivo `README.md` para obtener instrucciones sobre cómo ejecutar el script.  

1. Use un VHDX que contenga el sistema operativo Azure Stack HCI como origen para crear las máquinas virtuales de SDN. Si ha descargado e instalado el sistema operativo Azure Stack HCI desde una imagen ISO, puede crear este VHDX con la utilidad `convert-windowsimage`, tal y como se describe en la documentación.

1. Personalice el archivo `SDNExpress\scripts\MultiNodeSampleConfig.psd1` cambiando los valores específicos para que se adapten a su infraestructura, incluidos los nombres de host, los nombres de dominio, los nombres de usuario y las contraseñas, así como la información de red de las redes enumeradas, tal y como se indica en el tema [Planeamiento de una infraestructura de red definida por software](../concepts/plan-software-defined-networking-infrastructure.md). Este archivo tiene información específica acerca de lo que se debe rellenar en función de si va a implementar solo la controladora de red, o también la puerta de enlace y el equilibrador de carga de software.

1. Ejecute el siguiente script desde una cuenta de usuario con credenciales de administrador en los hosts de Hyper-V:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Una vez creadas las máquinas virtuales de la controladora de red, configure las actualizaciones dinámicas de DNS para el nombre de clúster Network Controller en el servidor DNS. Para más información, consulte el paso 3 de [Requisitos de implementación de Controladora de red](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Pasos siguientes

Administración de las máquinas virtuales. Para más información, consulte [Administración de máquinas virtuales](../manage/vm.md).