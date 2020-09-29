---
title: ¿Qué es Azure Kubernetes Service en Azure Stack HCl?
description: Azure Kubernetes Service en Azure Stack HCI es una implementación local de Azure Kubernetes Service (AKS) que automatiza la ejecución de aplicaciones en contenedores a gran escala.
ms.topic: overview
author: jasongerend
ms.author: jgerend
ms.date: 09/22/2020
ms.openlocfilehash: fad630d143fee31c4b63b4008c02bef697475155
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949423"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>¿Qué es Azure Kubernetes Service en Azure Stack HCl?

Azure Kubernetes Service en Azure Stack HCI es una implementación local de Azure Kubernetes Service (AKS) que automatiza la ejecución de aplicaciones en contenedores a gran escala. Azure Kubernetes Service ahora está en versión preliminar en Azure Stack HCl, lo que hace que se tarde menos en empezar a hospedar contenedores de Linux y Windows en un centro de recursos.

Para empezar a trabajar con una instancia local de Azure Kubernetes Service, [regístrese para obtener la versión preliminar](https://aka.ms/AKS-HCI-Evaluate) (no tendrá que pagar ningún costo adicional en esta versión). A continuación, consulte [Configuración de Azure Kubernetes Service en Azure Stack HCI](setup.md). Si en su lugar prefiere usar Azure Kubernetes Service para organizar los contenedores basados en la nube, consulte [Azure Kubernetes Service en Azure](/azure/aks/intro-kubernetes).

En las siguientes secciones se describen algunas de las razones para usar Azure Kubernetes Service en Azure Stack HCI. Asimismo, encontrará varias respuestas a algunas preguntas frecuentes sobre este servicio y cómo comenzar a usarlo. Para obtener información sobre los contenedores, consulte [Ventanas y contenedores](/virtualization/windowscontainers/about/).

## <a name="automate-management-of-containerized-applications"></a>Automatización de la administración de aplicaciones en contenedores

Aunque puede administrar algunos contenedores de forma manual mediante Docker y Windows, las aplicaciones suelen usar cinco, diez o incluso cientos de contenedores, por lo que es necesario utilizar el orquestador de Kubernetes.

Kubernetes es un orquestador de código abierto para automatizar la administración de contenedores a gran escala. Azure Kubernetes Service simplifica la implementación de Kubernetes local al proporcionar asistentes para configurar Kubernetes y complementos esenciales en Azure Stack HCI. También se puede utilizar para crear clústeres de Kubernetes a fin de hospedar las cargas de trabajo.

A continuación, se detallan algunas de las funcionalidades que proporciona Azure Kubernetes Service mientras se encuentra en versión preliminar en Azure Stack HCI:

- Implementación de aplicaciones en contenedores a gran escala en un clúster de máquinas virtuales (denominado clúster de Kubernetes) que se ejecutan en el clúster de Azure Stack HCI
- Conmutación por error cuando se produce un error en un nodo del clúster de Kubernetes
- Implementación y administración de aplicaciones en contenedores basadas en Linux y Windows
- Programación de las cargas de trabajo
- Supervisión del mantenimiento
- Opción de escalar o reducir verticalmente al agregar o quitar nodos en el clúster de Kubernetes
- Administración de redes
- Detección de servicios
- Coordinación de actualizaciones de aplicaciones
- Asignación de pods a los nodos de clúster con afinidad de nodo de clúster

Para obtener más información acerca de Kubernetes, consulte [Kubernetes.io](https://kubernetes.io).

## <a name="simplify-setting-up-kubernetes"></a>Simplificación de la configuración de Kubernetes

Azure Kubernetes Service simplifica el proceso de configuración de Kubernetes en Azure Stack HCI e incluye las siguientes características:

- Un asistente de Windows Admin Center para configurar Kubernetes y sus dependencias (por ejemplo, kubeadm, kubelet, kubectl y un complemento de red de pod)
- Un asistente de Windows Admin Center para crear clústeres de Kubernetes a fin de ejecutar aplicaciones en contenedores
- Cmdlets de PowerShell para configurar Kubernetes y crear clústeres de Kubernetes, en caso de que prefiera crear un script para la configuración del host y la creación del clúster de Kubernetes

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Visualización y administración de Kubernetes mediante herramientas locales o Azure Arc

Una vez que haya configurado Azure Kubernetes Service en el clúster de Azure Stack HCI y creado un clúster de Kubernetes, le proporcionaremos un par de maneras de administrar y supervisar la infraestructura de Kubernetes:

- **En el entorno local mediante herramientas conocidas como Kubectl y el panel de Kubernetes**: use una interfaz web de código abierto para implementar aplicaciones en un clúster de Kubernetes, administrar los recursos del clúster, solucionar problemas y ver las aplicaciones en ejecución.
- **En Azure Portal con Azure Arc**: use un servicio de Azure para administrar Azure Kubernetes Service y los clústeres de Kubernetes implementados en su nube y en los entornos locales. Puede usar Azure Arc para agregar y quitar los clústeres de Kubernetes y los nodos de un clúster de Kubernetes, cambiar la configuración de red e instalar complementos.
<br>Asimismo, Azure Arc le permite administrar los clústeres de Kubernetes con otros servicios de Azure, como, por ejemplo:

  - Azure Monitor
  - Azure Policy
  - Control de acceso basado en roles

## <a name="run-linux-and-windows-containers"></a>Ejecución de contenedores de Linux y Windows

Azure Kubernetes Service es totalmente compatible con los contenedores basados en Linux y en Windows. Al crear un clúster de Kubernetes en Azure Stack HCI, puede elegir si quiere crear grupos de nodos (grupos de máquinas virtuales idénticas) para ejecutar contenedores de Linux, de Windows o ambos. 

Azure Kubernetes Service crea las máquinas virtuales Linux y Windows para que no tenga que administrar directamente los sistemas operativos Linux o Windows.

## <a name="secure-your-container-infrastructure"></a>Protección de la infraestructura de contenedores

Azure Kubernetes Service incluye una serie de características que permitirán proteger la infraestructura del contenedor:

- **Aislamiento basado en el hipervisor para los nodos de trabajo**: cada clúster de Kubernetes se ejecuta en su propio conjunto aislado y dedicado de máquinas virtuales, por lo que los inquilinos pueden compartir la misma infraestructura física.
- **Imágenes de Windows y Linux que mantiene Microsoft para los nodos de trabajo**: los nodos de trabajo ejecutan las imágenes de máquinas virtuales Linux y Windows que crea Microsoft para cumplir con los procedimientos recomendados de seguridad. Asimismo, Microsoft actualiza estas imágenes mensualmente con las actualizaciones de seguridad más recientes.

El área de seguridad se actualiza constantemente en la versión preliminar de Azure Kubernetes Service en Azure Stack HCI, por lo que le recomendamos que siga atento a estos cambios.

## <a name="where-can-i-run-azure-kubernetes-service"></a>¿Dónde puedo ejecutar Azure Kubernetes Service?

Azure Kubernetes Service está disponible en las siguientes plataformas:

- En la nube de Azure a través de [Azure Kubernetes Service en Azure](/azure/aks/intro-kubernetes).
- De forma local a través de Azure Kubernetes Service en Azure Stack HCI (este es el tema que se trata en este artículo).
- De forma local en un entorno de Azure Stack Hub mediante el [motor de AKS en Azure Stack Hub](../user/azure-stack-kubernetes-aks-engine-overview.md).

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>¿Cómo funciona Kubernetes en Azure Stack HCI?

El funcionamiento de Azure Kubernetes Service varía ligeramente cuando se ejecuta en Azure Stack HCI que cuando se usa en la nube de Azure:

- El servicio Kubernetes de Azure es un servicio hospedado en el que se administra automáticamente gran parte de la infraestructura de administración de Kubernetes (plano de control). Tanto el plano de control como las aplicaciones en contenedores se ejecutan en máquinas virtuales de Azure.
- Con Azure Kubernetes Service en Azure Stack HCI, debe configurar el servicio directamente en el clúster de Azure Stack HCI, lo que le permitirá gestionar el plano de control. El plano de control, las aplicaciones en contenedores y la propia instancia de Azure Kubernetes Service se ejecutan en máquinas virtuales que hospeda un clúster hiperconvergido.

Una vez que Azure Kubernetes Service se configura en el clúster de Azure Stack HCI, funciona de forma similar a la instancia de Azure Kubernetes Service hospedada: el servicio se usa para crear clústeres de Kubernetes que ejecutan las aplicaciones en contenedores. Estos clústeres de Kubernetes son grupos de máquinas virtuales que actúan como nodos de trabajo y ejecutan los contenedores de la aplicación. Igualmente, el clúster de Kubernetes también contiene un plano de control en el que se encuentran los servicios del sistema de Kubernetes que se usan para organizar los contenedores de la aplicación.

A continuación, puede ver un par de diagramas simplificados que muestran cómo se comparan las arquitecturas de Azure Kubernetes Service cuando se ejecutan en Azure y en Azure Stack HCI.

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="En esta arquitectura de la instancia de Azure Kubernetes Service hospedada en Azure se muestra cómo Azure administra los servicios de la plataforma y la mayor parte del plano de control, mientras que el cliente se encarga de administrar los clústeres de Kubernetes que ejecutan las aplicaciones en contenedores." lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="En esta arquitectura de la instancia de Azure Kubernetes Service hospedada en Azure se muestra cómo Azure administra los servicios de la plataforma y la mayor parte del plano de control, mientras que el cliente se encarga de administrar los clústeres de Kubernetes que ejecutan las aplicaciones en contenedores." lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>Qué necesita para empezar

En las secciones siguientes se resumen los elementos que necesita para ejecutar Azure Kubernetes Service en Azure Stack HCI. Para obtener información detallada, consulte qué necesita [antes de instalar Azure Kubernetes Service en Azure Stack HCI](system-requirements.md).

### <a name="on-your-windows-admin-center-system"></a>En el sistema de Windows Admin Center

El sistema de administración de Windows Admin Center presenta los siguientes requisitos:

- Windows 10 (no admitimos los servidores de Windows Admin Center en este momento).
- 60 GB de espacio disponible
- Estar registrado en Azure
- Estar en el mismo dominio que el clúster de Azure Stack HCI.

### <a name="on-the-azure-stack-hci-cluster-that-hosts-azure-kubernetes-service"></a>En el clúster de Azure Stack HCI que hospeda Azure Kubernetes Service

El clúster que ejecuta la versión 20H2 o posterior de Azure Stack HCI presenta los siguientes requisitos:

- Un máximo de cuatro servidores en el clúster de esta versión preliminar
- 1 TB de capacidad disponible en el bloque de almacenamiento para Azure Kubernetes Service
- Al menos 30 GB de memoria disponible para ejecutar máquinas virtuales de Azure Kubernetes Service
- Todos los servidores del clúster deben usar la selección de región e idioma EN-US en esta versión preliminar.

Para ver los requisitos generales de Azure Stack HCI, consulte qué debe hacer [antes de implementar Azure Stack HCI](../hci/deploy/before-you-start.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Configuración de red para Azure Stack HCI

La red conectada a las máquinas virtuales del clúster de Azure Stack HCI requiere un ámbito dedicado de direcciones IPv4 de DHCP que estén disponibles para Azure Kubernetes Service y que sean accesibles para las máquinas virtuales en el clúster de Azure Stack HCI.

No puede usar las etiquetas VLAN en la red para Azure Kubernetes Service en Azure Stack HCI. Use puertos de acceso (sin etiquetar) en los conmutadores de red para la red que usan Azure Stack HCI y las máquinas virtuales de Azure Kubernetes Service.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a usar Azure Kubernetes Service en Azure Stack HCI, consulte los siguientes artículos:

- [Revisión de los requisitos](system-requirements.md)
- [Configuración de Azure Kubernetes Service en Azure Stack HCI](create-kubernetes-cluster.md)
