---
title: Descripción del motor de AKS en Azure Stack | Microsoft Docs
description: Aprenda a usar la herramienta de línea de comandos del motor de AKS para implementar y administrar un clúster de Kubernetes en Azure y Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 22779072b2dfed018a2ff6d5eac5bf2c294ccd31
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595126"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Descripción del motor de AKS en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar la herramienta de línea de comandos del motor de AKS para implementar y administrar un clúster de Kubernetes en Azure y Azure Stack. Use el motor de AKS para crear, actualizar y escalar clústeres nativos de Azure Resource Manager. Puede usar el motor para implementar un clúster en entornos conectados y desconectados. En este artículo se proporciona información general sobre el motor de AKS, escenarios admitidos para usar el motor con Azure Stack y una introducción a las operaciones como la implementación, la actualización y la escala.

> [!IMPORTANT]
> El motor de AKS actualmente está en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-the-aks-engine"></a>Introducción al motor de AKS

El [motor de AKS](https://github.com/Azure/aks-engine) proporciona una herramienta de línea de comandos para arrancar clústeres de Kubernetes en Azure y Azure Stack. Con Azure Resource Manager, el motor de AKS le ayuda a crear y mantener clústeres que se ejecutan en máquinas virtuales, redes virtuales y otros recursos de infraestructura como servicio (IaaS) en Azure Stack.

## <a name="aks-engine-on-azure-stack-considerations"></a>Consideraciones del motor de AKS en Azure Stack

Antes de usar el motor de AKS en Azure Stack, es importante que sepa que existen diferencias entre Azure Stack y Azure. En esta sección se identifican las diferentes características y consideraciones clave al usar Azure Stack con el motor de AKS para administrar el clúster de Kubernetes.

Para más información sobre los detalles del motor de AKS en Azure Stack y sus diferencias con respecto a Azure, consulte [El motor de AKS en Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Escenarios admitidos con el motor de AKS

El equipo de soporte técnico de Azure Stack admite los siguientes escenarios:

1.  El motor de AKS implementa todos los artefactos del clúster según las instrucciones de esta documentación y mediante la [plantilla siguiente](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  El motor de AKS implementa el clúster en una red virtual existente. Para más información, consulte [Uso de una red virtual personalizada con el motor de AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  [Actualice](azure-stack-kubernetes-aks-engine-upgrade.md) y [escale](azure-stack-kubernetes-aks-engine-scale.md) operaciones.

Para más información sobre el motor de AKS y Azure Stack, consulte [Directivas de compatibilidad para el motor de AKS en Azure Stack](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Instalación del motor de AKS e implementación de un clúster de Kubernetes

Para implementar un clúster de Kubernetes con el motor de AKS en Azure Stack:

1. [Configuración de los requisitos previos para el motor de AKS](azure-stack-kubernetes-aks-engine-set-up.md)
2. Instale el motor de AKS en una máquina con acceso al entorno de Azure Stack.
     - [Instalación del motor de AKS en Windows para Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Instalación del motor de AKS en Linux para Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Implementación de un clúster de Kubernetes con AKS-Engine en Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de los requisitos previos para el motor de AKS](azure-stack-kubernetes-aks-engine-set-up.md)