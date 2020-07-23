---
title: Aplicación de la actualización de un fabricante de equipos originales a Azure Stack Hub
description: Aprenda a aplicar la actualización de un fabricante de equipos originales (OEM) a Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppace
ms.openlocfilehash: cbc073150d8da6aa9b7542041b22f176c6147f22
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487539"
---
# <a name="azure-stack-hub-servicing-policy"></a>Directiva de servicio de Azure Stack Hub

En este artículo se describe la directiva de mantenimiento para sistemas integrados de Azure Stack Hub, es decir, lo que debe hacer para mantener el sistema en un estado admitido, y cómo obtener soporte técnico.

## <a name="keep-your-system-under-support"></a>Mantenimiento del sistema dentro del soporte técnico

Para continuar recibiendo soporte técnico, debe mantener actualizadas las actualizaciones de Azure Stack Hub.

Para que el sistema de Azure Stack Hub permanezca en un estado admitido, la instancia debe ejecutar la versión de actualización más reciente o cualquiera de las dos versiones de actualización anteriores.

Las revisiones no se consideran versiones de actualización importantes. Si a su sistema le faltan *más de dos actualizaciones de Azure Stack Hub*, se considera que no cumple los requisitos. Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.

Por ejemplo, si la versión de actualización disponible más reciente es la 1904 y las dos actualizaciones anteriores eran las versiones 1903 y 1902, estas dos últimas versiones siguen optando a recibir soporte técnico. Sin embargo, la versión 1901 queda fuera del soporte técnico. La directiva es válida cuando no hay ninguna versión durante uno o dos meses. Por ejemplo, si la versión actual es la 1807 y no hubo ninguna versión 1806, las dos actualizaciones anteriores, 1805 y 1804, permanecen dentro del soporte técnico.

Las actualizaciones de software de Microsoft no son acumulativas y necesitan la revisión o actualización anteriores como requisito previo. Si decide aplazar una o varias actualizaciones, tenga en cuenta el tiempo de ejecución total si quiere llegar hasta la versión más reciente.

## <a name="get-support"></a>Obtención de soporte técnico

Azure Stack Hub sigue el mismo proceso de soporte técnico que Azure. Los clientes de empresa pueden seguir el proceso descrito en [Creación de una solicitud de soporte técnico de Azure](/azure/azure-supportability/how-to-create-azure-support-request). Si es cliente de un proveedor de soluciones en la nube (CSP), póngase en contacto con el CSP para recibir soporte técnico. Para más información, consulte [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Pasos siguientes

[Administrar las actualizaciones en Azure Stack Hub](azure-stack-updates.md)
