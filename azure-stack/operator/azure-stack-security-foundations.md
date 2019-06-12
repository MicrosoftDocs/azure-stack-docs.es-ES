---
title: Información sobre los controles de seguridad de Azure Stack
description: Como administrador de servicios, conozca los controles de seguridad aplicados a Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: db73f0b787b9b163f39d5af89e260ca6a30855bd
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828136"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Posición de seguridad de la infraestructura de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Entre los principales factores que determinan el uso de nubes híbridas están las consideraciones de seguridad y las regulaciones de conformidad. Azure Stack está diseñado para estos escenarios. En este artículo se explican los controles de seguridad que hay para Azure Stack.

En Azure Stack coexisten dos capas de posición de seguridad. La primera capa es la infraestructura de Azure Stack, que incluye los componentes de hardware hasta Azure Resource Manager. La primera capa incluye los portales Administrador e Inquilino. La segunda capa consta de las cargas de trabajo creadas, implementadas y administradas por los inquilinos. La segunda capa incluye elementos como las máquinas virtuales y sitios web de App Services.

## <a name="security-approach"></a>Enfoque de seguridad

La posición de seguridad para Azure Stack se diseñó para defenderse contra las amenazas modernas, y se creó para satisfacer los requisitos de los principales estándares de cumplimiento. Como resultado, la posición de seguridad de Azure Stack se cimienta sobre dos pilares:

 - **Supuesto de infracción**  
A partir de la suposición de que el sistema ya se ha infringido, céntrese en *detectar y limitar el impacto de las infracciones*, en lugar de intentar únicamente impedir los ataques. 
 - **Protección predeterminada**  
Dado que la infraestructura se ejecuta en hardware y software bien definidos, Azure Stack *habilita, configura y valida todas las características de seguridad* de forma predeterminada.

Como Azure Stack se proporciona como un sistema integrado, la posición de seguridad de la infraestructura de Azure Stack la define Microsoft. Al igual que en Azure, los inquilinos son responsables de definir la posición de seguridad de sus cargas de trabajo de inquilino. En este documento se proporciona conocimiento básico sobre la posición de seguridad de la infraestructura de Azure Stack.

