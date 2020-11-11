---
title: Procedimientos recomendados del servicio Infrastructure Backup para Azure Stack | Microsoft Docs
description: Siga estos procedimientos recomendados al implementar y administrar Azure Stack para ayudar a mitigar la pérdida de datos en caso de error grave.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: d2775627f9834a6410fcf2888bbb4abca66d958b
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330330"
---
# <a name="infrastructure-backup-service-best-practices"></a>Procedimientos recomendados del servicio Infrastructure Backup

*Se aplica a: Centro de datos modular, Azure Stack Hub resistente*

Revise periódicamente estos procedimientos recomendados para comprobar que la instalación cumple aún las normas cuando se realicen cambios en el flujo de la operación. Si surgieran problemas al implementar estos procedimientos recomendados, póngase en contacto con el soporte técnico de Microsoft para obtener ayuda.

## <a name="configuration-best-practices"></a>Procedimientos recomendados de configuración

La copia de seguridad de la infraestructura está habilitada de forma predeterminada durante la implementación de un sistema nuevo y se almacena internamente. Con el portal de Azure Stack o PowerShell, puede proporcionar una ubicación de almacenamiento externa para exportar las copias de seguridad a una ubicación secundaria.

### <a name="networking"></a>Redes

La cadena de convención de nomenclatura universal (UNC) para la ruta de acceso debe utilizar un nombre de dominio completo. Se puede usar la dirección IP si no es posible la resolución de nombres. Una cadena UNC especifica la ubicación de recursos como archivos compartidos o dispositivos.

### <a name="encryption"></a>Cifrado

El certificado de cifrado se utiliza para cifrar los datos de copia de seguridad que se exportan a un almacenamiento externo. El certificado puede ser un certificado autofirmado, ya que el certificado se usa solo para transportar claves. Para más información sobre cómo crear un certificado, consulte New-SelfSignedCertificate.

El certificado se debe almacenar en una ubicación segura. El formato CER del certificado se utiliza para cifrar solo datos y no para establecer la comunicación.

## <a name="operational-best-practices"></a>Procedimientos recomendados operativos

### <a name="backups"></a>Copias de seguridad

- Los trabajos de copia de seguridad se ejecutan mientras se está ejecutando el sistema, de modo que no hay ningún tiempo de inactividad en las aplicaciones de usuario o las experiencias de administración. Los trabajos de copia de seguridad tardan entre 20 y 40 minutos para una solución que está bajo una carga razonable.

- Instrucciones adicionales que se proporcionan para realizar copias de seguridad manuales de los conmutadores de red y del host de ciclo de vida de hardware (HLH).

### <a name="folder-names"></a>Nombres de carpetas

- La infraestructura crea la carpeta MASBACKUP automáticamente. Se trata de un recurso compartido administrado por Microsoft. Puede crear recursos compartidos en el mismo nivel que MASBACKUP. No se recomienda crear carpetas o almacenar datos dentro de una carpeta MASBACKUP que no haya creado Azure Stack.

- Use el nombre de dominio completo y la región en el nombre de la carpeta para diferenciar los datos de copia de seguridad de nubes diferentes. El nombre de dominio completo (FQDN) de su implementación y puntos de conexión de Azure Stack es la combinación de los parámetros de región y de nombre de dominio externo. Para más información, vea [Integración del centro de datos de Azure Stack: DNS](../../operator/azure-stack-integrate-dns.md).

Por ejemplo, el recurso compartido de copia de seguridad es AzSBackups, hospedado en fileserver01.contoso.com. En ese recurso compartido de archivos puede haber una carpeta para cada implementación de Azure Stack que use el nombre de dominio externo y una subcarpeta que use el nombre de región.

- FQDN: contoso.com
- Región: nyc

```shell
\\fileserver01.contoso.com\AzSBackups
\\fileserver01.contoso.com\AzSBackups\contoso.com
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup
```

La carpeta `MASBackup` es donde Azure Stack almacena sus datos de copia de seguridad. No use esta carpeta para almacenar sus propios datos. Los fabricantes de equipos originales no deben usar tampoco esta carpeta para almacenar los datos de copia de seguridad.

Se recomienda a los OEM que almacenen los datos de copia de seguridad de sus componentes en la carpeta de la región. Cada conmutador de red, host de ciclo de vida de hardware, etc., puede almacenarse en su propia subcarpeta. Por ejemplo:

```shell
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration
```

### <a name="monitoring"></a>Supervisión

El sistema prevé las siguientes alertas:

