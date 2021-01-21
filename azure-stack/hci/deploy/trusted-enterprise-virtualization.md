---
title: Implementación de la virtualización empresarial de confianza en Azure Stack HCI
description: En este tema se proporcionan instrucciones sobre cómo planear, configurar e implementar una infraestructura de alta seguridad que use la virtualización empresarial de confianza en el sistema operativo de Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/07/2021
ms.openlocfilehash: bd9675efc87929c020f3c1514d220fece54099a7
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571601"
---
# <a name="deploy-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Implementación de la virtualización empresarial de confianza en Azure Stack HCI

>Se aplica a: Azure Stack HCI, versión 20H2

En este tema se proporcionan instrucciones sobre cómo planear, configurar e implementar una infraestructura de alta seguridad que use la virtualización empresarial de confianza en el sistema operativo de Azure Stack HCI. Aproveche su inversión en Azure Stack HCl para ejecutar cargas de trabajo seguras en hardware que use [seguridad basada en virtualización (VBS)](/windows-hardware/design/device-experiences/oem-vbs) y servicios en la nube híbrida desde Windows Admin Center y Azure Portal.

## <a name="overview"></a>Información general
VBS es un componente clave de las [inversiones de seguridad de Azure Stack HCl](/windows-server/get-started-19/whats-new-19#security) para proteger los hosts y las máquinas virtuales (VM) frente a las amenazas de seguridad. Por ejemplo, la [Guía de implementación técnica de seguridad (STIG)](https://nvd.nist.gov/ncp/checklist/914), que se publica como una herramienta para mejorar la seguridad de los sistemas de información del Departamento de Defensa (DoD), enumera VBS y la [integridad de código protegido por hipervisor (HVCI)](/windows-hardware/drivers/bringup/device-guard-and-credential-guard) como requisitos de seguridad generales. Es obligatorio usar hardware de host habilitado para VBS y HVCI con el fin de proteger las cargas de trabajo en las VM, ya que un host en peligro no puede garantizar la protección de las VM.

VBS utiliza características de virtualización de hardware para crear y aislar una región de memoria segura del sistema operativo. Puede usar el [modo seguro virtual (VSM)](/virtualization/hyper-v-on-windows/tlfs/vsm) de Windows para hospedar varias soluciones de seguridad a fin de aumentar considerablemente la protección frente a vulnerabilidades de seguridad maliciosas y del sistema operativo.

VBS usa el hipervisor de Windows para crear y administrar los límites de seguridad en el software del sistema operativo, aplicar restricciones para proteger los recursos vitales del sistema y proteger los recursos de seguridad, como las credenciales de usuario autenticadas. Con VBS, incluso si el malware obtiene acceso al kernel del sistema operativo, puede limitar considerablemente y contener las posibles vulnerabilidades, ya que el hipervisor impide que el malware ejecute código u obtenga acceso a los secretos de la plataforma.

El hipervisor, el nivel más privilegiado del software del sistema, establece y aplica permisos de página en toda la memoria del sistema. En VSM, las páginas solo se pueden ejecutar después de superar las comprobaciones de integridad de código. Incluso si se produce una vulnerabilidad, como un desbordamiento del búfer que podría permitir que el malware intente modificar la memoria, no se pueden modificar las páginas de código y no se puede ejecutar la memoria modificada. VBS y HVCI refuerzan significativamente la aplicación de directivas de integridad de código. Todos los archivos binarios y controladores de modo kernel se comprueban antes de iniciarse y los controladores o archivos del sistema no firmados no se pueden cargar en la memoria del sistema.

## <a name="deploy-trusted-enterprise-virtualization"></a>Implementación de la virtualización empresarial de confianza
En esta sección se describe de forma general cómo adquirir hardware para implementar una infraestructura de alta seguridad que use la virtualización empresarial de confianza en Azure Stack HCl y Windows Admin Center para la administración.

### <a name="step-1-acquire-hardware-for-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Paso 1: adquirir hardware para la virtualización empresarial de confianza en Azure Stack HCI
En primer lugar, deberá adquirir el hardware. La forma más fácil de hacerlo es encontrar su asociado de hardware de Microsoft preferido en el [Catálogo de Azure Stack HCI](https://hcicatalog.azurewebsites.net) y adquirir un sistema integrado con el sistema operativo de Azure Stack HCI preinstalado. En el catálogo, puede filtrar para ver el hardware del proveedor optimizado para este tipo de carga de trabajo.

En caso contrario, tendrá que implementar el sistema operativo de Azure Stack HCI en su propio hardware. Para obtener más información acerca de las opciones de implementación de Azure Stack HCI y la instalación de Windows Admin Center, consulte [Implementación del sistema operativo de Azure Stack HCI](./operating-system.md).

A continuación, use Windows Admin Center para [crear un clúster de Azure Stack HCI](./create-cluster.md).

Todo el hardware de asociado para Azure Stack HCl está certificado con la calificación adicional Hardware Assurance. El proceso de calificación prueba todas las funciones requeridas de [VBS](/windows-hardware/design/device-experiences/oem-vbs). Sin embargo, VBS y HVCI no están habilitados automáticamente en Azure Stack HCI. Para obtener más información acerca de la calificación adicional Hardware Assurance, consulte la sección "Hardware Assurance" en el apartado de **sistemas** del [catálogo de Windows Server](https://www.windowsservercatalog.com/content.aspx?ctf=AQinfo-systems.htm#:~:text=Hardware%20Assurance%20Windows%20Server%20systems%20that%20are%20awarded,of%20Windows%20Server%2C%20starting%20with%20Windows%20Server%202016).

   >[!WARNING]
   > Es posible que HVCI no sea compatible con los dispositivos de hardware que no figuran en el catálogo de Azure Stack HCI. Se recomienda usar hardware validado para Azure Stack HCI de nuestros asociados para la infraestructura de virtualización empresarial de confianza.

### <a name="step-2-enable-hvci"></a>Paso 2: habilitar HVCI
Habilite HVCI en el hardware del servidor y en las VM. Para obtener más información, consulte [Habilitar la protección basada en la virtualización de la integridad de código](/windows/security/threat-protection/device-guard/enable-virtualization-based-protection-of-code-integrity).

### <a name="step-3-set-up-azure-security-center-in-windows-admin-center"></a>Paso 3: configurar Azure Security Center en Windows Admin Center
En Windows Admin Center, configure [Azure Security Center](/azure/security-center/security-center-introduction) para agregar protección contra amenazas y evaluar rápidamente la postura de seguridad de las cargas de trabajo.

Para obtener más información, consulte [Protección de los recursos de Windows Admin Center con Security Center](/azure/security-center/windows-admin-center-integration).

Primeros pasos con Security Center:
- Necesita una suscripción a Microsoft Azure. Si no tiene ninguna suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/free).
- El plan de tarifa gratuito de Security Center está habilitado en todas las suscripciones de Azure actuales desde que visite el panel de Azure Security Center en Azure Portal o lo habilite mediante programación desde la API.
Para aprovechar la funcionalidad avanzada de administración de seguridad y de detección de amenazas, habilite Azure Defender. Puede usar Azure Defender gratis durante 30 días. Para obtener más información, vea la [página de precios de Security Center](https://azure.microsoft.com/pricing/details/security-center).
- Si está preparado para habilitar Azure Defender, consulte [Inicio rápido: Configuración de Azure Security Center](/azure/security-center/security-center-get-started) para obtener información sobre los pasos que debe seguir.

También puede usar Windows Admin Center para configurar servicios híbridos adicionales de Azure, como Backup, File Sync, Site Recovery, VPN de punto a sitio, Update Management y Azure Monitor.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información en relación con la virtualización empresarial de confianza, consulte:
- [Hyper-V en Windows Server](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)