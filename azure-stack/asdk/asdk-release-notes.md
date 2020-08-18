---
title: Notas de la versión del Kit de desarrollo de Azure Stack
description: Mejoras, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 08/10/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/10/2020
ms.openlocfilehash: 6bbbc76acd38984924c454c26204f1edb0d68142
ms.sourcegitcommit: 52b33ea180c38a5ecce150f5a9ea4a026344cc3d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/11/2020
ms.locfileid: "88074135"
---
# <a name="asdk-release-notes"></a>Notas de la versión del Kit de desarrollo de Azure Stack

En este artículo se proporciona información sobre los cambios, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack (ASDK). Si no está seguro de qué versión ejecuta, [use el portal para comprobarlo](../operator/azure-stack-updates.md).

Para estar al día de las novedades del ASDK, suscríbase a la [fuente RSS](https://docs.microsoft.com/api/search/rss?search=ASDK+release+notes&locale=en-us#) de ![RSS](./media/asdk-release-notes/feed-icon-14x14.png).

::: moniker range="azs-2005"
## <a name="build-12005040"></a>Compilación 1.2005.0.40

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de los problemas corregidos, los cambios y las nuevas características de esta versión, consulte las secciones correspondientes en las [notas de la versión de Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- La contraseña de certificación del descifrado es una nueva opción para especificar la contraseña para el certificado autofirmado (.pfx) que contiene la clave privada necesaria para descifrar los datos de copia de seguridad. Esta contraseña solo es necesaria si la copia de seguridad se cifra mediante un certificado.
- Se ha solucionado un problema que provocaba un error en la recuperación de la nube si la contraseña del certificado externo original cambiaba en el sistema de origen de varios nodos. 
- Para obtener una lista de problemas conocidos de Azure Stack en esta versión, consulte el artículo [Problemas conocidos](../operator/known-issues.md).
- Tenga en cuenta que las revisiones disponibles de Azure Stack no son aplicables al ASDK.

#### <a name="initial-configuration-fails-in-asdk"></a>Error en la configuración inicial en ASDK

- Al implementar ASDK, puede que reciba los mensajes de error **El estado de "Deployment-Phase0-DeployBareMetal" es "Error"** y **El estado de "Deployment-InitialSteps" is "Error"** .

- Solución alternativa:

1. Abra el archivo que se encuentra en C:\CloudDeployment\Roles\PhysicalMachines\Tests\BareMetal.Tests.ps1 en cualquier editor con un contador de líneas, como PowerShell ISE.

2. Sustituya la línea 822 por:

   ```powershell

   PartNumber = if($_.PartNumber) {$_.PartNumber.Trim()} else {""};

   ```  
::: moniker-end

::: moniker range="azs-2002"
## <a name="build-12002035"></a>Compilación 1.2002.0.35

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de los problemas corregidos, los cambios y las nuevas características de esta versión, consulte las secciones correspondientes en las [notas de la versión de Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- La contraseña de certificación del descifrado es una nueva opción para especificar la contraseña para el certificado autofirmado (.pfx) que contiene la clave privada necesaria para descifrar los datos de copia de seguridad. Esta contraseña solo es necesaria si la copia de seguridad se cifra mediante un certificado.

- Para obtener una lista de problemas conocidos de Azure Stack en esta versión, consulte el artículo [Problemas conocidos](../operator/known-issues.md).

- Tenga en cuenta que las revisiones disponibles de Azure Stack no son aplicables al ASDK.

#### <a name="sql-vm-provision-fails-in-asdk"></a>Error de aprovisionamiento de máquina virtual SQL en ASDK

- Aplicable a: Este problema se aplica a ASDK 2002.
- Causa: al crear una nueva máquina virtual de SQL en ASDK 2002, puede recibir un mensaje de error **No se encontró la extensión con el editor "Microsoft.SqlServer.Management ", el tipo "SqlIaaSAgent" y la versión del controlador de tipo "2.0" en el repositorio de extensiones**. No hay ningún **SqlIaaSAgent** 2.0 en Azure Stack Hub.
::: moniker-end

::: moniker range="azs-1910"

## <a name="build-11910058"></a>Compilación 1.1910.0.58

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de los problemas corregidos, los cambios y las nuevas características de esta versión, consulte las secciones correspondientes en las [notas de la versión de Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Se ha corregido un problema con la recopilación de registros y su almacenamiento en un contenedor de blobs de Azure Storage. La sintaxis de esta operación es como sigue:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- Se corrigió un problema de implementación por el que un servicio de administración de trabajos de carga lenta impedía la eliminación de algunas características de Windows y requería un reinicio.
- Para obtener una lista de problemas conocidos de Azure Stack en esta versión, consulte el artículo [Problemas conocidos](../operator/known-issues.md).
- Tenga en cuenta que las revisiones disponibles de Azure Stack no son aplicables al ASDK.
::: moniker-end