| Alerta                                                   | Descripción                                                                                     | Corrección                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Se ha producido un error de copia de seguridad porque el recurso compartido de archivos ha llegado al límite de su capacidad. | El recurso compartido de archivos ha llegado al límite de su capacidad y el controlador de copia de seguridad no puede exportar archivos de copia de seguridad a la ubicación. | Agregue más capacidad de almacenamiento e intente realizar la copia de seguridad de nuevo. Elimine copias de seguridad existentes (de la más antigua a la primera) para liberar espacio.                    |
| Error de copia de seguridad debido a problemas de conectividad.             | La red entre Azure Stack y el recurso compartido de archivos está experimentando problemas.                          | Solucione el problema de red e intente realizar la copia de seguridad de nuevo.                                                                                            |
| Se ha producido un error de copia de seguridad debido a un error en la ruta de acceso.                | No se puede resolver la ruta de acceso del recurso compartido de archivos.                                                          | Asigne el recurso compartido desde otro equipo para asegurarse de que es posible acceder a él. Es posible que tenga que actualizar la ruta de acceso si ya no es válida.       |
| Se ha producido un error de copia de seguridad debido a un problema de autenticación               | Puede haber un problema con las credenciales o un problema de red que afecta a la autenticación.    | Asigne el recurso compartido desde otro equipo para asegurarse de que es posible acceder a él. Es posible que tenga que actualizar las credenciales si ya no son válidas. |
| Se ha producido un error de copia de seguridad debido a un error general.                    | La solicitud con error podría deberse a un problema intermitente. Intente realizar la copia de seguridad de nuevo.                    | Llame al soporte técnico.                                                                                                                               |

### <a name="infrastructure-backup-service-components"></a>Componentes del servicio Copia de seguridad de infraestructura

El servicio Copia de seguridad de infraestructura incluye los siguientes componentes:

- **Infrastructure Backup Controller** : En cada nube de Azure Stack se genera una instancia de Infrastructure Backup Controller, que además reside en ellas.

- **Backup Resource Provider** : Backup Resource Provider (Backup RP) consta de la interfaz de usuario y las API que exponen la funcionalidad básica de copia de seguridad para la infraestructura de Azure Stack.

### <a name="infrastructure-backup-controller"></a>Infrastructure Backup Controller

Infrastructure Backup Controller es un servicio de Service Fabric del cual se generan instancias para una nube de Azure Stack. Los recursos de copia de seguridad se crean en un nivel regional y capturan datos de servicio específicos de la región de AD, CA, Azure Resource Manager, CRP, SRP, NRP, KeyVault y RBAC.

### <a name="backup-resource-provider"></a>Backup Resource Provider

Backup Resource Provider presenta una interfaz de usuario en el portal de Azure Stack para la configuración básica y la lista de recursos de copia de seguridad. Los operadores pueden realizar las siguientes acciones en la interfaz de usuario:

- Habilitar la copia de seguridad por primera vez proporcionando la ubicación de almacenamiento externa, las credenciales y la clave de cifrado.
- Ver los recursos de copia de seguridad creados y completados, y los recursos de estado en creación.
- Modificar la ubicación de almacenamiento en la que Backup Controller coloca los datos de copia de seguridad.
- Modificar las credenciales que utiliza Backup Controller para tener acceso a la ubicación de almacenamiento externa.
- Modificar la clave de cifrado que utiliza Backup Controller para cifrar las copias de seguridad.

## <a name="backup-controller-requirements"></a>Requisitos de Backup Controller

En esta sección se describen los requisitos importantes del servicio Copia de seguridad de infraestructura. Es recomendable que revise cuidadosamente la siguiente información antes de habilitar la copia de seguridad para su instancia de Azure Stack y que luego la consulte según sea necesario durante la implementación y el funcionamiento posterior.

Estos requisitos son:

- **Requisitos de software:** describe las ubicaciones de almacenamiento admitidas y las orientaciones sobre el ajuste de tamaño.
- **Requisitos de red** : describe los requisitos de red para las diferentes ubicaciones de almacenamiento.

### <a name="software-requirements"></a>Requisitos de software

#### <a name="supported-storage-locations"></a>Ubicaciones de almacenamiento admitidas

| Ubicación de almacenamiento                                                                  | Detalles                                                                                                                                                  |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Recurso compartido de archivos SMB hospedado en un dispositivo de almacenamiento dentro del entorno de red de confianza. | Recurso compartido SMB en el mismo centro de datos en el que está implementado Azure Stack u otro centro de datos. Varias instancias de Azure Stack pueden usar el mismo recurso compartido de archivos. |
| Recurso compartido de archivos SMB en Azure.                                                          | Actualmente no se admite.                                                                                                                                 |
| Blob Storage en Azure.                                                            | Actualmente no se admite.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versiones de SMB admitidas

|SMB  |Versión  |
|---------|---------|
|SMB     |   3.x      |

#### <a name="smb-encryption"></a>Cifrado de SMB

