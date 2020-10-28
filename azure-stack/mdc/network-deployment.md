---
title: Implementación de red Azure Stack para MDC
description: Obtenga información acerca de la implementación de red de Azure Stack para el dispositivo MDC.
author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 01/17/2020
ms.openlocfilehash: d57e4a276ea93a8be70eb2bba05bdb8b2a318924
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183469"
---
# <a name="network-deployment"></a>Implementación de red

En este tema se trata el permiso de acceso a los conmutadores TOR, las asignaciones de direcciones IP y otras tareas de implementación de red.

## <a name="plan-configuration-deployment"></a>Planeamiento de la implementación de la configuración

En las secciones siguientes se tratan los permisos y las asignaciones de direcciones IP.

### <a name="physical-switch-access-control-list"></a>Lista de control de acceso de los conmutadores físicos

Para proteger la solución Azure Stack, hemos implementado listas de control de acceso (ACL) en los conmutadores TOR. En esta sección se describe cómo se implementa esta seguridad. En la tabla siguiente se muestran los orígenes y destinos de cada red dentro de la solución Azure Stack:

![Diagrama de listas de control de acceso en los conmutadores TOR](media/network-deployment/acls.png)

En la tabla siguiente se correlacionan las referencias de ACL con las redes de Azure Stack.

| Interno de administración de BMC                            | El tráfico se limita a interno únicamente.                                                                                                                                      |   |   |   |   |   |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|---|
| Externo de administración de BMC                            | ACL permite el acceso más allá del dispositivo de borde.                                                                                                                            |   |   |   |   |   |
| Administración del almacenamiento extendido                        | Interfaces de administración dedicadas para el sistema de almacenamiento extendido                                                                                                           |   |   |   |   |   |
| Administración de conmutadores                                  | Interfaces de administración de conmutadores dedicadas.                                                                                                                                   |   |   |   |   |   |
| "Infraestructura de Azure Stack"                 | Servicios de infraestructura y VM de Azure Stack, red restringida.                                                                                                          |   |   |   |   |   |
| Público de la infraestructura de Azure Stack (PEP/ERCS) | Punto de conexión protegido de Azure Stack, servidor de consola de recuperación de emergencia. El cliente puede abrir la ACL para permitir el tráfico a su red de administración de centros de datos.                        |   |   |   |   |   |
| RouterIP de Tor1, Tor2                           | Interfaz de bucle invertido del modificador que se usa para el emparejamiento BGP entre el SLB y el conmutador o enrutador. El cliente tendrá la opción de desactivar el firewall para estas direcciones IP en el borde. |   |   |   |   |   |
| Storage                                      | Las direcciones IP privadas no se enrutan fuera de la región.                                                                                                                             |   |   |   |   |   |
| VIP internas                                | Las direcciones IP privadas no se enrutan fuera de la región.                                                                                                                             |   |   |   |   |   |
| VIP públicas                                  | Espacio de direcciones de red del inquilino administrado por la controladora de red.                                                                                                           |   |   |   |   |   |
| VIP de administración públicas                            | Pequeño subconjunto de direcciones del grupo de inquilinos que son necesarias para comunicarse con las VIP internas y la infraestructura de Azure Stack.                                                    |   |   |   |   |   |
| Redes permitidas                           | Red definida por el cliente.                                                                                                                                                 |   |   |   |   |   |
| 0.0.0.0                                      | Desde la perspectiva de Azure Stack 0.0.0.0 es el dispositivo de borde.                                                                                                         |   |   |   |   |   |
| **Permitir**                                   | El permiso del tráfico está habilitado, pero el acceso SSH está bloqueado de forma predeterminada.                                                                                                           |   |   |   |   |   |
| **Sin ruta**                                     | Las rutas no se propagan fuera del entorno de Azure Stack.                                                                                                         |   |   |   |   |   |
| **ACL de MUX**                                      | Se usan ACL de MUX de Azure Stack.                                                                                                                                       |   |   |   |   |   |
| **N/D**                                          | No es parte de una ACL de VLAN.                                                                                                                                                 |   |   |   |   |   |
|                                              |                                                                                                                                                                           |   |   |   |   |   |

