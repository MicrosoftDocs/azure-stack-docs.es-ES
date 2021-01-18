---
title: 'Azure Stack Hub Storage: diferencias y consideraciones'
titleSuffix: Azure Stack Hub
description: Conozca las diferencias entre Azure Stack Hub Storage y Azure Storage, junto con las consideraciones de implementación de Azure Stack Hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviwer: jiahan
ms.lastreviewed: 08/12/2020
ms.openlocfilehash: e49092e87f8e6801343ae0550aff4c71dd40408b
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103076"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Azure Stack Hub Storage: Diferencias y consideraciones

Azure Stack Hub Storage es el conjunto de servicios de almacenamiento en la nube de Microsoft Azure Stack Hub. Azure Stack Hub Storage proporciona blobs, tablas, colas y funcionalidad de administración de cuentas con una semántica coherente con Azure.

En este artículo se resumen las diferencias entre los servicios de Azure Stack Hub Storage y los de Azure Storage. También se muestran los aspectos que se deben tener en cuenta al implementar Azure Stack Hub. Para obtener información sobre las diferencias generales entre Azure global y Azure Stack Hub, consulte el artículo [Consideraciones clave](azure-stack-considerations.md).

## <a name="cheat-sheet-storage-differences"></a>Hoja de referencia rápida: Diferencias de almacenamiento

| Característica | Azure (global) | Azure Stack Hub |
| --- | --- | --- |
|File Storage|Se admiten recursos compartidos de archivos SMB basados en la nube. | Todavía no se admite.
|Cifrado del servicio Azure Storage para datos en reposo|Cifrado AES de 256 bits. Se admite el cifrado mediante claves administradas por el cliente en Key Vault.|Cifrado AES de 128 bits de BitLocker. No se admite el cifrado mediante claves administradas por el cliente.
|Tipo de cuenta de almacenamiento|Cuentas de uso general V1, V2 y de Blob Storage. |Solo de uso general V1.
|Opciones de replicación|Almacenamiento con redundancia local, almacenamiento con redundancia geográfica, almacenamiento con redundancia geográfica con acceso de lectura y almacenamiento con redundancia de zona. |Almacenamiento con redundancia local.
|Premium Storage|Proporcione almacenamiento de alto rendimiento y latencia baja. Solo admite blobs en páginas de cuentas de almacenamiento prémium.|Se pueden aprovisionar, pero no hay límite de rendimiento o garantía. No se realizaría ningún bloqueo con las opciones de blobs en bloques, blobs en anexos, tablas y colas en las cuentas de almacenamiento Premium.
|Discos administrados|Se admiten Premium y Estándar. |Se admite cuando se usa la versión 1808 o posterior.
|Nombre de blob|1 024 caracteres (2 048 bytes). |880 caracteres (1 760 bytes).
|Tamaño máximo de blob en bloque|4,75 TB (100 MB x 50 000 bloques). |4,75 TB (100 MB x 50 000 bloques) para la actualización 1802 o versiones más recientes. 50 000 x 4 MB (aproximadamente 195 GB) para las versiones anteriores.
|Copia de instantánea de blob en páginas|Copia de seguridad de discos de máquina virtual no administrados conectados a una máquina virtual en ejecución compatible. |Todavía no se admite.
|Copia de instantáneas incrementales del blob de página|Se admiten Blobs en páginas de Azure Estándar y Premium. |Todavía no se admite.
|Facturación de blobs en páginas|Los cargos se generan por páginas únicas, independientemente de si están en el blob o en la instantánea. No se incurrirá en cargos adicionales por las instantáneas asociadas a un blob hasta que el blob base se actualice.|Los cargos se generan para el blob base y las instantáneas asociadas. Se podrían generar cargos adicionales por cada instantánea individual.
|Niveles de almacenamiento para Blob Storage|Niveles de almacenamiento de acceso frecuente, de acceso esporádico y de acceso de archivo.|Todavía no se admite.
|Eliminación temporal para Blob Storage|Disponible en general. |Todavía no se admite.
|Tamaño máximo de blob en página|8 TB. |1 TB. 
|Tamaño de página de blob en página|512 bytes. |4 KB. 
|Clave de partición de tabla y tamaño de clave de fila|1 024 caracteres (2 048 bytes).|400 caracteres (800 bytes).
|Instantánea de blob|No se limita el número máximo de instantáneas de un blob.|El número máximo de instantáneas de un blob es 1 000.
|Autenticación de Azure AD para almacenamiento|En versión preliminar. |Todavía no se admite.
|Blobs inalterables|Disponible en general. |Se admite cuando se usa la versión 2008 o posterior.
|Firewall y reglas de red virtual para almacenamiento|Disponible en general. |Todavía no se admite.|

