---
title: ¿Qué es Azure Kubernetes Service en Azure Stack HCl?
description: Azure Kubernetes Service en Azure Stack HCI es una implementación local de Azure Kubernetes Service (AKS) que automatiza la ejecución de aplicaciones en contenedores a gran escala.
ms.topic: overview
author: v-susbo
ms.author: v-susbo
ms.date: 12/02/2020
ms.openlocfilehash: a7d97d2454e9d77c1760180b46be9ce219d6af68
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612665"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>¿Qué es Azure Kubernetes Service en Azure Stack HCl?
> Se aplica a: AKS en Azure Stack HCI, entorno en tiempo de ejecución de AKS en Windows Server 2019 Datacenter

Azure Kubernetes Service en Azure Stack HCI es una implementación local de Azure Kubernetes Service (AKS) que automatiza la ejecución de aplicaciones en contenedores a gran escala. Azure Kubernetes Service ahora está en versión preliminar en Azure Stack HCl y en Windows Server 2019 Datacenter, lo que hace que se tarde menos en empezar a hospedar contenedores de Linux y Windows en un centro de datos.

Para empezar a trabajar con una instancia local de Azure Kubernetes Service, [regístrese para obtener la versión preliminar](https://aka.ms/AKS-HCI-Evaluate) (no tendrá que pagar ningún costo adicional en esta versión). A continuación, consulte [Configuración de Azure Kubernetes Service en Azure Stack HCI](setup.md). Si en su lugar prefiere usar Azure Kubernetes Service para organizar los contenedores basados en la nube, consulte [Azure Kubernetes Service en Azure](/azure/aks/intro-kubernetes).

En las siguientes secciones se describen algunas de las razones para usar Azure Kubernetes Service en Azure Stack HCI. Asimismo, encontrará varias respuestas a algunas preguntas frecuentes sobre este servicio y cómo comenzar a usarlo. Para obtener información general sobre los contenedores, vea [Windows y contenedores](/virtualization/windowscontainers/about/) y, para obtener información sobre Kubernetes, vea [Conceptos básicos de Kubernetes](kubernetes-concepts.md) o [Kubernetes.io](https://kubernetes.io).

## <a name="use-aks-to-automate-management-of-containerized-applications"></a>Use AKS para automatizar la administración de aplicaciones en contenedores

Aunque puede administrar algunos contenedores de forma manual mediante Docker y Windows, las aplicaciones suelen usar cinco, diez o incluso cientos de contenedores, por lo que es necesario utilizar el orquestador de Kubernetes.

Kubernetes es un orquestador de código abierto para automatizar la administración de contenedores a gran escala. Azure Kubernetes Service simplifica la implementación de Kubernetes local al proporcionar asistentes para configurar Kubernetes y complementos esenciales en Azure Stack HCI. También se puede utilizar para crear clústeres de Kubernetes a fin de hospedar las cargas de trabajo.

A continuación, se detallan algunas de las funcionalidades que proporciona Azure Kubernetes Service mientras se encuentra en versión preliminar en Azure Stack HCI:

- Implementación de aplicaciones en contenedores a gran escala en clústeres de Kubernetes que se ejecutan en el clúster de Azure Stack HCI
- Implementación y administración de aplicaciones en contenedores basadas en Linux y Windows
- Implementación de AKS en Azure Stack HCI mediante Windows Admin Center o PowerShell
- Opción de escalar o reducir verticalmente al agregar o quitar nodos en el clúster de Kubernetes
- Administración del almacenamiento y las redes en el clúster de Kubernetes
- Actualizaciones automáticas para la implementación de Kubernetes
- Actualización a la versión más reciente de Kubernetes disponible
- Uso de los servicios populares de Azure mediante Azure Arc para Kubernetes

## <a name="simplify-setting-up-kubernetes"></a>Simplificación de la configuración de Kubernetes

Azure Kubernetes Service simplifica el proceso de configuración de Kubernetes en Azure Stack HCI y Windows Server 2019 Datacenter e incluye las siguientes características:

- Un asistente de Windows Admin Center para configurar Kubernetes y sus dependencias (por ejemplo, kubeadm, kubelet, kubectl y un complemento de red de pod)
- Un asistente de Windows Admin Center para crear clústeres de Kubernetes a fin de ejecutar aplicaciones en contenedores
- Cmdlets de PowerShell para configurar Kubernetes y crear clústeres de Kubernetes, en caso de que prefiera crear un script para la configuración del host y la creación del clúster de Kubernetes

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Visualización y administración de Kubernetes mediante herramientas locales o Azure Arc

Una vez que haya configurado Azure Kubernetes Service en el entorno local y creado un clúster de Kubernetes, le proporcionaremos un par de maneras de administrar y supervisar la infraestructura de Kubernetes:

- **En el entorno local mediante herramientas conocidas como Kubectl y el panel de Kubernetes**: use una interfaz web de código abierto para implementar aplicaciones en un clúster de Kubernetes, administrar los recursos del clúster, solucionar problemas y ver las aplicaciones en ejecución.
- **En Azure Portal con Azure Arc**: use Azure Arc para administrar las aplicaciones implementadas en clústeres de Kubernetes en entornos en la nube y locales. 
<br>Asimismo, Azure Arc le permite administrar los clústeres de Kubernetes con otros servicios de Azure, como, por ejemplo:

  - Azure Monitor
  - Azure Policy
  - Control de acceso basado en roles

## <a name="run-linux-and-windows-containers"></a>Ejecución de contenedores de Linux y Windows

Azure Kubernetes Service es totalmente compatible con los contenedores basados en Linux y en Windows. Al crear un clúster de Kubernetes en Azure Stack HCI, puede elegir si quiere crear grupos de nodos (grupos de nodos de clúster de Kubernetes idénticos) para ejecutar contenedores de Linux, de Windows o ambos. 

Azure Kubernetes Service crea los nodos Linux y Windows para que no tenga que administrar directamente los sistemas operativos Linux o Windows.

## <a name="secure-your-container-infrastructure"></a>Protección de la infraestructura de contenedores

Azure Kubernetes Service incluye una serie de características que permitirán proteger la infraestructura del contenedor:

- **Aislamiento basado en el hipervisor para los nodos de trabajo**: cada clúster de Kubernetes se ejecuta en su propio conjunto aislado y dedicado de máquinas virtuales, por lo que los inquilinos pueden compartir la misma infraestructura física.
- **Imágenes de Windows y Linux que mantiene Microsoft para los nodos de trabajo**: los nodos de trabajo ejecutan las imágenes de máquinas virtuales Linux y Windows que crea Microsoft para cumplir con los procedimientos recomendados de seguridad. Asimismo, Microsoft actualiza estas imágenes mensualmente con las actualizaciones de seguridad más recientes.

El área de seguridad se actualiza constantemente en la versión preliminar de Azure Kubernetes Service en Azure Stack HCI, por lo que le recomendamos que siga atento a estos cambios.

## <a name="where-can-i-run-azure-kubernetes-service"></a>¿Dónde puedo ejecutar Azure Kubernetes Service?

Azure Kubernetes Service está disponible en las siguientes plataformas:

- En la nube de Azure a través de [Azure Kubernetes Service en Azure](/azure/aks/intro-kubernetes).
- De forma local a través de Azure Kubernetes Service en Azure Stack HCI (este es el tema que se trata en este artículo).
- Entorno local mediante el entorno en tiempo de ejecución de Azure Kubernetes Service Runtime en Windows Server (este artículo también se aplica a AKSr en Windows Server)
- De forma local en un entorno de Azure Stack Hub mediante el [motor de AKS en Azure Stack Hub](../user/azure-stack-kubernetes-aks-engine-overview.md).

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>¿Cómo funciona Kubernetes en Azure Stack HCI?

El funcionamiento de Azure Kubernetes Service es ligeramente distinto si se ejecuta en Azure Stack HCI o se usa en la nube de Azure:

- El servicio Kubernetes de Azure es un servicio hospedado en el que se administra automáticamente gran parte de la infraestructura de administración de Kubernetes (plano de control). Tanto el plano de control como las aplicaciones en contenedores se ejecutan en máquinas virtuales de Azure.
- Con Azure Kubernetes Service en Azure Stack HCI, debe configurar el servicio directamente en el clúster de Azure Stack HCI, lo que le permitirá gestionar el plano de control. El plano de control, las aplicaciones en contenedores y la propia instancia de Azure Kubernetes Service se ejecutan en máquinas virtuales que hospeda un clúster hiperconvergido.

Una vez que Azure Kubernetes Service se configura en el clúster de Azure Stack HCI, funciona de forma similar a la instancia de Azure Kubernetes Service hospedada: el servicio se usa para crear clústeres de Kubernetes que ejecutan las aplicaciones en contenedores. Estos clústeres de Kubernetes son grupos de máquinas virtuales que actúan como nodos de trabajo y ejecutan los contenedores de la aplicación. Igualmente, el clúster de Kubernetes también contiene un plano de control en el que se encuentran los servicios del sistema de Kubernetes que se usan para organizar los contenedores de la aplicación.

A continuación, puede ver un par de diagramas simplificados que muestran cómo se comparan las arquitecturas de Azure Kubernetes Service cuando se ejecutan en Azure y en Azure Stack HCI.

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="En esta arquitectura de la instancia de Azure Kubernetes Service hospedada en Azure se muestra cómo Azure administra los servicios de la plataforma y la mayor parte del plano de control, mientras que el cliente se encarga de administrar los clústeres de Kubernetes que ejecutan las aplicaciones en contenedores." lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="En esta arquitectura de Azure Kubernetes Service en Azure Stack HCI, se muestra cómo se ejecuta todo en el clúster de Azure Stack HCI, incluida la plataforma de Azure Kubernetes Service, el plano de control y los clústeres de Kubernetes que ejecutan las aplicaciones en contenedores." lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>Qué necesita para empezar

En las secciones siguientes se resumen los elementos que necesita para ejecutar Azure Kubernetes Service en Azure Stack HCI. Para obtener información detallada, consulte qué necesita [antes de instalar Azure Kubernetes Service en Azure Stack HCI](system-requirements.md).

### <a name="on-your-windows-admin-center-system"></a>En el sistema de Windows Admin Center

La máquina que ejecuta la puerta de enlace de Windows Admin Center tiene los requisitos siguientes:

- Una máquina con Windows 10 (no se admite la ejecución en Windows Admin Center en Azure Stack HCI ni en Windows Server 2019 Datacenter en este momento).
- 60 GB de espacio disponible
- Estar registrado en Azure
- En el mismo dominio que el clúster de Azure Stack HCI o el de Windows Server 2019 Datacenter

### <a name="on-the-azure-stack-hci-cluster-or-windows-server-2019-datacenter-failover-cluster-that-hosts-azure-kubernetes-service"></a>En el clúster de Azure Stack HCI o en el clúster de conmutación por error de Windows Server 2019 Datacenter que hospeda Azure Kubernetes Service

El clúster de Azure Stack HCI o el clúster de conmutación por error de Windows Server 2019 Datacenter cumple los requisitos siguientes:

- Un máximo de cuatro servidores en el clúster de esta versión preliminar
- 1 TB de capacidad disponible en el bloque de almacenamiento para Azure Kubernetes Service
- Al menos 30 GB de memoria disponible para ejecutar máquinas virtuales de Azure Kubernetes Service
- Todos los servidores del clúster deben usar la selección de región e idioma EN-US en esta versión preliminar.

Para conocer los requisitos generales del sistema Azure Stack HCI, consulte [Requisitos del sistema Azure Stack HCI](../hci/concepts/system-requirements.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Configuración de red para Azure Stack HCI

La red conectada a las VM en Azure Stack HCI o Windows Server 2019 Datacenter requiere un ámbito dedicado de direcciones IPv4 de DHCP que estén disponibles para Azure Kubernetes Service y que sean accesibles para las VM en el clúster de Azure Stack HCI o Windows Server 2019 Datacenter.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a usar Azure Kubernetes Service en Azure Stack HCI, consulte los siguientes artículos:

- [Revisión de los requisitos](system-requirements.md)
- [Configuración de Azure Kubernetes Service en Azure Stack HCI](create-kubernetes-cluster.md)
