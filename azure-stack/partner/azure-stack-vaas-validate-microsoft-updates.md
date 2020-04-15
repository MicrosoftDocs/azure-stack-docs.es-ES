---
title: Validación de las actualizaciones de software de Microsoft
titleSuffix: Azure Stack Hub
description: Aprenda a validar las actualizaciones de software de Microsoft con la validación como servicio de Azure Stack Hub.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7eade045461052b9d978910fdbcf06bd895e5b09
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "77704530"
---
# <a name="validate-software-updates-from-microsoft"></a>Validación de las actualizaciones de software de Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft lanzará periódicamente actualizaciones del software de Azure Stack Hub. Estas actualizaciones se proporcionan para los asociados de ingeniería conjunta de Azure Stack Hub. Las actualizaciones se proporcionan antes de que estén públicamente disponibles. Puede comprobar las actualizaciones en función de su solución y proporcionar comentarios a Microsoft.

Las actualizaciones de software de Microsoft en Azure Stack Hub se designan mediante una convención de nomenclatura. Por ejemplo, el nombre 1803 indica que la actualización es de marzo de 2018. Para más información acerca de la directiva de mantenimiento de Azure Stack Hub y las notas de la versión, consulte [Directiva de mantenimiento de Azure Stack Hub](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Prerrequisitos

Antes de ejecutar el proceso de actualización mensual en la validación como servicio, debe estar familiarizado con los siguientes elementos:

- [Conceptos clave de la validación como servicio](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Pruebas necesarias

Para la validación mensual del software se deben ejecutar las siguientes pruebas en el orden siguiente:

- Flujo de trabajo de validación de OEM

## <a name="validating-software-updates"></a>Validación de actualizaciones de software

1. Cree un nuevo flujo de trabajo **Package Validation** (Validación del paquete).
1. Para las pruebas necesarias anteriores, siga las instrucciones de [Ejecución de pruebas de validación de paquetes](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Consulte la sección siguiente para obtener instrucciones adicionales sobre la prueba de **comprobación mensual de actualizaciones de Azure Stack Hub**.

Si tiene alguna pregunta o problema, póngase en contacto con el [servicio de ayuda de VaaS](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md)