---
title: Validación de las actualizaciones de software de Microsoft en la validación como servicio de Azure Stack | Microsoft Docs
description: Aprenda a validar las actualizaciones de software de Microsoft con la validación como servicio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67d17ffc7c2b8fcdb766ccf8a8248da8665a42d8
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "63447613"
---
# <a name="validate-software-updates-from-microsoft"></a>Validación de las actualizaciones de software de Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft lanzará periódicamente actualizaciones del software de Azure Stack. Estas actualizaciones se proporcionan para los asociados de ingeniería conjunta de Azure Stack. Las actualizaciones se proporcionan antes de que estén públicamente disponibles. Puede comprobar las actualizaciones en función de su solución y proporcionar comentarios a Microsoft.

Las actualizaciones de software de Microsoft en Azure Stack se designan utilizando una convención de nomenclatura, por ejemplo, 1803 indica la actualización de marzo de 2018. Para obtener información acerca de la directiva de actualización de Azure Stack, la cadencia y las notas de la versión disponibles, consulte [Directiva de mantenimiento de Azure Stack](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar el proceso de actualización mensual en VaaS, debe estar familiarizado con los siguientes elementos:

- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Conceptos clave de la validación como servicio)
- [Pruebas de comprobación de características interactivas](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Pruebas necesarias

Las siguientes pruebas para la validación mensual de software se deben ejecutar en el orden siguiente:

1. Monthly Azure Stack Update Verification
2. Cloud Simulation Engine

## <a name="validating-software-updates"></a>Validación de actualizaciones de software

1. Cree un nuevo flujo de trabajo **Package Validation** (Validación del paquete).
1. Para las pruebas necesarias anteriores, siga las instrucciones de [Ejecución de pruebas de validación de paquetes](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Consulte la sección siguiente para obtener instrucciones adicionales sobre la prueba de **verificación de la actualización mensual de Azure Stack**.

### <a name="apply-the-monthly-update"></a>Aplicación de la actualización mensual

1. Seleccione un agente con el que ejecutar las pruebas.
1. Programe la **verificación mensual de las actualizaciones de Azure Stack**.
1. Proporcione la ubicación para el paquete de extensión de OEM implementado actualmente en la marca y la ubicación del paquete de extensión de OEM que se aplicará durante la actualización. Para configurar las direcciones URL de estos paquetes, consulte la [administración de paquetes para la validación](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Siga los pasos descritos en la interfaz de usuario desde el agente seleccionado.

Si tiene alguna pregunta o problema, póngase en contacto con el [servicio de ayuda de VaaS](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md)