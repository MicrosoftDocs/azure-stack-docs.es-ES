---
title: Telemetría de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo configurar los valores de telemetría de Azure Stack con PowerShell.
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
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: c904f77834e9195ab942f13028fe5ef2fc7a5366
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025820"
---
# <a name="azure-stack-telemetry"></a>Telemetría de Azure Stack

Los datos del sistema, o telemetría, de Azure Stack se cargan automáticamente en Microsoft mediante la Experiencia del usuario asociada. Los equipos de Microsoft utilizan los datos recopilados de la telemetría de Azure Stack principalmente para mejorar la experiencia de los clientes. También se utilizan para realizar análisis de seguridad, estado, calidad y rendimiento.

Como operador de Azure Stack, la telemetría puede proporcionar información valiosa sobre las implementaciones empresariales y le ofrece una opción que ayuda a modelar las versiones futuras de Azure Stack.

> [!NOTE]
> Azure Stack también se puede configurar para reenviar información de uso a Azure para la facturación. Esta información es necesaria para los clientes de Azure Stack de varios nodos que eligen la facturación de pago por uso. Los informes de uso se controlan de forma independiente de la telemetría y no son necesarios para los clientes de varios nodos que eligen el modelo de capacidad o para los usuarios del Kit de desarrollo de Azure Stack (ASDK). En estos escenarios, los informes de uso se pueden desactivar [mediante el script de registro](../operator/azure-stack-usage-reporting.md).

La telemetría de Azure Stack se basa en el componente *Experiencia del usuario conectado y telemetría de Windows Server 2016*, que usa la tecnología de registro de [Seguimiento de eventos para Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) para recopilar y almacenar eventos de telemetría y datos. Los componentes de Azure Stack usan la misma tecnología de registro para publicar eventos y datos que se recopilan mediante las API de seguimiento y registro de eventos del sistema operativo. Algunos ejemplos de componentes de Azure Stack son Proveedor de recursos de red, Proveedor de recursos de almacenamiento, Proveedor de recursos de supervisión y Proveedor de recursos de actualización. El componente Experiencia del usuario y telemetría asociadas cifra los datos mediante SSL y usa la asignación de certificados para transmitir datos de telemetría al servicio Microsoft Data Management a través de HTTPS.

> [!NOTE]
> Para admitir el flujo de datos de telemetría, el puerto 443 (HTTPS) debe estar abierto en la red. El componente Experiencia del usuario conectado y telemetría se conecta al servicio Administración de datos de Microsoft en https://v10.vortex-win.data.microsoft.com y también a https://settings-win.data.microsoft.com para descargar la información de configuración.

