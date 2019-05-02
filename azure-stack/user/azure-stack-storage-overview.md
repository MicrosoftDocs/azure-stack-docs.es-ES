---
title: Introducción a Azure Stack Storage
description: Más información acerca de Azure Stack Storage
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 10cb9a2b1449c8cb280bf4787b009b6d2eb21619
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310755"
---
# <a name="introduction-to-azure-stack-storage"></a>Introducción a Azure Stack Storage

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="overview"></a>Información general

Azure Stack Storage es un conjunto de servicios de almacenamiento en la nube que incluye blobs, tablas y colas que son coherentes con los servicios de Azure Storage.

## <a name="azure-stack-storage-services"></a>Servicios de Azure Stack Storage

Azure Stack Storage proporciona los tres servicios siguientes:

- **Blob Storage**

    Blob Storage almacena datos de objetos no estructurados. Un blob puede ser un tipo cualquiera de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación.

- **Table Storage**

    El almacenamiento de tablas almacena conjuntos de datos estructurados. Se trata de un almacén de datos de clave-atributo NoSQL, que permite el desarrollo rápido de grandes cantidades de datos y el acceso inmediato a los mismos.

- **Queue Storage**

    El almacenamiento de colas ofrece una solución de mensajería confiable para el procesamiento de flujos de trabajo y para la comunicación entre los componentes de los servicios en la nube.

Una cuenta de Azure Stack Storage es una cuenta segura que proporciona acceso a los servicios de Azure Stack Storage. La cuenta de almacenamiento ofrece el espacio de nombres exclusivo para los recursos de almacenamiento. El siguiente diagrama muestra las relaciones entre los recursos de Azure Stack Storage en una cuenta de almacenamiento:

![Introducción a Azure Stack Storage](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Almacenamiento de blobs

Blob Storage ofrece una solución rentable y escalable a aquellos usuarios con grandes cantidades de datos de objetos no estructurados para almacenar en la nube. Esta característica se puede usar para almacenar contenido como:

- Documentos
- Datos de contenido social, como fotos, vídeos, música y blogs
- Copias de seguridad de archivos, equipos, bases de datos y dispositivos
- Imágenes y texto para las aplicaciones web
- Datos de configuración para las aplicaciones en la nube
- Datos de gran tamaño, como registros y otros conjuntos de datos grandes

Cada blob se organiza en un contenedor. Los contenedores también ofrecen una forma útil de asignar directivas de seguridad a grupos de objetos. Una cuenta de almacenamiento puede incluir un número cualquiera de contenedores y, a su vez, un contenedor puede incluir un número cualquiera de blobs, hasta alcanzar el límite de capacidad de la cuenta de almacenamiento.

Blob Storage ofrece tres tipos de blobs:

- **Blobs en bloques**

    Los blobs en bloques están optimizados para el streaming y para el almacenamiento de objetos en la nube y constituyen una opción idónea para almacenar documentos, archivos multimedia y copias de seguridad, entre otros.

- **Blobs en anexos**

    Los blobs en anexos son similares a los blobs en bloques, pero están optimizados para anexar las operaciones. Un blob de anexos puede actualizarse solo al agregar un nuevo bloque al final. Los blob en anexos son una buena opción para escenarios como el registro, donde es necesario escribir solo al final del blob nuevos datos.

- **Blobs en páginas**

    Los blobs en páginas están optimizados para representar discos de IaaS y admitir la escritura aleatoria. Pueden tener un tamaño máximo de 1 TB. Un disco IaaS asociado a una máquina virtual de Azure Stack es un VHD almacenado como blob en páginas.

### <a name="table-storage"></a>Almacenamiento de tablas

A menudo, las aplicaciones modernas demandan almacenes de datos con una flexibilidad y escalabilidad superiores a las que requerían las generaciones anteriores de software. Table Storage ofrece un tipo de almacenamiento de alta disponibilidad y escalabilidad masiva, de forma que las aplicaciones pueden escalarse automáticamente para ajustarse a la demanda de los usuarios. Este tipo de almacenamiento se basa en un almacén de claves/atributos NoSQL de Microsoft con un diseño sin esquema que lo diferencia de las bases de datos relacionales tradicionales. Si se tiene un almacén de datos sin esquema, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. Table Storage es fácil de usar, por lo que los desarrolladores pueden crear aplicaciones de forma rápida.

Este tipo de almacenamiento se basa en un almacén de clave-atributo, lo que significa que cada valor de una tabla se almacena con un nombre de propiedad con tipo. El nombre de propiedad se puede usar para filtrar y especificar criterios de selección. Una colección de propiedades y sus valores, componen una entidad. Puesto que este tipo de almacenamiento no tiene esquema, dos entidades de una misma tabla pueden contener distintas colecciones de propiedades y dichas propiedades pueden ser de distintos tipos.

Table Storage se puede usar para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos y cualquier otro tipo de metadatos requerido por el servicio. Para las aplicaciones actuales basadas en Internet, las bases de datos NoSQL, como las de Table Storage, ofrecen una alternativa popular a las bases de datos relacionales tradicionales.

Una cuenta de almacenamiento puede contener un número cualquiera de tablas y una tabla puede incluir un número cualquiera de entidades, hasta alcanzar el límite de capacidad de este tipo de cuenta.

### <a name="queue-storage"></a>Queue Storage

A la hora de diseñar aplicaciones para escala, los componentes de las mismas suelen desacoplarse para poder escalarlos de forma independiente. Queue Storage ofrece una solución de mensajería de confianza para la comunicación asincrónica entre los componentes de las aplicaciones, independientemente de que se ejecuten en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Además, este tipo de almacenamiento admite la administración de tareas asincrónicas y la creación de flujos de trabajo de procesos.

Una cuenta de almacenamiento puede contener un número cualquiera de colas y una cola puede incluir un número cualquiera de mensajes, hasta alcanzar el límite de capacidad de este tipo de cuenta. Los mensajes individuales pueden tener un tamaño máximo de 64 KB.

## <a name="next-steps"></a>Pasos siguientes

- [Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md) (Azure Stack Storage: diferencias y consideraciones)

- Para más información sobre Azure Storage, consulte [Introducción a Microsoft Azure Storage](/azure/storage/common/storage-introduction).
