---
title: Telemetría de Azure Stack | Microsoft Docs
description: Describe cómo configurar los valores de telemetría de Azure Stack mediante PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: comartin
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ce77e27cf6d7be70cb3ff4b9be0e0366beca0498
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984900"
---
# <a name="azure-stack-telemetry"></a>Telemetría de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

La telemetría de Azure Stack carga automáticamente datos del sistema en Microsoft mediante la Experiencia del usuario asociada. Los equipos de Microsoft utilizan los datos que la telemetría de Azure Stack reúne para mejorar las experiencias de los clientes. Estos datos también se utilizan para análisis de seguridad, estado, calidad y rendimiento.

Para un operador de Azure Stack, la telemetría puede proporcionar información valiosa sobre las implementaciones empresariales y le ofrece una opción que ayuda a modelar las versiones futuras de Azure Stack.

> [!NOTE]
> También se puede configurar Azure Stack para reenviar la información de uso a Azure con fines de facturación. Esta información es necesaria para los clientes de Azure Stack de varios nodos que eligen la facturación de pago por uso. Los informes de uso se controlan de forma independiente de la telemetría y no son necesarios para los clientes de varios nodos que eligen el modelo de capacidad o para los usuarios del Kit de desarrollo de Azure Stack. En estos escenarios, los informes de uso se pueden desactivar [mediante el script de registro](azure-stack-usage-reporting.md).

La telemetría de Azure Stack se basa en el componente Experiencia del usuario y telemetría asociadas de Windows Server 2016, que usa la tecnología de registro de seguimientos [ETW (Seguimiento de eventos para Windows)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) para recopilar y almacenar eventos y datos. Los componentes de Azure Stack usan la misma tecnología para publicar eventos y datos que se recopilan mediante las API de seguimiento y registro de eventos del sistema operativo. Entre los ejemplos de estos componentes de Azure Stack se incluyen los siguientes proveedores: Recursos de red, recursos de almacenamiento, recursos de supervisión y recursos de actualización. El componente Experiencia del usuario y telemetría asociadas cifra los datos mediante SSL y usa la asignación de certificados para transmitir datos al servicio Microsoft Data Management a través de HTTPS.

> [!IMPORTANT]
> Para habilitar el flujo de datos de telemetría, el puerto 443 (HTTPS) debe estar abierto en la red. El componente Experiencia del usuario y telemetría asociadas se conecta al servicio Microsoft Data Management en https://v10.vortex-win.data.microsoft.com. El componente Experiencia del usuario y telemetría asociadas también se conecta a https://settings-win.data.microsoft.com para descargar la información de la configuración.

## <a name="privacy-considerations"></a>Consideraciones sobre privacidad

