---
title: Introducción a Azure Stack Hub Storage
description: Obtenga información sobre los servicios de Azure Stack Hub Storage.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: ee08259248a49eef08ce0012d60be2705cc4f877
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704920"
---
# <a name="introduction-to-azure-stack-hub-storage"></a>Introducción a Azure Stack Hub Storage

## <a name="overview"></a>Información general

Azure Stack Hub Storage es un conjunto de servicios de almacenamiento en la nube coherente con los servicios proporcionados por Azure Storage. Estos servicios incluyen blobs, tablas y colas.

## <a name="azure-stack-hub-storage-services"></a>Servicios de Azure Stack Hub Storage

Azure Stack Hub Storage proporciona los tres servicios siguientes:

- **Blob Storage**

    Blob Storage almacena datos de objetos no estructurados. Un blob puede ser cualquier tipo de texto o datos binarios, como un documento, un archivo multimedia o un instalador de aplicación.

- **Table storage**

    El almacenamiento de tablas almacena conjuntos de datos estructurados. Se trata de un almacén de datos de clave-atributo NoSQL que permite el desarrollo rápido de grandes cantidades de datos y el acceso inmediato a los mismos.

- **Queue Storage**

    El almacenamiento de colas ofrece una solución de mensajería confiable para el procesamiento de flujos de trabajo y para la comunicación entre los componentes de los servicios en la nube.

Una cuenta de Azure Stack Hub Storage es una cuenta segura que proporciona acceso a los servicios de Azure Stack Hub Storage. La cuenta de almacenamiento ofrece el espacio de nombres exclusivo para los recursos de almacenamiento. El siguiente diagrama muestra las relaciones entre los recursos de Azure Stack Hub Storage en una cuenta de almacenamiento:

![Introducción a Azure Stack Hub Storage](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

Blob Storage ofrece una solución rentable y escalable a aquellos usuarios con grandes cantidades de datos de objetos no estructurados para almacenar en la nube. Use esta característica para almacenar contenido como:

- Documentos
- Datos de contenido social, como fotos, vídeos, música y blogs
- Copias de seguridad de archivos, equipos, bases de datos y dispositivos
- Imágenes y texto para aplicaciones web
- Datos de configuración para aplicaciones en la nube
- Datos de gran tamaño, como registros y otros conjuntos de datos grandes

Cada blob se organiza en un contenedor. Los contenedores también ofrecen una forma útil de asignar directivas de seguridad a grupos de objetos. Una cuenta de almacenamiento puede contener cualquier número de contenedores y un contenedor puede contener cualquier número de blobs (hasta el límite de la cuenta de almacenamiento).

Blob Storage ofrece tres tipos de blobs:

- **Blobs en bloques**

    Los blobs en bloques están optimizados para la transmisión y el almacenamiento de objetos de nube. Son una buena opción para almacenar documentos, archivos multimedia, copias de seguridad y otros archivos similares.

- **Blobs en anexos**

    Los blobs en anexos son similares a los blobs en bloques, pero están optimizados para anexar las operaciones. Un blob de anexos puede actualizarse solo al agregar un nuevo bloque al final. Los blob en anexos son una buena opción para escenarios como el registro, donde es necesario escribir solo al final del blob nuevos datos.

- **Blobs en páginas**

    Los blobs en páginas están optimizados para representar discos IaaS y admitir escrituras aleatorias de hasta 1 TB. Un disco IaaS conectado a una máquina virtual de Azure Stack Hub es un disco duro virtual almacenado como blob en páginas.

### <a name="table-storage"></a>Almacenamiento de tablas

A menudo, las aplicaciones modernas demandan almacenes de datos con una flexibilidad y escalabilidad superiores a las que requerían las generaciones anteriores de software. Table Storage ofrece almacenamiento de alta disponibilidad y escalabilidad masiva, de modo que la aplicación puede escalarse automáticamente para ajustarse a la demanda de los usuarios. Es el almacén de clave-atributo NoSQL de Microsoft, con un diseño sin esquema que lo diferencia de las bases de datos relacionales tradicionales. Con un almacén de datos sin esquema, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. Es fácil de usar, por lo que los desarrolladores pueden crear aplicaciones rápidamente.

Este tipo de almacenamiento se basa en un almacén de clave-atributo, lo que significa que cada valor de una tabla se almacena con un nombre de propiedad con tipo. El nombre de propiedad se usa para filtrar y especificar criterios de selección. Una colección de propiedades y sus valores, componen una entidad. Puesto que este tipo de almacenamiento no tiene esquema, dos entidades de una misma tabla pueden contener distintas colecciones de propiedades y dichas propiedades pueden ser de distintos tipos.

Puede usar Table Storage para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos y cualquier otro tipo de metadatos que necesite el servicio. Para las aplicaciones actuales basadas en Internet, las bases de datos NoSQL como Table Storage ofrecen una alternativa popular a las bases de datos relacionales tradicionales.

Una cuenta de almacenamiento puede contener un número cualquiera de tablas y una tabla puede incluir un número cualquiera de entidades, hasta alcanzar el límite de capacidad de este tipo de cuenta.

### <a name="queue-storage"></a>Queue Storage

A la hora de diseñar aplicaciones para escalar, los componentes de las mismas suelen desacoplarse para poder escalarse de forma independiente. Queue Storage ofrece una solución de mensajería de confianza para la comunicación asincrónica entre componentes de las aplicaciones, independientemente de si se ejecutan en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Además, este tipo de almacenamiento admite la administración de tareas asincrónicas y la creación de flujos de trabajo de procesos.

Una cuenta de almacenamiento puede contener cualquier número de colas y una cola puede contener cualquier número de mensajes (hasta el límite de capacidad de la cuenta de almacenamiento). Los mensajes individuales pueden tener un tamaño máximo de 64 KB.

## <a name="next-steps"></a>Pasos siguientes

- [Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md) (Azure Stack Storage: diferencias y consideraciones)

- Para más información sobre Azure Storage, consulte [Introducción a Microsoft Azure Storage](/azure/storage/common/storage-introduction).
