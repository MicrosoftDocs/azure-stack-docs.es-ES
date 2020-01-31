---
title: Implementación de aplicaciones virtuales de red de alta disponibilidad en Azure Stack Hub
description: Aprenda a implementar aplicaciones virtuales de red de alta disponibilidad en Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: cdcf4eca724e4b8b215b0fcb32e2b81969ef10d0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884151"
---
# <a name="deploy-highly-available-network-virtual-appliances-on-azure-stack-hub"></a>Implementación de aplicaciones virtuales de red de alta disponibilidad en Azure Stack Hub

En este artículo se muestra cómo implementar un conjunto de aplicaciones virtuales de red (NVA) de alta disponibilidad en Azure Stack Hub. Una NVA se usa normalmente para controlar el flujo del tráfico de red desde una red perimetral, conocida también como DMZ, hasta otras redes o subredes. El artículo incluye arquitecturas de ejemplo de solo entrada, solo salida y de entrada y salida.

Hay NVA de distintos proveedores disponibles en el [Marketplace de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items). Use uno de ellos para obtener un rendimiento óptimo.

La arquitectura tiene los siguientes componentes.

## <a name="networking-and-load-balancing"></a>Redes y equilibrio de carga

-   **Red virtual y subredes**. Todas las máquinas virtuales se implementan en una red virtual que se puede dividir en subredes. Cree una subred independiente para cada nivel.

