---
title: Administración de recursos de red
titleSuffix: Azure Stack Hub
description: Aprenda a administrar los recursos de red, incluido el grupo de direcciones MAC y el consumo de direcciones IP públicas en una región.
author: PatAltimore
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: patricka
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: f4fc98a7fae3a58d9467d8c1cafe46fcbef858fc
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868733"
---
# <a name="manage-network-resources-in-azure-stack-hub"></a>Administración de recursos de red en Azure Stack Hub

## <a name="mac-address-pool"></a>Grupo de direcciones MAC

Azure Stack Hub usa un grupo de direcciones MAC estáticas para generar y asignar automáticamente una dirección MAC a máquinas virtuales. Este grupo de direcciones MAC se generan automáticamente durante la implementación. Se usa el intervalo siguiente:

- StartMacAddress: 00-1D-D8-B7-00-00
- EndMacAddress: 00-1D-D8-F4-FF-FF

> [!Note]  
> Este grupo de direcciones MAC es el mismo en todos los sistemas de Azure Stack Hub y no se puede configurar.

En función de cómo se conectan las redes virtuales con las redes corporativas existentes, cabe esperar que haya direcciones MAC duplicadas de máquinas virtuales.

Para más información sobre el uso de grupos de direcciones MAC, use el cmdlet [Get-AzsMacAddressPool](/powershell/module/azs.fabric.admin/get-azsmacaddresspool) en el módulo de PowerShell del administrador de Azure Stack Hub.

## <a name="view-public-ip-address-consumption-in-azure-stack-hub"></a>Visualización del consumo de direcciones IP públicas en Azure Stack Hub

Los administradores de la nube pueden ver:
 - El número de direcciones IP públicas que se han asignado a los inquilinos.
 - El número de direcciones IP públicas que aún están disponibles para su asignación.
 - El porcentaje de direcciones IP públicas que se han asignado en esa ubicación.

El icono **Public IP pools usage** (Uso de grupos de IP públicas) muestra el número de direcciones IP públicas que se han consumido en los grupos de direcciones IP públicas. En cada dirección IP, el icono muestra el uso de instancias de VM IaaS de inquilino, los servicios de la infraestructura del generador y los recursos de las direcciones IP públicas que los usuarios crearon explícitamente.

El propósito del icono es que los operadores de Azure Stack Hub sepan el número de direcciones IP públicas que se usan en esta ubicación. Este número ayuda a los administradores a determinar si se están quedando sin este recurso.

El elemento de menú **Direcciones IP públicas** de **Recursos de inquilinos** enumera solo las direcciones IP públicas que los *inquilinos han creado de manera explícita*. El elemento de menú se puede encontrar en el panel **Proveedores de recursos** -> **Red**. El número de direcciones IP públicas **usadas** del icono **Uso de grupos de IP públicas** nunca coincide (es mayor) con el número del icono **Direcciones IP públicas** de **Recursos de inquilinos**.

### <a name="view-the-public-ip-address-usage-information"></a>Visualización de la información de uso de direcciones IP públicas

Para ver el número total de direcciones IP públicas que se han consumido en una región:

1. En el portal del administrador de Azure Stack Hub, seleccione **All services** (Todos los servicios). Después, en la categoría **ADMINISTRACIÓN**, seleccione **Red**.
1. El panel **Red** muestra el icono **Uso de grupos de IP públicas** en la sección **Información general**.

    ![Panel Proveedor de recursos en el portal de administración de Azure Stack Hub](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-01.png)

El número de **Usadas** representa el número direcciones IP públicas asignadas de grupos de direcciones IP públicas. El número que aparece debajo de **Libres** representa el número de direcciones IP públicas de los grupos de direcciones IP públicas que no se ha asignado y, por consiguiente, siguen disponibles. El número que aparece al lado de **% usado** representa el número de direcciones usadas o asignadas, en forma de porcentaje del número total de direcciones IP públicas de los grupos de direcciones IP públicas que hay en la ubicación.

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visualización de las direcciones IP públicas que han creado las suscripciones de los inquilinos

Seleccione **Direcciones IP públicas** en **Recursos de inquilinos**. Revise la lista de direcciones IP públicas creadas explícitamente por las suscripciones de los inquilinos en una región concreta.

![Direcciones IP públicas de inquilino en el portal de administración de Azure Stack Hub](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-02.png)

Puede ver que algunas direcciones IP públicas que se han asignado dinámicamente aparecen en la lista, pero aún no tienen una dirección asociada. El recurso de red se ha creado en el proveedor de recursos de red, pero aún no en la controladora de red.

La controladora de red no asigna una dirección al recurso hasta que este se enlaza a una interfaz, una tarjeta de interfaz de red (NIC), un equilibrador de carga o una puerta de enlace de red virtual. Cuando la dirección IP pública se enlaza a una interfaz, la controladora de red le asigna una dirección IP, que aparece en el campo **Dirección**.

### <a name="view-the-public-ip-address-information-summary-table"></a>Visualización de la tabla de resumen de la información acerca de las direcciones IP públicas

En varios casos, se asignan direcciones IP públicas que determinan si la dirección aparece en una lista o en otra.

| **Caso de asignación de dirección IP pública** | **Aparece en resumen de uso** | **Aparece en lista de direcciones IP públicas de inquilinos** |
| --- | --- | --- |
| La dirección IP pública dinámica aún no está asignada a un NIC o equilibrador de carga (temporal). |No |Sí |
| La dirección IP pública dinámica está asignada a un NIC o equilibrador de carga. |Sí |Sí |
| La dirección IP pública estática está asignada a un NIC o equilibrador de carga del inquilino. |Sí |Sí |
| La dirección IP pública estática está asignada a un punto de conexión del servicio de la infraestructura del tejido. |Sí |No |
| Dirección IP pública creada de manera implícita para instancias de VM de IaaS y usada para NAT saliente en la red virtual. Se crean en segundo plano cada vez que un inquilino crea una instancia de máquina virtual para que las máquinas virtuales pueden enviar información a Internet. |Sí |No |

## <a name="next-steps"></a>Pasos siguientes

[Administración de cuentas de almacenamiento en Azure Stack Hub](azure-stack-manage-storage-accounts.md)
