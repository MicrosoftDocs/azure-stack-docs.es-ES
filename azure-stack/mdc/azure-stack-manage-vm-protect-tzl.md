---
title: Protección de máquinas virtuales implementadas en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo crear un plan de recuperación para proteger las VM implementadas en Azure Stack de la pérdida de datos y el tiempo de inactividad no planeado.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: 31e574a24660367cd1189ad95890e2fffaaa15f2
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330283"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Protección de las máquinas virtuales implementadas en Azure Stack Hub

Use este artículo como guía para desarrollar un plan de protección de las máquinas virtuales que los usuarios implementan en Azure Stack Hub.

Para protegerse frente a la pérdida de datos y los tiempos de inactividad no planeados, implemente un plan de protección de datos y de recuperación ante desastres para aplicaciones basadas en máquinas virtuales en Azure Stack Hub. El plan de protección implementado dependerá de los requisitos empresariales y del diseño de la aplicación. Este plan debe seguir un marco establecido por la estrategia completa de continuidad empresarial y recuperación ante desastres (BC/DR) de su organización. Para obtener información general de alto nivel sobre los aspectos de continuidad empresarial y recuperación ante desastres para Azure Stack Hub, consulte Azure Stack: Consideraciones sobre continuidad empresarial y recuperación ante desastres.

## <a name="application-recovery-objectives"></a>Objetivos de recuperación de la aplicación

Determine la cantidad de tiempo de inactividad y la pérdida de datos que puede tolerar la organización para cada aplicación. La cuantificación de tiempo de inactividad y pérdida de datos le permite crear un plan de recuperación que minimice el impacto de un desastre en su organización. Para cada aplicación, tenga en cuenta:

- **Objetivo de tiempo de recuperación (RTO)** \
    El objetivo de tiempo de recuperación es el tiempo máximo aceptable que una aplicación puede no estar disponible después de un incidente. Por ejemplo, con un RTO de 90 minutos, debe ser capaz de restaurar la aplicación a un estado en ejecución en un plazo de 90 minutos desde el inicio de un desastre. Si tiene un RTO bajo, puede mantener una segunda implementación ejecutándose continuamente en modo de espera para protegerse contra una interrupción regional.

- **Objetivo de punto de recuperación (RPO)** \
    El objetivo de punto de recuperación (RPO) es la duración máxima de la pérdida de datos que es aceptable durante un desastre. Por ejemplo, si se almacenan datos en una única base de datos, de la que se realizan copias de seguridad cada hora, sin replicación en otras bases de datos, se podría perder hasta una hora de datos.

Realice una evaluación para definir el RTO y RPO de cada aplicación.

Otra métrica importante a tener en cuenta es el **tiempo medio para recuperación** (MTTR), que es el tiempo medio que se tarda en restaurar la aplicación después de un error. MTTR es un valor empírico de un sistema. Si el MTTR excede el RTO, un error en el sistema causará una interrupción inaceptable del negocio, porque no será posible restaurar el sistema dentro del RTO definido.

## <a name="protection-options-for-iaas-vms"></a>Opciones de protección para máquinas virtuales de IaaS

### <a name="backup-restore"></a>Copia de seguridad y restauración

El esquema de protección más habitual para aplicaciones basadas en VM consiste en utilizar software de copia de seguridad. Realizar una copia de seguridad de una máquina virtual normalmente incluye la configuración del sistema operativo, los binarios de aplicación y los datos de aplicación persistentes que contiene la máquina virtual. Las copias de seguridad se crean mediante un agente en el sistema operativo invitado para capturar la aplicación, el sistema operativo, o el sistema y los volúmenes de archivos. Otro enfoque, este sin agente, se basa en la integración con las API de Azure Stack Hub para leer la información sobre la configuración de la máquina virtual y realizar una instantánea de los discos conectados a esta. Tenga en cuenta que Azure Stack Hub no admite la copia de seguridad directamente desde el hipervisor.

### <a name="planning-your-backup-strategy"></a>Planeamiento de la estrategia de copia de seguridad

El planeamiento de la estrategia de copia de seguridad y la definición de los requisitos de escalado empieza por la cuantificación del número de instancias de máquina virtual que se deben proteger. Realizar una copia de seguridad de todas las máquinas virtuales del sistema posiblemente no sea la forma más eficaz de proteger la aplicación. Con Azure Stack Hub, no se debe realizar la copia de seguridad de las máquinas virtuales de un conjunto de escalado o de disponibilidad en el nivel de las máquinas virtuales. Estas máquinas virtuales se consideran efímeras, ya que se puede escalar o reducir horizontalmente el conjunto de máquinas virtuales. Idealmente, todos los datos duraderos que necesitan conservarse se encuentran en un repositorio independiente, como una base de datos o un almacén de objetos. Si las aplicaciones implementadas en una arquitectura de escalado horizontal contienen datos que deben conservarse y protegerse, será necesario realizar la copia de seguridad en el nivel de aplicación mediante las funcionalidades nativas que proporciona la aplicación o mediante un agente.

