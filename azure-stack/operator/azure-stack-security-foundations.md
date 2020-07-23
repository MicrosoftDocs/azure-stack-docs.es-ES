---
title: Controles de seguridad de Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Conozca la posición y los controles de seguridad aplicados a Azure Stack Hub.
author: JustinHall
ms.topic: article
ms.date: 06/10/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 04/07/2020
ms.openlocfilehash: 27ba6098755d93ef1de902a9a4e052f1ff6b53d5
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487879"
---
# <a name="azure-stack-hub-infrastructure-security-controls"></a>Controles de seguridad de la infraestructura de Azure Stack Hub

Entre los principales factores que determinan el uso de nubes híbridas están las consideraciones de seguridad y las regulaciones de conformidad. Azure Stack Hub está diseñado para estos escenarios. En este artículo se explican los controles de seguridad que existen para Azure Stack Hub.

En Azure Stack Hub coexisten dos capas de posición de seguridad. La primera capa es la infraestructura de Azure Stack Hub, que incluye los componentes de hardware hasta Azure Resource Manager. Esta primera capa incluye los portales del administrador y usuarios. La segunda capa consta de las cargas de trabajo creadas, implementadas y administradas por los inquilinos. La segunda capa incluye elementos como las máquinas virtuales y sitios web de App Services.

## <a name="security-approach"></a>Enfoque de seguridad

La posición de seguridad para Azure Stack Hub se diseñó para defenderse de las amenazas modernas y satisfacer los requisitos de los principales estándares de cumplimiento. Como resultado, la posición de seguridad de Azure Stack Hub se cimienta sobre dos pilares:

- **Supuesto de infracción**  
    A partir de la suposición de que el sistema ya se ha infringido, céntrese en *detectar y limitar el impacto de las infracciones*, en lugar de intentar únicamente impedir los ataques.

- **Protección predeterminada**  
    Dado que la infraestructura se ejecuta en hardware y software bien definidos, Azure Stack Hub *habilita, configura y valida todas las características de seguridad* de forma predeterminada.

Puesto que Azure Stack Hub se proporciona como un sistema integrado, la posición de seguridad de la infraestructura de Azure Stack Hub la define Microsoft. Al igual que en Azure, los inquilinos son responsables de definir la posición de seguridad de sus cargas de trabajo de inquilino. En este documento se proporciona conocimiento básico sobre la posición de seguridad de la infraestructura de Azure Stack Hub.

## <a name="data-at-rest-encryption"></a>Cifrado de datos en reposo

