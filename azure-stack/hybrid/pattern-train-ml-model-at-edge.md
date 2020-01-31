---
title: Patrón para entrenar un modelo de Machine Learning (ML) en el perímetro, mediante Azure y Azure Stack Hub.
description: Aprenda a usar servicios de Azure y Azure Stack Hub para realizar el entrenamiento del modelo de ML en el perímetro.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a95102bcdc57bcad7116803768830f317ea48c4c
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76877301"
---
# <a name="train-machine-learning-ml-model-at-the-edge-pattern"></a>Entrenamiento del modelo de Machine Learning (ML) en el patrón perimetral

Genere modelos de ML portátiles a partir de datos que solo existen en el entorno local.

## <a name="context-and-problem"></a>Contexto y problema

Muchas organizaciones desean desbloquear información de sus datos locales o heredados mediante herramientas que entienden sus científicos de datos. [Azure Machine Learning](/azure/machine-learning/) proporciona herramientas nativas de la nube para entrenar, ajustar e implementar modelos de ML y aprendizaje profundo.  

Sin embargo, algunos datos son demasiado grandes para enviarlos a la nube, o bien esto no es posible por razones normativas. Con este patrón, los científicos de datos pueden usar Azure Machine Learning para entrenar modelos mediante datos locales y proceso. 

## <a name="solution"></a>Solución

El entrenamiento en el patrón perimetral usa una máquina virtual (VM) que se ejecuta en Azure Stack Hub. La máquina virtual se registra como destino de proceso en Azure ML, lo que le permite obtener acceso a datos disponibles solo en el entorno local. En este caso, los datos se almacenan en Blob Storage de Azure Stack Hub. 

Una vez entrenado el modelo, se registra con Azure ML, con contenedores, y se agrega a una instancia de Azure Container Registry para su implementación. Para esta iteración del patrón, la máquina virtual de entrenamiento de Azure Stack Hub debe ser accesible a través de la red pública de Internet. 

[![entrenamiento del modelo de ml en la arquitectura perimetral](media/pattern-train-ml-model-at-edge/solution-architecture.png)](media/pattern-train-ml-model-at-edge/solution-architecture.png)

Así es como funciona la solución: 

1. La máquina virtual de Azure Stack Hub se implementa y se registra como destino de proceso con Azure ML.
2. Se crea un experimento en Azure ML que usa la máquina virtual de Azure Stack Hub como destino de proceso.
3. Una vez entrenado el modelo, se registra y se incluye en un contenedor.
4. Ahora se puede implementar el modelo en ubicaciones del entorno local o la nube.

## <a name="components"></a>Componentes

Esta solución usa los siguientes componentes:

| Nivel | Componente | Descripción |
|----------|-----------|-------------|
| Azure | Azure Machine Learning | [Azure Machine Learning](/azure/machine-learning/) orquesta el entrenamiento del modelo de ML. |
| | Azure Container Registry | Azure ML empaqueta el modelo en un contenedor y lo almacena en una instancia de [Azure Container Registry](/azure/container-registry/) para su implementación.|
| Azure Stack Hub | App Service | [Azure Stack Hub con App Service](/azure-stack/operator/azure-stack-app-service-overview) proporciona la base para los componentes en el perímetro. |
| | Proceso | Una máquina virtual de Azure Stack Hub que ejecuta Ubuntu con Docker se usa para entrenar el modelo de ML. |
| | Storage | Los datos privados se pueden hospedar en Blob Storage de Azure Stack Hub. |

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar esta solución:

### <a name="scalability"></a>Escalabilidad 

A fin de habilitar esta solución para escalarla, tendrá que crear una máquina virtual ajustada adecuadamente en Azure Stack Hub para su entrenamiento.

### <a name="availability"></a>Disponibilidad

Asegúrese de que los scripts de entrenamiento y la máquina virtual de Azure Stack Hub tienen acceso a los datos locales usados para el entrenamiento.

### <a name="manageability"></a>Facilidad de uso

Asegúrese de que los modelos y experimentos se registran y etiquetan, y de que se crean versiones de los mismos, a fin de evitar confusiones durante la implementación de modelo. 

### <a name="security"></a>Seguridad

Este patrón permite a Azure ML obtener acceso a posibles datos confidenciales en el entorno local. Asegúrese de que la cuenta usada para SSH en la máquina virtual de Azure Stack Hub tiene una contraseña segura y de que los scripts de entrenamiento no conservan ni cargan datos en la nube. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas presentados en este artículo:
- Consulte la [documentación de Azure Machine Learning](/azure/machine-learning) para obtener información general de ML y temas relacionados.
- Consulte [Azure Container Registry](/azure/container-registry/) para obtener información sobre cómo compilar, almacenar y administrar imágenes para implementaciones de contenedores.
- Consulte [App Service en Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-overview) para obtener más información sobre el proveedor de recursos y cómo realizar la implementación.
- Consulte [Consideraciones sobre el diseño de aplicaciones híbridas](overview-app-design-considerations.md) para más información sobre los procedimientos recomendados y responder a preguntas adicionales.
- Consulte la información relativa a la [familia de productos y soluciones de Azure Stack](/azure-stack) para más información sobre toda la gama de productos y soluciones.

Cuando esté listo para probar la solución de ejemplo, continúe con la [guía de implementación Entrenamiento del modelo de aprendizaje automático en el perímetro](https://aka.ms/edgetrainingdeploy). La guía de implementación proporciona instrucciones paso a paso para implementar y probar sus componentes.