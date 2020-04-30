---
title: Métricas de Azure Stack Hub compatibles con Azure Monitor
description: Descubra las métricas de Azure Stack Hub compatibles con Azure Monitor.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 2a200939dd987ba11edcd5fbca516c3fc6566dbd
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661031"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack-hub"></a>Métricas de Azure Stack Hub compatibles con Azure Monitor

Las métricas de Azure Monitor en Azure Stack Hub se recuperan de la misma manera que en Azure global. Puede crear las medidas en el portal, obtenerlas con la API REST o consultarlas con PowerShell o la CLI.

En las tablas siguientes, se muestran las métricas disponibles con la canalización de métricas de Azure Monitor en Azure Stack Hub. Para consultar estas métricas y acceder a ellas, utilice la versión **2018-01-01** del perfil de la API. Para más información sobre los perfiles de API y Azure Stack Hub, consulte [Administración de perfiles de versión de API en Azure Stack Hub](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Descripción | Dimensions |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Porcentaje de CPU | Porcentaje de CPU | Percent | Average | El porcentaje de unidades de proceso asignadas que las máquinas virtuales usan actualmente. | Sin dimensiones |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Descripción | Dimensions |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Capacidad usada | Bytes | Average | Capacidad usada de la cuenta. | Sin dimensiones |
| Transacciones | Transacciones | Count | Total | El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuestas. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure. | GeoType, ApiName |
| Salida | Salida | Bytes | Total | La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables. | GeoType, ApiName |
| SuccessServerLatency | Latencia del servidor correcta | Milisegundos | Average | La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latencia de E2E correcta | Milisegundos | Average | La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta. | GeoType, ApiName |
| Disponibilidad | Disponibilidad | Percent | Average | El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad, hay que tomar el valor de TotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Descripción | Dimensions |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Capacidad de Blob | Bytes | Total | Cantidad de almacenamiento que ha usado la instancia de Blob service de la cuenta de almacenamiento, en bytes. | BlobType |
| BlobCount | Recuento de blobs | Count | Total | Número de blobs en la instancia de Blob service de la cuenta de almacenamiento. | BlobType |
| ContainerCount | Recuento de contenedores de blobs | Count | Average | Número de contenedores en la instancia de Blob service de la cuenta de almacenamiento. | Sin dimensiones |
| Transacciones | Transacciones | Count | Total | El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuestas. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure. | GeoType, ApiName |
| Salida | Salida | Bytes | Total | La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables. | GeoType, ApiName |
| SuccessServerLatency | Latencia del servidor correcta | Milisegundos | Average | La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latencia de E2E correcta | Milisegundos | Average | La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta. | GeoType, ApiName |
| Disponibilidad | Disponibilidad | Percent | Average | El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad, hay que tomar el valor de TotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Descripción | Dimensions |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Capacidad de Table | Bytes | Average | Cantidad de almacenamiento que ha utilizado la instancia de Table service de la cuenta de almacenamiento, en bytes. | Sin dimensiones |
| TableCount | Recuento de tablas | Count | Average | Número de tablas en la instancia de Table service de la cuenta de almacenamiento. | Sin dimensiones |
| TableEntityCount | Recuento de entidades de Table | Count | Average | Número de entidades de tabla en la instancia de Table service de la cuenta de almacenamiento. | Sin dimensiones |
| Transacciones | Transacciones | Count | Total | El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuestas. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure. | GeoType, ApiName |
| Salida | Salida | Bytes | Total | La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables. | GeoType, ApiName |
| SuccessServerLatency | Latencia del servidor correcta | Milisegundos | Average | La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latencia de E2E correcta | Milisegundos | Average | La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta. | GeoType, ApiName |
| Disponibilidad | Disponibilidad | Percent | Average | El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad, hay que tomar el valor de TotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Descripción | Dimensions |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Capacidad de Queue | Bytes | Average | Cantidad de almacenamiento que ha utilizado la instancia de Queue service de la cuenta de almacenamiento, en bytes. | Sin dimensiones |
| QueueCount | Recuento de colas | Count | Average | Número de colas en la instancia de Queue service de la cuenta de almacenamiento. | Sin dimensiones |
| QueueMessageCount | Recuento de mensajes de Queue | Count | Average | Número aproximado de mensajes de cola en la instancia de Queue service de la cuenta de almacenamiento. | Sin dimensiones |
| Transacciones | Transacciones | Count | Total | El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuestas. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure. | GeoType, ApiName |
| Salida | Salida | Bytes | Total | La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables. | GeoType, ApiName |
| SuccessServerLatency | Latencia del servidor correcta | Milisegundos | Average | La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latencia de E2E correcta | Milisegundos | Average | La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta. | GeoType, ApiName |
| Disponibilidad | Disponibilidad | Percent | Average | El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad, hay que tomar el valor de TotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada. | GeoType, ApiName |

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre [Azure Monitor en Azure Stack Hub](azure-stack-metrics-azure-data.md).
