---
title: Referencia del servicio Infrastructure Backup
description: Material de referencia del servicio Copia de seguridad de infraestructura de Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 7fc20dfa20c0088f81d386d2b371b6a17bf45d5f
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871623"
---
# <a name="infrastructure-backup-service-reference"></a>Referencia del servicio Infrastructure Backup

## <a name="azure-backup-infrastructure"></a>Infraestructura de copia de seguridad de Azure

Azure Stack Hub consta de muchos servicios entre los que figuran el portal (Azure Resource Manager) y la experiencia global de administración de la infraestructura. La experiencia de administración de Azure Stack Hub, similar a la de una aplicación, se centra en reducir la complejidad expuesta al operador de la solución.

El servicio Copia de seguridad de infraestructura está diseñado para internalizar la complejidad de la copia de seguridad y la restauración de datos de los servicios de infraestructura, garantizando que los operadores se puedan concentrar en la administración de la solución y el mantenimiento del Acuerdo de Nivel de Servicio de los usuarios.

Se requiere la exportación de los datos de copia de seguridad a un recurso compartido externo para evitar el almacenamiento de copias de seguridad en el mismo sistema. La necesidad de un recurso compartido externo proporciona al administrador flexibilidad para determinar dónde se almacenarán los datos en función de las directivas existentes sobre continuidad empresarial y recuperación ante desastres de la empresa.

### <a name="infrastructure-backup-service-components"></a>Componentes del servicio Copia de seguridad de infraestructura

El servicio Copia de seguridad de infraestructura incluye los siguientes componentes:

 - **Infrastructure Backup Controller**  
 En cada nube de Azure Stack Hub se genera una instancia del Controlador de copia de seguridad de infraestructura, que además reside en ellas.
 - **Backup Resource Provider**  
 El Proveedor de recursos de copia de seguridad consta de la interfaz de usuario y las API que exponen la funcionalidad básica de copia de seguridad para la infraestructura de Azure Stack Hub.

#### <a name="infrastructure-backup-controller"></a>Infrastructure Backup Controller

El Controlador de copia de seguridad de infraestructura es un servicio de Service Fabric del cual se generan instancias para una nube de Azure Stack Hub. Los recursos de copia de seguridad se crean en un nivel regional y capturan datos de servicio específicos de la región de AD, CA, Azure Resource Manager, CRP, SRP, NRP, KeyVault y RBAC.

### <a name="backup-resource-provider"></a>Backup Resource Provider

El Proveedor de recursos de copia de seguridad presenta una interfaz de usuario en el portal de Azure Stack Hub para la configuración básica y la lista de recursos de copia de seguridad. Los operadores pueden realizar las siguientes acciones en la interfaz de usuario:

 - Habilitar la copia de seguridad por primera vez proporcionando la ubicación de almacenamiento externa, las credenciales y la clave de cifrado.
 - Ver los recursos de copia de seguridad creados y completados, y los recursos de estado en creación.
 - Modificar la ubicación de almacenamiento en la que Backup Controller coloca los datos de copia de seguridad.
 - Modificar las credenciales que utiliza Backup Controller para tener acceso a la ubicación de almacenamiento externa.
 - Modificar la clave de cifrado que utiliza Backup Controller para cifrar las copias de seguridad.


## <a name="backup-controller-requirements"></a>Requisitos de Backup Controller

En esta sección se describen los requisitos importantes del servicio Copia de seguridad de infraestructura. Se recomienda que revise cuidadosamente la siguiente información antes de habilitar la copia de seguridad para su instancia de Azure Stack Hub, y que luego la consulte según sea necesario durante la implementación y el funcionamiento posterior.

Estos requisitos son:

  - **Requisitos de software**: describe las ubicaciones de almacenamiento admitidas y las orientaciones sobre el ajuste de tamaño. 
  - **Requisitos de red**: describe los requisitos de red para las diferentes ubicaciones de almacenamiento.  

