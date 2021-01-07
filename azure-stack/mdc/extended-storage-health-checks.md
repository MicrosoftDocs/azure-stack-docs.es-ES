---
title: 'Azure Stack: comprobaciones de estado en el almacenamiento extendido para el almacenamiento de blobs del Centro de datos modular'
description: En este artículo se proporcionan instrucciones sobre cómo realizar comprobaciones de estado en el almacenamiento extendido para el almacenamiento de blobs del Centro de datos modular.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: patricka
ms.reviewer: karlt
ms.lastreviewed: 10/01/2020
ms.openlocfilehash: ebb39f3eab90b0f976e5fee2896cbe85d0d023f5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872405"
---
# <a name="extended-storage-health-checks"></a>Comprobaciones de estado del almacenamiento extendido

En este artículo se proporcionan instrucciones para comprobar el estado del hardware de almacenamiento extendido del centro de datos y la implementación de Azure Stack Hub.

Antes de empezar, revise y siga los pasos descritos en [Actualización del firmware para el almacenamiento extendido](extended-storage-firmware-updates.md).

El último paso antes de enviar el sistema es cerrarlo. Siga los pasos que se describen en la Guía de inicio rápido.

## <a name="extended-storage-health"></a>Estado del almacenamiento extendido

En esta sección se proporcionan instrucciones sobre la comprobación del estado del hardware de almacenamiento extendido.
Compruebe los eventos y notifique al administrador si hay problemas que se deben resolver. 


Para comprobar el estado del bloque de almacenamiento del clúster, ejecute el siguiente comando:
```console
isi storagepool health
```

Por ejemplo, si es correcto, el resultado del comando es similar al siguiente:
```console
All pools are healthy.
Unprovisioned drives: none
```

Si el estado es incorrecto o faltan unidades, el resultado del comando es similar al siguiente:

```console
SmartPools Health
Name                  Health  Type Prot   Members          Down          Smartfailed
--------------------- ------- ---- ------ ---------------- ------------- -------------
a2000_200tb_800gb-    -M---
ssd-sed_16gb
 a2000_200tb_800gb-   -M---   HDD  3x     3,11,14,19,23,30 Nodes:        Nodes:
ssd-sed_16gb:24                           ,38,41,46:bay6,1 Drives:       Drives:
                                          0-11,16,19,
                                          25:bay6,10-11,16

OK = Ok, U = Too few nodes, M = Missing drives,
D = Some nodes or drives are down, S = Some nodes or drives are smartfailed,
R = Some nodes or drives need repair
Unprovisioned drives: none
```

Para obtener ayuda con la solución de problemas, póngase en contacto con el soporte técnico de Microsoft.

El siguiente comando comprueba el estado general del clúster en una vista simplificada:
```console
isi status
```

Si todo es correcto, aparece una **marca de verificación** verde; de lo contrario, aparecen advertencias amarillas o errores rojos en la línea **Estado del clúster**, o en una o varias de las líneas de identificador de nodo de clúster de la tabla siguiente.

Si se necesita más información sobre el estado del clúster, se puede ejecutar un comando más detallado en una vista expandida:
```console
isi status -a
```

Para obtener ayuda con la solución de problemas, póngase en contacto con el soporte técnico de Microsoft.

## <a name="azure-stack-hub-health"></a>Estado de Azure Stack Hub

En esta sección se proporcionan instrucciones sobre la comprobación del estado de la implementación de Azure Stack Hub.

Para obtener una vista general de la implementación de Azure Stack integrada con el sistema de almacenamiento extendido, ejecute el siguiente script, que es un contenedor para las operaciones siguientes:
- Conexión a una VM de ERCS con las credenciales proporcionadas
- Ejecución del comando Test-AzureStack-Debug (que genera información de estado detallada directamente como una salida en la pantalla)

Los requisitos previos de este script son los siguientes:
- Archivo de script .\Invoke-ExtendedStorageConfiguration.ps1, que se encuentra en la carpeta C:\OEMSoftware\ExtendedStorage\ del host de ciclo de vida de hardware (HLH)
- Variable de credencial $AzScred, que se debe rellenar con las credenciales de *DOMAIN*\cloudadmin. Reemplace *DOMAIN* por el nombre de dominio real, como CONTOSO.


```powershell
$AzScred = Get-Credential -Credential 'DOMAIN\cloudadmin'
.\Invoke-ExtendedStorageConfiguration.ps1 -TestAzureStack -AzureStackCred $AzScred
```

Revise la salida y compruebe el estado de mantenimiento general de la implementación de Azure Stack, así como las secciones específicas del **NAS** de los resultados para obtener el estado de mantenimiento específico de la integración del almacenamiento extendido con Azure Stack.

Para obtener una visión más detallada del diagnóstico de Azure Stack, consulte [Validación del estado del sistema con Azure Stack Hub](../operator/azure-stack-diagnostic-test.md).

## <a name="technical-support"></a>Soporte técnico

Para obtener ayuda con la solución de problemas, póngase en contacto con el soporte técnico de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- [Actualizar el firmware](extended-storage-firmware-updates.md)