También hay diferencias en las métricas de almacenamiento:

* Los datos de transacción de las métricas de almacenamiento no distinguen entre el ancho de banda de red interna o externa.
* Los datos de transacción de las métricas de almacenamiento no incluyen el acceso de la máquina virtual a los discos montados.

## <a name="api-version"></a>Versión de API

Las siguientes versiones son compatibles con Azure Stack Hub Storage:

API de los servicios de Azure Storage:

::: moniker range=">=azs-2008"

Actualización 2008 o versiones más recientes:
- [2019-07-07](/rest/api/storageservices/version-2019-07-07)
- [2019-02-02](/rest/api/storageservices/version-2019-02-02)
- [2018-11-09](/rest/api/storageservices/version-2018-11-09)
- [2018-03-28](/rest/api/storageservices/version-2018-03-28)
- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

::: moniker-end

Actualización 2005:

- [2019-02-02](/rest/api/storageservices/version-2019-02-02)
- [2018-11-09](/rest/api/storageservices/version-2018-11-09)
- [2018-03-28](/rest/api/storageservices/version-2018-03-28)
- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

Versiones anteriores:

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

API de administración de los servicios de Azure Storage:

::: moniker range=">=azs-2008"

Actualización 2008 o versiones más recientes:
- [2018-02-01](/rest/api/storagerp/)
- [2017-10-01](/rest/api/storagerp/)
- [2017-06-01](/rest/api/storagerp/)
- [2016-12-01](/rest/api/storagerp/)
- [2016-05-01](/rest/api/storagerp/)
- [2016-01-01](/rest/api/storagerp/)
- [2015-06-15](/rest/api/storagerp/)
- [2015-05-01-preview](/rest/api/storagerp/)

::: moniker-end

De la actualización 1811 a la actualización 2005:

- [2017-10-01](/rest/api/storagerp/)
- [2017-06-01](/rest/api/storagerp/)
- [2016-12-01](/rest/api/storagerp/)
- [2016-05-01](/rest/api/storagerp/)
- [2016-01-01](/rest/api/storagerp/)
- [2015-06-15](/rest/api/storagerp/)
- [2015-05-01-preview](/rest/api/storagerp/)

Versiones anteriores:

- [2016-01-01](/rest/api/storagerp/)
- [2015-06-15](/rest/api/storagerp/)
- [2015-05-01-preview](/rest/api/storagerp/)

## <a name="powershell-version"></a>Versión de PowerShell

En lo que respecta a la instancia de PowerShell del módulo de almacenamiento, debe conocer cuál es la versión compatible con la API REST.

| Module | Versión admitida | Uso |
|---|---|---|
| Azure.Storage | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | Administra blobs, colas y tablas en las cuentas de almacenamiento de Azure Stack Hub. |
| Az.Storage | [5.0.4](https://www.powershellgallery.com/packages/Az.Storage/5.0.4) | Crea y administra cuentas de almacenamiento en Azure Stack Hub. |

Para más información sobre las bibliotecas de cliente de almacenamiento de Azure Stack Hub admitidas, consulte: [Introducción a las herramientas de desarrollo de Azure Stack Hub Storage](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a las herramientas de desarrollo de Azure Stack Hub Storage](azure-stack-storage-dev.md)
* [Uso de herramientas de transferencia de datos de Azure Stack Hub Storage](azure-stack-storage-transfer.md)
* [Introducción a Azure Stack Hub Storage](azure-stack-storage-overview.md)