### <a name="software-requirements"></a>Requisitos de software

#### <a name="supported-storage-locations"></a>Ubicaciones de almacenamiento admitidas

| Ubicación de almacenamiento                                                                 | Detalles                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Recurso compartido de archivos SMB hospedado en un dispositivo de almacenamiento dentro del entorno de red de confianza. | Recurso compartido SMB en el mismo centro de datos donde está implementado Azure Stack Hub o en otro centro de datos. Varias instancias de Azure Stack Hub pueden usar el mismo recurso compartido de archivos. |
| Recurso compartido de archivos SMB en Azure.                                                          | Actualmente no se admite.                                                                                                                                 |
| Blob Storage en Azure.                                                            | Actualmente no se admite.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versiones de SMB admitidas

| SMB | Versión |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>Cifrado de SMB

::: moniker range=">=azs-1907"

El servicio Copia de seguridad de infraestructura admite la transferencia de datos de copia de seguridad a una ubicación de almacenamiento externa con cifrado SMB habilitado en el lado servidor. Si el servidor no admite el cifrado SMB o no tiene habilitada la característica, el servicio Copia de seguridad de infraestructura revertirá a la transferencia de datos sin cifrar. Los datos de copia de seguridad que se colocan en la ubicación de almacenamiento externo siempre se cifran en reposo y no dependen del cifrado SMB.

::: moniker-end

#### <a name="storage-location-sizing"></a>Ajuste del tamaño de la ubicación de almacenamiento

Es recomendable realizar una copia de seguridad por lo menos dos veces al día y guardar como máximo siete días de copias de seguridad. Este es el comportamiento predeterminado cuando se habilitan las copias de seguridad de infraestructura en Azure Stack Hub.

| Escala del entorno | Tamaño previsto de copia de seguridad | Cantidad total de espacio necesario |
|-------------------|--------------------------|--------------------------------|
| 4 a 16 nodos        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

### <a name="network-requirements"></a>Requisitos de red

| Ubicación de almacenamiento                                                                 | Detalles                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Recurso compartido de archivos SMB hospedado en un dispositivo de almacenamiento dentro del entorno de red de confianza. | El puerto 445 es necesario si la instancia de Azure Stack Hub reside en un entorno protegido por firewall. Infrastructure Backup Controller iniciará una conexión con el servidor de archivos SMB en el puerto 445. |
| Para usar el FQDN del servidor de archivos, el nombre debe poder resolverse desde el PEP.             |                                                                                                                                                                                         |

#### <a name="firewall-rules"></a>Reglas de firewall
Asegúrese de configurar las reglas de firewall para permitir la conectividad entre las máquinas virtuales de ERCS en la ubicación de almacenamiento externa. 

| Source | Destino | Protocolo/puerto |
|------------------|-----------------------|--------------------------------|
| ERCS VM 1        | Ubicación de almacenamiento      | 445/SMB                        |
| ERCS VM 2        | Ubicación de almacenamiento      | 445/SMB                        |
| ERCS VM 3        | Ubicación de almacenamiento      | 445/SMB                        |

> [!Note]  
> Ningún puerto de entrada debe estar abierto.

### <a name="encryption-requirements"></a>Requisitos de cifrado

A partir de la actualización 1901, el servicio Copia de seguridad de infraestructura utilizará un certificado con una clave pública (.CER) para cifrar los datos de copia de seguridad y un certificado con la clave privada (.PFX) para descifrar los datos de copia de seguridad durante la recuperación en la nube.

 - El certificado se usa para el traslado de claves y no para establecer una comunicación autenticada segura. Por este motivo, el certificado puede ser un certificado autofirmado. Azure Stack Hub no requiere comprobar la raíz o confianza de este certificado, por lo que el acceso externo a Internet no es necesario.

