---
title: Patrón para implementar la detección de existencias agotadas en el perímetro con Azure y Azure Stack Edge
description: Aprenda a usar los servicios de Azure y Azure Stack Edge para implementar la detección de existencias agotadas.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: fb4eb410eefb8a34bd869543a191a1b044a42f72
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640098"
---
# <a name="out-of-stock-detection-at-the-edge-pattern"></a>Patrón de detección de existencias agotadas en el perímetro

Este patrón muestra cómo determinar si los estantes contienen artículos con existencias agotadas mediante un dispositivo de Azure Stack Edge o Azure IoT Edge y cámaras de red.

## <a name="context-and-problem"></a>Contexto y problema

Las tiendas físicas pierden ventas porque cuando los clientes buscan un artículo, no existe en el estante. Sin embargo, puede que el artículo esté en la trastienda pero que no se haya repuesto. A las tiendas les gusta usar a sus empleados de manera eficaz y recibir notificaciones automáticas cuando sea necesario reponer artículos.

## <a name="solution"></a>Solución

En el ejemplo de la solución se usa un dispositivo perimetral, como Azure Stack Edge en cada tienda, que procesa los datos de las cámaras de la tienda de manera eficaz. Este diseño optimizado permite a las tiendas enviar solo las imágenes y los eventos apropiados a la nube. El diseño ahorra ancho de banda y espacio de almacenamiento y garantiza la privacidad de los clientes. A medida que se leen los fotogramas de cada cámara, un modelo de aprendizaje automático procesa la imagen y devuelve todas las áreas con existencias agotadas. La imagen y las áreas con existencias agotadas se muestran en una aplicación web local. Estos datos se pueden enviar a un entorno de Time Series Insight para mostrar información detallada en Power BI.

![arquitectura de la solución de existencias agotadas en el perímetro](media/pattern-out-of-stock-at-edge/solution-architecture.png)

Así es como funciona la solución:
1. Las imágenes se capturan con una cámara de red a través de HTTP o RTSP.
2. La imagen se redimensiona y se envía al controlador de inferencia, que se comunica con el modelo de aprendizaje automático para determinar si hay alguna imagen de existencias agotadas.
3. El modelo de aprendizaje automático devuelve todas las áreas de existencias agotadas.
4. El controlador de inferencia carga la imagen sin procesar en un blob (si se especifica) y envía los resultados del modelo a Azure IoT Hub y a un procesador de rectángulo de selección en el dispositivo.
5. El procesador de rectángulo de selección agrega rectángulos de selección y almacena en caché la ruta de acceso de la imagen en una base de datos en memoria.
6. La aplicación web consulta las imágenes y las muestra en el orden recibido.
7. Los mensajes de IoT Hub se agregan en Time Series Insights.
8. Power BI muestra un informe interactivo de elementos con existencias agotadas con el tiempo con los datos de Time Series Insights.


## <a name="components"></a>Componentes

Esta solución usa los siguientes componentes:

| Nivel | Componente | DESCRIPCIÓN |
|----------|-----------|-------------|
| Hardware local | Cámara de red | Se requiere una cámara de red, con una fuente HTTP o RTSP para proporcionar inferencia a las imágenes. |
| Azure | Azure IoT Hub | [Azure IoT Hub](/azure/iot-hub/) administra el aprovisionamiento de dispositivos y la mensajería de los dispositivos perimetrales. |
|  | Azure Time Series Insights | [Azure Time Series Insights](/azure/time-series-insights/) almacena los mensajes de IoT Hub para su visualización. |
|  | Power BI | [Microsoft Power BI](https://powerbi.microsoft.com/) proporciona informes centrados en el negocio de los eventos de existencias agotadas. Power BI proporciona una interfaz de panel fácil de usar para ver los resultados de Azure Stream Analytics. |
| Dispositivo de Azure Stack Edge o de<br>Azure IoT Edge | Azure IoT Edge | [Azure IoT Edge](/azure/iot-edge/) orquesta el entorno de ejecución de los contenedores locales y controla la administración de dispositivos y las actualizaciones.|
| | Project Brainwave de Azure | En un dispositivo Azure Stack Edge, [Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) emplea matrices de puertas programables en campo (FPGA) para acelerar la inferencia de aprendizaje automático.|

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar esta solución:

### <a name="scalability"></a>Escalabilidad 

La mayoría de los modelos de aprendizaje automático solo se pueden ejecutar con un determinado número de fotogramas por segundo, en función del hardware proporcionado. Determine la frecuencia de muestreo óptima de las cámaras para asegurarse de que no se hace una copia de seguridad de la canalización de aprendizaje automático. Distintos tipos de hardware administrarán diferentes números de cámaras y velocidades de fotogramas.

### <a name="availability"></a>Disponibilidad

Es importante tener en cuenta lo que podría suceder si el dispositivo perimetral perdiera la conectividad. Considere qué datos pueden perderse del panel de Time Series Insights y Power BI. La solución de ejemplo proporcionada no está diseñada para una alta disponibilidad.

### <a name="manageability"></a>Manejabilidad

Esta solución puede abarcar muchos dispositivos y ubicaciones, lo que podría resultar complicado. Los servicios de IoT de Azure pueden poner en línea automáticamente nuevas ubicaciones y dispositivos y mantenerlos actualizados. También se deben seguir los procedimientos de gobierno de datos adecuados.

### <a name="security"></a>Seguridad

Este patrón controla los datos potencialmente confidenciales. Asegúrese de que las claves se rotan regularmente y de que los permisos de la cuenta de Azure Storage y los recursos compartidos locales están establecidos correctamente. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas presentados en este artículo:
- En este patrón se usan varios servicios relacionados con IoT, como [Azure IoT Edge](/azure/iot-edge/), [Azure IoT Hub](/azure/iot-hub/) y [Azure Time Series Insights](/azure/time-series-insights/).
- Para más información sobre Microsoft Project Brainwave, consulte el [anuncio del blog](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) y vea el [vídeo de Azure Machine Learning acelerado con Project Brainwave](https://www.youtube.com/watch?v=DJfMobMjCX0).
- Consulte [Consideraciones sobre el diseño de aplicaciones híbridas](overview-app-design-considerations.md) para más información sobre los procedimientos recomendados y responder a preguntas adicionales.
- Consulte la información relativa a la [familia de productos y soluciones de Azure Stack](/azure-stack) para más información sobre toda la gama de productos y soluciones.

Cuando esté listo para probar la solución de ejemplo, continúe con la [guía de implementación de soluciones de datos por niveles para análisis](https://aka.ms/edgeinferencingdeploy). La guía de implementación proporciona instrucciones paso a paso para implementar y probar sus componentes.