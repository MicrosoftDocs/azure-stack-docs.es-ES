---
title: Uso de Azure Portal con Azure Stack HCI
description: Visualización y administración de clústeres de Azure Stack HCI mediante Azure Portal.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/15/2020
ms.openlocfilehash: e261c54140ed62a7b2fd19588e2b3ffc3b3911b8
ms.sourcegitcommit: 0579942530284d63472fb346cf5ff55de328cea4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90560888"
---
# <a name="use-the-azure-portal-with-azure-stack-hci"></a>Uso de Azure Portal con Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se explica cómo conectarse a la parte de Azure Stack HCI de Azure Portal para obtener una vista global de los clústeres de Azure Stack HCI. Puede usar Azure Portal para administrar y supervisar los clústeres, incluso si la infraestructura física está hospedada en el entorno local. La supervisión basada en la nube elimina la necesidad de mantener un sistema de supervisión local y una base de datos, lo que reduce la complejidad de la infraestructura. También aumenta la escalabilidad mediante la carga de alertas y otra información directamente en Azure, que ya administra millones de objetos cada día.

## <a name="view-your-clusters-in-the-azure-portal"></a>Visualización de los clústeres en Azure Portal

Una vez que se registra un clúster de Azure Stack HCI, su recurso de Azure es visible en Azure Portal. Para verlo, primero inicie sesión en [Azure Portal](https://portal.azure.com). Si ya ha [registrado el clúster con Azure](../deploy/register-with-azure.md), debería ver un nuevo grupo de recursos con el nombre del clúster con el sufijo "-rg". Si no aparece el grupo de recursos de Azure Stack HCI, busque "HCI" y seleccione el clúster en el menú desplegable:

:::image type="content" source="media/azure-portal/azure-portal-search.png" alt-text="Busque "hci" en Azure Portal para encontrar el recurso de Azure Stack HCI":::

La página principal del servicio Azure Stack HCI muestra todos los clústeres junto con su grupo de recursos, la ubicación y la suscripción asociada.

:::image type="content" source="media/azure-portal/azure-portal-home.png" alt-text="Página principal del servicio Azure Stack HCI en Azure Portal":::

Haga clic en un recurso de Azure Stack HCI para ver la página de información general de ese recurso en la que se muestra un resumen detallado de los nodos de clúster y de servidor.

:::image type="content" source="media/azure-portal/azure-portal-overview.png" alt-text="Página de resumen de información general del recurso de Azure Stack HCI en Azure Portal":::

## <a name="view-the-activity-log"></a>Visualización del registro de actividad

El registro de actividad proporciona una lista de operaciones y eventos recientes del clúster junto con el estado, la hora, la suscripción asociada y el usuario que ha iniciado sesión. Puede filtrar los eventos por suscripción, gravedad, intervalo de tiempo, grupo de recursos y recurso.

:::image type="content" source="media/azure-portal/azure-portal-activity-log.png" alt-text="Pantalla del registro de actividad del recurso de Azure Stack HCI en Azure Portal":::

## <a name="configure-access-control"></a>Configuración del control de acceso

Use el control de acceso para comprobar el acceso de los usuarios, administrar roles y agregar y ver asignaciones de roles y de denegaciones.

:::image type="content" source="media/azure-portal/azure-portal-iam.png" alt-text="Pantalla de control de acceso del recurso de Azure Stack HCI en Azure Portal":::

## <a name="add-and-edit-tags"></a>Incorporación y edición de etiquetas

Las etiquetas son pares nombre-valor que permiten categorizar los recursos y ver una facturación consolidada mediante la aplicación de la misma etiqueta en varios recursos y grupos de recursos. Los nombres de las etiquetas no distinguen mayúsculas de minúsculas, mientras que sus valores distinguen mayúsculas de minúsculas. [Más información sobre las etiquetas](/azure/azure-resource-manager/management/tag-resources).

:::image type="content" source="media/azure-portal/azure-portal-tags.png" alt-text="Agregar o editar etiquetas para el recurso de Azure Stack HCI en Azure Portal":::

## <a name="compare-azure-portal-and-windows-admin-center"></a>Comparación entre Azure Portal y Windows Admin Center

A diferencia de Windows Admin Center, la experiencia de Azure Portal para Azure Stack HCI está diseñada para la supervisión de varios clústeres a escala global. Utilice la tabla siguiente como ayuda para determinar qué herramienta de administración es la adecuada para sus necesidades. En su conjunto, ofrecen un diseño coherente y son útiles en escenarios complementarios.

| Windows Admin Center | Azure portal |
| --------------- | --------------- |
| Hardware local perimetral y administración de máquinas virtuales siempre disponible | Administración a escala, características adicionales |
| Administración de la infraestructura de Azure Stack HCI | Administración de otros servicios de Azure |
| Supervisión y actualización de clústeres individuales | Supervisión y actualización a gran escala |
| Aprovisionamiento y administración manual de máquinas virtuales | Máquinas virtuales de autoservicio de Azure Arc |

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Conexión de Azure Stack HCl a Azure](../deploy/register-with-azure.md)
- [Supervisión de Azure Stack HCI con Azure Monitor](azure-monitor.md)