## <a name="data-at-rest-encryption"></a>Cifrado de datos en reposo
Todos los datos de la infraestructura de Azure Stack y de los inquilinos se cifran en reposo mediante BitLocker. Este cifrado protege contra la pérdida física o el robo de componentes de almacenamiento de Azure Stack. Para más información, consulte [Cifrado de datos en reposo en Azure Stack](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Cifrado de los datos en tránsito
Los componentes de la infraestructura de Azure Stack se comunican mediante canales cifrados con TLS 1.2. Los certificados de cifrado se administran automáticamente en la infraestructura. 

Todos los puntos de conexión externos de la infraestructura, como los puntos de conexión REST o el portal de Azure Stack, admiten TLS 1.2 para proteger las comunicaciones. Para estos puntos de conexión, se deben proporcionar certificados de cifrado, bien de un tercero o de la entidad de certificación de la empresa. 

Aunque se pueden usar certificados autofirmados para estos puntos de conexión externos, Microsoft aconseja encarecidamente no hacerlo. 

## <a name="secret-management"></a>Administración de secretos
La infraestructura de Azure Stack emplea multitud de secretos, como las contraseñas, para funcionar. La mayoría de ellos se rotan automáticamente con frecuencia, ya que son cuentas de servicio administradas de grupo, que rotan cada 24 horas.

El resto de secretos no son cuentas de servicio administradas de grupo y se pueden rotar manualmente con un script en el punto de conexión con privilegios.

## <a name="code-integrity"></a>Integridad del código
Azure Stack emplea las características de seguridad de Windows Server 2016 más recientes. Una de ellas es Device Guard de Windows Defender, que proporciona la inclusión en lista blanca de aplicaciones y que garantiza que solo se ejecuta en la infraestructura de Azure Stack código autorizado. 

El código autorizado está firmado por Microsoft o el socio de OEM. El código autorizado firmado se incluye en la lista de software permitido especificada en una directiva definida por Microsoft. En otras palabras, solo se puede ejecutar software que esté aprobado para ejecutarse en la infraestructura de Azure Stack. Cualquier intento de ejecutar código no autorizado se bloqueará y se generará una auditoría.

La directiva de Device Guard también impide que agentes o software de terceros se ejecuten en la infraestructura de Azure Stack.

## <a name="credential-guard"></a>Protección de credenciales
Otra característica de seguridad de Windows Server 2016 en Azure Stack es Credential Guard de Windows Defender, que se usa para proteger las credenciales de infraestructura de Azure Stack de los ataques pass-the-hash y pass-the-ticket.

## <a name="antimalware"></a>Antimalware
Todos los componentes de Azure Stack (tanto los hosts de Hyper-V como Virtual Machines) están protegidos con el antivirus de Windows Defender.

En escenarios conectados, las actualizaciones de definiciones y motores de antivirus se aplican varias veces al día. En escenarios desconectados, se aplican las actualizaciones de antimalware como parte de las actualizaciones mensuales de Azure Stack. Para más información, vea [Actualización de Antivirus de Windows Defender en Azure Stack](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Modelo de administración restringida
La administración de Azure Stack está controlada mediante tres puntos de entrada, cada uno con un fin específico: 
1. El [portal de administrador](azure-stack-manage-portals.md) proporciona una experiencia "apuntar y hacer clic" para las operaciones de administración diarias.
2. Azure Resource Manager expone todas las operaciones de administración del portal de administrador mediante una API de REST, que usan PowerShell y la CLI de Azure. 
3. Para operaciones específicas de bajo nivel, por ejemplo, integración del centro de datos o escenarios de soporte técnico, Azure Stack expone un punto de conexión de PowerShell denominado [punto de conexión con privilegios](azure-stack-privileged-endpoint.md). Este punto de conexión solo expone un conjunto de cmdlets incluidos en lista blanca y está sometido a una auditoría exhaustiva.

## <a name="network-controls"></a>Controles de red
La infraestructura de Azure Stack incluye varias capas de listas de control de acceso a redes (ACL). Las ACL impiden el acceso no autorizado a los componentes de la infraestructura y limitan las comunicaciones de esta con solo las rutas de acceso que son necesarias para su funcionamiento. 

Las ACL de red se exigen en tres capas:
1.  Parte superior del conmutador del rack
2.  Red definida por el software
3.  Firewalls de sistema operativo host y de máquina virtual

## <a name="regulatory-compliance"></a>Cumplimiento de normativas

Azure Stack ha pasado por una evaluación formal realizada por una auditora independiente. Como resultado, hay disponible documentación donde se explica que la infraestructura de Azure Stack cumple los controles aplicables de varios estándares de cumplimiento importantes. La documentación no es una certificación de Azure Stack debido a que los estándares incluyen varios controles relacionados con el personal y con el proceso. Pero los clientes pueden usar esta documentación para impulsar su proceso de certificación.

Las evaluaciones incluyen estos estándares:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) aborda el sector de tarjetas de pago.
- [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) es una asignación completa entre varios estándares, incluidos FedRAMP Moderate, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 y otros.
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) para clientes gubernamentales.

La documentación de cumplimiento está disponible en el [Portal de confianza del servicio de Microsoft](https://servicetrust.microsoft.com/ViewPage/Blueprint). Las guías de cumplimiento son un recurso protegido y para consultarlas es necesario iniciar sesión con sus credenciales de servicio en la nube de Azure.

## <a name="next-steps"></a>Pasos siguientes

- [Aprenda a cambiar los secretos en Azure Stack](azure-stack-rotate-secrets.md)
- [Documentos de PCI-DSS y CSA-CCM para Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Documentos de DoD y NIST para Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
