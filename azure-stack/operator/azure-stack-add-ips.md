---
title: Agregar direcciones IP públicas en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo agregar direcciones IP públicas a Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 6d99e5b293f86f4bdb62d35fc111054f12d57172
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118720"
---
# <a name="add-public-ip-addresses"></a>Incorporación de direcciones IP públicas
*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*  

En este artículo, se hace referencia a las direcciones externas como direcciones IP públicas. En el contexto de Azure Stack, una dirección IP pública es una dirección IP a la que se puede acceder desde fuera de Azure Stack. Para los fines de este artículo, no es relevante si la red externa es pública y enrutable en Internet o si está en una intranet y utiliza un espacio de direcciones privado, ya que los pasos son los mismos.

## <a name="add-a-public-ip-address-pool"></a>Agregar un grupo de direcciones IP públicas
Puede agregar direcciones IP públicas a su sistema de Azure Stack en cualquier momento después de la implementación inicial del sistema Azure Stack. Puede obtener información para realizar esta operación en [View Public IP address consumption](azure-stack-viewing-public-ip-address-consumption.md) (Visualización del consumo de direcciones IP públicas), para ver cuál es el uso actual y la disponibilidad de las direcciones IP públicas en Azure Stack.

En un nivel alto, el proceso para agregar un nuevo bloque de direcciones IP públicas a Azure Stack tiene el siguiente aspecto:

 ![Agregar un flujo de IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Obtener el bloque de direcciones del proveedor
Lo primero que debe hacer es obtener el bloque de direcciones que quiere agregar a Azure Stack. Según de dónde obtenga el bloque de direcciones, tenga en cuenta cuál es el plazo y administre este dato en función de la velocidad a la que consume las direcciones IP públicas en Azure Stack.

> [!IMPORTANT]
> Azure Stack aceptará cualquier bloque de direcciones que proporcione, siempre que sea un bloque de direcciones válido y no se superponga con un intervalo de direcciones ya existente en Azure Stack. Asegúrese de obtener un bloque de direcciones válido que sea enrutable y que no se superponga con la red externa a la que se conecta Azure Stack. Una vez que agregue el intervalo a Azure Stack, no podrá eliminarlo.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Agregar el rango de direcciones IP a Azure Stack

1. En un explorador web, vaya al panel de su portal de administración. Para este ejemplo, usaremos https://adminportal.local.azurestack.external.
2. Inicie sesión en el portal de administración de Azure Stack como operador en la nube.
3. En el panel predeterminado, busque la lista Administración de regiones y haga clic en la región que quiere administrar. En este ejemplo, se usa local.
4. Busque el icono Proveedores de recursos y haga clic en el proveedor de recursos de red.
5. Haga clic en el icono para usar los grupos de IP públicas.
6. Haga clic en el botón Agregar grupo de IP.
7. Escriba un nombre para el grupo de IP. El nombre que elija le ayudará a identificar fácilmente el grupo de IP. Es una buena opción que el nombre sea el mismo que el rango de direcciones, pero no es necesario.
8. Escriba el bloque de direcciones que quiere agregar en la notación CIDR. Por ejemplo:  192.168.203.0/24
9. Cuando proporcione un intervalo de CIDR válido en el campo Intervalo de direcciones (bloque CIDR), los campos Dirección IP inicial, Dirección IP final y Direcciones IP disponibles se completarán automáticamente. Recuerde que son de solo lectura y que se generan automáticamente; por lo tanto, no podrá cambiar estos campos sin modificar el valor del campo Intervalo de direcciones.
10. Después de revisar la información en la hoja y confirmar que todo está bien, haga clic en **Aceptar** para confirmar el cambio y agregar el intervalo de direcciones a Azure Stack.


## <a name="next-steps"></a>Pasos siguientes 
[Revisar las acciones de nodo de unidad de escalado](azure-stack-node-actions.md)