### <a name="ip-address-assignments"></a>Asignaciones de direcciones IP

En la hoja de cálculo de implementación, se le pide que proporcione las siguientes direcciones de red para admitir el proceso de implementación de Azure Stack. El equipo de implementación utiliza la herramienta de hoja de cálculo de implementación para dividir las redes IP en el conjunto de redes más pequeñas que requiere el sistema.

En este ejemplo, rellenaremos la pestaña Configuración de red de la hoja de cálculo de implementación con los siguientes valores:

-   Red de BMC: 10.193.132.0 /27

-   VIP internas y red de almacenamiento de red privada: 11.11.128.0 /20

-   Red de infraestructura: 12.193.130.0 /24

-   Red IP virtual (VIP) pública: 13.200.132.0 /24

-   Red de la infraestructura de conmutadores: 10.193.132.128 /26

Al ejecutar la función Generar de la herramienta de hoja de cálculo de implementación, se crean dos nuevas pestañas en la hoja de cálculo. La primera pestaña corresponde al resumen de la subred y muestra cómo se han dividido las superredes para crear todas las redes que requiere el sistema. En el ejemplo siguiente, solo hay un subconjunto de las columnas que se encuentran en esta pestaña. El resultado real tiene más detalles de cada red enumerada:

| **Bastidor** | **Tipo de subred** | **Nombre**                                   | **Subred IPv4**   | **Direcciones IPv4** |
|----------|-----------------|--------------------------------------------|-------------------|--------------------|
| Borde   | Vínculo P2P        | P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 | 4                  |
| Borde   | Vínculo P2P        | P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 | 4                  |
| Borde   | Vínculo P2P        | P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 | 4                  |
| Borde   | Vínculo P2P        | P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 | 4                  |
| Borde   | Vínculo P2P        | P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 | 4                  |
| Borde   | Vínculo P2P        | P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 | 4                  |
| Bastidor1    | Bucle invertido        | Loopback0_Rack1_TOR1                       | 10.193.132.152/32 | 1                  |
| Bastidor1    | Bucle invertido        | Loopback0_Rack1_TOR2                       | 10.193.132.153/32 | 1                  |
| Bastidor1    | Bucle invertido        | Loopback0_Rack1_TOR2                        | 10.193.132.154/32 | 1                  |
| Bastidor1    | Vínculo P2P        | P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 | 4                  |
| Bastidor1    | Vínculo P2P        | P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 | 4                  |
| Bastidor1    | VLAN            | BMCMgmt                                    | 10.193.132.0/27   | 32                 |
| Bastidor1    | VLAN            | SwitchMgmt                                 | 10.193.132.168/29 | 8                  |
| Bastidor1    | VLAN            | CL01-RG01-SU01-Storage                     | 11.11.128.0/25    | 128                |
| Bastidor1    | VLAN            | CL01-RG01-SU01-Infra                       | 12.193.130.0/24   | 256                |
| Bastidor1    | Otros           | CL01-RG01-SU01-VIPS                        | 13.200.132.0/24   | 256                |
| Bastidor1    | Otros           | CL01-RG01-SU01-InternalVIPS                | 11.11.128.128/25  | 128                |

La segunda pestaña es **Uso de direcciones IP** y muestra cómo se consumen las direcciones IP:

#### <a name="bmc-network"></a>Red de BMC

La superred de la red de BMC requiere una red /26 como mínimo. La puerta de enlace usa la primera dirección IP de la red seguida de los dispositivos de BMC del bastidor. El host de ciclo de vida de hardware tiene varias direcciones asignadas en esta red y se puede usar para implementar, supervisar y admitir el bastidor. Estas direcciones IP se distribuyen en tres grupos: DVM, InternalAccessible y ExternalAccessible.

- Bastidor: Bastidor1         
- Nombre: BMCMgmt      

