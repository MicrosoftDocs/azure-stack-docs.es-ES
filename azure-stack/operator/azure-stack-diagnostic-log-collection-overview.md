---
title: Información general sobre la recopilación de registros de diagnóstico de Azure Stack | Microsoft Docs
description: Explica la recopilación de registros de diagnóstico en Ayuda y soporte técnico de Azure Stack, incluida la recopilación de registros a petición y automática.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 17f68039382453312114dff8737072d1515a583d
ms.sourcegitcommit: b752f4e6733d9ebe56dbd171a14528dcb9a693fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68522006"
---
# <a name="overview-of-azure-stack-diagnostic-log-collection"></a>Información general sobre la recopilación de registros de diagnóstico de Azure Stack 

*Se aplica a: Sistemas integrados de Azure Stack*

Azure Stack es una gran colección de componentes que funcionan juntos e interactúan entre sí. Todos estos componentes generan sus propios registros únicos. Esto puede dificultar el diagnóstico de problemas, especialmente en el caso de errores procedentes de varios componentes de Azure Stack que interactúan. Para abordar este reto, hemos diseñado una experiencia de recopilación de registros de diagnóstico. 

Antes de la versión 1907, la experiencia de diagnóstico incluía el uso de [Test-AzureStack](azure-stack-diagnostic-test.md) para validar el estado del sistema y el uso del [punto de conexión con privilegios (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) para recopilar los registros para la solución de problemas. 

A partir de la versión 1907, la página **Ayuda y soporte técnico** agrega una experiencia más sencilla mediante la **recopilación de registros de diagnóstico**. 
La **recopilación de registros de diagnóstico** es parte de una inversión continuada para mejorar la experiencia del operador de Azure Stack con el proceso de solución de problemas. Con estas mejoras, los operadores pueden recopilar y compartir rápidamente los registros de diagnóstico con los servicios de soporte al cliente (CSS) de Microsoft. Los registros se pueden almacenar en un contenedor de blobs en Azure, donde el acceso se puede personalizar según sea necesario.    
   
La **recopilación de registros de diagnóstico** puede recopilar registros de diagnóstico de dos maneras diferentes:

- **Recopilación automática**: si está habilitada (recomendado), las alertas de estado específicas desencadenan automáticamente la recopilación de registros y se almacenan en la cuenta de almacenamiento de Azure.
- **Recopilar registros ahora**: se trata de una opción a petición en la que puede optar por recopilar registros de una ventana deslizante de 1 a 4 hora de los últimos siete días.

![Captura de pantalla de opciones de recopilación de registros de diagnóstico](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

La **recopilación de registros de diagnóstico** tiene una interfaz de usuario sencilla y no requiere PowerShell. Los registros se recopilan de forma confiable incluso si los servicios de infraestructura están inactivos.
Si la directiva permite compartir registros de diagnóstico con CSS, la **recopilación de registros de diagnóstico** es el método de recopilación recomendado a partir de la versión 1907. Solo debe usar [el PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) para recopilar registros si la **recopilación de registros de diagnóstico** en Ayuda y soporte técnico no está disponible.

## <a name="automatic-diagnostic-log-collection"></a>Recopilación de registros de diagnóstico automática 

La recopilación de registros de diagnóstico automática carga de forma proactiva los registros de diagnóstico de Azure Stack en un blob de almacenamiento de Azure cuando se generan ciertas alertas críticas, lo que reduce significativamente el tiempo necesario para compartir registros de diagnóstico con CSS.

Para más información sobre cómo configurar la recopilación de registros automática, consulte [Configuración de la recopilación de registros de diagnóstico automática de Azure Stack](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="on-demand-diagnostic-log-collection"></a>Recopilación de registros de diagnóstico a petición

Con la recopilación a petición, los registros de diagnóstico se cargan desde Azure Stack a un blob de almacenamiento en Azure cuando un operador de Azure Stack desencadena manualmente la dicha recopilación.
CSS proporcionará la dirección URL de la firma de acceso compartido (SAS) a un blob de almacenamiento propiedad de CSS. Un operador de Azure Stack puede hacer clic en **Recopilar registros ahora** y escribir la dirección URL de SAS. Después los registros de diagnóstico se cargarán directamente en el blob de CSS sin necesidad de un recurso compartido intermedio. 

Para más información sobre la recopilación de registros a petición, consulte [Recopilación de registros de diagnóstico de Azure Stack ahora](azure-stack-configure-on-demand-diagnostic-log-collection.md).

## <a name="bandwidth-considerations"></a>Consideraciones sobre el ancho de banda

El tamaño medio de la recopilación de registros de diagnóstico varía en función de si se ejecuta a petición o automáticamente. El tamaño medio de la recopilación de registros automática es de aproximadamente 2 GB, mientras que el tamaño de la recopilación de registros a petición depende del número de horas que se van a recopilar. 

En la tabla siguiente se enumeran las consideraciones para entornos con conexiones limitadas o de uso medido con Azure.

| Conexión de red | Impacto |
|--------------------|--------|
| Conexión de alta latencia o ancho de banda bajo | La carga de registros tardará una cantidad de tiempo ampliada en completarse | 
| Conexión compartida | La carga solo puede afectar a otras aplicaciones o usuarios que comparten la conexión de red |
| Conexión de uso medido | Puede haber un cargo adicional de su ISP por el uso adicional de la red |

Para más información, consulte [Procedimientos recomendados para la recopilación de registros automática de Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Otras referencias

[Control de datos de clientes y registro de Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Uso de Firmas de acceso compartido (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Procedimientos recomendados para la recopilación de registros automática de Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
