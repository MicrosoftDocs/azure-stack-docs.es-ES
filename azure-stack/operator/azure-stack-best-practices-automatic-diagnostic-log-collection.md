---
title: Procedimientos recomendados para la recopilación automática de registros de Azure Stack Hub
description: Procedimientos recomendados para la recopilación automática de registros en Ayuda y soporte técnico de Azure Stack Hub
author: justinha
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: c14c2cf0d65c29ea7b2968558e89a20b504d8f53
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700483"
---
# <a name="best-practices-for-automatic-azure-stack-hub-log-collection"></a>Procedimientos recomendados para la recopilación automática de registros de Azure Stack Hub 

En este tema se explican los procedimientos recomendados para administrar la recopilación automática de registros de diagnóstico en Azure Stack Hub. 

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Recopilación de registros de varios sistemas de Azure Stack Hub

Configure un contenedor de blobs para todas las unidades de escalado de Azure Stack Hub de las que quiera recopilar registros. Para más información sobre cómo configurar el contenedor de blobs, consulte [Configuración de la recopilación de registros de diagnóstico automática de Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection.md). Como procedimiento recomendado, guarde solo los registros de diagnóstico de la misma unidad de escalado de Azure Stack Hub en un solo contenedor de blobs. 

## <a name="retention-policy"></a>Directiva de retención

Cree una [regla de administración del ciclo de vida](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) de Azure Blob Storage para administrar la directiva de retención de registros. Recomendamos que los registros de diagnóstico se retengan durante 30 días. Para crear una regla de administración del ciclo de vida en Azure Storage, inicie sesión en Azure Portal, haga clic en **Cuentas de almacenamiento** y en el contenedor de blobs y, en **Blob service**, haga clic en **Administración del ciclo de vida**.

![Captura de pantalla que muestra la administración del ciclo de vida en Azure Portal](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>Expiración del token de SAS

Establezca la expiración de la dirección URL de SAS en dos años. Si alguna vez renueva sus claves de cuenta, asegúrese de regenerar la dirección URL de SAS. Debe administrar el token de SAS según los procedimientos recomendados. Para obtener más información, consulte [Procedimientos recomendados al usar SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).


## <a name="bandwidth-consumption"></a>Consumo de ancho de banda

El tamaño medio de la recopilación de registros de diagnóstico varía en función de si esta es a petición o automática. 

Para la recopilación de registros a petición, el tamaño de la recopilación de registros depende de cuántas horas se recopilan. Puede elegir cualquier ventana deslizante de entre 1 y 4 horas de los últimos siete días. 

Al habilitarse la recopilación de registros de diagnóstico automática, el servicio se supervisa para alertas críticas. Una vez que se genera una alerta crítica y persiste durante unos 30 minutos, el servicio recopila y carga los registros adecuados. El tamaño medio de esta recopilación de registros es aproximadamente 2 GB. En caso de que se produzca un error de revisiones y actualizaciones, la recopilación de registros automática solo se iniciará si se genera una alerta crítica y persiste durante unos 30 minutos. Recomendamos que siga las [instrucciones sobre la supervisión de las revisiones y actualizaciones](azure-stack-updates.md).
La supervisión de alertas, la recopilación de registros y la carga son transparentes para el usuario. 



En un sistema correcto, no se recopilarán registros en absoluto. En un sistema incorrecto, la recopilación de registros puede ejecutarse dos o tres veces en un día, pero normalmente se ejecuta solo una vez. Como máximo, podría ejecutarse hasta diez veces en un día en el peor de los escenarios.  

La siguiente tabla puede ayudar a los entornos con conexiones limitadas a Azure a tener en cuenta el impacto de la habilitación de la recopilación de registros automática.

| Conexión de red | Impacto |
|--------------------|--------|
| Conexión de alta latencia o ancho de banda bajo | La carga de registros tardará una cantidad de tiempo ampliada en completarse | 
| Conexión compartida | La carga solo puede afectar a otras aplicaciones o usuarios que comparten la conexión de red |
| Conexión de uso medido | Puede haber un cargo adicional de su ISP por el uso adicional de la red |


## <a name="managing-costs"></a>Administración de los costos

Los [cargos de Azure Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) Storage dependen de cuántos datos se guardan cada mes y de otros factores como la redundancia de datos. Si no tiene una cuenta de almacenamiento existente, puede iniciar sesión en Azure Portal, hacer clic en **Cuentas de almacenamiento** y seguir los pasos para [crear una dirección URL de SAS del contenedor de blobs de Azure](azure-stack-configure-automatic-diagnostic-log-collection.md).

Como procedimiento recomendado, cree una [directiva de administración del ciclo de vida](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) de Azure Blob Storage para minimizar los costos de almacenamiento en curso. Para más información sobre cómo configurar la cuenta de almacenamiento, consulte [Configuración de la recopilación de registros de diagnóstico automática de Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Consulte también

[Configuración de la recopilación de registros de diagnóstico automática de Azure Stack Hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md)

