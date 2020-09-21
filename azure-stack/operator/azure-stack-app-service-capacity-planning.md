---
title: Planeamiento de la capacidad de los roles de servidor de App Service en Azure Stack Hub
description: Obtenga información sobre el planeamiento de la capacidad de los roles de servidor de App Service en Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2020
ms.openlocfilehash: ed29e43c7863b3bad3075ab97a9ce0085edb88c3
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573299"
---
# <a name="capacity-planning-for-app-service-server-roles-in-azure-stack-hub"></a>Planeamiento de la capacidad de los roles de servidor de App Service en Azure Stack Hub

Para configurar una implementación lista para producción de Azure App Service en Azure Stack Hub, debe planear la capacidad que espera que el sistema admita.  

En este artículo se ofrece una guía del número mínimo de instancias de proceso y SKU de proceso que debe usar para una implementación de producción.

> [!NOTE]
> La guía sobre la SKU de proceso recomendada para los roles se actualizó con la versión 2020 Q2 de Azure App Service en Azure Stack Hub para llevar a cabo implementaciones estándar en línea con las implementaciones de Azure.

Puede planear la estrategia de capacidad de App Service mediante estas instrucciones.

| Rol de servidor de App Service | Número mínimo recomendado de instancias | SKU de proceso recomendadas|
| --- | --- | --- |
| Controller | 2 | A4v2 |
| Front End | 2 | A4_v2 |
| Administración | 2 | D3_v2 |
| Publicador | 2 | A2_v2 |
| Roles de trabajo web: compartidos | 2 | A4_v2 |
| Roles de trabajo web: dedicados, pequeños | 2 por nivel | A1_v2 |
| Roles de trabajo web: dedicados, medios | 2 por nivel | A2_v2 |
| Roles de trabajo web: dedicados, grandes | 2 por nivel | A4_v2 |

## <a name="controller-role"></a>Rol de controlador

**Mínima recomendada**: Dos instancias de A4v2

El controlador de servicio de Azure App Service suele tener un consumo bajo de CPU, memoria y recursos de red. Sin embargo, para lograr una alta disponibilidad, debe tener dos controladores. Dos controladores también son el número máximo de controladores permitidos. Puede crear el segundo controlador de sitios web directamente desde el instalador durante la implementación.

## <a name="front-end-role"></a>Rol de front-end

**Mínima recomendada**: Dos instancias de A4v_2

El front-end enruta las solicitudes a los trabajos web según su disponibilidad. Para lograr una alta disponibilidad, debe tener más de un front-end, y en total puede tener más de dos. A fin de planear la capacidad, tenga en cuenta que cada núcleo puede atender aproximadamente 100 solicitudes por segundo.

## <a name="management-role"></a>Rol de administración

**Mínima recomendada**: Dos instancias de D3v2

El rol del modelo de implementación clásica de Azure App Service es responsable de los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos. El rol de servidor de administración solo suele requerir aproximadamente 4 GB de RAM en un entorno de producción. Sin embargo, puede experimentar niveles elevados de CPU cuando se realizan muchas tareas de administración (por ejemplo, la creación de sitios web). Para lograr una alta disponibilidad, debe tener más de un servidor asignado a este rol y al menos dos núcleos por servidor.

## <a name="publisher-role"></a>Rol de publicador

**Mínima recomendada**: Dos instancias de A2v2

Si muchos usuarios están publicando simultáneamente, el rol de publicador puede experimentar un uso elevado de CPU. Para lograr una alta disponibilidad, asegúrese de disponer de más de un rol de publicador. El publicador solo controla el tráfico FTP o FTPS.

## <a name="web-worker-role"></a>Rol de trabajo web

**Mínima recomendada**: Dos instancias de A4_v2

Para lograr una alta disponibilidad, debe tener al menos cuatro roles de trabajo web: dos para el modo de sitio web compartido y dos para cada nivel de trabajo dedicado que planee ofrecer. Los modos de proceso dedicado y compartido proporcionan diferentes niveles de servicio a los inquilinos. Puede que necesite más roles de trabajo web si tiene muchos clientes:

- Que usen niveles de trabajo de modo de proceso dedicado (que consumen gran cantidad de recursos).
- Que se ejecuten en modo de proceso compartido.

Una vez que un usuario crea un plan de App Service para la SKU de modo de proceso dedicado, el número de trabajos web especificado en el plan de App Service ya no está disponible para los usuarios.

Para proporcionar Azure Functions a los usuarios en el modelo de plan de consumo, debe implementar los roles de trabajo web compartidos.

Cuando decida el número de roles de trabajo web compartidos que utilizará, revise estas consideraciones:

- **Memoria**: es el recurso más crítico para un rol de trabajo web. Si no hay memoria suficiente, afecta al rendimiento del sitio web cuando se intercambia memoria virtual del disco. Cada servidor necesita aproximadamente 1,2 GB de RAM para el sistema operativo. La RAM por encima de este umbral puede utilizarse para ejecutar sitios web.
- **Porcentaje de sitios web activos**: por lo general, aproximadamente el cinco por ciento de las aplicaciones en una implementación de Azure App Service en Azure Stack Hub están activas. Sin embargo, el porcentaje de aplicaciones que están activas en un momento dado puede ser superior o inferior. Con una tasa de aplicaciones activas del cinco por ciento, el número máximo de aplicaciones que se pueden incluir en una instancia de Azure App Service en la implementación de Azure Stack Hub debe ser menor que el número de sitios activos multiplicado por 20 (5 x 20 = 100).
- **Superficie de memoria promedio**: la superficie de memoria promedio en las aplicaciones observada en entornos de producción es de aproximadamente 70 MB. Con esta cantidad, la memoria asignada en todas las máquinas virtuales o los equipos con el rol de trabajo web se puede calcular de la siguiente manera:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Por ejemplo, si hay 5000 aplicaciones en el entorno que ejecutan diez roles de trabajo web, cada máquina virtual con el rol de trabajo web debe tener 7060 MB de memoria RAM:

   `5,000 * 70 * 0.05 - (10 * 1044) = 7060 (= about 7 GB)`

   Para obtener información sobre cómo agregar más instancias de trabajo, consulte [Incorporación de más roles de trabajo](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>Consideraciones adicionales para los trabajadores dedicados durante la actualización y el mantenimiento

Durante la actualización y mantenimiento de los trabajos, Azure App Service en Azure Stack Hub llevará a cabo mantenimiento en un 20 % de cada nivel de trabajo en cualquier momento.  Por lo tanto, los administradores de la nube siempre deben mantener un grupo del 20 % de los roles de trabajo sin asignar por nivel de trabajo a fin de garantizar que sus inquilinos no experimentan ninguna pérdida de servicio durante la actualización y el mantenimiento.  Por ejemplo, si tiene diez roles de trabajo en un nivel de trabajo, debe asegurarse de que dos se dejan sin asignar, para permitir la actualización y el mantenimiento. Si los diez roles de trabajo quedan asignados, debe ampliar el nivel de trabajo para mantener un grupo de roles de trabajo sin asignar. 

Durante la actualización y el mantenimiento, Azure App Service moverá las cargas de trabajo a los roles de trabajo sin asignar para garantizar que las cargas siguen funcionando. Si no hay ningún rol de trabajo sin asignar disponible durante la actualización, es posible que se produzca un tiempo de inactividad en la carga de trabajo del inquilino. En lo que respecta a los nodos de trabajo compartidos, los clientes no tienen que aprovisionar más, ya que el servicio asignará automáticamente las aplicaciones de inquilino a los nodos de trabajo disponibles. Para lograr una alta disponibilidad, hay un requisito mínimo de dos roles de trabajo en este nivel.

Los administradores de la nube pueden supervisar su asignación de nivel de trabajo en el área de administración de App Service del portal del administrador de Azure Stack Hub. Vaya a App Service y, a continuación, seleccione Niveles de trabajo en el panel izquierdo. En la tabla Niveles de trabajo se muestran el nombre del nivel de trabajo, el tamaño, la imagen utilizada, el número de trabajos disponibles (no asignados), el número total de trabajos en cada nivel y el estado general del nivel de trabajo.

![Administración de App Service: niveles de trabajo][1]

## <a name="file-server-role"></a>Rol de servidor de archivos

Para el rol de servidor de archivos, puede usar un servidor de archivos independiente para desarrollo y pruebas. Por ejemplo, si implementa Azure App Service en el Kit de desarrollo de Azure Stack (ASDK), puede usar esta [plantilla](https://aka.ms/appsvconmasdkfstemplate).  Para fines de producción, debe utilizar un servidor de archivos preconfigurado de Windows o un servidor preconfigurado que no sea de Windows.

En entornos de producción, el rol de servidor de archivos experimenta una intensa actividad de E/S de disco. Puesto que contiene todos los archivos de contenido y de aplicación para los sitios web de usuario, debe configurar previamente uno de los siguientes recursos para que ejerza este rol:

- Servidor de archivos de Windows
- Clúster de servidores de archivos de Windows
- Servidor de archivos que no sea de Windows
- Clúster de servidores de archivos que no son de Windows
- Dispositivo NAS (almacenamiento conectado a la red)

Para obtener más información, consulte [Aprovisionamiento de un servidor de archivos](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Pasos siguientes

[Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png