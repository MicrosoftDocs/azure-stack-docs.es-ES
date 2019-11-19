---
title: Patrón híbrido para implementar la detección de afluencia de compradores basada en IA con Azure y Azure Stack Hub
description: Aprenda a usar los servicios de Azure y Azure Stack Hub para implementar una solución de afluencia de compradores basada en IA para analizar el tráfico de las tiendas.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 10/31/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 10/31/2019
ms.openlocfilehash: a7a7563db3c315c4913287e8f286f07abd633602
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640002"
---
# <a name="footfall-detection-pattern"></a>Patrón de detección de afluencia de compradores

Este patrón proporciona información general para la implementación de una solución de detección de afluencia de compradores basada en IA, con el fin de analizar el tráfico de visitantes en las tiendas. La solución genera información de acciones del mundo real mediante Azure, Azure Stack Hub y el kit de desarrollo de inteligencia artificial de Custom Vision.

## <a name="context-and-problem"></a>Contexto y problema

Contoso Stores quiere obtener información sobre el modo en que los clientes reciben sus productos actuales, en relación con el diseño de la tienda. No pueden colocar personal en cada sección, y es ineficaz que un equipo de analistas revise la grabación completa de la cámara de una tienda. Además, ninguno de sus almacenes tiene suficiente ancho de banda como para transmitir vídeo desde todas sus cámaras a la nube para su análisis. 

Contoso quiere encontrar una manera discreta y fácil para determinar los datos demográficos, la fidelidad y las reacciones de sus clientes ante el diseño de la tienda y los productos.

## <a name="solution"></a>Solución

Este patrón de análisis minorista usa un enfoque por niveles para la inferencia en el perímetro. Mediante el kit de desarrollo de inteligencia artificial de Custom Vision, solo las imágenes con caras humanas se envían para su análisis a una instancia privada de Azure Stack Hub que ejecute Azure Cognitive Services. Los datos agregados anonimizados se envían a Azure para su agregación en todas las tiendas y su visualización en Power BI. La combinación de la nube pública y perimetral permite a Contoso aprovechar la tecnología moderna de IA, a la vez que sigue cumpliendo las directivas corporativas y respetando la privacidad de sus clientes.

[![Solución de detección de afluencia de compradores](media/pattern-retail-footfall-detection/solution-architecture.png)](media/pattern-retail-footfall-detection/solution-architecture.png)

A continuación se muestra un resumen de cómo funciona la solución: 

1. El kit de desarrollo de IA de Custom Vision obtiene una configuración de IoT Hub, que instala el tiempo de ejecución de IoT Edge y un modelo de ML.
2. Si el modelo ve una persona, le hace una foto y la carga en el almacenamiento de blobs de Azure Stack Hub. 
3. Blob service desencadena una función de Azure en Azure Stack Hub. 
4. Azure Functions llama a un contenedor con la Face API para obtener datos demográficos y de emociones de la imagen.
5. Los datos se anonimizan y se envían a un centro de eventos de Azure.
6. El centro de eventos envía los datos a Stream Analytics.
7. Stream Analytics agrega los datos y los envía a Power BI.

## <a name="components"></a>Componentes

Esta solución usa los siguientes componentes:

