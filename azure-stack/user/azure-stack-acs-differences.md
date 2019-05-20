---
title: 'Almacenamiento de Azure Stack: Diferencias y consideraciones | Microsoft Docs'
description: Comprender las diferencias entre el almacenamiento de Azure Stack Storage y Azure Storage, junto con las consideraciones de implementación de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: e2bfea5cb7bb068a4cf3ca64dc3852b450a37066
ms.sourcegitcommit: 61de365c93eb7435891867f9808ec37486882d7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65097085"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack Storage: Diferencias y consideraciones

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

El almacenamiento de Azure Stack es el conjunto de servicios de almacenamiento en la nube de Microsoft Azure Stack. El almacenamiento de Azure Stack proporciona blobs, tablas, colas y funcionalidad de administración de cuentas con una semántica coherente con Azure.

En este artículo se resumen las diferencias entre Azure Stack Storage y los servicios de Azure Storage. También se muestran los aspectos que se deben tener en cuenta al implementar Azure Stack. Para información sobre las diferencias generales entre Azure global y Azure Stack, consulte el tema [Consideraciones clave](azure-stack-considerations.md).

## <a name="cheat-sheet-storage-differences"></a>Hoja de referencia rápida: Diferencias de almacenamiento

| Característica | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|Recursos compartidos de archivos SMB basado en la nube admitidos|Todavía no se admite
|Cifrado del servicio Azure Storage para datos en reposo|Cifrado AES de 256 bits. Se admite el cifrado mediante claves administradas por el cliente en Key Vault.|Cifrado AES de 128 bits de BitLocker. No se admite el cifrado mediante claves administradas por el cliente.
|Tipo de cuenta de almacenamiento|Cuentas de uso general V1, V2 y de Blob Storage|Solo de uso general V1.
|Opciones de replicación|Almacenamiento con redundancia local, almacenamiento con redundancia geográfica, almacenamiento con redundancia geográfica con acceso de lectura y almacenamiento con redundancia de zona|Almacenamiento con redundancia local.
|Premium Storage|Proporciona almacenamiento de alto rendimiento y baja latencia. Solo admite blobs en páginas de cuentas de almacenamiento prémium.|Se pueden aprovisionar, pero no hay límite de rendimiento o garantía. No se realizaría ningún bloqueo con las opciones de blobs en bloques, anexar blobs, tablas y colas en las cuentas de almacenamiento prémium.
|Discos administrados|Premium y estándar admitidos|Se admite cuando se usa la versión 1808 o posterior.
|Nombre de blob|1 024 caracteres (2 048 bytes)|880 caracteres (1 760 bytes)
|Tamaño máximo de blob en bloque|4,75 TB (100 MB x 50 000 bloques)|4,75 TB (100 MB x 50 000 bloques) para la actualización 1802 o versiones más recientes. 50 000 x 4 MB (aproximadamente 195 GB) para las versiones anteriores.
|Copia de instantánea de blob en páginas|Copia de seguridad de discos de máquina virtual no administrados conectados a una máquina virtual en ejecución compatible|Todavía no se admite.
|Copia de instantáneas incrementales del blob de página|Blobs en páginas de Azure estándar y premium admitidos|Todavía no se admite.
|Facturación de blobs en páginas|Los cargos se generan por páginas únicas, independientemente de si están en el blob o en la instantánea. No se incurrirá en cargos adicionales por las instantáneas asociadas a un blob hasta que el blob base se actualice.|Los cargos se generan para el blob base y las instantáneas asociadas. Se podrían generar cargos adicionales por cada instantánea individual.
|Niveles de almacenamiento para Blob Storage|Niveles de almacenamiento de acceso frecuente, de acceso esporádico y de acceso de archivo.|Todavía no se admite.
|Eliminación temporal para Blob Storage|Disponibilidad general|Todavía no se admite.
|Tamaño máximo de blob en página|8 TB|1 TB
|Tamaño de página de blob en página|512 bytes|4 KB
|Clave de partición de tabla y tamaño de clave de fila|1 024 caracteres (2 048 bytes)|400 caracteres (800 bytes)
|Instantánea de blob|No se limita el número máximo de instantáneas de un blob.|El número máximo de instantáneas de un blob es 1 000.
|Autenticación de Azure AD para almacenamiento|En versión preliminar|Todavía no se admite.
|Blobs inalterables|Disponibilidad general|Todavía no se admite.
|Firewall y reglas de red virtual para almacenamiento|Disponibilidad general|Todavía no se admite.|

También hay diferencias en las métricas de almacenamiento:

* Los datos de transacción de las métricas de almacenamiento no distinguirán el ancho de banda de red interna o externa.
* Los datos de transacción de las métricas de almacenamiento no incluyen el acceso de la máquina virtual a los discos montados.

## <a name="api-version"></a>Versión de API

Solo las siguientes versiones son compatibles con Azure Stack Storage:

API de los servicios de Azure Storage:

Actualización 1811 o versiones más recientes:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Versiones anteriores:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

API de administración de los servicios de Azure Storage:

Actualización 1811 o versiones más recientes:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Versiones anteriores:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Para más información sobre bibliotecas de cliente de almacenamiento de Azure Stack admitidas, consulte: [Introducción a las herramientas de desarrollo de almacenamiento de Azure Stack](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Pasos siguientes

* [Empezar a trabajar con herramientas de desarrollo de Azure Stack Storage](azure-stack-storage-dev.md)
* [Uso de herramientas de transferencia de datos en almacenamiento de Azure Stack](azure-stack-storage-transfer.md)
* [Introducción a Azure Stack Storage](azure-stack-storage-overview.md)
