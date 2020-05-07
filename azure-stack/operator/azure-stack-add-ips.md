---
title: Incorporación de direcciones IP públicas en Azure Stack Hub
description: Aprenda a agregar direcciones IP públicas a Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 04/20/2020
ms.author: justinha
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 8cd89a90cb29c802c79e900e07cdb50bfe9c0894
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847918"
---
# <a name="add-public-ip-addresses"></a>Incorporación de direcciones IP públicas

En este artículo, se hace referencia a las direcciones externas como direcciones IP públicas. En el contexto de Azure Stack Hub, una dirección IP pública es una dirección IP a la que se puede acceder desde fuera de Azure Stack Hub. Para los fines de este artículo, no es relevante si la red externa es pública y enrutable en Internet o si está en una intranet y utiliza un espacio de direcciones privado, ya que los pasos son los mismos.

> [!IMPORTANT]
> Los pasos descritos en este artículo se aplican solo a los sistemas que se implementaron con la versión 1809 o posterior del kit de herramientas de asociados. Los sistemas que se implementaron antes de la versión 1809 requieren que las listas de control de acceso (ACL) de la parte superior del rack (TOR) se actualicen para permitir el nuevo intervalo de grupos de la dirección IP virtual pública. Si se ejecutan configuraciones de conmutador anteriores, trabaje con el OEM para agregar las ACL de permiso apropiadas para el nuevo grupo de direcciones IP públicas o vuelva a configurar el conmutador con el kit de herramientas de asociados más reciente para evitar que se bloquee la nueva dirección IP pública.

## <a name="add-a-public-ip-address-pool"></a>Agregar un grupo de direcciones IP públicas
Puede agregar direcciones IP públicas a su sistema de Azure Stack Hub en cualquier momento después de la implementación inicial del sistema Azure Stack Hub. Puede obtener información para realizar esta operación en [Visualización del consumo de direcciones IP públicas en Azure Stack Hub](azure-stack-viewing-public-ip-address-consumption.md), para ver cuál es el uso actual y la disponibilidad de las direcciones IP públicas en Azure Stack Hub.

De modo general, el proceso para agregar un nuevo bloque de direcciones IP públicas a Azure Stack Hub es similar al siguiente:

 ![Agregar un flujo de IP](media/azure-stack-add-ips/flow.svg)

## <a name="obtain-the-address-block-from-your-provider"></a>Obtener el bloque de direcciones del proveedor
Lo primero que debe hacer es obtener el bloque de direcciones que quiere agregar a Azure Stack Hub. Según de dónde obtenga el bloque de direcciones, tenga en cuenta cuál es el plazo y administre este dato en función de la velocidad a la que consume las direcciones IP públicas en Azure Stack Hub.

> [!IMPORTANT]
> Azure Stack Hub aceptará cualquier bloque de direcciones que proporcione, siempre que sea un bloque de direcciones válido y no se superponga con un intervalo de direcciones ya existente en Azure Stack Hub. Asegúrese de obtener un bloque de direcciones válido que sea enrutable y que no se superponga con la red externa a la que se conecta Azure Stack Hub. Después de agregar el intervalo a Azure Stack Hub, no podrá quitarlo.

## <a name="add-the-ip-address-range-to-azure-stack-hub"></a>Incorporación del intervalo de direcciones IP a Azure Stack Hub

1. En un explorador web, vaya al panel de su portal del administrador. Para este ejemplo, usaremos `https://adminportal.local.azurestack.external`.
2. Inicie sesión en el portal del administrador de Azure Stack Hub como operador de la nube.
3. En el panel predeterminado, busque la lista Administración de regiones y haga clic en la región que quiere administrar. En este ejemplo, se usa local.
4. Busque el icono Proveedores de recursos y haga clic en el proveedor de recursos de red.
5. Haga clic en el icono para usar los grupos de IP públicas.
6. Haga clic en el botón Agregar grupo de IP.
7. Escriba un nombre para el grupo de IP. El nombre que elija le ayudará a identificar fácilmente el grupo de IP. Es una buena opción que el nombre sea el mismo que el rango de direcciones, pero no es necesario.
8. Escriba el bloque de direcciones que quiere agregar en la notación CIDR. Por ejemplo: 192.168.203.0/24
9. Cuando proporcione un intervalo de CIDR válido en el campo Intervalo de direcciones (bloque CIDR), los campos Dirección IP inicial, Dirección IP final y Direcciones IP disponibles se completarán automáticamente. Recuerde que son de solo lectura y que se generan automáticamente; por lo tanto, no podrá cambiar estos campos sin modificar el valor del campo Intervalo de direcciones.
10. Después de revisar la información en la hoja y confirmar que todo está bien, seleccione **Aceptar** para confirmar el cambio y agregar el intervalo de direcciones a Azure Stack Hub.


## <a name="next-steps"></a>Pasos siguientes 
[Revisar las acciones de nodo de unidad de escalado](azure-stack-node-actions.md)
