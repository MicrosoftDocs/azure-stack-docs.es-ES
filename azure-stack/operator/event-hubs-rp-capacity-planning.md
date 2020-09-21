---
title: Planeación de la capacidad para Event Hubs en Azure Stack Hub
description: Obtenga información sobre cómo planear la capacidad del proveedor de recursos de Event Hubs en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: ec369d8f01ed9dc5e6e5635af4922ef80736c4c5
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572160"
---
# <a name="how-to-do-capacity-planning-for-event-hubs-on-azure-stack-hub"></a>Planeación de la capacidad de Event Hubs en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Como operador, puede administrar la capacidad de Azure Stack Hub mediante [cuotas](azure-stack-quota-types.md) en los recursos. Puede controlar el consumo de recursos de Event Hubs estableciendo cuotas en el número máximo de núcleos que los clústeres de Event Hubs usan. Los usuarios crean clústeres de Event Hubs al implementar un recurso de Event Hubs. También hay varios requisitos de consumo de recursos para el proveedor de recursos, que se describen en este artículo.

## <a name="cluster-resource-consumption"></a>Consumo de recursos de clúster

Para conocer el consumo de la capacidad de las implementaciones de Event Hubs, es importante tener en cuenta que los usuarios crean clústeres de Event Hubs basados en unidades de capacidad (CU). No especifican un número de núcleos de CPU al crear un clúster de Event Hubs. Sin embargo, cada unidad de capacidad se asigna directamente a un número específico de núcleos consumidos. 

Los usuarios deberán crear clústeres de Event Hubs con unidades de capacidad que satisfagan sus requisitos empresariales. Para considerar su decisión sobre la configuración de cuotas, se muestra la siguiente tabla:
- El número total de núcleos usados por un clúster de Event Hubs de una unidad de capacidad.
- La capacidad aproximada necesaria para el consumo de otros recursos, como las cuentas de almacenamiento, la memoria y el almacenamiento de máquinas virtuales.

| | Tipo de máquina virtual | Nodos de clúster | Núcleos por máquina virtual y nodo | Total Cores (Núcleos totales) | Almacenamiento de máquinas virtuales | Memoria | Cuentas de almacenamiento | Direcciones IP públicas |
|-|---------|-------|-------------------|-------------|------------|--------|------------------|---|
| **Clúster de Event Hubs de 1 CU** | [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) | 5 | 2 | 10 | 500 GiB | 70 GiB | 4 | 1 |

Todos los clústeres de Event Hubs usan un tipo de máquina virtual [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) para sus nodos. Un tipo de máquina virtual D11_V2 consta de dos núcleos. Por lo tanto, el clúster de Event Hubs de 1 CU usa 5 máquinas virtuales D11_V2, lo que se traduce en que se usan 10 núcleos. A la hora de determinar el número de núcleos que se van a configurar para una cuota, use un múltiplo del número total de núcleos utilizados por 1 CU. Este cálculo refleja el número máximo de unidades de capacidad que permitirá a los usuarios usar al crear clústeres de Event Hubs. Por ejemplo, para configurar una cuota que permita a los usuarios crear un clúster con una capacidad de 2 CU, establezca la cuota en 20 núcleos.

> [!NOTE]
> **Solo en versión preliminar pública**: la versión disponible de Event Hubs en Azure Stack Hub solo admite la creación de clústeres de 1 CU. La versión de disponibilidad general (GA) de Event Hubs incluirá compatibilidad con diferentes opciones de configuración de CU.

## <a name="resource-provider-resource-consumption"></a>Consumo de recursos del proveedor de recursos  

El consumo de recursos por parte del proveedor de recursos de Event Hubs es constante e independiente del número o de los tamaños de los clústeres que los usuarios crean. En la tabla siguiente se muestra el uso de núcleos por parte del proveedor de recursos de Event Hubs en Azure Stack Hub y el consumo aproximado de recursos por parte de otros recursos. El proveedor de recursos de Event Hubs usa un tipo de máquina virtual [D2_V2](../user/azure-stack-vm-sizes.md#dv2-series) para la implementación.

|                                  | Tipo de máquina virtual | Nodos de clúster | Núcleos | Almacenamiento de máquinas virtuales | Memoria | Cuentas de almacenamiento | Direcciones IP públicas |
|----------------------------------|---------|---------------|-------|------------|--------|------------------|------------|
| **Proveedor de recursos de Event Hubs** | D2_V2   | 3     | 6     | 300 GiB | 21 GiB | 2 | 1 |

> [!IMPORTANT]
> El consumo del proveedor de recursos no se controla mediante cuotas. No es necesario que quepan los núcleos que el proveedor de recursos usa en las configuraciones de cuota. Los proveedores de recursos se instalan mediante una suscripción de administrador. La suscripción no impone límites de consumo de recursos en los operadores al instalar los proveedores de recursos necesarios.

## <a name="total-resource-consumption"></a>Consumo de recursos totales

La capacidad total consumida por el servicio Event Hubs incluye el consumo de recursos por parte del proveedor de recursos y el consumo por parte de los clústeres creados por el usuario.

En la tabla siguiente se muestra el consumo total de Event Hubs con distintas configuraciones, independientemente de si se administran por cuota. Estos números se basan en los consumos de clúster de Event Hubs y el proveedor de recursos presentados anteriormente. Puede calcular fácilmente el uso total de Azure Stack Hub para otros tamaños de implementación mediante estos ejemplos.

|                                      | Núcleos | Almacenamiento de máquinas virtuales | Memoria  | Cuentas de almacenamiento | Almacenamiento total\* | Direcciones IP públicas\*\* |
|--------------------------------------|-------|------------|---------|------------------|---------------|------------|
| **Clúster de 1-CU + proveedor de recursos** | 16    | 800 GiB    | 91 GiB  | 6                | Variable    | 2 |
| **Clúster de 2-CU + proveedor de recursos** | 26    | 1,3 TB     | 161 GiB | 10               | Variable    | 2 |
| **Clúster de 4-CU + proveedor de recursos** | 46    | 2,3 TB     | 301 GiB | 18               | Variable    | 2 |

\* La velocidad de los bloques de datos de entrada (mensajes y eventos) y la retención de mensajes son dos factores importantes que contribuyen al almacenamiento que usan los clústeres de Event Hubs. Por ejemplo, si la retención de mensajes se establece en 7 días al crear un centro de eventos y los mensajes se ingieren a una velocidad de 1 MB/s, el almacenamiento aproximado utilizado es de 604 GB (1 MB x 60 segundos x 60 minutos x 24 horas X 7 días). Si los mensajes se envían a una velocidad de 20 MB/s con una retención de 7 días, el consumo de almacenamiento aproximado es de 12 TB. Asegúrese de tener en cuenta la velocidad de datos de entrada y el tiempo de retención para conocer completamente el consumo de la capacidad de almacenamiento.

\*\* Las direcciones IP públicas se consumen de la [cuota de red proporcionada como parte de la suscripción](azure-stack-quota-types.md#network-quota-types).

## <a name="next-steps"></a>Pasos siguientes

Complete los [requisitos previos para instalar Event Hubs en Azure Stack Hub](event-hubs-rp-prerequisites.md) antes de comenzar el proceso de instalación.