El certificado autofirmado viene en dos partes, una con la clave pública y otra con la clave privada:

 - Cifrar los datos de copia de seguridad: el certificado con la clave pública (exportada a un archivo .CER) se usa para cifrar los datos de copia de seguridad.
 - Descifrar los datos de copia de seguridad: el certificado con la clave privada (exportada a un archivo .PFX) se usa para descifrar los datos de copia de seguridad.

El certificado con la clave pública (.CER) no lo administra la rotación interna de secretos. Para girar el certificado, deberá crear un nuevo certificado autofirmado y actualizar la configuración de copia de seguridad con el nuevo archivo (.CER). 
 
 - Todas las copias de seguridad existentes permanecen cifradas mediante la clave pública anterior. Las nuevas copias de seguridad usarán la nueva clave pública.
 
El certificado usado durante la recuperación en la nube con la clave privada (.PFX) no se conserva en Azure Stack Hub por motivos de seguridad. Este archivo debe proporcionarse explícitamente durante la recuperación en la nube.  

**Modo de compatibilidad con versiones anteriores**. A partir de 1901, la compatibilidad con las claves de cifrado está en desuso y se eliminará en las futuras versiones. Si actualiza desde la versión 1811 con la copia de seguridad ya habilitada con una clave de cifrado, Azure Stack Hub continuará usando la clave de cifrado. El modo de compatibilidad con versiones anteriores se admitirá para al menos 3 versiones. Después de ese momento, será necesario un certificado.

 * La actualización de clave de cifrado al certificado es una operación unidireccional.  
 * Todas las copias de seguridad existentes permanecerán cifradas con la clave de cifrado. Las nuevas copias de seguridad usarán el certificado. 

## <a name="infrastructure-backup-limits"></a>Límites de Infrastructure Backup

Tenga en cuenta estos límites a medida que planee, implemente y trabaje con las instancias de Microsoft Azure Stack Hub. En la siguiente tabla se describen estos límites.

### <a name="infrastructure-backup-limits"></a>Límites de Infrastructure Backup

| Identificador de límites                                                 | Límite        | Comentarios                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Tipo de copia de seguridad                                                      | Solo completa    | Infrastructure Backup Controller solo admite copias de seguridad completas. Las copias de seguridad incrementales no se admiten.                                          |
| Copias de seguridad programadas                                                | Programadas y manuales  | Backup Controller es compatible con las copias de seguridad programadas y a petición.                                                                                 |
| Número máximo de trabajos de copia de seguridad simultáneos                                   | 1            | Se admite solo un trabajo de copia de seguridad activo por instancia de Backup Controller.                                                                  |
| Configuración del conmutador de red                                     | Fuera del ámbito | El administrador debe hacer una copia de seguridad de la configuración del conmutador de red con herramientas de OEM. Consulte la documentación para Azure Stack Hub proporcionada por cada proveedor de OEM. |
| Host de ciclo de vida de hardware                                          | Fuera del ámbito | El administrador debe hacer una copia del host de ciclo de vida de hardware mediante herramientas de OEM. Consulte la documentación para Azure Stack Hub proporcionada por cada proveedor de OEM.      |
| Número máximo de recursos compartidos de archivos                                    | 1            | Solo se puede usar un recurso compartido de archivo para almacenar los datos de copia de seguridad.                                                                                        |
| Backup App Services, Function, SQL, mysql, datos de proveedores de recursos | Fuera del ámbito | Consulte las orientaciones publicadas para implementar y administrar los proveedores de recursos de valor añadido creados por Microsoft.                                                  |
| Copia de seguridad de proveedores de recursos de terceros                              | Fuera del ámbito | Consulte las orientaciones publicadas para implementar y administrar los proveedores de recursos de valor agregado creados por otros proveedores.                                          |

## <a name="next-steps"></a>Pasos siguientes

 - Para más información sobre el servicio Copia de seguridad de infraestructura, consulte [Recuperación de datos en Azure Stack Hub con el servicio Copia de seguridad de infraestructura](azure-stack-backup-infrastructure-backup.md).