| **Asignado a**      | **Dirección IPv4** |
|----------------------|------------------|
| Red              | 10.193.132.0     |
| Puerta de enlace              | 10.193.132.1     |
| HLH-BMC              | 10.193.132.2     |
| AzS-Node01           | 10.193.132.3     |
| AzS-Node02           | 10.193.132.4     |
| AzS-Node03           | 10.193.132.5     |
| AzS-Node04           | 10.193.132.6     |
| ExternalAccessible-1 | 10.193.132.19    |
| ExternalAccessible-2 | 10.193.132.20    |
| ExternalAccessible-3 | 10.193.132.21    |
| ExternalAccessible-4 | 10.193.132.22    |
| ExternalAccessible-5 | 10.193.132.23    |
| InternalAccessible-1 | 10.193.132.24    |
| InternalAccessible-2 | 10.193.132.25    |
| InternalAccessible-3 | 10.193.132.26    |
| InternalAccessible-4 | 10.193.132.27    |
| InternalAccessible-5 | 10.193.132.28    |
| CL01-RG01-SU01-DVM00 | 10.193.132.29    |
| HLH-OS               | 10.193.132.30    |
| Difusión            | 10.193.132.31    |

#### <a name="storage-network"></a>Red de almacenamiento

La red de almacenamiento es una red privada y no está pensada para enrutarse más allá del bastidor. Es la primera mitad de la superred de la red privada y la usa el conmutador distribuido, tal y como se muestra en la tabla siguiente. La puerta de enlace es la primera IP de la subred. La segunda mitad usada para las VIP internas es un grupo privado de direcciones administradas por Azure Stack SLB, que no se muestra en la pestaña Uso de direcciones IP. Estas redes admiten Azure Stack. Existen ACL en los conmutadores de TOR que impiden acceder a estas redes, o anunciarlas, fuera de la solución.

- Bastidor: Bastidor1                                    
- Nombre: CL01-RG01-SU01-Storage 

| **Asignado a**              | **Dirección IPv4** |
|------------------------------|------------------|
| Red                      | 11.11.128.0      |
| Puerta de enlace                      | 11.11.128.1      |
| TOR1                         | 11.11.128.2      |
| TOR2                         | 11.11.128.3      |
| Difusión                    | 11.11.128.127    |

#### <a name="azure-stack-infrastructure-network"></a>Red de la infraestructura de Azure Stack

La superred de la red de infraestructura requiere una red /24, que sigue siendo una red /24 después de ejecutar la herramienta de hoja de cálculo de implementación. La puerta de enlace será la primera IP de la subred.

- Bastidor: Bastidor1               
- Nombre: CL01-RG01-SU01-Infra 

| **Asignado a**            | **Dirección IPv4** |
|----------------------------|------------------|
| Red                    | 12.193.130.0     |
| Puerta de enlace                    | 12.193.130.1     |
| TOR1                       | 12.193.130.2     |
| TOR2                       | 12.193.130.3     |
| Difusión                  | 12.193.130.255   |

#### <a name="switch-infrastructure-network"></a>Red de la infraestructura de conmutadores

La red de la infraestructura se divide en varias redes que se utilizan en la infraestructura de conmutadores físicos. Esto difiere de la infraestructura de Azure Stack, que solo admite el software de Azure Stack. La red de la infraestructura de conmutadores solo admite la infraestructura de conmutadores físicos. Las redes que admite la infraestructura son:

| **Nombre**                                   | **Subred IPv4**   |
|--------------------------------------------|-------------------|
| P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 |
| P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 |
| P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 |
| P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 |
| P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 |
| P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 |
| Loopback0_Rack1_TOR1                       | 10.193.132.152/32 |
| Loopback0_Rack1_TOR2                       | 10.193.132.153/32 |
| Loopback0_Rack1_TOR2                        | 10.193.132.154/32 |
| P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 |
| P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 |
| SwitchMgmt                                 | 10.193.132.168/29 |
|                                            |                   |

-   Punto a punto (P2P): estas redes permiten la conectividad entre todos los conmutadores. El tamaño de la subred es una red /30 para cada P2P. La dirección IP más baja siempre se asigna al dispositivo ascendente (norte) en la pila.

-   Bucle invertido: estas direcciones son redes /32 asignadas a cada conmutador que se usa en el bastidor. No se asigna un bucle invertido a los dispositivos de borde, ya que no se espera que formen parte de la solución Azure Stack.

