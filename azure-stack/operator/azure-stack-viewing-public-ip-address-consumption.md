---
title: Visualización del consumo de direcciones IP públicas en Azure Stack | Microsoft Docs
description: Los administradores pueden ver el consumo de direcciones IP públicas de una región
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
origin.date: 01/14/2019
ms.date: 03/18/2019
ms.author: v-jay
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2cb1dde60f8d8aa76e0b678347df9be120c39e7c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308733"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Visualización del consumo de direcciones IP públicas en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Los administradores de la nube pueden ver:
 - El número de direcciones IP públicas que se han asignado a los inquilinos.
 - El número de direcciones IP públicas que aún están disponibles para su asignación.
 - El porcentaje de direcciones IP públicas que se han asignado en esa ubicación.

El icono **Public IP pools usage** (Uso de grupos de IP públicas) muestra el número de direcciones IP públicas que se han consumido en los grupos de direcciones IP públicas. En cada dirección IP, el icono muestra el uso de instancias de VM IaaS de inquilino, los servicios de la infraestructura del generador y los recursos de las direcciones IP públicas que los usuarios crearon explícitamente.

El propósito del icono es que los operadores de Azure Stack sepan el número de direcciones IP públicas que se usan en esta ubicación. Este número ayuda a los administradores a determinar si se están quedando sin algún recurso.

El elemento de menú **Direcciones IP públicas** de **Recursos de inquilinos** enumera solo las direcciones IP públicas que los *inquilinos han creado de manera explícita*. El elemento de menú se puede encontrar en el panel **Proveedores de recursos**, **Red**. El número de direcciones IP públicas **usadas** del icono **Uso de grupos de IP públicas** nunca coincide (es mayor) con el número del icono **Direcciones IP públicas** de **Recursos de inquilinos**.

## <a name="view-the-public-ip-address-usage-information"></a>Visualización de la información de uso de direcciones IP públicas

Para ver el número total de direcciones IP públicas que se han consumido en una región:

1. En el portal de administración de Azure Stack, seleccione **Todos los servicios**. Después, en la categoría **ADMINISTRACIÓN**, seleccione **Red**.
1. El panel **Red** muestra el icono **Uso de grupos de IP públicas** en la sección **Información general**.

![Panel Proveedor de recursos de red](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

El número de **Usadas** representa el número direcciones IP públicas asignadas de grupos de direcciones IP públicas. El número que aparece debajo de **Libres** representa el número de direcciones IP públicas de los grupos de direcciones IP públicas que no se ha asignado y, por consiguiente, siguen disponibles. El número que aparece al lado de **% usado** representa el número de direcciones usadas o asignadas, en forma de porcentaje del número total de direcciones IP públicas de los grupos de direcciones IP públicas que hay en la ubicación.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visualización de las direcciones IP públicas que han creado las suscripciones de los inquilinos

Seleccione **Direcciones IP públicas** en **Recursos de inquilinos**. Revise la lista de direcciones IP públicas creadas explícitamente por las suscripciones de los inquilinos en una región concreta.

![Direcciones IP públicas de inquilinos](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Puede ver que algunas direcciones IP públicas que se han asignado dinámicamente aparecen en la lista, pero aún no tienen una dirección asociada. El recurso de red se ha creado en el proveedor de recursos de red, pero aún no en la controladora de red.

La controladora de red no asigna una dirección al recurso hasta que este se enlaza a una interfaz, una tarjeta de interfaz de red (NIC), un equilibrador de carga o una puerta de enlace de red virtual. Cuando la dirección IP pública se enlaza a una interfaz, la controladora de red le asigna una dirección IP, que aparece en el campo **Dirección**.

## <a name="view-the-public-ip-address-information-summary-table"></a>Visualización de la tabla de resumen de la información acerca de las direcciones IP públicas

En varios casos, se asignan direcciones IP públicas que determinan si la dirección aparece en una lista o en otra.

| **Caso de asignación de dirección IP pública** | **Aparece en resumen de uso** | **Aparece en lista de direcciones IP públicas de inquilinos** |
| --- | --- | --- |
| La dirección IP pública dinámica aún no está asignada a un NIC o equilibrador de carga (temporal). |Sin  |Sí |
| La dirección IP pública dinámica está asignada a un NIC o equilibrador de carga. |Sí |Sí |
| La dirección IP pública estática está asignada a un NIC o equilibrador de carga del inquilino. |Sí |Sí |
| La dirección IP pública estática está asignada a un punto de conexión del servicio de la infraestructura del tejido. |Sí |Sin  |
| Dirección IP pública creada de manera implícita para instancias de VM de IaaS y usada para NAT saliente en la red virtual. Se crean en segundo plano cada vez que un inquilino crea una instancia de máquina virtual para que las máquinas virtuales pueden enviar información a Internet. |Sí |Sin  |

## <a name="next-steps"></a>Pasos siguientes

[Administración de cuentas de almacenamiento en Azure Stack](azure-stack-manage-storage-accounts.md)

<!-- Update_Description: wording update -->