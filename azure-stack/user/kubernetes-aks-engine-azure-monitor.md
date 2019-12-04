---
title: Uso de Azure Monitor para contenedores en Azure Stack Hub | Microsoft Docs
description: Aprenda a usar Azure Monitor para contenedores en Azure Stack Hub.
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
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: a3941a3ada52a8588b504884a2d03cb00dd2c850
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310325"
---
# <a name="use-azure-monitor-for-containers-on-azure-stack-hub"></a>Uso de Azure Monitor para contenedores en Azure Stack Hub

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) para contenedores para supervisar los contenedores en un clúster de Kubernetes implementado en un motor de AKS en Azure Stack Hub. 

> [!IMPORTANT]
> Azure Monitor para contenedores en Azure Stack Hub está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Puede revisar el rendimiento del contenedor con Azure Monitor mediante la recopilación de métricas de procesador y memoria de controladores, nodos y contenedores disponibles en Kubernetes mediante la API de métricas. Además, el servicio recopila registros de contenedor. Puede usar estos registros para diagnosticar problemas en el clúster local desde Azure. Después de configurar la supervisión de los clústeres de Kubernetes, estas métricas y registros se recopilan automáticamente. Una versión para contenedores del agente de Azure Monitor Log Analytics para Linux recopila los registros. Azure Monitor almacena las métricas y los registros en el área de trabajo de Log Analytics accesible en su suscripción de Azure.

Hay dos maneras de habilitar Azure Monitor en el clúster. Ambos métodos requieren la configuración de un área de trabajo de Azure Monitor Log Analytics en Azure.

## <a name="prerequisites"></a>Requisitos previos

Ambos métodos requieren los [requisitos previos](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers#pre-requisites) que se indican en [Azure Monitor: Contenedores](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers).

## <a name="method-one"></a>Método uno

También puede usar el gráfico de [Helm](https://helm.sh/) para instalar los agentes de supervisión en el clúster. Siga las instrucciones que se indican en el siguiente artículo, [Azure Monitor: Contenedores](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers).

## <a name="method-two"></a>Método dos

Puede especificar un **complemento** en el archivo JSON de especificaciones del clúster del motor de AKS. El archivo también se denomina modelo de API. En este complemento, proporcione la versión codificada en Base64 de **WorkspaceGUID** y **WorkspaceKey** del área de trabajo de Azure Log Analytics donde se almacenará la información de supervisión.

Las definiciones de API admitidas para el clúster de Azure Stack Hub se pueden encontrar en este ejemplo: [kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). En concreto, busque la propiedad **addons** en **kubernetesConfig**:

```JSON  
 "orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Guid in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).  
- Consulte [Introducción a Azure Monitor para contenedores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).
