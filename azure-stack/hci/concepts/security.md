---
title: Consideraciones sobre la seguridad de Azure Stack HCI
description: En este tema se proporcionan instrucciones sobre las consideraciones de seguridad para el sistema operativo Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 6a8a0c3068061c599ac18e160ebd32c7b5cc8eda
ms.sourcegitcommit: b147d617c32cea138b5bd4bab568109282e44317
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2020
ms.locfileid: "90010822"
---
# <a name="azure-stack-hci-security-considerations"></a>Consideraciones sobre la seguridad de Azure Stack HCI

>Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se proporcionan consideraciones de seguridad y recomendaciones relacionadas con el sistema operativo Azure Stack HCI:
- En la parte 1 se cubren las herramientas y tecnologías de seguridad básicas para proteger el sistema operativo, los datos y las identidades a fin de crear de forma eficaz una base segura para su organización.
- En la parte 2 se describen los recursos disponibles a través de Azure Security Center.
- En la parte 3 se cubren las consideraciones de seguridad más avanzadas para reforzar aún más el nivel de seguridad de su organización en estas áreas.

## <a name="why-are-security-considerations-important"></a>¿Por qué son importantes las consideraciones de seguridad?
La seguridad afecta a todos los usuarios de su organización, desde las personas que ocupan cargos directivos hasta aquellas que trabajan con la información. Una seguridad inadecuada implica un riesgo real para las organizaciones, ya que una infracción de seguridad podría interrumpir toda la actividad habitual y paralizar la organización. Cuanto antes pueda detectar un posible ataque, más rápido podrá mitigar cualquier riesgo de seguridad.

Un atacante, después de investigar los puntos débiles de un entorno para aprovecharlos, suele tardar entre 24 y 48 horas en escalar los privilegios para tomar el control de los sistemas de la red desde el compromiso inicial. Las medidas de seguridad adecuadas protegen los sistemas del entorno para ampliar el tiempo que tarda un atacante en tomar el control de horas a semanas o incluso meses al bloquear los movimientos del atacante. La implementación de las recomendaciones de seguridad de este tema permitirá que su organización detecte y responda a estos ataques lo más rápido posible.

## <a name="part-1-build-a-secure-foundation"></a>Parte 1: creación de una base segura
En las secciones siguientes se recomiendan las herramientas y tecnologías de seguridad para crear una base segura para los servidores que ejecutan el sistema operativo de Azure Stack HCI en su entorno.

