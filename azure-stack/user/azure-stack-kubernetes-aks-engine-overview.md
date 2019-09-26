---
title: Descripción del motor de AKS en Azure Stack | Microsoft Docs
description: Aprenda a usar la herramienta de línea de comandos AKS-Engine para implementar y administrar un clúster de Kubernetes en Azure y Azure Stack.
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
ms.openlocfilehash: 0b1c28369fb1865778c68040894d100af9ea7fd6
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019381"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Descripción del motor de AKS en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar la herramienta de línea de comandos de AKS-Engine para implementar y administrar un clúster de Kubernetes en Azure y Azure Stack. Use AKS-Engine para crear, actualizar y escalar clústeres nativos de Azure Resource Manager. Puede usar el motor para implementar un clúster en entornos conectados y desconectados. En este artículo se proporciona información general sobre AKS-Engine, escenarios admitidos para usar el motor con Azure Stack y una introducción a las operaciones como la implementación, la actualización y la escala.

> [!IMPORTANT]
> AKS-Engine actualmente está en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-the-aks-engine"></a>Introducción a AKS-Engine

[AKS-Engine](https://github.com/Azure/aks-engine) proporciona una herramienta de línea de comandos para arrancar clústeres de Kubernetes en Azure y Azure Stack. Con Azure Resource Manager, AKS-Engine le ayuda a crear y mantener clústeres que se ejecutan en máquinas virtuales, redes virtuales y otros recursos de infraestructura como servicio (IaaS) en Azure Stack.

## <a name="aks-engine-on-azure-stack-considerations"></a>Consideraciones de AKS-Engine en Azure Stack

Antes de usar AKS-Engine en Azure Stack, es importante que sepa que existen diferencias entre Azure Stack y Azure. En esta sección se identifican las diferentes características y consideraciones clave al usar Azure Stack con AKS-Engine para administrar el clúster de Kubernetes.

Para más información sobre los detalles de AKS-Engine en Azure Stack y sus diferencias con respecto a Azure, consulte [AKS-Engine en Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Escenarios admitidos con AKS-Engine

El equipo de soporte técnico de Azure Stack admite los siguientes escenarios:

1.  AKS-Engine implementa todos los artefactos del clúster según las instrucciones de esta documentación y mediante la [siguiente plantilla](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  AKS-Engine implementa el clúster en una red virtual existente. Para más información, consulte [Uso de una red virtual personalizada con AKS-Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  [Actualice](azure-stack-kubernetes-aks-engine-upgrade.md) y [escale](azure-stack-kubernetes-aks-engine-scale.md) operaciones.

Para más información sobre AKS-Engine y Azure Stack, consulte [Directivas de compatibilidad para AKS-Engine en Azure Stack](azure-stack-kubernetes-ask-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Instalación de AKS-Engine e implementación de un clúster de Kubernetes

Para implementar un clúster de Kubernetes con AKS-Engine en Azure Stack:

1. [Configuración de los requisitos previos para AKS-Engine](azure-stack-kubernetes-aks-engine-set-up.md)
2. Instale AKS-Engine en una máquina con acceso al entorno de Azure Stack.
     - [Instalación de AKS-Engine en Windows para Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Instalación de AKS-Engine en Linux para Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Implementación de un clúster de Kubernetes con AKS-Engine en Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de los requisitos previos para AKS-Engine](azure-stack-kubernetes-aks-engine-set-up.md)