-   **Equilibrador de carga de capa 7.** Como Application Gateway todavía no está disponible en Azure Stack Hub, existen alternativas disponibles en el [Marketplace de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items) como: [KEMP LoadMaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) o [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Equilibradores de carga.** Use [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) para distribuir el tráfico de red desde el nivel web al nivel de empresa y desde el nivel de empresa a SQL Server.

-   **Grupos de seguridad de red** (NSG). Use NSG para restringir el tráfico de red dentro de la red virtual. Por ejemplo, en la arquitectura de tres niveles que se muestra aquí, el nivel de base de datos no acepta el tráfico procedente del front-end web, solo el procedente del nivel de empresa y la subred de administración.

-   **Rutas definidas por el usuario.** Use [*rutas definidas por el usuario*](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview/) (UDR) para enrutar el tráfico al equilibrador de carga específico.

En este artículo se supone que el usuario tiene un conocimiento básico de las redes de Azure Stack Hub.

## <a name="architecture-diagrams"></a>Diagramas de arquitectura

Una aplicación virtual de red se puede implementar en una red perimetral en muchas arquitecturas diferentes. Por ejemplo, en la siguiente ilustración se muestra el uso de una única aplicación virtual de red para la entrada.

![Captura de pantalla de una descripción de publicación de redes sociales generada automáticamente](./media/iaas-architecture-nva-architecture/image1.png)

En esta arquitectura, la aplicación de red virtual proporciona un límite de red segura, ya que comprueba todo el tráfico de red de entrada y salida y solo deja pasar aquel que cumple las reglas de seguridad de la red. El hecho de que todo el tráfico de la red deba pasar por la aplicación virtual de red significa que dicha aplicación es un único punto de error en la red. Así que, si se produce un error en la aplicación de red virtual, no habrá ninguna otra ruta para el tráfico de red ni ninguna subred back-end disponible.

Para conseguir que una aplicación virtual de red tenga una alta disponibilidad, implemente varias de estas aplicaciones en un conjunto de disponibilidad.

Las arquitecturas siguientes describen los recursos y la configuración que son necesarios para dotar a las aplicaciones virtuales de red de una alta disponibilidad:

| Solución | Ventajas | Consideraciones |
| --- | --- | --- |
| Entrada con NVA de capa 7 | Todos los nodos NVA están activos. | Requiere una aplicación virtual de red que pueda terminar las conexiones y usar SNAT.<br>Requiere un conjunto independiente de NVA para el tráfico procedente de la red empresarial o Internet y de Azure Stack Hub.<br>Solo puede usarse con el tráfico que se origina fuera de Azure Stack Hub.  |
| Salida con NVA de capa 7 | Todos los nodos NVA están activos. | Requiere una aplicación virtual de red que pueda terminar las conexiones e implemente la traducción de direcciones de red de origen (SNAT). |
| Entrada y salida con NVA de capa 7 | Todos los nodos están activos.<br>Puede controlar el tráfico que se origina en Azure Stack Hub. | Requiere una aplicación virtual de red que pueda terminar las conexiones y usar SNAT.<br>Requiere un conjunto independiente de NVA para el tráfico procedente de la red empresarial o Internet y de Azure Stack Hub. |

## <a name="ingress-with-layer-7-nvas"></a>Entrada con NVA de capa 7

En la siguiente ilustración se muestra una arquitectura de alta disponibilidad que implementa una red perimetral de entrada detrás de un equilibrador de carga accesible desde Internet. Esta arquitectura está diseñada para proporcionar conectividad a las cargas de trabajo de Azure Stack Hub con tráfico de capa 7, como HTTP o HTTPS:

![Captura de pantalla de una descripción de mapa generada automáticamente](./media/iaas-architecture-nva-architecture/image2.png)

La ventaja de esta arquitectura es que todas las aplicaciones virtuales de red están activas y, si una no funciona correctamente, el equilibrador de carga dirige el tráfico de red a la otra. Ambas aplicaciones virtuales de red enrutan el tráfico al equilibrador de carga interno, de modo que, mientras una aplicación virtual de red esté activa, el tráfico seguirá fluyendo. Las aplicaciones virtuales de red son necesarias para terminar el tráfico de SSL destinado a las máquinas virtuales de nivel web. Estas aplicaciones virtuales de red no se pueden ampliar para administrar el tráfico de la red empresarial puesto que este necesita otro conjunto dedicado de aplicaciones virtuales de red con sus propias rutas de red.

## <a name="egress-with-layer-7-nvas"></a>Salida con NVA de capa 7

La entrada con arquitectura de NVA de capa 7 puede ampliarse para proporcionar una red perimetral de salida para las solicitudes que se originan en la carga de trabajo de Azure Stack Hub. La siguiente arquitectura está diseñada para proporcionar alta disponibilidad de las aplicaciones virtuales de red en la red perimetral para el tráfico de capa 7, como HTTP o HTTPS:

![Captura de pantalla de una descripción de teléfono móvil generada automáticamente](./media/iaas-architecture-nva-architecture/image3.png)

En esta arquitectura, todo el tráfico que se origina en Azure Stack Hub se enruta a un equilibrador de carga interno. El equilibrador de carga distribuye las solicitudes salientes entre un conjunto de aplicaciones virtuales de red. Estas aplicaciones virtuales de red dirigen el tráfico a Internet mediante sus direcciones IP públicas individuales.

## <a name="ingress-egress-with-layer-7--nvas"></a>Entrada y salida con NVA de capa 7

En las dos arquitecturas de entrada y salida, había una red perimetral independiente para la entrada y la salida. La siguiente arquitectura muestra cómo crear una red perimetral que puede usarse tanto para la entrada como para la salida con el tráfico de capa 7, como HTTP o HTTPS:

![Captura de pantalla de una descripción de publicación de redes sociales generada automáticamente](./media/iaas-architecture-nva-architecture/image4.png)

En la entrada y salida con la arquitectura de NVA de capa 7, las NVA procesan las solicitudes entrantes de un equilibrador de carga de capa 7. Las aplicaciones virtuales de red también procesan las solicitudes que salen de las máquinas virtuales de carga de trabajo en el grupo back-end del equilibrador de carga. Como el tráfico entrante se enruta con un equilibrador de carga de capa 7 y el tráfico saliente lo hace con un equilibrador de carga básico de Azure Stack Hub (SLB), las aplicaciones virtuales de red son las responsables de mantener la afinidad de la sesión. Es decir, el equilibrador de carga de capa 7 mantiene una asignación de solicitudes entrantes y salientes, de forma que pueda reenviar la respuesta correcta al solicitante original. Sin embargo, el equilibrador de carga interno no tiene acceso a las asignaciones del equilibrador de carga de capa 7, así que emplea su propia lógica para enviar las respuestas a las aplicaciones virtuales de red. Es posible que el equilibrador de carga envíe una respuesta a una aplicación virtual de red que inicialmente no recibió la solicitud del equilibrador de carga de capa 7. En este caso, las aplicaciones virtuales de red deben comunicarse y transferirse la respuesta entre ellos para que la aplicación virtual de red correcta pueda reenviar la respuesta al equilibrador de carga de capa 7.

> [!Note]  
> Otra manera de resolver el problema del enrutamiento asimétrico es asegurarse de que las aplicaciones virtuales de red realicen la traducción de las direcciones de red de origen (SNAT). De esta forma, se reemplazaría la dirección IP de origen original del solicitante por una de las direcciones IP de la aplicación virtual de red usada en el flujo de entrada. Con ello se garantiza que pueda usar varias aplicaciones virtuales de red al mismo tiempo sin perder la simetría de la ruta.

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de las máquinas virtuales de Azure Stack Hub, consulte [Características de las máquinas virtuales de Azure Stack Hub](azure-stack-vm-considerations.md).  
- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
