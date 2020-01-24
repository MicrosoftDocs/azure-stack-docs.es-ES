---
title: Protección de las máquinas virtuales implementadas en Azure Stack Hub | Microsoft Docs
description: Aprenda a crear un plan de recuperación para proteger las máquinas virtuales implementadas en Azure Stack Hub de la pérdida de datos y el tiempo de inactividad no planeado.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: 9211a3221a3c12c91b9d8875c04bbdfbedaf81e2
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536171"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Protección de las máquinas virtuales implementadas en Azure Stack Hub

Use este artículo como guía para desarrollar un plan de protección de las máquinas virtuales que los usuarios implementan en Azure Stack Hub.


Para protegerse frente a la pérdida de datos y los tiempos de inactividad no planeados, debe implementar un plan de recuperación y copia de seguridad, o de recuperación ante desastres para las aplicaciones de usuario y los datos. Este plan es único para cada aplicación, pero sigue un marco establecido por la estrategia completa de continuidad empresarial y recuperación ante desastres (BC/DR) de su organización. Un buen punto de partida es [Azure Stack Hub: Considerations for business continuity and disaster recovery](https://aka.ms/azurestackbcdrconsiderationswp) (Azure Stack: Consideraciones sobre continuidad empresarial y recuperación ante desastres).

## <a name="azure-stack-hub-infrastructure-recovery"></a>Recuperación de la infraestructura de Azure Stack Hub

Los usuarios son los responsables de la protección de sus máquinas virtuales de forma independiente de los servicios de infraestructura de Azure Stack Hub.

El plan de recuperación de los servicios de infraestructura de Azure Stack Hub **no** incluye la recuperación de máquinas virtuales, cuentas de almacenamiento o bases de datos del usuario. Como propietario de la aplicación, es el responsable de implementar un plan de recuperación de las aplicaciones y los datos.

Si la nube de Azure Stack Hub está sin conexión durante un largo período de tiempo o queda irrecuperable permanentemente, es preciso disponer de un plan de recuperación que:

* Garantice el tiempo de inactividad mínimo.
* Mantenga en ejecución las VM críticas, como los servidores de base de datos.
* Permita a las aplicaciones seguir atendiendo las solicitudes de usuario.

El operador de la nube de Azure Stack Hub es responsable de la creación de un plan de recuperación de la infraestructura y los servicios subyacentes de Azure Stack Hub. Para obtener más información, consulte [Recuperación después de una pérdida de datos grave](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>Consideraciones para máquinas virtuales IaaS
El sistema operativo instalado en la VM IaaS limita los productos que puede usar para proteger los datos que esta contiene. Para máquinas virtuales basadas en Windows, puede usar los productos de Microsoft y de asociados para proteger los datos. Para máquinas virtuales IaaS basadas en Linux, la única opción es usar productos de asociados. Consulte en [esta hoja de datos todos los asociados de BC/DR con productos validados para Azure Stack Hub](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Combinaciones de origen y destino

Cada nube de Azure Stack Hub se implementa en un centro de datos. Se necesita un entorno independiente para recuperar las aplicaciones. El entorno de recuperación puede ser otra nube de Azure Stack Hub que se encuentre en otro centro de datos o en la nube pública de Azure. Los requisitos de soberanía y privacidad de los datos determinan el entorno de recuperación de la aplicación. Cuando habilita la protección de cada aplicación, puede elegir una opción de recuperación específica para cada una. Puede hacer que las aplicaciones de una suscripción realicen una copia de seguridad de los datos en otro centro de datos. En otra suscripción, puede replicar los datos en la nube pública de Azure.

Planee la estrategia de copia de seguridad y recuperación, y la de recuperación ante desastres de cada aplicación para determinar el destino de cada aplicación. Un plan de recuperación ayuda a su organización a estimar correctamente la capacidad de almacenamiento necesaria en los dispositivos locales y el consumo de los proyectos en la nube pública.

|  | Azure global | Azure Stack Hub implementado en un centro de datos de CSP y operado por este | Azure Stack Hub implementado en un centro de datos de cliente y operado por este |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack Hub implementado en un centro de datos de CSP y operado por este** | Las máquinas virtuales de usuario se implementan en una instancia de Azure Stack Hub controlada por CSP.<br><br>Las máquinas virtuales de usuario se restauran a partir de una copia de seguridad o se conmutan por error directamente en Azure. | CSP opera las instancias principal y secundaria de Azure Stack Hub en sus propios centros de datos.<br><br>Las máquinas virtuales de usuario se restauran o se conmutan por error entre las dos instancias de Azure Stack Hub. | CSP opera Azure Stack Hub en el sitio principal.<br><br>El centro de datos del cliente es el destino de restauración o de conmutación por error. |
| **Azure Stack Hub implementado en un centro de datos de cliente y operado por este** | Las máquinas virtuales del usuario se implementan en la instancia de Azure Stack Hub controlada por el cliente.<br><br>Las máquinas virtuales de usuario se restauran a partir de una copia de seguridad o se conmutan por error directamente en Azure. | El cliente controla Azure Stack Hub en el sitio principal.<br><br>El centro de datos del CSP es el destino de restauración o de conmutación por error. | El cliente controla las instancias principal y secundaria de Azure Stack Hub en su propio centros de datos.<br><br>Las máquinas virtuales de usuario se restauran o se conmutan por error entre las dos instancias de Azure Stack Hub. |

![Combinaciones de origen y destino](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="app-recovery-objectives"></a>Objetivos de recuperación de la aplicación

Determine la cantidad de tiempo de inactividad y la pérdida de datos que puede tolerar la organización para cada aplicación. La cuantificación de tiempo de inactividad y pérdida de datos le permite crear un plan de recuperación que minimice el impacto de un desastre en su organización. Para cada aplicación, tenga en cuenta lo siguiente:

 - **Objetivo de tiempo de recuperación (RTO)**  
El objetivo de tiempo de recuperación es el tiempo máximo aceptable que una aplicación puede no estar disponible después de un incidente. Por ejemplo, con un RTO de 90 minutos, debe ser capaz de restaurar la aplicación a un estado en ejecución en un plazo de 90 minutos desde el inicio de un desastre. Si tiene un RTO bajo, puede mantener una segunda implementación ejecutándose continuamente en modo de espera para protegerse contra una interrupción regional.
 - **Objetivo de punto de recuperación (RPO)**  
El objetivo de punto de recuperación (RPO) es la duración máxima de la pérdida de datos que es aceptable durante un desastre. Por ejemplo, si se almacenan datos en una única base de datos, de la que se realizan copias de seguridad cada hora, sin replicación en otras bases de datos, se podría perder hasta una hora de datos.

RTO y el RPO son requisitos empresariales. Lleve a cabo una evaluación de riesgos para definir el RTO y el RPO de la aplicación.

Otra métrica es el **tiempo medio para recuperación** (MTTR), que es el tiempo medio que se tarda en restaurar la aplicación después de un error. MTTR es un valor empírico de un sistema. Si el MTTR excede el RTO, un error en el sistema causará una interrupción inaceptable del negocio, porque no será posible restaurar el sistema dentro del RTO definido.

### <a name="backup-restore"></a>Copia de seguridad y restauración

El esquema de protección más habitual para aplicaciones basadas en VM consiste en utilizar software de copia de seguridad. Realizar una copia de seguridad de una VM normalmente incluye la configuración del sistema operativo, los binarios de aplicación y los datos de aplicación. Las copias de seguridad se crean tomando una instantánea de los volúmenes y los discos o de toda la máquina virtual. Con Azure Stack Hub, tiene la flexibilidad de poder realizar copias de seguridad desde dentro del contexto del SO invitado o desde las API de almacenamiento y proceso de Azure Stack Hub. Azure Stack Hub no admite la realización de copias de seguridad en el nivel de hipervisor.
 
![Copia de seguridad y restauración](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

La recuperación de la aplicación requiere la restauración de una o más VM en la misma nube o en una nube nueva. Puede elegir como destino una nube en su centro de datos o en la nube pública. La nube está completamente bajo su control y se basa en los requisitos de privacidad y soberanía sobre sus datos.

 - RTO: tiempo de inactividad medido en horas
 - RPO: pérdida de datos variables (dependiendo de la frecuencia de las copias de seguridad)
 - Topología de implementación: activa/pasiva

#### <a name="planning-your-backup-strategy"></a>Planeamiento de la estrategia de copia de seguridad

El planeamiento de la estrategia de copia de seguridad y la definición de los requisitos de escalado empieza por la cuantificación del número de instancias de máquina virtual que se deben proteger. La copia de seguridad de todas las máquinas virtuales de todos los servidores de un entorno es una estrategia común. Sin embargo, con Azure Stack Hub, hay algunas máquinas virtuales de las que se debe realizar una copia de seguridad. Por ejemplo, las máquinas virtuales de un conjunto de escalado se consideran recursos efímeros que vienen y van, a veces sin previo aviso. Todos los datos duraderos que necesitan protección se almacenan en un repositorio independiente, como una base de datos o un almacén de objetos.

Consideraciones importantes a la hora de realizar copias de seguridad de máquinas virtuales en Azure Stack Hub:

 - **Categorización**
    - Considere un modelo en el que los usuarios pueden participar en la copia de seguridad de la máquina virtual.
    - Defina un Acuerdo de Nivel de Servicio (SLA) de recuperación basado en la prioridad de las aplicaciones o el impacto en el negocio.
 - **Escala**
    - Considere la posibilidad de realizar copias de seguridad escalonadas si incorpora un gran número de máquinas virtuales nuevas (si es necesaria la copia de seguridad).
    - Evalúe productos de copia de seguridad que puedan capturar y transmitir eficazmente los datos de copia de seguridad para minimizar el contenido de los recursos de la solución.
    - Evalúe productos de seguridad que almacenen eficazmente los datos de copia seguridad mediante copias de seguridad incrementales o diferenciales para minimizar la necesidad de extraer copias de seguridad completas de todas las máquinas virtuales del entorno.
 - **Restauración**
    - Los productos de copia de seguridad pueden restaurar discos virtuales, datos de aplicaciones dentro de una VM existente, o todos los recursos de la VM y los discos virtuales asociados. El esquema de restauración necesario depende de cómo se va a restaurar la aplicación. Por ejemplo, puede ser más fácil volver a implementar una instancia de SQL Server desde una plantilla y, a continuación, restaurar las bases de datos en lugar de restaurar la máquina virtual completa o un conjunto de máquinas virtuales.

### <a name="replicationmanual-failover"></a>Replicación o conmutación por error manual

Un enfoque alternativo a admitir la alta disponibilidad es replicar las VM de la aplicación en otra nube y confiar en una conmutación por error manual. La replicación del sistema operativo, los binarios de la aplicación y los datos de esta se puede realizar en el nivel de la VM o en el nivel del sistema operativo invitado. La conmutación por error se administra mediante software adicional que no forma parte de la aplicación.

Con este enfoque, la aplicación se implementa en una nube y su VM se replica en la otra nube. Si se desencadena una conmutación por error, las máquinas virtuales secundarias se deben activar en la segunda nube. En algunos escenarios, la conmutación por error crea las máquinas virtuales y conecta discos a ellas. Este proceso puede tardar mucho tiempo en completarse, especialmente en una aplicación de niveles múltiples que requiere una secuencia de inicio específica. Puede que sea necesario también seguir una serie de pasos antes de que la aplicación esté preparada para empezar a atender las solicitudes.

![Replicación y conmutación por error](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: tiempo de inactividad medido en minutos
 - RPO: pérdida de datos variables (dependiendo de la frecuencia de replicación)
 - Topología de implementación: espera activa/pasiva
 
### <a name="high-availabilityautomatic-failover"></a>Alta disponibilidad o conmutación por error automática

Para aquellas aplicaciones para las que su empresa solo puede tolerar unos pocos segundos o minutos de tiempo de inactividad y una pérdida de datos mínima, considere la posibilidad de una configuración de alta disponibilidad. Las aplicaciones de alta disponibilidad están diseñadas para recuperarse de forma rápida y automática de los errores. En el caso de errores del hardware local, la infraestructura de Azure Stack Hub implementa alta disponibilidad en la red física mediante los dos principales conmutadores del bastidor. En el caso de errores en el nivel de proceso, Azure Stack Hub usa varios nodos en una unidad de escalado. En el nivel de la VM, puede utilizar conjuntos de escalado en combinación con dominios de error para asegurarse de que los errores de los nodos no desactivan la aplicación.

Además de los conjuntos de escalado, es necesario que la aplicación admita la alta disponibilidad de forma nativa o admita el uso de software de agrupación en clústeres. Por ejemplo, Microsoft SQL Server admite alta disponibilidad de forma nativa para bases de datos mediante el modo de confirmación sincrónica. Sin embargo, si solo puede admitir la replicación asincrónica, se producirá alguna pérdida de datos. Las aplicaciones también se pueden implementar en un clúster de conmutación por error en el que el software de agrupación en clústeres controla la conmutación automática por error de la aplicación.

Con este enfoque, la aplicación solo está activa en una nube, pero el software se implementa en varias. Las otras nubes permanecen en el modo en espera listas para iniciar la aplicación cuando se desencadene la conmutación por error.

 - RTO: tiempo de inactividad medido en segundos
 - RPO: pérdida de datos mínima
 - Topología de implementación: espera activa/activa

### <a name="fault-tolerance"></a>Tolerancia a errores

La redundancia física y la disponibilidad del servicio de infraestructura de Azure Stack Hub solo protegen contra errores en el nivel del hardware como, por ejemplo, un disco, la fuente de alimentación, un puerto de red o un nodo. No obstante, si la aplicación debe estar siempre disponible y nunca puede perder ningún dato, debe implementar la tolerancia a errores de forma nativa en la aplicación o usar software adicional para habilitar la tolerancia a errores.

En primer lugar, debe asegurarse de que las VM de la aplicación se implementen mediante conjuntos de escalado para protegerlas ante errores en el nivel de nodo. Para protegerla en caso de que la nube se quede sin conexión, la misma aplicación ya debe estar implementada en una nube diferente, de forma que pueda continuar atendiendo solicitudes sin interrupción. A este modelo normalmente se le conoce como una implementación activa-activa.

Tenga en cuenta que cada nube de Azure Stack Hub es independiente, por lo que las nubes siempre se consideran activas desde una perspectiva de infraestructura. En este caso, se implementan varias instancias activas de la aplicación en una o más nubes activas.

 - RTO: sin tiempo de inactividad
 - RPO: No se produce pérdida de datos
 - Topología de implementación: activa/activa

### <a name="no-recovery"></a>Sin recuperación

Puede que algunas aplicaciones del entorno no necesiten protección frente a tiempos de inactividad o pérdidas de datos no planeados. Por ejemplo, no suele ser necesario recuperar las VM que se utilizan para desarrollo y pruebas. Es su decisión hacerlo sin la protección de una aplicación o una VM específica. Azure Stack Hub no proporciona copia de seguridad ni replicación de las máquinas virtuales desde la infraestructura subyacente. Al igual que con Azure, debe participar en la protección de cada VM en cada una de las suscripciones.

 - RTO: irrecuperable
 - RPO: pérdida de datos completa

## <a name="recommended-topologies"></a>Topologías recomendadas

Consideraciones importantes para la implementación de Azure Stack Hub:

|     | Recomendación | Comentarios |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Copia de seguridad o restauración de máquinas virtuales en un destino de copia de seguridad externo ya implementado en el centro de datos | Recomendado | Saque partido a la infraestructura de copia de seguridad y a las capacidades operativas existentes. Asegúrese de cambiar el tamaño de la infraestructura de copia de seguridad para que esté lista para proteger las instancias de VM adicionales. Asegúrese de que la infraestructura de copia de seguridad no está cerca del origen. Puede restaurar las máquinas virtuales en la instancia de origen de Azure Stack Hub, en una instancia secundaria de Azure Stack Hub o en Azure. |
| Copia de seguridad o restauración de máquinas virtuales en un destino de copia de seguridad externo dedicado a Azure Stack Hub | Recomendado | Puede comprar una nueva infraestructura de copia de seguridad o aprovisionar una infraestructura de copia de seguridad dedicada para Azure Stack Hub. Asegúrese de que la infraestructura de copia de seguridad no está cerca del origen. Puede restaurar las máquinas virtuales en la instancia de origen de Azure Stack Hub, en una instancia secundaria de Azure Stack Hub o en Azure. |
| Copia de seguridad o restauración de máquinas virtuales directamente en Azure global o en un proveedor de servicios de confianza | Recomendado | Siempre que cumpla los requisitos de privacidad de los datos y los requisitos reglamentarios, puede almacenar las copias de seguridad en Azure global o en un proveedor de servicios de confianza. Lo ideal es que el proveedor de servicios también ejecute Azure Stack Hub, ya que así se conseguirá una mayor coherencia en la experiencia operativa al hacer la restauración. |
| Replicación o conmutación por error de máquinas virtuales en una instancia independiente de Azure Stack Hub | Recomendado | En el caso de la conmutación por error, es preciso tener una segunda nube de Azure Stack Hub totalmente operativa para evitar que el tiempo de inactividad de la aplicación sea excesivo. |
| Replicación o conmutación por error de máquinas virtuales directamente en Azure o en un proveedor de servicios de confianza | Recomendado | Siempre que cumpla los requisitos de privacidad de los datos y los requisitos reglamentarios, puede replicar los datos en Azure global o en un proveedor de servicios de confianza. Lo ideal es que el proveedor de servicios también ejecute Azure Stack Hub, ya que así se consigue una mayor coherencia en la experiencia operativa después de la conmutación por error. |
| Implementación del destino de copia de seguridad en la misma nube de Azure Stack Hub con los datos de la aplicación | No recomendado | No guarde las copias de seguridad en la misma nube de Azure Stack Hub. Un tiempo de inactividad imprevisto de la nube puede apartarle de los datos principales y de los datos de copia de seguridad. Si decide implementar un destino de copia de seguridad como una aplicación virtual (para los fines de optimización de copia de seguridad y restauración), debe asegurarse de que todos los datos se copian continuamente en una ubicación de copia de seguridad externa. |
| Implementación de un dispositivo físico de copia de seguridad en el mismo bastidor en que está instalada la solución Azure Stack Hub | No compatible | Actualmente, no puede conectar ningún otro dispositivo en los conmutadores de la parte superior del rack que no forme parte de la solución original. |

## <a name="next-steps"></a>Pasos siguientes

En este artículo se proporcionan directrices generales para proteger las máquinas virtuales de usuario implementadas en Azure Stack Hub. Para obtener información sobre el uso de los servicios de Azure para proteger las máquinas virtuales de usuario, consulte:

- [Consideraciones sobre continuidad empresarial y recuperación ante desastres](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Servidor de Azure Backup
 - [Uso de Azure Backup para realizar copias de seguridad de archivos y aplicaciones en Azure Stack Hub](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Compatibilidad de Azure Backup Server con Azure Stack Hub](https://docs.microsoft.com/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Compatibilidad de Azure Site Recovery con Azure Stack Hub](https://docs.microsoft.com/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Productos de asociado
 - [Hoja de datos del Ecosistema de asociados de integración de centros de datos de Azure Stack Hub](https://aka.ms/azurestackbcdrpartners)

Para más información sobre los productos de asociados que ofrecen protección a las máquinas virtuales en Azure Stack Hub, consulte [Protección de aplicaciones y datos en Azure Stack Hub](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