Consideraciones importantes sobre la copia de seguridad de máquinas virtuales en Azure Stack:

- **Categorización**
  - Considere un modelo en el que los usuarios pueden participar en la copia de seguridad de la máquina virtual.
  - Defina un Acuerdo de Nivel de Servicio (SLA) de recuperación basado en la prioridad de las aplicaciones o el impacto en el negocio.
- **Escala**
  - Considere la posibilidad de realizar copias de seguridad escalonadas si incorpora un gran número de máquinas virtuales nuevas (si es necesaria la copia de seguridad).
  - Evalúe productos de copia de seguridad que puedan capturar y transmitir eficazmente los datos de copia de seguridad para minimizar el contenido de los recursos de la solución.
  - Evalúe productos de seguridad que almacenen eficazmente los datos de copia seguridad mediante copias de seguridad incrementales o diferenciales para minimizar la necesidad de extraer copias de seguridad completas de todas las máquinas virtuales del entorno.
- **Restaurar**
  - Los productos de copia de seguridad pueden restaurar discos virtuales, datos de aplicaciones dentro de una VM existente, o todos los recursos de la VM y los discos virtuales asociados. El esquema de restauración necesario depende de cómo se va a restaurar la aplicación. Por ejemplo, puede ser más fácil volver a implementar una instancia de SQL Server desde una plantilla y, a continuación, restaurar las bases de datos en lugar de restaurar la máquina virtual completa o un conjunto de máquinas virtuales.

### <a name="replicationmanual-failover"></a>Replicación o conmutación por error manual

Un enfoque alternativo para respaldar la recuperación consiste en replicar los datos en otro entorno. Los datos se pueden limitar a la aplicación, como en una replicación de base de datos, o al sistema operativo del sistema operativo invitado mediante un agente, o al nivel de la máquina virtual mediante la integración con las API de Azure Stack Hub. En caso de desastre, se requiere la conmutación por error a la ubicación secundaria. La conmutación por error se puede controlar de forma nativa mediante la aplicación al igual que con los grupos de disponibilidad de SQL, o en el nivel del sistema operativo invitado mediante agentes o tecnología de clústeres, o en el nivel de la máquina virtual mediante algún producto de protección.

### <a name="high-availabilityautomatic-failover"></a>Alta disponibilidad o conmutación por error automática

Las aplicaciones que admiten de forma nativa alta disponibilidad o que se basan en el software de clúster para lograr una alta disponibilidad en los nodos se pueden implementar en un grupo de máquinas virtuales de una instancia de Azure Stack Hub o en varias instancias de este. En todos los casos, se requiere cierto nivel de equilibrio de carga para garantizar que el tráfico de la aplicación se enruta correctamente. Con esta configuración, la aplicación puede recuperarse automáticamente de errores. En el caso de errores del hardware local, la infraestructura de Azure Stack Hub implementa alta disponibilidad y tolerancia a errores en la infraestructura física. En el caso de errores en el nivel de proceso, Azure Stack Hub usa varios nodos en una unidad de escalado con una configuración N-1. En el nivel de la máquina virtual, la disponibilidad y los conjuntos de escalado modelan cada nodo de la unidad de escalado como un dominio de error para garantizar la antiafinidad en el nivel de nodo de forma que los errores de nodo no afecten a una aplicación distribuida.

### <a name="no-protection"></a>Sin protección

Es posible que algunas aplicaciones no tengan datos que deban conservarse. Por ejemplo, no suele ser necesario recuperar las máquinas virtuales que se utilizan para desarrollo y pruebas. Otro ejemplo sería una aplicación sin estado que se puede volver a implementar desde una canalización de CI/CD en caso de que se produzca un error. Es importante identificar las aplicaciones que no requieren protección para evitar que las máquinas virtuales se protejan innecesariamente.

<!-- ## Recommended topologies

Important considerations for your Azure Stack deployment: -->

## <a name="next-steps"></a>Pasos siguientes

En este artículo se proporcionan instrucciones generales para proteger las máquinas virtuales de usuario implementadas en Azure Stack. Para obtener información sobre el uso de los servicios de Azure para proteger las máquinas virtuales de usuario, consulte:

- [Consideraciones sobre continuidad empresarial y recuperación ante desastres](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="partner-products"></a>Productos de asociado

- [Hoja de datos del Ecosistema de asociados de integración de centros de datos de Azure Stack](https://aka.ms/azurestackbcdrpartners)
- Para más información sobre los productos de asociados que ofrecen protección de máquina virtual en Azure Stack, consulte [Protección de aplicaciones y datos en Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