### <a name="harden-the-environment"></a>Protección del entorno
En esta sección se explica cómo proteger los servicios y las máquinas virtuales (VM) que se ejecutan en el sistema operativo:
- El **hardware con certificación de Azure Stack HCI** proporciona una configuración de arranque seguro, UEFI y TPM coherente y lista para usar. La combinación de la seguridad basada en virtualización y el hardware certificado ayuda a proteger las cargas de trabajo críticas para la seguridad. También puede conectar esta infraestructura de confianza a Azure Security Center para activar el análisis de comportamiento y los informes a fin de tener en cuenta las cargas de trabajo y amenazas que cambian rápidamente.

    - *Arranque seguro* es un estándar de seguridad desarrollado por la industria de la informática que permite garantizar que un dispositivo solo arranque con el software en que confíe el fabricante de equipos originales (OEM). Para obtener más información, vea [Arranque seguro](/windows-hardware/design/device-experiences/oem-secure-boot).
    - *United Extensible Firmware Interface (UEFI)* controla el proceso de arranque del servidor y, a continuación, pasa el control a Windows o a otro sistema operativo. Para obtener más información, vea [Requisitos de firmware de UEFI](/windows-hardware/design/device-experiences/oem-uefi).
    - La tecnología de *Módulo de plataforma segura (TPM)* proporciona funciones relacionadas con la seguridad basadas en hardware. Un chip de TPM es un procesador de cifrado seguro que genera, almacena y limita el uso de claves criptográficas. Para obtener más información, consulte [Información general sobre la tecnología del Módulo de plataforma segura](/windows/security/information-protection/tpm/trusted-platform-module-overview).

    Para obtener más información sobre los proveedores de hardware con certificación de Azure Stack HCl, consulte el sitio web de [soluciones de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

- **Device Guard** y **Credential Guard**. Device Guard protege contra malware sin firma conocida, código sin firmar y malware que obtiene acceso al kernel para capturar información confidencial o dañar el sistema. Credential Guard de Windows Defender utiliza la seguridad basada en la virtualización para aislar los secretos, de modo que solo el software del sistema con privilegios pueda acceder a ellos.

    Para obtener más información, consulte [Administrar Credential Guard de Windows Defender](/windows/security/identity-protection/credential-guard/credential-guard-manage) y descargue la [herramienta de preparación de hardware de Device Guard y Credential Guard](https://www.microsoft.com/en-us/download/details.aspx?id=53337).

- Las actualizaciones del **firmware** y de **Windows** son esenciales en clústeres, servidores (incluidas las máquinas virtuales invitadas) y equipos para garantizar que el sistema operativo y el hardware del sistema están protegidos contra los atacantes. Puede usar la herramienta **Actualizaciones** de Windows Admin Center para aplicar actualizaciones a sistemas individuales. Si el proveedor de hardware incluye compatibilidad con Windows Admin Center para obtener actualizaciones de controladores, firmware y soluciones, puede obtener estas actualizaciones al mismo tiempo que las actualizaciones de Windows; de lo contrario, puede obtenerlas directamente de su proveedor.

    Para obtener más información, consulte [Actualización del clúster](../manage/update-cluster.md).

    Para administrar las actualizaciones en varios clústeres y servidores a la vez, considere la posibilidad de suscribirse al servicio de Azure Update Management opcional, que se integra con Windows Admin Center. Para obtener más información, consulte [Azure Update Management mediante Windows Admin Center](https://www.thomasmaurer.ch/2018/11/azure-update-management-windows-admin-center).

### <a name="protect-data"></a>Protección de datos
En esta sección se describe cómo usar Windows Admin Center para proteger los datos y las cargas de trabajo en el sistema operativo:

- **BitLocker para Espacios de almacenamiento** protege los datos en reposo. Puede usar BitLocker para cifrar el contenido de los volúmenes de datos de Espacios de almacenamiento en el sistema operativo. El uso de BitLocker para proteger los datos puede ayudar a las organizaciones a cumplir estándares gubernamentales, regionales y específicos del sector, como FIPS 140-2 e HIPAA.
 
    Para más información acerca del uso de BitLocker en Windows Admin Center, consulte [Habilitación del cifrado de volumen, desduplicación y compresión](../manage/volume-encryption-deduplication.md).

- El cifrado de **SMB** para redes de Windows protege los datos en tránsito. El *Bloque de mensajes del servidor (SMB)* es un protocolo de uso compartido de archivos de red que permite que las aplicaciones de un equipo puedan leer archivos y escribir en ellos, así como solicitar servicios desde los programas de servidor en una red de equipos.

    Para habilitar el cifrado SMB, consulte [Mejoras de seguridad SMB](/windows-server/storage/file-server/smb-security).

- El **Antivirus de Windows Defender** en Windows Admin Center protege el sistema operativo en clientes y servidores frente a virus, malware, spyware y otras amenazas. Para obtener más información, consulte [Antivirus de Microsoft Defender en Windows Server 2016 y 2019](/windows/security/threat-protection/microsoft-defender-antivirus/microsoft-defender-antivirus-on-windows-server-2016).

### <a name="protect-identities"></a>Protección de identidades
En esta sección se describe cómo usar Windows Admin Center para proteger las identidades con privilegios:

- El **control de acceso** puede mejorar la seguridad de su entorno de administración. Si usa un servidor de Windows Admin Center (en lugar de ejecutar un equipo Windows 10), puede controlar dos niveles de acceso a Windows Admin Center: usuarios de puerta de enlace y administradores de puerta de enlace. Entre las opciones del proveedor de identidades del administrador de puertas de enlace se incluyen las siguientes:
    - Active Directory o grupos de máquinas locales para aplicar la autenticación de tarjeta inteligente.
    - Azure Active Directory para aplicar el acceso condicional y la autenticación multifactor.
 
    Para obtener más información, consulte [Opciones de acceso de usuario con Windows Admin Center](/windows-server/manage/windows-admin-center/plan/user-access-options) y [Configuración de los permisos y el control de acceso de usuarios](/windows-server/manage/windows-admin-center/configure/user-access-control).

- El **tráfico del explorador** a Windows Admin Center usa HTTPS. El tráfico desde Windows Admin Center a los servidores administrados usa Instrumental de administración de Windows (WMI) y PowerShell estándar en Administración remota de Windows (WinRM). Windows Admin Center es compatible con la Solución de contraseñas de administrador local (LAPS), la delegación restringida basada en recursos, el control de acceso de puerta de enlace mediante Active Directory (AD) o Microsoft Azure Active Directory (Azure AD) y el control de acceso basado en roles (RBAC) para administrar servidores de destino.

    Windows Admin Center es compatible con Microsoft Edge (Windows 10, versión 1709 o posterior), Google Chrome y Microsoft Edge Insider en Windows 10. Puede instalar Windows Admin Center en un equipo Windows 10 o Windows Server.

    Si instala Windows Admin Center en un servidor, se ejecuta como puerta de enlace, sin interfaz de usuario en el servidor host. En este escenario, los administradores pueden iniciar sesión en el servidor a través de una sesión HTTPS, protegida por un certificado de seguridad autofirmado en el host. Sin embargo, es mejor usar un certificado SSL adecuado de una entidad de certificación de confianza para el proceso de inicio de sesión, ya que los exploradores admitidos tratan una conexión autofirmada como no segura, incluso si la conexión es a una dirección IP local a través de una VPN de confianza.

    Para obtener más información sobre las opciones de instalación de su organización, consulte [¿Qué tipo de instalación es la adecuada para usted?](/windows-server/manage/windows-admin-center/plan/installation-options).

- **CredSSP** es un proveedor de autenticación que Windows Admin Center usa en algunos casos para pasar credenciales a máquinas más allá del servidor específico al que destina la administración. Actualmente, Windows Admin Center requiere CredSSP para:
    - Cree un clúster nuevo.
    - Acceder a la herramienta **Actualizaciones** para usar las características de clústeres de conmutación por error o de actualización compatible con clústeres.
    - Administrar el almacenamiento de SMB desagregado en máquinas virtuales.

    Para más información, consulte [¿Windows Admin Center usa CredSSP?](/windows-server/manage/windows-admin-center/understand/faq#does-windows-admin-center-use-credssp).

- El **control de acceso basado en roles (RBAC)** en Windows Admin Center permite a los usuarios tener acceso limitado a los servidores que deben administrar en lugar de convertirlos en administradores locales completos. Para usar RBAC en Windows Admin Center, configure cada servidor administrado con un punto de conexión de Just Enough Administration de PowerShell.

    Para obtener más información, consulte el artículo [Control de acceso basado en roles](/windows-server/manage/windows-admin-center/plan/user-access-options#role-based-access-control) y [Just Enough Administration](/powershell/scripting/learn/remoting/jea/overview?view=powershell-7).

- Las **herramientas de seguridad** de Windows Admin Center que puede usar para administrar y proteger las identidades incluyen Active Directory, certificados, firewall, usuarios locales y grupos, etc.

    Para obtener más información, vea [Administración de servidores con Windows Admin Center](/windows-server/manage/windows-admin-center/use/manage-servers).

## <a name="part-2-use-azure-security-center"></a>Parte 2: Uso de Azure Security Center
*Azure Security Center* es un sistema unificado de administración de seguridad de la infraestructura que fortalece el nivel de seguridad de los centros de datos y proporciona una protección contra amenazas avanzada de todas las cargas de trabajo híbridas que se encuentran en la nube, así como también en el entorno local. Security Center proporciona herramientas para evaluar el estado de seguridad de la red, proteja cargas de trabajo, genere alertas de seguridad y siga las recomendaciones específicas para corregir ataques y abordar amenazas futuras. Security Center realiza todos estos servicios a alta velocidad en la nube sin ninguna sobrecarga en la implementación a través del aprovisionamiento automático y la protección de los servicios de Azure.

Security Center protege las máquinas virtuales para servidores Windows y Linux instalando el agente de Log Analytics en estos recursos. Azure correlaciona los eventos que los agentes recopilan en recomendaciones (tareas de protección) que realiza para proteger las cargas de trabajo. Las tareas de protección basadas en los procedimientos recomendados de seguridad incluyen la administración y la aplicación de directivas de seguridad. A continuación, puede realizar un seguimiento de los resultados y administrar el cumplimiento y la gobernanza a lo largo del tiempo a través de la supervisión de Security Center, a la vez que reduce la superficie expuesta a ataques en todos los recursos.

Administrar quién puede acceder a los recursos y suscripciones de Azure constituye una parte importante de la estrategia de gobernanza de Azure. El control de acceso basado en rol (RBAC) de Azure es el principal método de administrar el acceso en Azure. Para obtener más información, consulte [Administración del acceso al entorno de Azure con el control de acceso basado en roles](/azure/cloud-adoption-framework/ready/azure-setup-guide/manage-access).

Para trabajar con Security Center a través de Windows Admin Center, necesita una suscripción a Azure. Para empezar, consulte [Integración de Azure Security Center con Windows Admin Center](/azure/security-center/windows-admin-center-integration).

Después del registro, acceda a Security Center en Windows Admin Center: En la página **Todas las conexiones**, seleccione un servidor o una máquina virtual. En **Herramientas**, seleccione **Azure Security Center** y, a continuación, seleccione **Sign into Azure** (Iniciar sesión en Azure).

Para obtener más información, vea [¿Qué es Azure Security Center?](/azure/security-center/security-center-intro)

## <a name="part-3-add-advanced-security"></a>Parte 3: adición de seguridad avanzada
En las secciones siguientes se recomiendan las herramientas y tecnologías de seguridad avanzadas para proteger aún más los servidores que ejecutan el sistema operativo de Azure Stack HCI en su entorno.

### <a name="harden-the-environment"></a>Protección del entorno
- Las **bases de referencia de seguridad de Microsoft** se basan en recomendaciones de seguridad de Microsoft obtenidas a través de la asociación con organizaciones comerciales y el gobierno de EE. UU., como el Departamento de Defensa. Las bases de referencia de seguridad incluyen la configuración de seguridad recomendada para Windows Firewall y Windows Defender, entre otras.

    Las bases de referencia de seguridad se proporcionan como copias de seguridad del objeto de directiva de grupo que puede importar en Active Directory Domain Services (AD DS) y, a continuación, implementar en servidores unidos a un dominio para proteger el entorno. También puede usar las herramientas del script local para configurar servidores independientes (no unidos a un dominio) con bases de referencia de seguridad. Para empezar a usar las bases de referencia de seguridad, descargue [Microsoft Security Compliance Toolkit 1.0](https://www.microsoft.com/download/details.aspx?id=55319).

    Para obtener más información, consulte [Bases de referencia de seguridad de Microsoft](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines).

### <a name="protect-data"></a>Protección de datos
- La **protección del entorno de Hyper-V** requiere también la protección de Windows Server que se ejecuta en una máquina virtual, al igual que protegería el sistema operativo que se ejecuta en un servidor físico. Dado que los entornos virtuales suelen tener varias máquinas virtuales que comparten el mismo host físico, es necesario proteger tanto el host físico como las máquinas virtuales que se ejecutan en él. Un atacante que ponga un host en peligro puede afectar a varias máquinas virtuales y causar un mayor impacto en las cargas de trabajo y los servicios. En esta sección se describen los siguientes métodos que puede usar para proteger Windows Server en un entorno de Hyper-V:

    - **El tejido protegido y las máquinas virtuales blindadas** fortalecen la seguridad de las máquinas virtuales que se ejecutan en entornos de Hyper-V, lo que evita que los atacantes modifiquen los archivos de la máquina virtual. Un *tejido protegido* consta de un Servicio de protección de host (HGS), que normalmente es un clúster de tres nodos, uno o más hosts protegidos y un conjunto de máquinas virtuales blindadas. El servicio de atestación evalúa la validez de las solicitudes de hosts, mientras que el servicio de protección de claves determina si se deben liberar las claves que los hosts protegidos pueden usar para iniciar la máquina virtual blindada.

        Para más información, consulte [Información general sobre el tejido protegido y las máquinas virtuales blindadas](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms).
     
     - El **Módulo de plataforma segura virtual (vTPM)** en Windows Server admite TPM para máquinas virtuales, lo que permite usar tecnologías de seguridad avanzadas, como BitLocker en máquinas virtuales. Puede habilitar la compatibilidad con TPM en cualquier máquina virtual de Hyper-V de generación 2 mediante el administrador de Hyper-V o el cmdlet de Windows PowerShell `Enable-VMTPM`.
     
        Para obtener más información, vea [Enable-VMTPM](/powershell/module/hyper-v/enable-vmtpm?view=win10-ps)
     
     - Las **Redes definidas por software (SDN)** en Azure Stack HCl y Windows Server configuran y administran de forma centralizada dispositivos de red físicos y virtuales, como enrutadores, conmutadores y puertas de enlace en su centro de datos. Los elementos de red virtual, como el conmutador virtual de Hyper-V, la virtualización de red de Hyper-V y la puerta de enlace de RAS están diseñados para ser elementos integrales de la infraestructura de SDN.

        Para obtener más información, consulte [Redes definidas por software (SDN)](/windows-server/networking/sdn/).

### <a name="protect-identities"></a>Protección de identidades
- **Local Administrator Password Solution (LAPS)** es un mecanismo ligero para los sistemas unidos a un dominio de Active Directory que establece periódicamente la contraseña de la cuenta de administrador local de cada equipo en un nuevo valor aleatorio y único. Las contraseñas se almacenan en un atributo confidencial protegido en el objeto del equipo correspondiente de Active Directory, en que solo los usuarios que están autorizados específicamente pueden recuperarlas. LAPS usa cuentas locales para la administración remota de equipos, de modo que ofrece algunas ventajas sobre el uso de las cuentas de dominio. Para obtener más información, consulte [Uso remoto de cuentas locales: LAPS lo cambia todo](/archive/blogs/secguide/remote-use-of-local-accounts-laps-changes-everything).

    Para empezar a usar LAPS, descargue [Local Administrator Password Solution (LAPS)](https://www.microsoft.com/download/details.aspx?id=46899).

- **Microsoft Advanced Threat Analytics (ATA)** es un producto local que se puede usar para detectar atacantes que intenten poner en peligro las identidades con privilegios. ATA analiza el tráfico de red para la autenticación, autorización y protocolos de recopilación de información, como Kerberos y DNS. ATA usa los datos para generar perfiles de comportamiento de los usuarios y otras entidades de la red a fin de detectar anomalías y patrones de ataque conocidos.
    
    Para más información, consulte [¿Qué es Advanced Threat Analytics?](/advanced-threat-analytics/what-is-ata)

- **Credential Guard remoto de Windows Defender** protege las credenciales a través de una conexión de Escritorio remoto. Para ello, redirige las solicitudes de Kerberos al dispositivo que solicita la conexión. También proporciona un inicio de sesión único (SSO) para sesiones de Escritorio remoto. Durante una sesión de Escritorio remoto, si el dispositivo de destino se ve comprometido, las credenciales no se exponen, ya que estas y los derivados de estas nunca se pasan a través de la red al dispositivo de destino.

    Para obtener más información, consulte [Administración de Credential Guard de Windows Defender](/windows/security/identity-protection/credential-guard/credential-guard-manage).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la seguridad y el cumplimiento normativo, vea también:
- [Seguridad y control](/windows-server/security/security-and-assurance)
- [Procedimientos recomendados de seguridad para soluciones de Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)
