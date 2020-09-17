---
title: Protección de las máquinas virtuales implementadas en Azure Stack Hub
description: Aprenda a crear un plan de recuperación para proteger las máquinas virtuales implementadas en Azure Stack Hub de la pérdida de datos y el tiempo de inactividad no planeado.
author: mattbriggs
ms.topic: conceptual
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/5/2020
ms.openlocfilehash: 5634cd783a010f5aa45de88ba923dfe6a8378c4c
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742703"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Protección de las máquinas virtuales implementadas en Azure Stack Hub

Utilice este artículo como guía para ayudarle a desarrollar una estrategia de protección de datos y recuperación ante desastres para aquellas máquinas virtuales IaaS que los usuarios han implementado en Azure Stack Hub.

Para protegerse contra la pérdida de datos y un tiempo de inactividad prolongado, implemente un plan de recuperación de copia de seguridad o de recuperación ante desastres para las aplicaciones de usuario y sus datos. Cada aplicación se debe evaluar como parte de la estrategia global de continuidad empresarial y recuperación ante desastres de su organización. Un buen punto de partida es [Azure Stack Hub: Considerations for business continuity and disaster recovery](https://aka.ms/azurestackbcdrconsiderationswp) (Azure Stack: Consideraciones sobre continuidad empresarial y recuperación ante desastres).

## <a name="considerations-for-protecting-iaas-vms"></a>Consideraciones sobre la protección de máquinas virtuales de IaaS

### <a name="roles-and-responsibilities"></a>Roles y responsabilidades

En primer lugar, asegúrese de que se conocen perfectamente tanto los roles como las responsabilidades atribuidos a los propietarios y operadores de las aplicaciones en el contexto de protección y recuperación.

Los usuarios son los responsables de proteger las máquinas virtuales. Los operadores son los responsables de mantener Azure Stack Hub en línea y en buen estado. Azure Stack Hub incluye un servicio que realiza una copia de seguridad de los datos de servicio internos de los servicios de infraestructura, pero **no** incluye datos de usuarios, como las máquinas virtuales creadas por usuarios, las cuentas de almacenamiento con datos de usuarios o de aplicaciones, o las bases de datos de usuarios.


| Propietario o arquitecto de la aplicación   | Operador de Azure Stack Hub  |
|---    |---    |
| <ul><li>Alineación de la arquitectura de las aplicaciones con los principios de diseño en la nube.</li></br><li>Modernización de las aplicaciones tradicionales tanto como sea necesario para prepararlas para el entorno en la nube.</li></br><li>Definición del RTO y RPO aceptables para la aplicación.</li></br><li>Identificación de los repositorios de datos y los recursos de la aplicación que deben protegerse.</li></br><li>Implementación de un método de recuperación de datos y aplicaciones que se alinee mejor con la arquitectura de la aplicación y los requisitos del cliente.</li></ul>     | <ul><li>Identificación de los objetivos de continuidad empresarial y recuperación ante desastres de la organización.</li></br><li>Implementación de suficientes instancias de Azure Stack Hub para satisfacer los objetivos de continuidad empresarial y recuperación ante desastres de la organización.</li></br><li>Diseño y manejo de la infraestructura de protección de datos y aplicaciones.</li></br><li>Aportación de soluciones administradas o acceso de autoservicio a los servicios de protección.</li></br><li>Trabajo con los propietarios o arquitectos de las aplicaciones para conocer el diseño de estas y recomendar estrategias de protección.</li></br><li>Habilitación de la copia de seguridad de la infraestructura para la reparación del servicio y la recuperación en la nube.</li></ul>    |

## <a name="sourcetarget-combinations"></a>Combinaciones de origen y destino

Los usuarios que necesitan protección contra una posible interrupción en un sitio web o un centro de datos pueden usar otra instancia de Azure Stack Hub o Azure para proporcionar alta disponibilidad o una recuperación rápida. Con las ubicaciones principal y secundaria, los usuarios pueden implementar aplicaciones en una configuración activo/activo o activo/pasivo en dos entornos. En el caso de cargas de trabajo menos críticas, los usuarios pueden usar la capacidad de la ubicación secundaria para restaurar aplicaciones a petición desde una copia de seguridad.

En un centro de datos se pueden implementar una o varias nubes de Azure Stack Hub. Para sobrevivir ante un desastre catastrófico, la implementación de al menos una nube de Azure Stack Hub en otro centro de datos garantiza que se puede realizar la conmutación por error de las cargas de trabajo y minimizar el tiempo de inactividad no planeado. Si solo tiene una instancia de Azure Stack Hub, debería plantearse la posibilidad de usar la nube pública de Azure para la recuperación. El lugar en que se puede ejecutar la aplicación lo determinarán las regulaciones gubernamentales, las directivas corporativas y los estrictos requisitos de latencia. El usuario tiene la flexibilidad de determinar la ubicación de recuperación adecuada por aplicación. Por ejemplo, puede hacer que las aplicaciones de una suscripción realicen copias de seguridad de los datos en otro centro de datos y en otra suscripción. Para ello, es preciso replicar los datos en la nube pública de Azure.

## <a name="application-recovery-objectives"></a>Objetivos de recuperación de la aplicación

Los propietarios de la aplicación son responsables principalmente de determinar la cantidad de tiempo de inactividad y la pérdida de datos que tanto la aplicación como la organización pueden tolerar. La cuantificación del tiempo de inactividad y de la pérdida de datos aceptables le permite crear un plan de recuperación que minimice el impacto de un desastre en su organización. Tenga en cuenta lo siguiente en todas las aplicaciones:

 - **Objetivo de tiempo de recuperación (RTO)**  
El objetivo de tiempo de recuperación es el tiempo máximo aceptable que una aplicación puede no estar disponible después de un incidente. Por ejemplo, con un RTO de 90 minutos, debe ser capaz de restaurar la aplicación a un estado en ejecución en un plazo de 90 minutos desde el inicio de un desastre. Si tiene un RTO bajo, puede mantener una segunda implementación ejecutándose continuamente en modo de espera para protegerse contra una interrupción regional.
 - **Objetivo de punto de recuperación (RPO)**  
El objetivo de punto de recuperación (RPO) es la duración máxima de la pérdida de datos que es aceptable durante un desastre. Por ejemplo, si se almacenan datos en una única base de datos, de la que se realizan copias de seguridad cada hora, sin replicación en otras bases de datos, se podría perder hasta una hora de datos.

Otra métrica es el *tiempo medio para recuperación* (MTTR), que es el tiempo medio que se tarda en restaurar la aplicación después de un error. MTTR es un valor empírico de un sistema. Si el MTTR supera el RTO, un error en el sistema provocará una interrupción inaceptable del negocio, ya que el sistema no se podrá restaurar dentro del RTO definido.

## <a name="protection-options"></a>Opciones de protección 

### <a name="backup-restore"></a>Copia de seguridad y restauración

La copia de seguridad tanto de las aplicaciones como de los conjuntos de datos le permite recuperarse rápidamente del tiempo de inactividad provocado por datos dañados, eliminaciones involuntarias o desastres. En el caso de las aplicaciones basadas en máquinas virtuales de IaaS, puede usar un agente invitado para proteger los datos de la aplicación, la configuración del sistema operativo y los datos almacenados en los volúmenes. 

#### <a name="backup-using-in-guest-agent"></a>Copia de seguridad mediante un agente invitado

La copia de seguridad de cualquier máquina virtual mediante un agente del sistema operativo invitado habitualmente incluye la captura de la configuración del sistema operativo, archivos y carpetas, discos, así como archivos binarios y datos de las aplicaciones. 

La recuperación de una aplicación desde un agente requiere volver a crear manualmente la máquina virtual e instalar tanto el sistema operativo como el agente invitado. A partir de ese momento, los datos se pueden restaurar en el sistema operativo invitado o directamente en la aplicación.

#### <a name="backup-using-disk-snapshot-for-stopped-vms"></a>Realización de copias de seguridad mediante instantáneas de disco en máquinas virtuales detenidas

Los productos de copia de seguridad pueden proteger la configuración de máquinas virtuales de IaaS y de discos conectados a una máquina virtual detenida. Use una copia de seguridad de los productos que se integran en las API de Azure Stack Hub para capturar la configuración de las máquinas virtuales y crear instantáneas de discos. Si es posible que haya un tiempo de inactividad planeado para la aplicación, asegúrese de que la máquina virtual está en estado detenido antes de iniciar el flujo de trabajo de la copia de seguridad.  

#### <a name="backup-using-disk-snapshot-snapshot-for-running-vms"></a>Realización de copias de seguridad mediante instantáneas de discos para máquinas virtuales en funcionamiento

> [!Important]  
> Actualmente, no se admite el uso de instantáneas de discos para ninguna máquina virtual que se encuentre en ejecución. La creación de una instantánea de un disco conectado a una máquina virtual en ejecución puede reducir el rendimiento o el impacto de la disponibilidad del sistema operativo o de la aplicación en la máquina virtual. La recomendación es usar un agente invitado para proteger la aplicación si no se puede disponer de un tiempo de inactividad planeado. 

### <a name="vms-in-a-scale-set-or-availability-set"></a>Máquinas virtuales en un conjunto de escalado o conjunto de disponibilidad

En el nivel de máquina virtual no se deben realizar copias de seguridad de las máquinas virtuales de un conjunto de escalado o grupo de disponibilidad que se consideren recursos efímeros, sobre todo si la aplicación está sin estado. En el caso de las aplicaciones con estado implementadas en un conjunto de escalado o de disponibilidad, considere la posibilidad de proteger los datos de las aplicaciones (por ejemplo, una base de datos o un volumen de un grupo de almacenamiento). 

### <a name="replicationmanual-failover"></a>Replicación o conmutación por error manual

En el caso de las aplicaciones que requieren una pérdida mínima de datos o un tiempo de inactividad mínimo, la replicación de datos se puede habilitar en el nivel de sistema operativo invitado o de aplicación para replicar datos en otra ubicación. Algunas aplicaciones, como Microsoft SQL Server, admiten la replicación de forma nativa. Si la aplicación no admite la replicación, puede usar software en el sistema operativo invitado para replicar discos, o bien alguna solución de asociado que se instale como un agente en el sistema operativo invitado.

Con este enfoque, la aplicación se implementa en una nube y los datos se replican en la otra nube, en el entorno local o en Azure. Cuando se desencadena una conmutación por error, la aplicación del destino deberá iniciarse y asociarse a los datos replicados para que pueda empezar a atender solicitudes.
 
### <a name="high-availabilityautomatic-failover"></a>Alta disponibilidad o conmutación por error automática

Para aquellas aplicaciones sin estado que pueden tolerar pocos segundos o minutos de tiempo de inactividad, considere la posibilidad de una configuración de alta disponibilidad. Las aplicaciones de alta disponibilidad están diseñadas para implementarse en varias ubicaciones en una topología activa/activa en la que todas las instancias pueden atender solicitudes. En el caso de errores del hardware local, la infraestructura de Azure Stack Hub implementa alta disponibilidad en la red física mediante los dos principales conmutadores del bastidor. En el caso de errores en el nivel de proceso, Azure Stack Hub usa varios nodos en una unidad de escalado y realiza automáticamente la conmutación por error de una máquina virtual. En el nivel de la máquina virtual, puede utilizar conjuntos de escalado o máquinas virtuales en un conjunto de disponibilidad para asegurarse de que los errores de los nodos no desactiven la aplicación. La misma aplicación debe implementarse en una ubicación secundaria de la misma configuración. Para que la aplicación sea activa/activa, se puede usar un equilibrador de carga o un sistema de nombres de dominio que dirijan las solicitudes a todas las instancias disponibles.

### <a name="no-recovery"></a>Sin recuperación

Puede que algunas aplicaciones del entorno no necesiten protección frente a tiempos de inactividad o pérdidas de datos no planeados. Por ejemplo, las máquinas virtuales que se utilizan para el desarrollo y las pruebas, no es necesario recuperarlas normalmente. Es el usuario quien debe decidir si se hace sin protección en una aplicación o un conjunto de datos.

## <a name="recommended-topologies"></a>Topologías recomendadas

Consideraciones importantes para la implementación de Azure Stack Hub:

|     | Recomendación | Comentarios |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|**Copia de seguridad o restauración de máquinas virtuales en un destino de copia de seguridad externo ya implementado en el centro de datos**| Recomendado | Saque partido a la infraestructura de copia de seguridad y a las capacidades operativas existentes. Asegúrese de cambiar el tamaño de la infraestructura de copia de seguridad para que esté lista para proteger las instancias de VM adicionales. Asegúrese de que la infraestructura de copia de seguridad no está cerca del origen. Puede restaurar las máquinas virtuales en la instancia de origen de Azure Stack Hub, en una instancia secundaria de Azure Stack Hub o en Azure. |
|**Copia de seguridad o restauración de máquinas virtuales en un destino de copia de seguridad externo dedicado a Azure Stack Hub**| Recomendado | Puede comprar una nueva infraestructura de copia de seguridad o aprovisionar una infraestructura de copia de seguridad dedicada para Azure Stack Hub. Asegúrese de que la infraestructura de copia de seguridad no está cerca del origen. Puede restaurar las máquinas virtuales en la instancia de origen de Azure Stack Hub, en una instancia secundaria de Azure Stack Hub o en Azure. |
|**Copia de seguridad o restauración de máquinas virtuales directamente en Azure global o en un proveedor de servicios de confianza**| Recomendado | Siempre que cumpla los requisitos de privacidad de los datos y los requisitos reglamentarios, puede almacenar las copias de seguridad en Azure global o en un proveedor de servicios de confianza. Lo ideal es que el proveedor de servicios también ejecute Azure Stack Hub, ya que así se conseguirá una mayor coherencia en la experiencia operativa al hacer la restauración. |
|**Replicación o conmutación por error de máquinas virtuales en una instancia independiente de Azure Stack Hub**| Recomendado | En el caso de la conmutación por error, es preciso tener una segunda nube de Azure Stack Hub totalmente operativa para evitar que el tiempo de inactividad de la aplicación sea excesivo. |
|**Replicación o conmutación por error de máquinas virtuales directamente en Azure o en un proveedor de servicios de confianza**| Recomendado | Siempre que cumpla los requisitos de privacidad de los datos y los requisitos reglamentarios, puede replicar los datos en Azure global o en un proveedor de servicios de confianza. Lo ideal es que el proveedor de servicios también ejecute Azure Stack Hub, ya que así se consigue una mayor coherencia en la experiencia operativa después de la conmutación por error. |
|**Implemente un destino de copia de seguridad en la misma instancia de Azure Stack Hub que hospeda todas las aplicaciones protegidas por el mismo destino de copia de seguridad.**| Destino independiente: No recomendado </br> Destino que replica los datos de copia de seguridad externamente: Recomendado | Si decide implementar un dispositivo de copia de seguridad en Azure Stack Hub (con el fin de optimizar una restauración operativa), debe asegurarse de que todos los datos se copian continuamente en una ubicación de copia de seguridad externa. |
|**Implementación de un dispositivo físico de copia de seguridad en el mismo bastidor en que está instalada la solución Azure Stack Hub**| No compatible | Actualmente, no puede conectar ningún otro dispositivo en los conmutadores de la parte superior del rack que no forme parte de la solución original. |

## <a name="next-steps"></a>Pasos siguientes

En este artículo se proporcionan directrices generales para proteger las máquinas virtuales de usuario implementadas en Azure Stack Hub. Para obtener información sobre el uso de los servicios de Azure para proteger las máquinas virtuales de usuario, consulte:

- [Azure Stack IaaS (parte 4): Protección de sus máquinas virtuales](https://azure.microsoft.com/blog/azure-stack-iaas-part-four/)
- [Consideraciones sobre continuidad empresarial y recuperación ante desastres](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Servidor de Azure Backup
 - [Uso de Azure Backup para realizar copias de seguridad de archivos y aplicaciones en Azure Stack Hub](/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Compatibilidad de Azure Backup Server con Azure Stack Hub](/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Compatibilidad de Azure Site Recovery con Azure Stack Hub](/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Productos de asociado
 - [Hoja de datos del Ecosistema de asociados de integración de centros de datos de Azure Stack Hub](https://aka.ms/azurestackbcdrpartners)