## <a name="privacy-considerations"></a>Consideraciones sobre privacidad
Las rutas del servicio ETW envían datos de telemetría al almacenamiento en la nube protegido. El acceso a los datos de telemetría se rige por el principio de menores privilegios. Solo el personal de Microsoft con una necesidad empresarial válida tiene permiso de acceso a los datos de telemetría. Microsoft no comparte los datos personales de los clientes con terceros, excepto si así lo decide el cliente o para los fines limitados descritos en la [declaración de privacidad de Azure Stack](https://privacy.microsoft.com/PrivacyStatement). Se comparten informes empresariales con los OEM y asociados que incluyen información de telemetría acumulada y anónima. Las decisiones de compartir los datos las realiza un equipo interno de Microsoft entre los que se cuentan las partes interesadas de privacidad, aspectos legales y administración de datos.

Microsoft cree en la minimización de la información, y la practica. Nos esforzamos por recopilar solo la información necesaria y la almacenamos solo mientras la necesitamos para proporcionar un servicio o realizar un análisis. Gran parte de la información sobre cómo funcionan el sistema de Azure Stack y los servicios de Azure se elimina al cabo de seis meses. Los datos acumulados o resumidos se conservan más tiempo.

Sabemos que la privacidad y seguridad de la información de nuestros clientes es importante. Por este motivo, hemos adoptado un enfoque serio y completo hacia la privacidad del cliente y la protección de sus datos con Azure Stack. Los administradores de TI disponen de controles para personalizar características y configuraciones de privacidad en cualquier momento. Nuestro compromiso a la transparencia y la confianza es claro:
- No escondemos a nuestros clientes los tipos de datos que recopilamos.
- Ponemos a los clientes de empresa al mando: ellos pueden personalizar su propia configuración de privacidad.
- Colocamos la seguridad y la privacidad del cliente en primer lugar.
- Somos transparentes sobre el modo en que se usa la telemetría.
- Usamos la telemetría para mejorar las experiencias del cliente.

Microsoft no tiene la intención de recopilar información confidencial, como números de tarjetas de crédito, nombres de usuario y contraseñas, y direcciones de correo electrónico. Si determinamos que, por accidente, se ha recibido información confidencial, la eliminamos.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Ejemplos de cómo Microsoft usa los datos de telemetría
La telemetría desempeña un importante papel a la hora de ayudarnos a identificar y corregir rápidamente problemas críticos de confiabilidad en las implementaciones y configuraciones de nuestros clientes. La información que extraemos de los datos de telemetría que recopilamos nos ayuda a identificar rápidamente problemas con servicios o configuraciones de hardware. La posibilidad que tiene Microsoft de obtener estos datos de los clientes y de impulsar las mejoras en el ecosistema ayuda a elevar el estándar de calidad de nuestras soluciones integradas de Azure Stack.

La telemetría también ayuda a Microsoft a entender mejor cómo los clientes implementan componentes y usan características y servicios para lograr sus objetivos empresariales. La obtención de información de estos ayuda a asignar prioridad a las inversiones en ingeniería en áreas que pueden tener una repercusión directa sobre las experiencias y las cargas de trabajo de nuestros clientes.

Algunos ejemplos incluyen el uso de contenedores por parte del cliente, el almacenamiento y las configuraciones de redes que están asociadas con roles de Azure Stack. También usamos la información para impulsar mejoras e inteligencia en algunas de nuestras soluciones de administración y supervisión. Esta mejora ayuda a los clientes a diagnosticar problemas de calidad y ahorrar dinero al hacer menos llamadas de soporte técnico a Microsoft.

## <a name="manage-telemetry-collection"></a>Administración de la recopilación de telemetría
No se recomienda desactivar la telemetría en su organización, ya que proporciona datos que controlan la funcionalidad y la estabilidad mejorada del producto. Sin embargo, reconocemos que en algunos casos puede ser necesario.

En estos casos, puede configurar el nivel de telemetría que se envía a Microsoft mediante la implementación previa de la configuración del Registro o mediante la implementación posterior de los puntos de conexión de telemetría.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Establecimiento del nivel de telemetría en el Registro de Windows
El Editor del Registro de Windows se usa para establecer manualmente el nivel de telemetría en el equipo host físico antes de implementar Azure Stack. Si ya existe una directiva de administración, como la directiva de grupo, reemplaza este valor del registro.

Antes de implementar Azure Stack en el host del ASDK, inicie CloudBuilder.vhdx y ejecute el siguiente script en una ventana con privilegios elevados de PowerShell:

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

Los niveles de telemetría son acumulativos y se clasifican en cuatro niveles (0-3):

**0 (Seguridad)** : Solo datos de seguridad. Información que es necesaria para ayudar a proteger el sistema operativo, que incluye datos sobre la configuración del componente Experiencia del usuario conectado y telemetría y Windows Defender. En este nivel no se emite ninguna telemetría específica de Azure Stack.

**1 (Básico)** : datos de seguridad y básicos de mantenimiento y calidad. Información básica del dispositivo, que incluye: datos relacionados con la calidad, compatibilidad con aplicaciones, datos de uso de aplicaciones y datos del nivel seguridad. Al establecer el nivel de telemetría en Básico se habilita la telemetría de Azure Stack. Los datos recopilados en este nivel incluyen:

- **Información básica del dispositivo** que ayuda a comprender los tipos y las configuraciones de instancias de Windows Server 2016 nativas y virtualizadas del ecosistema, como por ejemplo:
  - Atributos de la máquina, como el OEM y el modelo.
  - Atributos de la red, como el número y la velocidad de los adaptadores de red.
  - Atributos del procesador y la memoria, como el número de núcleos y el tamaño de la memoria.
  - Atributos de almacenamiento, como número de unidades, tipo y tamaño
- **Funcionalidad de telemetría**, que incluye el porcentaje de eventos cargados, eventos descartados y hora de última carga.
- **Información relacionada con la calidad** que ayuda a Microsoft a desarrollar un conocimiento básico del funcionamiento de Azure Stack. Un ejemplo es el número de alertas críticas en una configuración de hardware determinada.
- **Datos de compatibilidad**, que permiten saber qué proveedores de recursos están instalados en un sistema y en una VM, e identifican posibles problemas de compatibilidad.

**2 (Mejorado)** : información adicional, que incluye cómo se usa el sistema operativo y otros servicios de Azure Stack, cuál es su rendimiento, datos avanzados de confiabilidad y datos de los niveles Básico y Seguridad.

**3 (Completo)** : todos los datos necesarios para identificar y solucionar problemas, además de los datos de los niveles Seguridad, Básico y Mejorado.

> [!NOTE]
> El valor de nivel de telemetría predeterminado es 2 (mejorado).

La desactivación de la telemetría de Windows y de Azure Stack deshabilita la de SQL. Para más información acerca de las implicaciones de la configuración de telemetría de Windows Server, consulte las [Notas del producto de telemetría de Windows](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Estos niveles de telemetría solo se aplican a los componentes de Microsoft Azure Stack. Los componentes y servicios de software que no sean de Microsoft que se ejecutan en el host de ciclo de vida de hardware de los asociados de hardware de Azure Stack se pueden comunicar con sus servicios en la nube fuera de estos niveles de telemetría. Acuda a su proveedor de soluciones de hardware de Azure Stack para que le informe de su directiva de telemetría y cómo puede usarla o no.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Habilitación o deshabilitación de la telemetría después de la implementación

Para habilitar o deshabilitar la telemetría después de la implementación, debe tener acceso al punto de conexión con privilegios (PEP) que se expone en las máquinas virtuales de ERCS.
1.  Para habilitarla: `Set-Telemetry -Enable`
2.  Para deshabilitar: `Set-Telemetry -Disable`

Detalle del PARÁMETRO:
> .PARÁMETRO Enable: activa la carga de telemetría
> 
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
[Inicio y detención del kit de desarrollo de Azure Stack](asdk-start-stop.md)