Todos los datos de la infraestructura y los inquilinos de Azure Stack Hub se cifran en reposo mediante BitLocker. Este cifrado protege contra la pérdida física o el robo de componentes de almacenamiento de Azure Stack Hub. Para más información, consulte [Cifrado de datos en reposo en Azure Stack Hub](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Cifrado de los datos en tránsito

Los componentes de la infraestructura de Azure Stack Hub se comunican mediante canales cifrados con TLS 1.2. Los certificados de cifrado se administran automáticamente en la infraestructura.

Todos los puntos de conexión externos de la infraestructura, como los puntos de conexión REST o el portal de Azure Stack Hub, admiten TLS 1.2 para proteger las comunicaciones. Para estos puntos de conexión, se deben proporcionar certificados de cifrado, bien de un tercero o de la entidad de certificación de la empresa.

Aunque se pueden usar certificados autofirmados para estos puntos de conexión externos, Microsoft aconseja encarecidamente no hacerlo.
Para más información sobre cómo aplicar TLS 1.2 en los puntos de conexión externos de Azure Stack Hub, consulte [Configuración de los controles de seguridad de Azure Stack Hub](azure-stack-security-configuration.md).

## <a name="secret-management"></a>Administración de secretos

La infraestructura de Azure Stack Hub emplea multitud de secretos, como contraseñas y certificados, para funcionar. La mayoría de las contraseñas asociadas a las cuentas de servicio internas se rotan automáticamente cada 24 horas ya que se trata de [cuentas de servicio administradas de grupo (gMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview), un tipo de cuenta de dominio administrado directamente por el controlador de dominio interno.

La infraestructura de Azure Stack Hub usa claves RSA de 4096 bits para todos sus certificados internos. También se pueden usar los certificados de la misma longitud de clave para los puntos de conexión externos. Para más información sobre secretos y la rotación de certificados, consulte [Cambio de secretos en Azure Stack Hub](azure-stack-rotate-secrets.md).

## <a name="windows-defender-application-control"></a>Control de aplicaciones de Windows Defender

Azure Stack Hub emplea las características de seguridad de Windows Server más recientes. Una de ellas es Control de aplicaciones de Windows Defender (MDAC, conocida anteriormente como Integridad de código), que proporciona la inclusión en lista blanca de archivos ejecutables y que garantiza que solo se ejecuta en la infraestructura de Azure Stack Hub código autorizado.

El código autorizado está firmado por Microsoft o el socio de OEM. El código autorizado firmado se incluye en la lista de software permitido especificada en una directiva definida por Microsoft. En otras palabras, solo se puede ejecutar software que esté aprobado para ejecutarse en la infraestructura de Azure Stack Hub. Cualquier intento de ejecutar código no autorizado se bloqueará y se generará una alerta. Azure Stack Hub aplica la Integridad de código del modo de usuario (UMCI) y la Integridad del código de hipervisor (HVCI).

La directiva de MDAC también impide que agentes o software de terceros se ejecuten en la infraestructura de Azure Stack Hub.
Para más información sobre WDAC, consulte [Control de aplicaciones de Windows Defender y protección de la integridad del código basada en la virtualización](/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control).

## <a name="credential-guard"></a>Protección de credenciales

Otra característica de seguridad de Windows Server en Azure Stack Hub es Credential Guard de Windows Defender, que se usa para proteger las credenciales de la infraestructura de Azure Stack Hub de los ataques Pass-the-Hash y Pass-the-Ticket.

## <a name="antimalware"></a>Antimalware

Todos los componentes de Azure Stack Hub (tanto los hosts de Hyper-V como las máquinas virtuales) están protegidos con Antivirus de Windows Defender.

En escenarios conectados, las actualizaciones de definiciones y motores de antivirus se aplican varias veces al día. En escenarios desconectados, se aplican las actualizaciones de antimalware como parte de las actualizaciones mensuales de Azure Stack Hub. En caso de que se necesite una actualización más frecuente de las definiciones de Windows Defender en escenarios desconectados, Azure Stack Hub también admite la importación de actualizaciones de Windows Defender. Para más información, consulte [Actualización de Antivirus de Windows Defender en Azure Stack Hub](azure-stack-security-av.md).

## <a name="secure-boot"></a>Arranque seguro

Azure Stack Hub aplica el arranque seguro en todos los hosts y máquinas virtuales de la infraestructura de Hyper-V. 

## <a name="constrained-administration-model"></a>Modelo de administración restringida

La administración de Azure Stack Hub está controlada mediante tres puntos de entrada, cada uno con un fin específico:

- El [portal de administrador](azure-stack-manage-portals.md) permite realizar las operaciones diarias con una experiencia de "apuntar y hacer clic".
- Azure Resource Manager expone todas las operaciones de administración del portal de administrador mediante una API REST, que usan PowerShell y la CLI de Azure.
- Para las operaciones específicas de bajo nivel (por ejemplo, integración en el centro de datos o escenarios de soporte técnico), Azure Stack Hub expone un punto de conexión de PowerShell llamado [punto de conexión con privilegios](azure-stack-privileged-endpoint.md). Este punto de conexión solo expone un conjunto de cmdlets incluidos en una lista de autorizados y está sometido a una auditoría exhaustiva.

## <a name="network-controls"></a>Controles de red

La infraestructura de Azure Stack Hub incluye varios niveles en las listas de control de acceso (ACL) a la red. Las ACL impiden el acceso no autorizado a los componentes de la infraestructura y limitan las comunicaciones de esta con solo las rutas de acceso que son necesarias para su funcionamiento.

Las ACL de red se exigen en tres capas:

- Nivel 1: Parte superior del conmutador del rack
- Nivel 2: Red definida por el software
- Nivel 3: Firewalls de sistema operativo host y de máquina virtual

## <a name="regulatory-compliance"></a>Cumplimiento de normativas

Azure Stack Hub ha pasado por una evaluación formal de la funcionalidad realizada por una auditora independiente. Como resultado, hay disponible documentación donde se explica que la infraestructura de Azure Stack Hub cumple los controles aplicables de varios estándares de cumplimiento importantes. La documentación no es una certificación de Azure Stack Hub porque los estándares incluyen varios controles relacionados con el personal y con el proceso. Pero los clientes pueden usar esta documentación para impulsar su proceso de certificación.

Las evaluaciones incluyen estos estándares:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) aborda el sector de tarjetas de pago.
- [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) es una asignación completa entre varios estándares, incluidos FedRAMP Moderate, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 y otros.
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) para clientes gubernamentales.

La documentación de cumplimiento está disponible en el [Portal de confianza del servicio de Microsoft](https://aka.ms/azurestackcompliance). Las guías de cumplimiento son un recurso protegido y para consultarlas es necesario iniciar sesión con sus credenciales de servicio en la nube de Azure.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de los controles de seguridad de Azure Stack Hub](azure-stack-security-configuration.md)
- [Aprenda a cambiar los secretos en Azure Stack Hub](azure-stack-rotate-secrets.md)
- [Documentos de PCI-DSS y CSA-CCM para Azure Stack Hub](https://aka.ms/azurestackcompliance)
