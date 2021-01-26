---
title: Implementación de la infraestructura de escritorio virtual (VDI) en Azure Stack HCI
description: En este tema se proporcionan instrucciones sobre cómo planear, configurar e implementar la infraestructura de escritorio virtual (VDI) en el sistema operativo Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: dfe22eb768b5bf661760c31c913d8c93caf590a4
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571602"
---
# <a name="deploy-virtual-desktop-infrastructure-vdi-on-azure-stack-hci"></a>Implementación de la infraestructura de escritorio virtual (VDI) en Azure Stack HCI

>Se aplica a: Azure Stack HCI, versión 20H2

En este tema se proporcionan instrucciones sobre cómo planear, configurar e implementar la infraestructura de escritorio virtual (VDI) en el sistema operativo Azure Stack HCI. Aproveche su inversión en Azure Stack HCI para ofrecer una administración centralizada, de alta disponibilidad, simplificada y segura para los usuarios de la organización. Use esta guía para habilitar escenarios como "Traiga su propio dispositivo" (BYOD) para los usuarios, a la vez que les proporciona una experiencia coherente y confiable para las aplicaciones críticas para la empresa sin sacrificar la seguridad.

## <a name="overview"></a>Información general
VDI usa hardware de servidor para ejecutar sistemas operativos de escritorio y programas de software en una máquina virtual (VM). De esta manera, VDI permite ejecutar cargas de trabajo de escritorio tradicionales en servidores centralizados. Las ventajas de VDI en una configuración empresarial incluyen el mantenimiento de las aplicaciones y los datos confidenciales de la empresa en un centro de datos seguro y la adaptación de una directiva BYOD sin preocuparse por la combinación de datos personales con recursos corporativos. VDI también se ha convertido en el estándar para ofrecer soporte a los trabajadores remotos y de sucursales y proporcionar acceso a contratistas y asociados.

Azure Stack HCI ofrece la plataforma óptima para VDI. Una solución de Azure Stack HCI validada combinada con los [Servicios de Escritorio remoto (RDS)](/windows-server/remote/remote-desktop-services/welcome-to-rds) de Microsoft le permiten conseguir una arquitectura altamente escalable y de alta disponibilidad.

Además, la infraestructura de escritorio virtual de Azure Stack HCI proporciona las siguientes funcionalidades únicas basadas en la nube para proteger las cargas de trabajo y los clientes de VDI:
- Actualizaciones administradas centralmente con Azure Update Management
- Administración de la seguridad unificada y protección contra amenazas avanzada para los clientes de VDI

## <a name="deploy-vdi"></a>Implementación de VDI
En esta sección se describe de forma general cómo adquirir hardware para implementar VDI en Azure Stack HCI y cómo usar Windows Admin Center para la administración. También se describe la implementación de RDS para admitir VDI.

### <a name="step-1-acquire-hardware-for-vdi-on-azure-stack-hci"></a>Paso 1: Adquisición de hardware para VDI en Azure Stack HCI
En primer lugar, deberá adquirir el hardware. La forma más fácil de hacerlo es encontrar su asociado de hardware de Microsoft preferido en el [Catálogo de Azure Stack HCI](https://hcicatalog.azurewebsites.net) y adquirir un sistema integrado con el sistema operativo de Azure Stack HCI preinstalado. En el catálogo, puede filtrar para ver el hardware del proveedor optimizado para este tipo de carga de trabajo. Asegúrese de consultar a su asociado de hardware para asegurarse de que el hardware puede admitir el número de escritorios virtuales que desea hospedar en el clúster.

En caso contrario, tendrá que implementar el sistema operativo de Azure Stack HCI en su propio hardware. Para obtener más información acerca de las opciones de implementación de Azure Stack HCI y la instalación de Windows Admin Center, consulte [Implementación del sistema operativo de Azure Stack HCI](./operating-system.md).

A continuación, use Windows Admin Center para [crear un clúster de Azure Stack HCI](./create-cluster.md).

### <a name="step-2-set-up-azure-update-management-in-windows-admin-center"></a>Paso 2: Configuración de Azure Update Management en Windows Admin Center
En Windows Admin Center, configure [Azure Update Management](/windows-server/manage/windows-admin-center/azure/azure-update-management) para evaluar rápidamente el estado de las actualizaciones disponibles, programar las actualizaciones necesarias y revisar los resultados de la implementación para comprobar las actualizaciones aplicadas.

Para empezar a trabajar con Azure Update Management, necesita una suscripción de Microsoft Azure. Si no tiene ninguna suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/free).

También puede usar Windows Admin Center para configurar servicios híbridos adicionales de Azure, como Backup, File Sync, Site Recovery, VPN de punto a sitio y Azure Security Center.

### <a name="step-3-deploy-remote-desktop-services-rds-for-vdi-support"></a>Paso 3: Implementación de Servicios de Escritorio remoto (RDS) para admitir VDI
Después de completar la implementación de Azure Stack HCI y registrarse en Azure para usar Update Management, está listo para usar las instrucciones de esta sección para crear e implementar RDS para admitir VDI.

Para crear e implementar RDS:
1. [Implementación del entorno de Escritorio remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)
1. [Creación de una colección de sesiones de RDS](/windows-server/remote/remote-desktop-services/rds-create-collection) para compartir aplicaciones y recursos
1. [Licencia de tu implementación de RDS](/windows-server/remote/remote-desktop-services/rds-client-access-license)
1. Dirigir a los usuarios para que instalen un  [cliente de Escritorio remoto](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) para acceder a las aplicaciones y los recursos
1. Agregue agentes de conexión y hosts de sesión para habilitar la alta disponibilidad:
    - [Escalar horizontalmente una colección de RDS existente con una granja de servidores de host de sesión de Escritorio remoto](/windows-server/remote/remote-desktop-services/rds-scale-rdsh-farm)
    - [Agregar alta disponibilidad a la infraestructura del agente de conexión a Escritorio remoto](/windows-server/remote/remote-desktop-services/rds-connection-broker-cluster)
    - [Agregar alta disponibilidad al front-end web de la puerta de enlace de Escritorio remoto y web de RD](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)
    - [Implementación de un sistema de archivos de dos nodos de espacios de almacenamiento directo para el almacenamiento de UPD](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment)

## <a name="next-steps"></a>Pasos siguientes
Para más información relacionada con VDI, consulte [Configuraciones admitidas para Servicios de Escritorio remoto](/windows-server/remote/remote-desktop-services/rds-supported-config).