El servicio ETW enruta los datos de telemetría de vuelta al almacenamiento de nube protegido. El acceso a los datos de telemetría se rige por el principio de menores privilegios. Solo el personal de Microsoft con una necesidad empresarial válida tiene acceso a los datos de telemetría. Microsoft no comparte los datos personales de los clientes con terceros, excepto si así lo decide el cliente o con las finalidades limitadas que se describen en la [declaración de privacidad de Microsoft](https://privacy.microsoft.com/PrivacyStatement). Los informes empresariales que se comparten tanto con OEM como con asociados incluyen datos agregados y anónimos. Las decisiones de compartir los datos las realiza un equipo interno de Microsoft entre los que se cuentan las partes interesadas de privacidad, aspectos legales y administración de datos.

Microsoft cree en la minimización de la información, y la practica. Nos esforzamos por recopilar solo la información necesaria y la almacenamos solo durante el tiempo necesario para proporcionar un servicio o realizar un análisis. Gran parte de la información sobre cómo funcionan el sistema de Azure Stack y los servicios de Azure se elimina al cabo de seis meses. Los datos acumulados o resumidos se mantienen durante más tiempo.

Somos conscientes de que la privacidad y seguridad de la información del cliente es importante.  Microsoft adopta un enfoque serio y completo hacia la privacidad del cliente y la protección de sus datos con Azure Stack. Los administradores de TI disponen de controles para personalizar características y configuraciones de privacidad en cualquier momento. Nuestro compromiso a la transparencia y la confianza es claro:

- No escondemos a nuestros clientes los tipos de datos que recopilamos.
- Ponemos a los clientes de empresa al mando: ellos pueden personalizar su propia configuración de privacidad.
- Colocamos la seguridad y la privacidad del cliente en primer lugar.
- Somos transparentes sobre el modo en que se usan los datos de telemetría.
- Usamos los datos de telemetría para mejorar las experiencias del cliente.

La intención de Microsoft no es recopilar datos confidenciales, como números de tarjeta de crédito, nombres de usuario y contraseñas, direcciones de correo electrónico u otra información igualmente confidencial. Si determinamos que, por accidente, se ha recibido información confidencial, la eliminamos.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Ejemplos de cómo Microsoft usa los datos de telemetría

La telemetría desempeña un importante papel a la hora de ayudar a identificar y corregir rápidamente problemas críticos de confiabilidad en las implementaciones y configuraciones de los clientes. La información de los datos de telemetría ayuda a identificar problemas con servicios o configuraciones de hardware. La posibilidad que tiene Microsoft de obtener estos datos de los clientes y de impulsar las mejoras en el ecosistema eleva el estándar de calidad de nuestras soluciones integradas de Azure Stack.

La telemetría también ayuda a Microsoft a entender mejor cómo los clientes implementan componentes y usan características y servicios para lograr sus objetivos empresariales. Esta información ayuda a asignar prioridad a las inversiones en ingeniería en áreas que pueden tener una repercusión directa sobre las experiencias y las cargas de trabajo de los clientes.

Algunos ejemplos incluyen el uso de contenedores por parte del cliente, el almacenamiento y las configuraciones de redes que están asociadas con roles de Azure Stack. También usamos la información para impulsar mejoras e inteligencia en las soluciones de administración y supervisión de Azure Stack. Estas mejoras permiten a los clientes diagnosticar fácilmente problemas y ahorrar dinero, ya que realizan menos llamadas de soporte técnico a Microsoft.

## <a name="manage-telemetry-collection"></a>Administración de la recopilación de telemetría

No recomendamos desactivar la telemetría en la organización. Sin embargo, en algunos escenarios puede ser necesario.

En estos escenarios, puede configurar el nivel de telemetría que se envía a Microsoft mediante la configuración del Registro antes implementar Azure Stack o mediante los puntos de conexión de telemetría después de implementar Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Niveles de telemetría y colección de datos

Antes de cambiar la configuración de telemetría, debe entender los niveles de telemetría y qué datos se recopilan en cada nivel.

La configuración de telemetría se agrupan en cuatro niveles (0-3) que son acumulativos y se clasifican de la siguiente manera:

**0 (Seguridad)**</br>
Solo datos de seguridad. Información necesaria para mantener protegido el sistema operativo. Esto incluye datos sobre la configuración del componente Experiencia del usuario y telemetría asociadas, y Windows Defender. En este nivel no se emite ninguna telemetría específica de Azure Stack.

**1 (Básico)**</br>
Datos de seguridad y datos básicos de mantenimiento y calidad. Información básica del dispositivo, lo que incluye datos relacionados con la calidad, compatibilidad con aplicaciones, datos de uso de aplicaciones y datos del nivel **Seguridad**. Al establecer el nivel de telemetría en Básico se habilita la telemetría de Azure Stack. Los datos recopilados en este nivel incluyen:

- *Información básica del dispositivo* que permite comprender los tipos y las configuraciones de instancias de Windows Server 2016 nativas y virtuales del ecosistema. Esto incluye:

  - Atributos de la máquina, como OEM, y modelo.
  - Atributos de red, como número de adaptadores de red y su velocidad.
  - Atributos de procesador y memoria, como número de núcleos y cantidad de memoria instalada.
  - Atributos de almacenamiento, como el número de unidades, así como el tipo y tamaño de las unidades.

- *Funcionalidad de telemetría*, que incluye el porcentaje de eventos cargados, eventos descartados y hora de última carga.
- *Información relacionada con la calidad* que ayuda a Microsoft a desarrollar un conocimiento básico del funcionamiento Azure Stack. Por ejemplo, el número de alertas críticas en una configuración de hardware determinada.
- *Datos de compatibilidad* que ayudan a saber qué proveedores de recursos están instalados en un sistema y en una máquina virtual. Esto identifica posibles problemas de compatibilidad.

**2 (Mejorado)**</br>
Información adicional, que incluye cómo se usan el sistema operativo y otros servicios de Azure Stack, cuál es su rendimiento, datos avanzados de confiabilidad y datos de los niveles **Básico** y **Seguridad**.

> [!NOTE]
> Es la configuración de telemetría predeterminada.

**3 (Completo)**</br>
Todos los datos necesarios para identificar problemas y ayudar a su solución, además de los datos de los niveles **Seguridad**, **Básico** y **Mejorado**.

> [!IMPORTANT]
> Estos niveles de telemetría solo se aplican a los componentes de Microsoft Azure Stack. Los componentes y servicios de software que no sean de Microsoft que se ejecutan en el host de ciclo de vida de hardware de los asociados de hardware de Azure Stack se pueden comunicar con sus servicios en la nube fuera de estos niveles de telemetría. Acuda a su proveedor de soluciones de hardware de Azure Stack para que le informe de su directiva de telemetría y cómo puede usarla o no.

La desactivación de la telemetría de Windows y de Azure Stack también deshabilita la de SQL. Para más información acerca de las implicaciones de la configuración de la telemetría de Windows Server, consulte las [notas del producto de la telemetría de Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: establecimiento del nivel de telemetría en el Registro de Windows

Se puede usar el Editor del Registro de Windows para establecer manualmente el nivel de telemetría en el equipo host físico antes de implementar Azure Stack. Si ya existe una directiva de administración, como la directiva de grupo, reemplaza este valor del registro.

Antes de implementar Azure Stack en el host del kit de desarrollo, inicie CloudBuilder.vhdx y ejecute el siguiente script en una ventana de PowerShell con privilegios elevados:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK y varios nodos: habilitación o deshabilitación de la telemetría después de la implementación

Para habilitar o deshabilitar la telemetría después de la implementación, debe tener acceso al punto de conexión con privilegios (PEP) que se expone en las máquinas virtuales de ERCS.

1. Para habilitarla: `Set-Telemetry -Enable`
2. Para deshabilitar: `Set-Telemetry -Disable`

Detalles de los parámetros:
> .PARÁMETRO Enable: activa la carga de telemetría</br>
> .PARÁMETRO Disable: desactiva la carga de datos de telemetría  

**Script para habilitar la telemetría:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Script para deshabilitar la telemetría:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Pasos siguientes

[Registro de Azure Stack en Azure](azure-stack-registration.md)