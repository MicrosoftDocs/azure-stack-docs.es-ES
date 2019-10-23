---
title: Tipos de cuota en Azure Stack | Microsoft Docs
description: Vea y edite los diferentes tipos de cuota disponibles para los servicios y recursos de Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: e3e7ae6cc29756486ae5c292de6fea7e5259ecc1
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283459"
---
# <a name="quota-types-in-azure-stack"></a>Tipos de cuota en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Las [cuotas](service-plan-offer-subscription-overview.md#plans) definen los límites de recursos que puede aprovisionar o consumir una suscripción de usuario. Por ejemplo, una cuota podría permitir que un usuario creara hasta cinco máquinas virtuales. Cada recurso puede tener sus propios tipos de cuotas.

> [!IMPORTANT]
> Pueden pasar hasta dos horas hasta que las nuevas cuotas estén disponibles en el portal del usuario o antes de que se aplique una cuota modificada.

## <a name="compute-quota-types"></a>Tipos de cuota de proceso

| **Tipo** | **Valor predeterminado** | **Descripción** |
| --- | --- | --- |
| Número máximo de máquinas virtuales | 50 | El número máximo de máquinas virtuales que puede crear una suscripción en esta ubicación. |
| Número máximo de núcleos de máquina virtual | 100 | El número máximo de núcleos que puede crear una suscripción en esta ubicación (por ejemplo, una máquina virtual A3 tiene cuatro núcleos). |
| Número máximo de conjuntos de disponibilidad | 10 | El número máximo de conjuntos de disponibilidad que se pueden crear en esta ubicación. |
| Número máximo de conjuntos de escalado de máquinas virtuales | 100 | El número máximo de conjuntos de escalado de máquinas virtuales que se pueden crear en esta ubicación. |
| Capacidad máxima (en GB) de disco administrado estándar | 2048 | La capacidad máxima de los discos administrados estándar que se pueden crear en esta ubicación. |
| Capacidad máxima (en GB) de disco administrado premium | 2048 | La capacidad máxima de discos administrados premium que se pueden crear en esta ubicación. |

> [!NOTE]  
> La capacidad máxima de discos no administrados (blobs en páginas) es independiente de la cuota de disco administrado. Puede establecer este valor en **Storage quotas** (Cuotas de almacenamiento).

## <a name="storage-quota-types"></a>Tipos de cuotas de almacenamiento

| **Elemento** | **Valor predeterminado** | **Descripción** |
| --- | --- | --- |
| Capacidad máxima (GB) |2048 |Capacidad de almacenamiento total (incluidos los blobs y todas las instantáneas, tablas y colas asociadas) que puede consumir una suscripción en esta ubicación. |
| Número total de cuentas de almacenamiento |20 |El número máximo de cuentas de almacenamiento que puede crear una suscripción en esta ubicación. |

> [!NOTE]  
> La capacidad máxima de los discos administrados es independiente de la cuota de almacenamiento total. Puede establecer este valor en **Compute quotas** (Cuotas de proceso).

## <a name="network-quota-types"></a>Tipos de cuota de red

| **Elemento** | **Valor predeterminado** | **Descripción** |
| --- | --- | --- |
| Número máximo de redes virtuales |50 |El número máximo de redes virtuales que puede crear una suscripción en esta ubicación. |
| Número máximo de puertas de enlace de red virtual |1 |El número máximo de puertas de enlace de red virtual (puertas de enlace de VPN) que puede crear una suscripción en esta ubicación. |
| Número máximo de conexiones de red |2 |El número máximo de conexiones de red (punto a punto o sitio a sitio) que puede crear una suscripción en todas las puertas de enlace de red virtual de esta ubicación. |
| Número máximo de direcciones IP públicas |50 |El número máximo de direcciones IP públicas que puede crear una suscripción en esta ubicación. |
| Número máximo de NIC |100 |El número máximo de interfaces de red que puede crear una suscripción en esta ubicación. |
| Número máximo de equilibradores de carga |50 |El número máximo de equilibradores de carga que puede crear una suscripción en esta ubicación. |
| Número máximo de grupos de seguridad de red |50 |El número máximo de grupos de seguridad de red que puede crear una suscripción en esta ubicación. |

## <a name="view-an-existing-quota"></a>Visualización de una cuota existente

Hay dos maneras diferentes de ver una cuota existente:

### <a name="plans"></a>Planes

1. En el panel de navegación izquierdo del portal de administración, seleccione **Plans** (Planes).
2. Haga clic en el nombre del plan cuyos detalles desea ver para seleccionarlo.
3. En la hoja que se abre, seleccione **Services and quotas** (Servicios y cuotas).
4. Haga clic en la columna **Name** (Nombre) para seleccionar la columna que quiere ver.

    [![Cuotas](media/azure-stack-quota-types/quotas1sm.png "Ver cuotas")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Proveedores de recursos

1. En el panel predeterminado del portal de administración, busque el icono **Proveedores de recursos**.
2. Seleccione el servicio con la cuota que quiere ver, como **Compute**, **Network** o **Storage**.
3. Seleccione **Cuotas** y luego seleccione la cuota que desea ver.

## <a name="edit-a-quota"></a>Edición de una cuota

Hay dos maneras diferentes de editar una cuota:

### <a name="edit-a-plan"></a>Edición de un plan

1. En el panel de navegación izquierdo del portal de administración, seleccione **Plans** (Planes).
2. Haga clic en el nombre del plan cuya cuota quiere editar para seleccionarlo.
3. En la hoja que se abre, seleccione **Services and quotas** (Servicios y cuotas).
4. Haga clic en la columna **Name** (Nombre) para seleccionar la cuota que quiere editar.
    [![Cuotas](media/azure-stack-quota-types/quotas1sm.png "Ver cuotas")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. En la hoja que se abre, seleccione **Edit in Compute** (Editar en Compute), **Edit in Network** (Editar en Network) o **Edit in Storage** (Editar en Storage).
    ![Cuotas](media/azure-stack-quota-types/quotas3.png "Ver cuotas")

Como alternativa, puede seguir este procedimiento para editar una cuota:

1. En el panel predeterminado del portal de administración, busque el icono **Resource providers** (Proveedores de recursos).
2. Seleccione el servicio con la cuota que desea ver como **Compute**, **Network** o **Storage**.
3. A continuación seleccione **Cuotas** y luego la cuota que desea cambiar.
4. En el panel **Set Storage quotas** (Establecer cuotas de Storage), **Set Compute quotas** (Establecer cuotas de Compute) o **Set Network quotas** (Establecer cuotas de Network) (según el tipo de cuota que haya decidido editar), edite los valores y, luego, seleccione **Save** (Guardar).

### <a name="edit-original-configuration"></a>Edición de la configuración original
  
Puede elegir editar la configuración original de una cuota en lugar de [utilizar un plan complementario](create-add-on-plan.md). Al editar una cuota, la nueva configuración se aplica automáticamente de forma global a todos los planes que utilizan esa cuota y a todas las suscripciones existentes que usan esos planes. La edición de una cuota es diferente a cuando se utiliza un plan complementario para proporcionar una cuota modificada a la que un usuario decide suscribirse.

Los nuevos valores para la cuota se aplican globalmente a todos los planes que utilizan la cuota modificada y a todas las suscripciones existentes que usan esos planes.

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre servicios, planes, ofertas y cuotas.](service-plan-offer-subscription-overview.md)
- [Crear cuotas durante la creación de un plan.](azure-stack-create-plan.md)