-   Administración de conmutadores: Esta red /29 es compatible con las interfaces de administración dedicadas de los conmutadores del bastidor. Las direcciones IP se asignan de la manera siguiente (esta tabla también se puede encontrar en la pestaña Uso de direcciones IP de la hoja de cálculo de implementación):

- Bastidor: Bastidor1      
- Nombre: SwitchMgmt

| **Asignado a**  | **Dirección IPv4** |
|------------------|------------------|
| Red          | 10.193.132.168   |
| Puerta de enlace          | 10.193.132.169   |
| TOR1             | 10.193.132.170   |
| TOR2             | 10.193.132.171   |
| Difusión        | 10.193.132.175   |

## <a name="prepare-environment"></a>Preparación del entorno

La imagen del host de ciclo de vida de hardware incluye el contenedor de Linux necesario que se usa para generar la configuración del conmutador de red físico.

El kit de herramientas de implementación de asociados más reciente incluye la imagen de contenedor más reciente.
La imagen de contenedor del host de ciclo de vida de hardware se puede reemplazar si es necesario generar una configuración de conmutador actualizada.

Estos son los pasos para actualizar la imagen del contenedor:

1.  Descargar la imagen de contenedor.

2.  Reemplazar la imagen de contenedor en la siguiente ubicación.

## <a name="generate-configuration"></a>Generación de la configuración

Aquí le guiaremos a través de los pasos necesarios para generar los archivos JSON y los archivos de configuración del conmutador de red:

1.  Abra la hoja de cálculo de implementación.

2.  Rellene todos los campos obligatorios en todas las pestañas.

3.  Invoque la función "Generar" en la hoja de cálculo de implementación.  
    Se crearán dos pestañas adicionales que muestran las subredes IP y las asignaciones generadas.

4.  Revise los datos y, una vez confirmados, invoque la función "Exportar".  
    Se le pedirá que proporcione una carpeta en la que se guardarán los archivos JSON.

5.  Ejecute el contenedor mediante Invoke-SwitchConfigGenerator.ps1. Este script necesita una consola de PowerShell con privilegios elevados para ejecutarse y requiere que se ejecuten los siguientes parámetros.

    -   ContainerName: nombre del contenedor que generará las configuraciones del conmutador.

    -   ConfigurationData: ruta de acceso al archivo ConfigurationData.json exportado de la hoja de cálculo de implementación.

    -   OutputDirectory: ruta de acceso al directorio de salida.

    -   Offline: indica que el script se ejecuta en modo sin conexión.

    ```powershell
    C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline
    ```

Cuando se complete el script, se generará un archivo .zip con el prefijo usado en la hoja de cálculo. 

```console
C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Seconds : 2
Section : Validation
Step    : WindowsRequirement
Status  : True
Detail  : @{CurrentImage=10.0.18363.0}


Seconds : 2
Section : Validation
Step    : DockerService
Status  : True
Detail  : @{Status=Running}


Seconds : 9
Section : Validation
Step    : DockerSetup
Status  : True
Detail  : @{CPU=4; Memory=4139085824; OS=Docker Desktop; OSType=linux}


Seconds : 9
Section : Validation
Step    : DockerImage
Status  : True
Detail  : @{Container=generalonrampacr.azurecr.io/master:1.1910.78.1}


Seconds : 10
Section : Run
Step    : Container
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c; ExternalPort=32768}


Seconds : 38
Section : Generate
Step    : Config
Status  : True
Detail  : @{OutputFile=c:\temp\N22R19.zip}


Seconds : 38
Section : Exit
Step    : StopContainer
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c}
```

### <a name="custom-configuration"></a>Configuración personalizada

Puede modificar algunas opciones de configuración del entorno en la configuración del conmutador Azure Stack. Puede identificar cuál de las opciones de configuración puede cambiar en la plantilla. En este artículo se explica cada una de esas opciones de configuración personalizables y de qué manera afectan los cambios a Azure Stack. Estas opciones de configuración incluyen la actualización de contraseña, el servidor syslog, la supervisión de SNMP, la autenticación y la lista de control de acceso.