| Nivel | Componente | DESCRIPCIÓN |
|----------|-----------|-------------|
| Hardware en la tienda | [Kit de desarrollo de IA de Custom Vision](https://azure.github.io/Vision-AI-DevKit-Pages/) | Proporciona filtrado en la tienda mediante un modelo de ML local que solo captura imágenes de personas para su análisis. Aprovisionado y actualizado de forma segura a través de IoT Hub.<br><br>|
| Azure | [Azure Event Hubs](/azure/event-hubs/) | Azure Event Hubs proporciona una plataforma escalable para la ingesta de datos anonimizados que se integra perfectamente con Azure Stream Analytics. |
|  | [Azure Stream Analytics](/azure/stream-analytics/) | Un trabajo de Azure Stream Analytics agrega los datos anonimizados y los agrupa en ventanas de 15 segundos para su visualización. |
|  | [Microsoft Power BI](https://powerbi.microsoft.com/) | Power BI proporciona una interfaz de panel fácil de usar para ver los resultados de Azure Stream Analytics. |
| Azure Stack Hub | [App Service](../operator/azure-stack-app-service-overview.md) | El proveedor de recursos (RP) de App Service proporciona una base para los componentes perimetrales. Incluye características de hospedaje y administración para aplicaciones web, API y Functions. |
| | Clúster del [motor de](https://github.com/Azure/aks-engine) Azure Kubernetes Service (AKS) | El RP de AKS con el clúster de AKS-Engine implementado en Azure Stack Hub proporciona un motor escalable y resistente para ejecutar el contenedor de Face API. |
| | [Contenedores de Face API](/azure/cognitive-services/face/face-how-to-install-containers) de Azure Cognitive Services| El RP de Azure Cognitive Services con contenedores de Face API proporciona detección de visitantes exclusivos, de emociones y demográfica en la red privada de Contoso. |
| | Blob Storage | Las imágenes capturadas con el kit de desarrollo de IA se cargan en el almacenamiento de blobs de Azure Stack Hub. |
| | Azure Functions | Una función de Azure que se ejecuta en Azure Stack Hub recibe la entrada del almacenamiento de blobs y administra las interacciones con Face API. Emite datos anonimizados a un centro de eventos ubicado en Azure.<br><br>|

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar esta solución:

### <a name="scalability"></a>Escalabilidad 

Para permitir que esta solución se escale entre varias cámaras y ubicaciones, deberá asegurarse de que todos los componentes pueden controlar el aumento de la carga. Es posible que tenga que realizar acciones como las siguientes:

- Aumentar el número de unidades de streaming de Stream Analytics
- Escalar horizontalmente la implementación de Face API
- Aumentar el rendimiento de Event Hubs
- En casos extremos, puede que sea necesario migrar desde Azure Functions a una máquina virtual.

### <a name="availability"></a>Disponibilidad

Dado que esta solución está en capas, es importante pensar en cómo tratar los errores de red o de alimentación. En función de las necesidades empresariales, podría ser adecuado implementar un mecanismo para almacenar localmente en caché las imágenes y, después, reenviarlas a Azure Stack Hub cuando vuelva a tener conectividad. Si la ubicación es lo suficientemente grande, podría ser una opción mejor implementar una instancia de Data Box Edge con el contenedor de Face API en esa ubicación.

### <a name="manageability"></a>Manejabilidad

Esta solución puede abarcar muchos dispositivos y ubicaciones, lo que podría resultar complicado. [Los servicios de IoT de Azure](/azure/iot-fundamentals/) se pueden usar para poner en línea automáticamente nuevas ubicaciones y dispositivos y mantenerlos actualizados. 

### <a name="security"></a>Seguridad

Esta solución captura imágenes de clientes, por lo que es fundamental tener en cuenta la seguridad. Asegúrese de que todas las cuentas de almacenamiento están protegidas con las directivas de acceso adecuadas y que las claves se cambian con regularidad. Asegúrese de que las cuentas de almacenamiento y Event Hubs tienen directivas de retención que cumplen las normas de privacidad corporativa y gubernamental. Asegúrese también de organizar los niveles de acceso de los usuarios. La organización por niveles garantiza que los usuarios solo tienen acceso a los datos que necesitan para su rol.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas presentados en este artículo:
- Consulte [Patrón de datos en niveles](https://aka.ms/tiereddatadeploy), que aprovecha el patrón de detección de afluencia de compradores.
- Para más información sobre el uso de Custom Vision, consulte [Kit de desarrollo de IA de Custom Vision](https://azure.github.io/Vision-AI-DevKit-Pages/). 

Cuando esté listo para probar la solución de ejemplo, continúe con la [guía de implementación de la detección de afluencia de compradores](solution-deployment-guide-retail-footfall-detection.md). La guía de implementación proporciona instrucciones paso a paso para implementar y probar sus componentes.