---
title: Consideraciones sobre el diseño de aplicaciones híbridas al compilar en Azure y Azure Stack
description: Consideraciones al compilar una aplicación híbrida para la nube y el perímetro inteligentes.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a5d282a45f340461b62acb3462cfb84822e0c2be
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640248"
---
# <a name="hybrid-application-design-considerations"></a>Consideraciones sobre el diseño de aplicaciones híbridas 

Microsoft Azure es la única nube híbrida coherente. Permite volver a usar las inversiones de desarrollo y habilita aplicaciones que pueden abarcar Azure global, las nubes soberanas de Azure y Azure Stack, que es una extensión de Azure en su centro de datos. Las aplicaciones que abarcan las nubes también se conocen como *aplicaciones híbridas*.

En la [*Guía de la arquitectura de aplicaciones en Azure*](https://docs.microsoft.com/azure/architecture/guide) se describe un enfoque estructurado para diseñar aplicaciones escalables, resistentes y de alta disponibilidad. Las consideraciones de la [*Guía de la arquitectura de aplicaciones en Azure*](https://docs.microsoft.com/azure/architecture/guide) se aplican por igual a las aplicaciones diseñadas para una sola nube y a las que abarcan varias nubes.

En este artículo se amplían los [*Fundamentos de calidad del software*](https://docs.microsoft.com/azure/architecture/guide/pillars) de la [*Guía de la arquitectura*](https://docs.microsoft.com/azure/architecture/guide/) [*de aplicaciones en Azure*](https://docs.microsoft.com/azure/architecture/guide/), centrándose específicamente en el diseño de aplicaciones híbridas. Además, se agrega un fundamento de *ubicación*, ya que las aplicaciones híbridas no son exclusivas de una nube ni de un centro de datos local.

Los escenarios híbridos varían considerablemente con los recursos que están disponibles para el desarrollo, y abarcan consideraciones como la geografía, la seguridad, el acceso a Internet, etc. Aunque en esta guía no se pueden enumerar las consideraciones específicas del usuario, se pueden proporcionar algunas directrices y procedimientos recomendados clave que este puede seguir. Un diseño, una configuración, una implementación y un mantenimiento correctos de una arquitectura de aplicación híbrida implica muchas consideraciones de diseño que es posible que no conozca.

Este documento pretende recopilar las posibles cuestiones que pueden surgir a la hora de implementar aplicaciones híbridas y proporciona consideraciones (estos fundamentos) y procedimientos recomendados para trabajar con ellas. Al abordar estas cuestiones durante la fase de diseño, se evitan los problemas que podrían causar en producción.

Básicamente, se trata de cuestiones que se deben considerar antes de crear una aplicación híbrida. Para empezar, debe hacer lo siguiente:

-   Identificar y evaluar los componentes de la aplicación.

-   Evaluar los componentes de la aplicación con respecto a los fundamentos.

## <a name="evaluate-the-application-components"></a>Evaluar los componentes de la aplicación

Cada componente de una aplicación tiene su propio rol específico dentro de esta y debe revisarse con todas las consideraciones de diseño. Las características y los requisitos de cada componente deben compararse con estas consideraciones para ayudar a determinar la arquitectura de la aplicación.

Descomponga la aplicación en sus componentes mediante el examen de su arquitectura y la determinación de su contenido. Los componentes también pueden incluir otras aplicaciones con las que interactúa la aplicación. Cuando identifique los componentes, evalúe las operaciones híbridas previstas según sus características, por ejemplo:

-   ¿Cuál es el propósito del componente?

-   ¿Cuáles son las interdependencias entre los componentes?

Por ejemplo, una aplicación puede tener un front-end y un back-end definidos como dos componentes. En un escenario híbrido, el front-end está en una nube y el back-end en la otra. Aplicación

proporciona canales de comunicación entre el front-end y el usuario y, además, entre el front-end y el back-end.

Un componente de aplicación se define mediante muchas formas y escenarios. La tarea más importante es identificarlos a ellos y a su ubicación en la nube o local.

Los componentes comunes de aplicación que se van a incluir en el inventario se muestran en la tabla 1.

### <a name="table-1-common-app-components"></a>Tabla 1. Componentes comunes de aplicación


| **Componente** | **Guía de aplicación híbrida** |
| ---- | ---- |
| Conexiones de cliente | La aplicación (en cualquier dispositivo) puede acceder a los usuarios de varias maneras, desde un punto de entrada único, incluidas las siguientes:<br>- Un modelo cliente-servidor que requiere que el usuario tenga instalado un cliente para trabajar con la aplicación. Una aplicación basada en servidor a la que se accede desde un explorador web.<br>- Las conexiones de cliente pueden incluir notificaciones cuando se interrumpe la conexión o alertas cuando se pueden aplicar cargos de itinerancia. |
| Authentication  | La autenticación puede ser necesaria para un usuario que se conecta a la aplicación o desde un componente que se conecta a otro. |
| API existentes  | Puede proporcionar a los desarrolladores acceso mediante programación a la aplicación con bibliotecas de clases y conjuntos de API y ofrecer una interfaz de conexión basada en estándares de Internet. También puede usar API para descomponer una aplicación en unidades lógicas que operan de forma independiente. |
| Services  | Puede emplear servicios breves para proporcionar las características de una aplicación. Un servicio puede ser el motor en el que se ejecuta la aplicación. |
| Colas | Puede usar colas para organizar el estado de los ciclos de vida y los estados de los componentes de la aplicación. Estas colas pueden proporcionar capacidades de mensajería, notificaciones y almacenamiento en búfer a las partes suscritas. |
| Almacenamiento de datos | Una aplicación puede ser con o sin estado. Las aplicaciones con estado necesitan almacenamiento de datos que diversos formatos y volúmenes puedan proporcionar. |
| Almacenamiento en caché de datos  | Un componente de almacenamiento en caché de datos del diseño puede abordar de forma estratégica problemas de latencia y desempeñar un rol en la activación de la ampliación en la nube. |
| Ingesta de datos | Los datos se pueden enviar a una aplicación de muchas maneras, desde valores enviados por el usuario en un formulario web hasta flujo de datos continuo de gran volumen. |
| Procesamiento de datos | Las tareas de procesamiento de datos (como informes, análisis, exportaciones de lotes y transformación de datos) pueden procesarse en el origen o descargarse en un componente independiente mediante una copia de los datos. |

## <a name="assess-app-components-for-pillars"></a>Evaluación de los componentes de la aplicación con respecto a los fundamentos

Evalúe las características de cada componente con respecto a cada fundamento. A medida que evalúa cada componente con respecto a todos los fundamentos, es posible que sea consciente de cuestiones que podría no haber considerado y que afectan al diseño de la aplicación híbrida. El tomar medidas con respecto a estas consideraciones podría agregar valor a la hora de optimizar la aplicación. En la tabla 2 se proporciona una descripción de cada fundamento y su relación con las aplicaciones híbridas.

### <a name="table-2-pillars"></a>Tabla 2. Fundamentos

| **Fundamento** | **Descripción** |
| ----------- | --------------------------------------------------------- |
| Ubicación  | Posicionamiento estratégico de componentes en aplicaciones híbridas. |
| Escalabilidad  | La capacidad de un sistema para controlar el aumento de la carga. |
| Disponibilidad  | Proporción de tiempo que una aplicación híbrida es funcional y está en funcionamiento. |
| Resistencia | Capacidad de recuperación de una aplicación híbrida. |
| Manejabilidad | Procesos de operaciones que mantienen un sistema ejecutándose en producción. |
| Seguridad | Protección de las aplicaciones híbridas y los datos frente a amenazas. |

## <a name="placement"></a>Ubicación

Una aplicación híbrida tiene de forma intrínseca una consideración de ubicación, por ejemplo, para el centro de datos.

La ubicación es la importante tarea de posicionar componentes para que puedan servir de la mejor manera posible a una aplicación híbrida. Por definición, las aplicaciones híbridas abarcan ubicaciones que van desde entornos locales a la nube y entre distintas nubes. Los componentes de la aplicación se pueden colocar en nubes de dos maneras:

-   **Aplicaciones híbridas verticales**  
    Los componentes de la aplicación se distribuyen entre ubicaciones. Cada componente individual puede tener varias instancias posicionadas solamente en una ubicación.

-   **Aplicaciones híbridas horizontales**  
    Los componentes de la aplicación se distribuyen entre ubicaciones. Cada componente individual puede tener varias instancias que abarcan varias ubicaciones.
    
    Algunos componentes pueden ser conscientes de su ubicación, mientras que otros no tienen ningún conocimiento de su ubicación y posicionamiento. Este virtuosismo se puede lograr con una capa de abstracción. Esta capa, con un marco de trabajo de la aplicación moderno, como microservicios, puede definir cómo se presta servicio a la aplicación mediante el posicionamiento de componentes de la aplicación en nodos de las nubes.

### <a name="placement-checklist"></a>Lista de comprobación de ubicación

**Comprobar las ubicaciones necesarias.** Asegúrese de que la aplicación o cualquiera de sus componentes debe funcionar en una nube específica o requiere certificación para ella. Esto puede incluir requisitos de soberanía de la empresa o dictados por la normativa. Además, determine si se requieren operaciones locales para una ubicación o configuración regional determinada.

**Determinar las dependencias de conectividad.** Las ubicaciones necesarias y otros factores pueden dictar las dependencias de conectividad entre los componentes. Al colocar los componentes, determine la conectividad y la seguridad óptimas para la comunicación entre ellos. Las opciones incluyen [*VPN*,](https://docs.microsoft.com/azure/vpn-gateway/) [*ExpressRoute*](https://docs.microsoft.com/azure/expressroute/) y [*Conexiones híbridas*.](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

**Evaluar las capacidades de la plataforma.** Vea si el proveedor de recursos necesario para cada componente de la aplicación está disponible en la nube y si el ancho de banda puede adaptarse a los requisitos de rendimiento y latencia esperados.

**Planear la portabilidad.** Use marcos de trabajo de la aplicación modernos, como contenedores o microservicios, para planear las operaciones de migración y evitar las dependencias de servicio.

**Determinar los requisitos de soberanía de datos.** Las aplicaciones híbridas están orientadas al aislamiento de datos, por ejemplo, en un centro de datos local. Revise la ubicación de los recursos para optimizar la consecución de este requisito.

**Planear la latencia.** Las operaciones entre nubes pueden incorporar distancia física entre los componentes de la aplicación. Determine los requisitos para admitir cualquier latencia.

**Controlar los flujos de tráfico.** Administre aspectos como el uso máximo y las comunicaciones adecuadas y seguras para los datos de identificación personal al acceder a ellos mediante el front-end de una nube pública.

## <a name="scalability"></a>Escalabilidad

La escalabilidad es la capacidad de un sistema de controlar un aumento de la carga en una aplicación, que puede variar con el tiempo a medida que otros factores, y fuerzas, afectan al tamaño de la audiencia además de al tamaño y el ámbito de la aplicación.

Para obtener la explicación básica de este fundamento, vea [*Escalabilidad*](https://docs.microsoft.com/azure/architecture/guide/pillars#scalability) en Fundamentos de calidad del software.

Un enfoque de escalado horizontal de las aplicaciones híbridas permite agregar más instancias para satisfacer la demanda y luego deshabilitarlas durante períodos más tranquilos.

En escenarios híbridos, el escalado horizontal de componentes individuales requiere consideración adicional si los componentes están distribuidos entre nubes. El escalado de una parte de la aplicación puede exigir el escalado de otra. Por ejemplo, si el número de conexiones de cliente aumenta pero los servicios web de la aplicación no se escalan horizontalmente en consecuencia, la carga en la base de datos podría saturar la aplicación.

Algunos componentes de la aplicación se pueden escalar horizontalmente de forma lineal, mientras que otros tienen dependencias de escalado y pueden estar limitados a la medida en que se pueden escalar. Por ejemplo, un túnel VPN que proporciona conectividad híbrida para las ubicaciones de los componentes de la aplicación tiene un límite con respecto al ancho de banda y la latencia a los que se puede escalar. ¿Cómo se escalan los componentes de la aplicación para garantizar que se cumplan estos requisitos?

### <a name="scalability-checklist"></a>Lista de comprobación de escalabilidad

**Determinar los umbrales de escalado.** Para controlar las distintas dependencias de la aplicación, determine en qué medida se pueden escalar los componentes de la aplicación de nubes diferentes de forma independiente, sin dejar de cumplir los requisitos para ejecutar la aplicación. Las aplicaciones híbridas suelen necesitar escalar determinadas áreas de la aplicación para controlar una característica cuando interactúa con el resto de la aplicación y afecta a esta. Por ejemplo, si se supera un número de instancias de front-end, es posible que sea necesario escalar el back-end.

**Definir programaciones de escalado.** La mayoría de las aplicaciones tienen períodos de máxima actividad, por lo que debe agregar sus horas punta a las programaciones para coordinar el escalado óptimo.

**Usar un sistema de supervisión centralizado.** Las capacidades de supervisión de plataforma pueden proporcionar escalado automático, pero las aplicaciones híbridas necesitan un sistema de supervisión centralizado que incorpore el estado y la carga del sistema. Un sistema de supervisión centralizado puede iniciar el escalado de un recurso en una ubicación y el escalado de un recurso dependiente en otra. Además, un sistema de supervisión centralizado puede realizar un seguimiento de las nubes que hacen un escalado automático de los recursos y de las que no.

**Aprovechar las capacidades de escalado automático (según disponibilidad).** Si las capacidades de escalado automático forman parte de la arquitectura, implemente el escalado automático al establecer umbrales que definan cuándo se debe escalar en vertical o en horizontal un componente de la aplicación. Un ejemplo de escalado automático es una conexión de cliente que se escala automáticamente en una nube para controlar un aumento de la capacidad, pero que hace que también se escalen otras dependencias de la aplicación distribuidas entre distintas nubes. Deben determinarse las capacidades de escalado automático de estos componentes dependientes.

Si el escalado automático no está disponible, considere la posibilidad de implementar scripts y otros recursos para admitir el escalado manual desencadenado por umbrales del sistema de supervisión centralizado.

**Determinar la carga esperada por ubicación.** Las aplicaciones híbridas que administrar solicitudes de cliente pueden basarse principalmente en una sola ubicación. Cuando la carga de solicitudes de cliente supera un umbral,

se pueden agregar recursos adicionales de otra ubicación para distribuir la carga de solicitudes entrantes. Asegúrese de que las conexiones de cliente puedan soportar el aumento de cargas y determine también los procedimientos automatizados para que las conexiones de cliente controlen la carga.

## <a name="availability"></a>Disponibilidad

La disponibilidad es el tiempo que un sistema es funcional y está en funcionamiento. La disponibilidad se mide como porcentaje del tiempo de actividad. Los errores de aplicación, los problemas de infraestructura y la carga del sistema pueden reducir la disponibilidad.

Para obtener la explicación básica de este fundamento, vea [*Disponibilidad*](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) en Fundamentos de calidad del software.

### <a name="availability-checklist"></a>Lista de comprobación de disponibilidad

**Proporcionar redundancia para la conectividad.** Las aplicaciones híbridas requieren conectividad entre las nubes en las que se distribuye la aplicación. Dispone de una serie de tecnologías para la conectividad híbrida, así que además de la opción de tecnología principal, use otra tecnología para proporcionar redundancia con capacidades de conmutación por error automatizadas en caso de error de la tecnología principal.

**Clasificar dominios de error.** Las aplicaciones tolerantes a errores requieren varios dominios de error. Los dominios de error ayudan a aislar el punto de error, por ejemplo, si se produce un error en un solo disco duro local, si un conmutador para la parte superior del rack deja de funcionar o si el centro de datos completo no está disponible. En una aplicación híbrida, una ubicación se puede clasificar como un dominio de error. Con más requisitos de disponibilidad, más necesita evaluar cómo debe clasificarse un dominio de error único.

**Clasificar dominios de actualización.** Los dominios de actualización se usan para garantizar que instancias de componentes de la aplicación están disponibles mientras a otras instancias del mismo componente se les aplican actualizaciones o actualizaciones de características. Al igual que los dominios de error, los dominios de actualización se pueden clasificar por su posicionamiento en las ubicaciones. Debe determinar si un componente de la aplicación puede ser actualizado en una ubicación antes de actualizarse en otra, o si se requieren otras configuraciones de dominio. Una sola ubicación puede tener varios dominios de actualización.

**Realizar un seguimiento de las instancias y la disponibilidad.** Los componentes de la aplicación de alta disponibilidad pueden estar disponibles a través del equilibrio de carga y la replicación sincrónica de datos. Debe determinar el número de instancias que pueden estar sin conexión antes de que se interrumpa el servicio.

**Implementar la recuperación automática.** En caso de que un problema provoque una interrupción en la disponibilidad de la aplicación, una detección por parte de un sistema de supervisión podría iniciar actividades de recuperación automática en la aplicación, como la purga de la instancia con errores y su reimplementación. Lo más probable es que esto requiera una solución de supervisión central integrada con una canalización de integración y entrega continuas (CI/CD) híbrida. La aplicación se integra con un sistema de supervisión para identificar problemas que puedan requerir la reimplementación de un componente de la aplicación. El sistema de supervisión también puede desencadenar una CI/CD híbrida para volver a implementar el componente de la aplicación y potencialmente cualquier otro componente dependiente de la misma ubicación o de otras.

**Mantener Acuerdos de Nivel de Servicio (SLA).** La disponibilidad es fundamental para cualquier acuerdo que tenga con los clientes de mantener la conectividad con los servicios y las aplicaciones. Cada ubicación en la que se base la aplicación híbrida puede tener su propio SLA. Estos distintos SLA pueden afectar al SLA general de la aplicación híbrida.

## <a name="resiliency"></a>Resistencia

La resistencia es la capacidad de un sistema y una aplicación híbridos de recuperarse de los errores y seguir funcionando. El objetivo de la resistencia es devolver la aplicación a un estado plenamente operativo después de un error. Las estrategias de resistencia incluyen soluciones como la copia de seguridad, la replicación y la recuperación ante desastres.

Para obtener la explicación básica de este fundamento, vea [*Resistencia*](https://docs.microsoft.com/azure/architecture/guide/pillars#resiliency) en Fundamentos de calidad del software.

### <a name="resiliency-checklist"></a>Lista de comprobación de resistencia

**Descubrir las dependencias de recuperación ante desastres.** La recuperación ante desastres en una nube podría requerir cambios en los componentes de la aplicación de otra nube. Si uno o varios componentes de una nube conmutan por error a otra ubicación, ya sea dentro de la misma nube o en otra, se deben notificar estos cambios a los componentes dependientes. Esto también incluye las dependencias de conectividad. La resistencia requiere un plan de recuperación de aplicaciones totalmente probado para cada nube.

**Establecer el flujo de recuperación.** Un diseño de flujo de recuperación eficaz ha evaluado la capacidad de los componentes de la aplicación de admitir búferes, reintentos, reintentos de transferencia de datos con errores y, si fuera necesario, revertir a otro servicio o flujo de trabajo. Debe determinar qué mecanismo de copia de seguridad se va a usar, qué implica su procedimiento de restauración y con qué frecuencia se prueba. También debe determinar la frecuencia de las copias de seguridad incrementales y completas.

**Probar recuperaciones parciales.** Una recuperación parcial de parte de la aplicación puede proporcionar seguridad a los usuarios de que no todo no está disponible. Esta parte del plan debe garantizar que una restauración parcial no tenga ningún efecto secundario, como que un servicio de copia de seguridad y restauración que interactúe con la aplicación la cierre correctamente antes de realizar la copia de seguridad.

**Determinar los instigadores de recuperación ante desastres y asignar la responsabilidad.** Un plan de recuperación debe describir quién, y qué roles, pueden iniciar acciones de copia de seguridad y recuperación, además de lo que se puede incluir en la copia de seguridad y la restauración.

**Comparar los umbrales de recuperación automática con la recuperación ante desastres.** Determine las capacidades de recuperación automática de una aplicación para el inicio de la recuperación automática y el tiempo necesario para que la recuperación automática de una aplicación se considere un error o un éxito. Determine los umbrales de cada nube.

**Comprobar la disponibilidad de las características de resistencia.** Determine la disponibilidad de las características y capacidades de resistencia de cada ubicación. Si una ubicación no proporciona las capacidades necesarias, considere la posibilidad de integrar esa ubicación en un servicio centralizado que proporcione las características de resistencia.

**Determinar los tiempos de inactividad.** Determine el tiempo de inactividad esperado debido al mantenimiento de la aplicación como un todo y como componentes de la aplicación.

**Documentar los procedimientos de solución de problemas.** Defina los procedimientos de solución de problemas para volver a implementar los recursos y los componentes de la aplicación.

## <a name="manageability"></a>Manejabilidad

Las consideraciones sobre cómo administrar las aplicaciones híbridas son fundamentales a la hora de diseñar la arquitectura. Una aplicación híbrida bien administrada proporciona una infraestructura como código que permite la integración de código de aplicación coherente en una canalización de desarrollo común. Mediante la implementación de pruebas coherentes de todo el sistema e individuales de los cambios en la infraestructura, puede garantizar una implementación integrada si los cambios pasan las pruebas, lo que permite que se combinen en el código fuente.

Para obtener la explicación básica de este fundamento, vea [*DevOps y administración*](https://docs.microsoft.com/azure/architecture/guide/pillars#management-and-devops) en Fundamentos de calidad del software.

### <a name="manageability-checklist"></a>Lista de comprobación de manejabilidad

**Implementar la supervisión.** Use un sistema de supervisión centralizado de los componentes de la aplicación distribuidos entre nubes para proporcionar una vista general de su estado y rendimiento. Este sistema incluye la supervisión de los componentes de la aplicación y las capacidades de la plataforma relacionadas.

Determine las partes de la aplicación que requieren supervisión.

**Coordinar directivas.** Cada ubicación que abarca una aplicación híbrida puede tener su propia directiva que cubra los tipos de recursos permitidos, las convenciones de nomenclatura, las etiquetas y otros criterios.

**Definir y usar roles.** Como administrador de bases de datos, debe determinar los permisos necesarios para los distintos usuarios (por ejemplo, un propietario de aplicación, un administrador de base de datos y un usuario final) que necesitan acceder a los recursos de la aplicación. Estos permisos deben configurarse en los recursos y dentro de la aplicación. Un sistema de control de acceso basado en rol (RBAC) permite establecer estos permisos en los recursos de la aplicación. Estos derechos de acceso plantean un reto cuando todos los recursos están implementados en una sola nube, pero requieren aún más atención cuando los recursos están distribuidos entre nubes. Los permisos de los recursos establecidos en una nube no se aplican a los recursos establecidos en otra.

**Usar canalizaciones de CI/CD.** Una canalización de integración y entrega continuas (CI/CD) puede proporcionar un proceso coherente para la creación e implementación de aplicaciones que abarcan varias nubes, y proporcionar control de calidad para su infraestructura y aplicación. Esta canalización permite probar la infraestructura y la aplicación en una nube e implementarlas en otra. La canalización incluso permite implementar determinados componentes de la aplicación híbrida en una nube y otros componentes en otra, formando la base de la implementación de la aplicación híbrida. Un sistema de CI/CD es fundamental para gestionar las dependencias que los componentes de la aplicación tienen entre sí durante la instalación, por ejemplo, que la aplicación web necesite una cadena de conexión a la base de datos.

**Administrar el ciclo de vida.** Dado que los recursos de una aplicación híbrida pueden abarcar ubicaciones, cada capacidad de administración del ciclo de vida de cada ubicación debe agregarse a una sola unidad de administración del ciclo de vida. Tenga en cuenta cómo se crean, actualizan y eliminan.

**Examinar las estrategias de solución de problemas.** La solución de problemas de una aplicación híbrida implica a más componentes de la aplicación que si la misma aplicación se ejecuta en una sola nube. Además de la conectividad entre las nubes, la aplicación se ejecuta en dos plataformas en lugar de en una. Una tarea importante de la solución de problemas de aplicaciones híbridas es examinar la supervisión agregada de estado y rendimiento de los componentes de la aplicación.

## <a name="security"></a>Seguridad

La seguridad es una de las principales consideraciones de cualquier aplicación de nube y es incluso más importante para las aplicaciones de nube híbridas.

Para obtener la explicación básica de este fundamento, vea [*Seguridad*](https://docs.microsoft.com/azure/architecture/guide/pillars#security) en Fundamentos de calidad del software.

### <a name="security-checklist"></a>Lista de comprobación de seguridad

**Suponer infracciones.** Si una parte de la aplicación se ve comprometida, asegúrese de que haya soluciones para minimizar la propagación de la infracción, no solo dentro de la misma ubicación, sino también entre ubicaciones.

**Supervisar el acceso a la red permitido.** Determine las directivas de acceso a la red de la aplicación, por ejemplo, acceder a la aplicación solo desde una subred específica y únicamente permitir los puertos y protocolos mínimos entre los componentes necesarios para que la aplicación funcione correctamente.

**Emplear una autenticación sólida.** Un esquema de autenticación sólido es fundamental para la seguridad de la aplicación. Considere la posibilidad de usar un proveedor de identidades federado que proporcione capacidades de inicio de sesión único y emplee uno o varios de los siguientes esquemas: inicio de sesión mediante nombre de usuario y contraseña, claves públicas y privadas, autenticación en dos fases o multifactor y grupos de seguridad de confianza. Determine los recursos adecuados para almacenar datos confidenciales y otros secretos para la autenticación de la aplicación, además de los tipos de certificado y sus requisitos.

**Usar cifrado.** Identifique las áreas de la aplicación que usan cifrado, por ejemplo para el almacenamiento de datos o la comunicación y el acceso del cliente.

**Usar canales seguros.** Un canal seguro en las nubes es fundamental para proporcionar comprobaciones de seguridad y autenticación, protección en tiempo real, cuarentena y otros servicios entre nubes.

**Definir y usar roles.** Implemente roles para las configuraciones de recursos y el acceso de identidad única entre nubes. Determine los requisitos de control de acceso basado en rol (RBAC) de la aplicación y sus recursos de plataforma.

**Auditar el sistema.** La supervisión del sistema puede registrar y agregar datos de los componentes de la aplicación y de las operaciones de plataforma en la nube relacionadas.

## <a name="summary"></a>Resumen

En este artículo se proporciona una lista de comprobación de los elementos que es importante tener en cuenta durante la creación y el diseño de las aplicaciones híbridas. La revisión de estos fundamentos antes de implementar la aplicación evita que estas cuestiones surjan durante las interrupciones de producción y, posiblemente, exijan volver a plantearse el diseño.

Puede parecer una tarea previa que consume mucho tiempo, pero si se diseña la aplicación en función de estos fundamentos, el retorno de la inversión se logra fácilmente.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

-   [Nube híbrida](https://azure.microsoft.com/overview/hybrid-cloud/)
-   [Soluciones en la nube híbrida](https://azure.microsoft.com/solutions/hybrid-cloud-app/)
-   [Desarrollo de plantillas de Azure Resource Manager para mantener la coherencia en la nube](https://aka.ms/consistency)