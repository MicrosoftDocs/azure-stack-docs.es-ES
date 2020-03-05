---
title: Patrón para implementar una solución de retransmisión híbrida con Azure y Azure Stack Hub
description: Aprenda a usar los servicios de Azure y Azure Stack Hub para conectarse a recursos perimetrales o a dispositivos protegidos por firewalls.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 945aaf9fa9d422418718c87545c238239220bc06
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77688787"
---
# <a name="hybrid-relay-pattern"></a>Patrón de retransmisión híbrida

Aprenda a conectarse a recursos o dispositivos en el perímetro que están protegidos mediante firewalls mediante instancias de Azure Service Bus Relay.

## <a name="context-and-problem"></a>Contexto y problema

Los dispositivos perimetrales suelen estar detrás de un firewall corporativo o un dispositivo NAT. Aunque son seguros, es posible que no puedan comunicarse con la nube pública o con dispositivos perimetrales de otras redes corporativas. Puede que sea necesario exponer determinados puertos y funcionalidad a los usuarios de la nube pública de forma segura. 

## <a name="solution"></a>Solución

El patrón de retransmisión híbrida usa instancias de Azure Service Bus Relay para establecer un túnel de WebSockets entre dos puntos de conexión que no se pueden comunicar directamente. Los dispositivos que no están en el entorno local, pero que necesitan conectarse a un punto de conexión local, se conectarán a un punto de conexión de la nube pública. Este punto de conexión redirigirá el tráfico de las rutas predefinidas por un canal seguro. Un punto de conexión dentro del entorno local recibe el tráfico y lo enruta al destino correcto. 

![arquitectura de la solución de retransmisión híbrida](media/pattern-hybrid-relay/solution-architecture.png)

Así es como funciona la solución: 

1. Un dispositivo se conecta a la máquina virtual (VM) de Azure en un puerto predefinido.
2. El tráfico se reenvía a Service Bus Relay en Azure.
3. La máquina virtual de Azure Stack Hub, que ya ha establecido una conexión de larga duración con Service Bus Relay, recibe el tráfico y lo reenvía al destino.
4. El servicio o punto de conexión local procesa la solicitud. 

## <a name="components"></a>Componentes

Esta solución usa los siguientes componentes:

| Nivel | Componente | Descripción |
|----------|-----------|-------------|
| Azure | Azure VM | Una máquina virtual de Azure proporciona un punto de conexión accesible públicamente para el recurso local. |
| | Azure Service Bus Relay | Una [instancia de Azure Service Bus Relay](/azure/service-bus-relay/) proporciona la infraestructura para mantener el túnel y la conexión entre la máquina virtual de Azure y la máquina virtual de Azure Stack Hub.|
| Azure Stack Hub | Proceso | Una máquina virtual de Azure Stack Hub proporciona el lado servidor del túnel de retransmisión híbrida. |
| | Storage | El clúster del motor de AKS implementado en Azure Stack Hub proporciona un motor escalable y resistente para ejecutar el contenedor de Face API.|

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar esta solución:

### <a name="scalability"></a>Escalabilidad 

Este patrón solo permite asignaciones de puerto 1:1 en el cliente y el servidor. Por ejemplo, si se tuneliza el puerto 80 para un servicio del punto de conexión de Azure, no se puede usar para otro servicio. Las asignaciones de puertos deben planearse en consecuencia. Service Bus Relay y las máquinas virtuales deben escalarse adecuadamente para administrar el tráfico.

### <a name="availability"></a>Disponibilidad

Estos túneles y conexiones no son redundantes. Para garantizar alta disponibilidad, podría interesarle implementar el código de comprobación de errores. Otra opción es tener un grupo de máquinas virtuales conectadas con Service Bus Relay detrás de un equilibrador de carga.

### <a name="manageability"></a>Facilidad de uso

Esta solución puede abarcar muchos dispositivos y ubicaciones, lo que podría resultar complicado. Los servicios de IoT de Azure pueden poner en línea automáticamente nuevas ubicaciones y dispositivos y mantenerlos actualizados.

### <a name="security"></a>Seguridad

Este patrón, tal como se muestra, permite el acceso ilimitado a un puerto en un dispositivo interno desde el perímetro. Considere la posibilidad de agregar un mecanismo de autenticación al servicio en el dispositivo interno o delante del punto de conexión de retransmisión híbrida. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas presentados en este artículo:
- Este patrón usa instancias de Azure Service Bus Relay. Para más información, consulte la [documentación de Azure Service Bus Relay](/azure/service-bus-relay/).
- Consulte [Consideraciones sobre el diseño de aplicaciones híbridas](overview-app-design-considerations.md) para más información sobre los procedimientos recomendados y responder a preguntas adicionales.
- Consulte la información relativa a la [familia de productos y soluciones de Azure Stack](/azure-stack) para más información sobre toda la gama de productos y soluciones.

Cuando esté listo para probar la solución de ejemplo, continúe con la [guía de implementación de soluciones de retransmisión híbrida](https://aka.ms/hybridrelaydeployment). La guía de implementación proporciona instrucciones paso a paso para implementar y probar sus componentes.