Durante la implementación de la solución de Azure Stack, el fabricante de equipos originales (OEM) crea y aplica la configuración del conmutador para TOR y BMC. A continuación, el OEM usa la herramienta de automatización de Azure Stack para confirmar que las opciones necesarias estén configuradas correctamente en los dispositivos. La configuración se basa en la información de la hoja de cálculo de implementación de Azure Stack. 

>[!NOTE]
>**No** modifique la configuración sin el consentimiento del OEM o del equipo de ingeniería de Microsoft Azure Stack. Recuerde que un cambio en la configuración del dispositivo de red puede afectar significativamente a la operación o a la resolución de problemas de red en su instancia de Azure Stack. Para obtener más información acerca de estas funciones en el dispositivo de red o cómo realizar estos cambios, póngase en contacto con el proveedor de hardware del OEM o con el Soporte técnico de Microsoft. El OEM tiene el archivo de configuración que se creó con la herramienta de automatización en función de la hoja de cálculo de implementación de Azure Stack. 

Sin embargo, hay algunos valores que se pueden agregar, quitar o cambiar en la configuración de los conmutadores de red.

#### <a name="password-update"></a>Actualización de contraseñas

El operador puede actualizar en cualquier momento la contraseña de cualquier usuario en los conmutadores de red. No es necesario cambiar ninguna información en el sistema de Azure Stack ni usar los pasos para el Cambio de secretos en Azure Stack.

#### <a name="syslog-server"></a>Servidor de Syslog

Los operadores pueden redirigir los registros del conmutador a un servidor de syslog en su centro de datos.
Use esta configuración para asegurarse de que los registros de un momento determinado se puedan usar para la resolución de problemas. De forma predeterminada, los registros se almacenan en los conmutadores; tenga en cuenta que su capacidad para almacenar registros es limitada. Consulte la sección que cuenta con las actualizaciones de la lista de control de acceso para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

#### <a name="snmp-monitoring"></a>Supervisión de SNMP

El operador puede configurar las versiones 2 o 3 del Protocolo simple de administración de redes (SNMP) para supervisar los dispositivos de red y enviar capturas a una aplicación de supervisión de red en el centro de datos. Por motivos de seguridad, use SNMPv3, ya que es más seguro que la versión 2. Consulte a su proveedor de hardware OEM para conocer las MIB y la configuración necesaria.
Consulte la sección que cuenta con las actualizaciones de la lista de control de acceso para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

#### <a name="authentication"></a>Authentication

El operador puede configurar los elementos RADIUS o TACACS para administrar la autenticación en los dispositivos de red. Consulte a su proveedor de hardware OEM para conocer los métodos admitidos y la configuración necesaria. Consulte la sección que cuenta con las actualizaciones de la lista de control de acceso para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

#### <a name="access-control-list-updates"></a>Actualizaciones de la lista de control de acceso

El operador puede cambiar la lista de control de acceso (ACL) para permitir el acceso a las interfaces de administración de dispositivos de red y al host de ciclo de vida de hardware (HLH) desde un intervalo de red del centro de datos de confianza. El operador puede elegir qué componente será accesible y desde dónde se podrá obtener acceso al mismo. Con la lista de control de acceso, el operador puede permitir que sus VM de jumpbox de administración de un intervalo de red específico obtengan acceso a la interfaz de administración de conmutadores, al sistema operativo HLH y al BMC HLH.

Para obtener más información, consulte [Lista de control de acceso de los conmutadores físicos](#physical-switch-access-control-list).

#### <a name="tacacs-radius-and-syslog"></a>TACACS, RADIUS y Syslog

La solución Azure Stack no se incluirá con una solución TACACS o RADIUS para el control de acceso de dispositivos como los conmutadores y enrutadores, ni tampoco con una solución Syslog para capturar registros de conmutadores, pero todos estos dispositivos admiten esos servicios.
Para facilitar la integración con un servidor TACACS, RADIUS o Syslog existente en su entorno, se proporcionará un archivo adicional con la configuración del conmutador de red que permitirá al ingeniero in situ personalizar el conmutador según las necesidades del cliente.

## <a name="next-steps"></a>Pasos siguientes

[Integración de red](network-integration.md)
