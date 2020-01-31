---
title: Patrón para implementar una solución de datos en capas para análisis con Azure y Azure Stack Hub.
description: Aprenda a usar los servicios de Azure y Azure Stack Hub para implementar una solución de datos en capas a través de la nube híbrida.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 91f23e7362ec0a1a733417dad1f48dc04b80d19f
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875528"
---
# <a name="tiered-data-for-analytics-pattern"></a>Patrón de datos en capas para análisis

Este patrón ilustra el uso de Azure Stack Hub y Azure para organizar, analizar, procesar, sanear y almacenar datos en varias ubicaciones locales y en la nube.

## <a name="context-and-problem"></a>Contexto y problema

Uno de los problemas a los que se enfrentan a las organizaciones empresariales en el panorama tecnológico moderno tiene que ver con el almacenamiento, el procesamiento y el análisis de datos seguros. Entre las consideraciones se incluyen las siguientes:
- contenido de datos
- ubicación
- requisitos de seguridad y privacidad
- permisos de acceso
- mantenimiento
- almacenamiento

Azure, en combinación con Azure Stack Hub, aborda los problemas de los datos y ofrece soluciones de bajo costo. Esta solución se expresa mejor a través de una empresa de fabricación o logística distribuida. 

La solución se basa en el siguiente escenario:
- Una gran organización de fabricación de varias sucursales.
- Es necesario el almacenamiento, el procesamiento y la distribución de datos de forma rápida y segura entre las ubicaciones remotas globales y su sede central. 
- Actividad de empleados y maquinaria, información de instalaciones y datos de informes empresariales que deben permanecer seguros. Los datos se deben distribuir de forma adecuada y deben satisfacer la directivas regional de cumplimiento y las normativas del sector.

## <a name="solution"></a>Solución

La utilización de entornos locales y en la nube pública para satisfacer las necesidades de empresas con varias sedes. Azure Stack Hub ofrece una solución rápida, segura y flexible para recopilar, procesar, almacenar y distribuir datos locales y remotos. Esto es importante cuando la seguridad, confidencialidad, directiva corporativa y los requisitos normativos pueden diferir entre ubicaciones y usuarios. 

![Datos en capas para la arquitectura de la solución de análisis](media/pattern-tiered-data-analytics/solution-architecture.png)

## <a name="components"></a>Componentes

Esta solución usa los siguientes componentes:

| Nivel | Componente | Descripción |
|----------|-----------|-------------|
| Azure | Storage | Una cuenta de [Azure Storage](/azure/storage/) proporciona un punto de conexión de consumo de datos estéril. Azure Storage es la solución de almacenamiento de Microsoft para los escenarios modernos de almacenamiento de datos. Azure Storage ofrece un almacén de objetos que se puede escalar de forma masiva destinado a objetos de datos y un servicio de sistema de archivos para la nube. También proporciona una tienda de mensajería para mensajería confiable y una tienda NoSQL. |
| Azure Stack Hub | Storage | Se usa una cuenta de [almacenamiento de Azure Stack Hub](/azure-stack/user/azure-stack-storage-overview) para varios servicios:<br>- **Blob Storage** para el almacenamiento de datos sin procesar. Blob Storage puede contener cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. Cada blob se organiza en un contenedor. Los contenedores ofrecen una forma útil de asignar directivas de seguridad a grupos de objetos. Una cuenta de almacenamiento puede incluir un número cualquiera de contenedores y, a su vez, un contenedor puede incluir un número cualquiera de blobs, hasta alcanzar el límite de capacidad de 500 TB de la cuenta de almacenamiento.<br>- **Blob Storage** para archivo de datos. Hay ventajas en el almacenamiento de bajo costo para el archivo de datos de acceso esporádico. Entre los ejemplos de datos de acceso esporádico se encuentran las copias de seguridad, el contenido multimedia, los datos científicos, la información de cumplimiento y los datos de archivo. En general, los datos a los que se accede con poca frecuencia se consideran de almacenamiento de acceso esporádico. Se trata de datos en capas organizar los basados en atributos como la frecuencia de acceso y el período de retención. A los datos del cliente se accede con poca frecuencia, pero requieren latencia y rendimiento similares a los datos de acceso frecuente.<br>- **Queue Storage** para el almacenamiento de datos procesado. Queue Storage proporciona mensajería en la nube entre componentes de aplicaciones. A la hora de diseñar aplicaciones para escala, los componentes de las mismas suelen desacoplarse, por lo que se pueden escalar de forma independiente. Queue Storage ofrece mensajería asincrónica para la comunicación entre los componentes de aplicaciones,  ya sea que se ejecuten en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Además, este tipo de almacenamiento admite la administración de tareas asincrónicas y la creación de flujos de trabajo de procesos. |
| | Azure Functions | El servicio de [Azure Functions](/azure/azure-functions/) lo proporciona el proveedor de recursos de [Azure App Service en Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-overview). Azure Functions permite ejecutar el código en un entorno sencillo sin servidor para ejecutar scripts o código en respuesta a diversos eventos. Azure Functions se escala para satisfacer la demanda sin tener que crear una máquina virtual o publicar una aplicación web con el lenguaje de programación que prefiera. La solución usa Functions para:<br>- **Entrada de datos**<br>- **Esterilización de datos.** Las funciones desencadenadas manualmente pueden realizar el procesamiento de datos, la limpieza y el archivado programado de los datos. Entre los ejemplos se incluyen las limpiezas nocturnas de las listas de clientes y el procesamiento mensual de informes.|

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar esta solución:

### <a name="scalability"></a>Escalabilidad 

Las funciones de Azure y las soluciones de almacenamiento se escalan para satisfacer las demandas de procesamiento y volumen de datos. Para más información sobre la escalabilidad y los destinos de Azure, consulte la [documentación de escalabilidad de Azure Storage](/azure/storage/common/storage-scalability-targets). 

### <a name="availability"></a>Disponibilidad

El almacenamiento es la consideración de disponibilidad principal para este patrón. Se requiere una conexión mediante vínculos rápidos para el procesamiento y distribución de volúmenes de datos grandes. 

### <a name="manageability"></a>Facilidad de uso

La manejabilidad de esta solución depende de las herramientas de creación en uso y de la involucración del control de código fuente. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas presentados en este artículo:
- Consulte la documentación de [Azure Storage](/azure/storage/) y [Azure Functions](/azure/azure-functions/). Este patrón hace un uso intensivo de cuentas de Azure Storage y Azure Functions, tanto en Azure como en Azure Stack Hub.
- Consulte [Consideraciones sobre el diseño de aplicaciones híbridas](overview-app-design-considerations.md) para más información sobre los procedimientos recomendados y responder a preguntas adicionales.
- Consulte la información relativa a la [familia de productos y soluciones de Azure Stack](/azure-stack) para más información sobre toda la gama de productos y soluciones.

Cuando esté listo para probar la solución de ejemplo, continúe con la [guía de implementación de soluciones de datos por niveles para análisis](https://aka.ms/tiereddatadeploy). La guía de implementación proporciona instrucciones paso a paso para implementar y probar sus componentes.