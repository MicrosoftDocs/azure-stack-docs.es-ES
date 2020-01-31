---
title: Descripción del motor de AKS en Azure Stack Hub
description: Aprenda a usar la herramienta de línea de comandos del motor de AKS para implementar y administrar clústeres de Kubernetes en Azure y Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: af1651f24830cf33d160e9655325adc0aa05d523
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884771"
---
# <a name="what-is-the-aks-engine-on-azure-stack-hub"></a>Descripción del motor de AKS en Azure Stack Hub

La herramienta de línea de comandos del motor de AKS se puede usar para implementar y administrar clústeres de Kubernetes en Azure y Azure Stack Hub. Use el motor de AKS para crear, actualizar y escalar clústeres nativos de Azure Resource Manager. Puede usar el motor para implementar un clúster en entornos conectados y desconectados. En este artículo se proporciona información general sobre el motor de AKS, escenarios en los que se puede usar el motor con Azure Stack Hub y una introducción a operaciones como la implementación, la actualización y la escala.

## <a name="overview-of-the-aks-engine"></a>Introducción al motor de AKS

El [motor de AKS](https://github.com/Azure/aks-engine) proporciona una herramienta de línea de comandos para arrancar clústeres de Kubernetes en Azure y Azure Stack Hub. Mediante Azure Resource Manager, el motor de AKS le ayuda a crear y mantener clústeres que se ejecutan en máquinas virtuales, redes virtuales y otros recursos de infraestructura como servicio (IaaS) en Azure Stack Hub.

## <a name="aks-engine-on-azure-stack-hub-considerations"></a>Consideraciones acerca del motor de AKS en Azure Stack Hub

Antes de usar el motor de AKS en Azure Stack Hub es importante que conozca las diferencias que existen entre Azure Stack Hub y Azure. En esta sección se identifican las diferentes características y consideraciones clave al usar Azure Stack Hub con el motor de AKS para administrar un clúster de Kubernetes.

Para más información sobre los detalles del motor de AKS en Azure Stack Hub y sus diferencias con respecto a Azure, consulte [El motor de AKS en Azure Stack Hub](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Escenarios admitidos con el motor de AKS

El equipo de soporte técnico de Azure Stack Hub admite los siguientes escenarios:

1.  El motor de AKS implementa todos los artefactos del clúster según las instrucciones de esta documentación y mediante la [plantilla siguiente](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  El motor de AKS implementa el clúster en una red virtual existente. Para más información, consulte [Uso de una red virtual personalizada con el motor de AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  [Actualice](azure-stack-kubernetes-aks-engine-upgrade.md) y [escale](azure-stack-kubernetes-aks-engine-scale.md) operaciones.

Para más información sobre el motor de AKS y Azure Stack Hub, consulte [Directivas de compatibilidad para el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Instalación del motor de AKS e implementación de un clúster de Kubernetes

Para implementar un clúster de Kubernetes con el motor de AKS en Azure Stack Hub:

1. [Configuración de los requisitos previos para el motor de AKS](azure-stack-kubernetes-aks-engine-set-up.md)
2. Instale el motor de AKS en una máquina con acceso a su entorno de Azure Stack Hub.
     - [Instalación del motor de AKS en Windows para Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Instalación del motor de AKS en Linux para Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Implementación de un clúster de Kubernetes con el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de los requisitos previos para el motor de AKS](azure-stack-kubernetes-aks-engine-set-up.md)