El servicio Copia de seguridad de infraestructura admite la transferencia de datos de copia de seguridad a una ubicación de almacenamiento externa con cifrado SMB habilitado en el lado servidor. Si el servidor no admite el cifrado SMB o no tiene habilitada la característica, el servicio Copia de seguridad de infraestructura revertirá a la transferencia de datos sin cifrar. Los datos de copia de seguridad que se colocan en la ubicación de almacenamiento externo siempre se cifran en reposo y no dependen del cifrado SMB.

#### <a name="storage-location-sizing"></a>Ajuste del tamaño de la ubicación de almacenamiento

Es recomendable realizar una copia de seguridad por lo menos dos veces al día y guardar como máximo siete días de copias de seguridad. Este es el comportamiento predeterminado cuando se habilitan las copias de seguridad de infraestructura en Azure Stack.


|Escala del entorno  |Tamaño previsto de copia de seguridad  |Cantidad total de espacio necesario  |
|---------|---------|---------|
|4 a 16 nodos     |  20 GB       |  280 GB       |
<!-- TZLASDKFIX 
|ASDK     |   10 GB      |   140 GB     |
-->
### <a name="network-requirements"></a>Requisitos de red

|Ubicación de almacenamiento  |Detalles  |
|---------|---------|
|Recurso compartido de archivos SMB hospedado en un dispositivo de almacenamiento dentro del entorno de red de confianza.     |  El puerto 445 es necesario si la instancia de Azure Stack reside en un entorno protegido por firewall. Infrastructure Backup Controller iniciará una conexión con el servidor de archivos SMB en el puerto 445.       |
| Para usar el FQDN del servidor de archivos, el nombre debe poder resolverse desde el PEP.     |         |

> [!NOTE]
> Ningún puerto de entrada debe estar abierto.

### <a name="encryption-requirements"></a>Requisitos de cifrado

El servicio Copia de seguridad de infraestructura usa un certificado con una clave pública (.CER) para cifrar los datos de copia de seguridad. El certificado se usa para el traslado de claves y no para establecer una comunicación autenticada segura. Por este motivo, el certificado puede ser un certificado autofirmado. Azure Stack no requiere comprobar la raíz o confianza de este certificado, por lo que el acceso externo a Internet no es necesario.

El certificado autofirmado viene en dos partes, una con la clave pública y otra con la clave privada:

- Cifrar los datos de copia de seguridad: el certificado con la clave pública (exportada a un archivo .CER) se usa para cifrar los datos de copia de seguridad.
- Descifrar los datos de copia de seguridad: el certificado con la clave privada (exportada a un archivo .PFX) se usa para descifrar los datos de copia de seguridad.

El certificado con la clave pública (.CER) no lo administra la rotación interna de secretos. Para rotar el certificado, deberá crear un nuevo certificado autofirmado y actualizar la configuración de copia de seguridad con el nuevo archivo (.CER).

Todas las copias de seguridad existentes permanecen cifradas mediante la clave pública anterior. Las nuevas copias de seguridad usarán la nueva clave pública.

Por motivos de seguridad, el certificado usado durante la recuperación en la nube con la clave privada (.PFX) no se conserva en Azure Stack.

## <a name="infrastructure-backup-limits"></a>Límites de Infrastructure Backup

Tenga en cuenta estos límites a medida que planee, implemente y maneje las instancias de Microsoft Azure Stack. En la siguiente tabla se describen estos límites.

|Identificador de límites  |Límite  |Comentarios  |
|---------|---------|---------|
|Tipo de copia de seguridad     | Solo completa        | Infrastructure Backup Controller solo admite copias de seguridad completas. Las copias de seguridad incrementales no se admiten.        |
|Copias de seguridad programadas     | Programadas y manuales        | Backup Controller es compatible con las copias de seguridad programadas y a petición.        |
|Número máximo de trabajos de copia de seguridad simultáneos      | 1        | Se admite solo un trabajo de copia de seguridad activo por instancia de Backup Controller.        |
|Configuración del conmutador de red     | Fuera del ámbito         | El administrador debe hacer una copia de seguridad de la configuración del conmutador de red con herramientas de OEM. Consulte la documentación para Azure Stack proporcionada por cada proveedor de OEM.        |
|Host de ciclo de vida de hardware     | Fuera del ámbito         | El administrador debe hacer una copia del host de ciclo de vida de hardware mediante herramientas de OEM. Consulte la documentación para Azure Stack proporcionada por cada proveedor de OEM.        |
|Número máximo de recursos compartidos de archivos     | 1        | Solo se puede usar un recurso compartido de archivo para almacenar los datos de copia de seguridad.        |
|Copia de seguridad de proveedores de recursos de valor agregado     | En el ámbito        | Copia de seguridad de infraestructura incluye la copia de seguridad de los proveedores de recursos de Event Hubs, IoT Hub y Data Box Edge.        |

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre el [servicio Copia de seguridad de infraestructura](../../operator/azure-stack-backup-reference.md).
