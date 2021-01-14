---
title: Obtención de información de autenticación para Azure Stack Hub
description: Aprenda a obtener información de autenticación para Azure Stack Hub
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 1e762ab25250a6414087c1b22e911bb7a8d151ed
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974118"
---
# <a name="get-authentication-information-for-azure-stack-hub"></a>Obtención de información de autenticación para Azure Stack Hub

Para autenticarse en Azure Stack Hub es preciso especificar el identificador de la suscripción, el identificador del inquilino y la ubicación, y el punto de conexión de Resource Manager de Azure Stack Hub. Estos valores se pueden obtener en el [punto de conexión de Resource Manager de Azure Stack](./azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint) para Azure Stack Hub. También se puede obtener mediante los pasos que se describen en este artículo.

## <a name="values-needed-to-authenticate"></a>Valores necesarios para la autenticación

Necesita la siguiente información:

-   **Subscription ID**  

    Puede usar el identificador de suscripción para acceder a las ofertas de Azure Stack Hub.

-   **Id. de inquilino**

    Un directorio es un contenedor que alberga información sobre usuarios, aplicaciones, grupos y entidades de servicio. Un inquilino de directorio es una organización, como Microsoft o su propia empresa.

-   **Ubicación**

    La ubicación, o región, es un conjunto de centros de datos implementados dentro de un perímetro definido por la latencia y conectados a través de una red regional dedicada de baja latencia. Con Azure Stack Hub, la ubicación puede incluir un centro de datos local, en lugar de una región de Azure.

-   **Punto de conexión de Resource Manager de Azure Stack**

    Microsoft Azure Resource Manager es un marco de administración que permite a sus administradores implementar, administrar y supervisar recursos de Azure. Azure Resource Manager puede controlar estas tareas como grupo, en vez de individualmente, en una sola operación.

## <a name="get-the-subscription-id"></a>Obtención del identificador de la suscripción

Para obtener el identificador de la suscripción:

1.  Inicie sesión en el portal de usuarios de Azure Stack Hub.

2.  Seleccione **Todos los servicios**.

    > ![Identificador del inquilino del identificador de suscripción de la información de autenticación de Azure Stack Hub](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-infoa.png)

3.  Seleccione **Suscripciones**.

4.  Seleccione la suscripción que quiere usar.

5.  Copie el **identificador de suscripción** de **Información general**.

## <a name="get-the-tenant-id"></a>Obtención del identificador de inquilino

Para obtener el identificador del inquilino:

1.  Inicie sesión en el portal de usuarios de Azure Stack Hub.

2.  Mantenga el mouse sobre el nombre de usuario en la parte superior derecha de la hoja.

3.  El **identificador de directorio** es el identificador del inquilino.

## <a name="get-the-azure-resource-manager-endpoint"></a>Obtención del punto de conexión de Azure Resource Manager

El punto de conexión de Azure Resource Manager es el punto de conexión de metadatos para el servicio de administración e implementación de Azure Stack Hub. Proporciona una capa de administración que le permite crear, actualizar y eliminar recursos de su suscripción de Azure.

En el caso de un sistema integrado, la dirección URL del punto de conexión de Azure Resource Manager es:<br>`https://management.<location>.<fqdn>`

Para obtener el punto de conexión de metadatos que apunta a propiedades como el punto de conexión de la galería, el punto de conexión del grafo, el punto de conexión del portal, el punto de conexión del inicio de sesión y las audiencias, la dirección URL es: `<ResourceManager>/metadata/endpoints?api-version=1.0`

## <a name="next-steps"></a>Pasos siguientes

Más información sobre el uso de [Resource Manager de Azure Stack Hub](./azure-stack-version-profiles.md) con Azure Stack Hub.
