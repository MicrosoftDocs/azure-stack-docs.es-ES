---
title: 'Conceptos: Aspectos básicos de Kubernetes para Azure Kubernetes Services en Azure Stack HCI'
description: Obtenga información sobre los componentes básicos del clúster y la carga de trabajo de Kubernetes y cómo se relacionan con las características de Azure Kubernetes Service en Azure Stack HCI.
author: daschott
ms.author: daschott
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 49ecf74033b2b6c68de1fa5fdfba84136c845397
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949335"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-on-azure-stack-hci"></a>Conceptos básicos de Kubernetes para Azure Kubernetes Service en Azure Stack HCI
Azure Kubernetes Service en Azure Stack HCI es una plataforma de contenedores de Kubernetes de nivel empresarial con tecnología de Azure Stack HCI. Incluye una instancia de Kubernetes principal compatible con Microsoft, complementos, un host de contenedores creado específicamente para Windows y un host de contenedores de Linux compatible con Microsoft, a fin de tener una  **experiencia sencilla de implementación y administración del ciclo de vida**.

Este artículo presenta los componentes centrales de la infraestructura de Kubernetes, como el ​​plano de control, nodos y agrupaciones de nodos. También se presentan los recursos de la carga de trabajo, como los pods, las implementaciones y los conjuntos, junto con información acerca de cómo agrupar los recursos en espacios de nombres.

## <a name="kubernetes-cluster-architecture"></a>Arquitectura del clúster de Kubernetes
Kubernetes es el componente principal de Azure Kubernetes Service en Azure Stack HCI. Azure Kubernetes Service en Azure Stack HCI usa un conjunto de configuraciones predefinidas para implementar clústeres de Kubernetes de forma eficaz y con la escalabilidad en mente.
 
La operación de implementación creará varias máquinas virtuales Linux o Windows y estas se unirán para crear clústeres de Kubernetes. 
 
El sistema implementado está listo para recibir cargas de trabajo estándar de Kubernetes, escalar estas cargas de trabajo, o incluso escalar o reducir verticalmente el número de máquinas virtuales y de clústeres según sea necesario.

Un clúster de Azure Kubernetes Service se divide en dos componentes principales en Azure Stack HCI:

- El clúster de *administración* proporciona el mecanismo de orquestación principal y la interfaz para implementar y administrar uno o más clústeres de destino.
- Los clústeres de *destino* (también conocidos como clústeres de carga de trabajo) constituyen el lugar donde se ejecutan las cargas de trabajo de la aplicación y donde un clúster de administración las administra.

![Muestra la arquitectura técnica de Azure Kubernetes Service en Azure Stack HCI](.\media\concepts\architecture.png)

## <a name="management-cluster"></a>Clúster de administración
Cuando crea un clúster de Azure Kubernetes Service en Azure Stack HCI, se crea y configura automáticamente un clúster de administración. Este clúster de administración es responsable del aprovisionamiento y la administración de los clústeres de destino en los que se ejecutan las cargas de trabajo. Un clúster de administración incluye los siguientes componentes principales de Kubernetes:
- *Servidor de la API*: el servidor de la API es el modo en el que se exponen las API de Kubernetes subyacentes. Este componente proporciona la interacción para herramientas de administración como Windows Admin Center, los módulos de PowerShell o `kubectl`.
- *Equilibrador de carga*: el equilibrador de carga es una máquina virtual Linux dedicada con una regla de equilibrio de carga para el servidor de la API del clúster de administración.

### <a name="windows-admin-center"></a>Windows Admin Center
Windows Admin Center ofrece una interfaz de usuario intuitiva para que el operador de Kubernetes administre el ciclo de vida de los clústeres de Azure Kubernetes Service en Azure Stack HCI.

### <a name="powershell-module"></a>Módulo de PowerShell
El módulo de PowerShell es una manera fácil de descargar, configurar e implementar Azure Kubernetes Service en Azure Stack HCI. El módulo de PowerShell también admite la implementación y configuración de clústeres de destino adicionales, así como la reconfiguración de los existentes.

## <a name="target-cluster"></a>Clúster de destino
El clúster de destino (o de cargas de trabajo) es una implementación de alta disponibilidad de Kubernetes con máquinas virtuales Linux para ejecutar componentes del plano de control de Kubernetes, así como nodos de trabajo de Linux. Las máquinas virtuales basadas en Windows Server Core se usan para establecer los nodos de trabajo de Windows. Un clúster de administración puede administrar uno o varios clústeres de destino.

### <a name="worker-nodes"></a>Nodos de trabajo
Para ejecutar las aplicaciones y los servicios de soporte técnico, necesitará un nodo de Kubernetes. Un clúster de destino de Azure Kubernetes Service en Azure Stack HCI tiene uno o varios nodos de trabajo, los cuales son máquinas virtuales que ejecutan los componentes de nodo de Kubernetes y que hospedan los pods y servicios que constituyen la carga de trabajo de la aplicación.

### <a name="load-balancer"></a>Equilibrador de carga
El equilibrador de carga es una máquina virtual que ejecuta Linux y HAProxy + KeepAlive para proporcionar servicios con equilibrio de carga para los clústeres de destino que implementa el clúster de administración.

Para cada clúster de destino, Azure Kubernetes Service en Azure Stack HCI agregará al menos una máquina virtual que actúa como equilibrador de carga. Además, se puede crear otro equilibrador de carga para conseguir una alta disponibilidad del servidor de la API en el clúster de destino. Cualquier servicio de Kubernetes del tipo `LoadBalancer` que se cree en el clúster de destino terminará creando una regla de equilibrio de carga en la máquina virtual que actúa como equilibrador de carga.

### <a name="add-on-components"></a>Componentes del complemento
Hay varios componentes complementarios opcionales que se pueden implementar en un clúster determinado, los más destacados son: Azure Arc, Prometheus, Grafana o Kubernetes Dashboard.

## <a name="kubernetes-components"></a>Componentes de Kubernetes
En esta sección se presentan los componentes principales de la carga de trabajo de Kubernetes que se pueden implementar en los clústeres de destino de Azure Kubernetes Service en Azure Stack HCI como, por ejemplo, pods, implementaciones y conjuntos, y también se explica cómo agrupar los recursos en espacios de nombres.

### <a name="pods"></a>pods

Kubernetes utiliza *pods* para ejecutar una instancia de la aplicación. Un pod representa una instancia individual de la aplicación. Los pods suelen tener una correspondencia 1:1 con un contenedor, aunque hay escenarios avanzados en los que un pod puede incluir varios contenedores. Los pods de varios contenedores se programan conjuntamente en el mismo nodo y permiten que los contenedores compartan recursos relacionados.

Al crear un pod, puede definir *solicitudes de recursos* para solicitar una determinada cantidad de recursos de memoria o CPU. Scheduler de Kubernetes intenta programar los pods para que se ejecuten en un nodo con recursos disponibles para satisfacer la solicitud. También puede especificar los límites de recursos máximos que impiden que un determinado pod consuma demasiados recursos de proceso del nodo subyacente. Un procedimiento recomendado consiste en incluir los límites de recursos de todos los pods para ayudar a Scheduler de Kubernetes a comprender qué recursos son necesarios y cuáles se permiten.

Para obtener más información, consulte [Pods de Kubernetes][kubernetes-pods] y [Ciclo de vida de pods de Kubernetes][kubernetes-pod-lifecycle].

Un pod es un recurso lógico, pero los contenedores se refieren al lugar donde se ejecutan las cargas de trabajo de la aplicación. Normalmente los pods son recursos desechables y efímeros, y los pods programados individualmente pierden algunas de las características de redundancia y alta disponibilidad que proporciona Kubernetes. En su lugar, los pods se implementan y administran mediante *controladores* de Kubernetes, como el controlador de implementación.

### <a name="deployments-and-yaml-manifests"></a>Implementaciones y manifiestos YAML

Una *implementación* representa uno o varios pods idénticos, administrados por el controlador de implementación de Kubernetes. Una implementación define el número de *réplicas* (pods) que se van a crear y Scheduler de Kubernetes garantiza que, si los pods o nodos tienen algún problema, se programen pods adicionales en los nodos correctos.

Puede actualizar implementaciones para cambiar la configuración de los pods, la imagen del contenedor que se ha utilizado o el almacenamiento conectado. El controlador de implementación purga y finaliza un determinado número de réplicas, crea réplicas a partir de la nueva definición de implementación y continúa el proceso hasta que se actualizan todas las réplicas de la implementación.

La mayoría de las aplicaciones sin estado debe usar el modelo de implementación en lugar de programar pods individuales. Kubernetes puede supervisar el estado de las implementaciones para asegurarse de que se ejecute el número de réplicas necesario dentro del clúster. Cuando solo programa pods individuales, los pods no se reinician si se produce un problema y no se reprograman en nodos correctos si su nodo actual encuentra un problema.

Si una aplicación requiere que un cuórum de instancias siempre esté disponible para tomar decisiones de administración, el usuario no quiere que un proceso de actualización interrumpa dicha capacidad. Los *presupuestos de interrupción de pods* se pueden usar para definir el número de réplicas de una implementación que se pueden quitar durante una actualización o la actualización de un nodo. Por ejemplo, si tiene *cinco (5)* réplicas en la implementación, puede definir una interrupción del pod de *4* para permitir que solo se elimine o se vuelva a programar una réplica a la vez. Como en el caso de los límites de recursos del pod, un procedimiento recomendado consiste en definir los presupuestos de interrupciones de pods en aplicaciones que requieren que siempre esté presente un número mínimo de réplicas.

Normalmente, las implementaciones se crean o administran con `kubectl create` o `kubectl apply`. Para crear una implementación, defina un archivo de manifiesto en formato YAML (YAML no tiene lenguaje de marcado). En el ejemplo siguiente, se crea una implementación básica del servidor web NGINX. La implementación especifica que se crearán *tres (3)* réplicas y requiere que el puerto *80* esté abierto en el contenedor. También se definen las solicitudes de recursos y los límites de CPU y memoria.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Las aplicaciones más complejas se pueden crear incluyendo también servicios como los equilibradores de carga en el manifiesto de YAML.

Para más información, consulte el artículo sobre las [implementaciones de Kubernetes][kubernetes-deployments].

##### <a name="mixed-os-deployments"></a>Implementaciones de sistema operativo mixto

Si un clúster de destino determinado de Azure Kubernetes Service en Azure Stack HCI consta de nodos de trabajo de Linux y Windows, se deben programar las cargas de trabajo en un sistema operativo que admita su aprovisionamiento. Kubernetes ofrece dos mecanismos para garantizar que las cargas de trabajo se dirijan a nodos con un sistema operativo de destino:

- *Selector de nodos* es un campo simple de la especificación del pod que limita a los pods para que solo se programen en los nodos correctos que coincidan con el sistema operativo. 
- Las *intolerancias y tolerancias* funcionan conjuntamente para garantizar que los pods no se programan en los nodos de forma involuntaria. Se puede aplicar una intolerancia a un nodo para que no acepte pods que no admitan explícitamente esa intolerancia. Esto último se logra agregando una tolerancia en la especificación del pod.

Para más información, consulte [selectores de nodo][node-selectors] e [intolerancias y tolerancias][taints-tolerations].

### <a name="statefulsets-and-daemonsets"></a>StatefulSets y DaemonSets

El controlador de implementación usa Scheduler de Kubernetes para ejecutar un determinado número de réplicas en cualquier nodo disponible con recursos disponibles. Este enfoque del uso de las implementaciones puede ser suficiente para las aplicaciones sin estado, pero no para aquellas que requieren un almacenamiento o una convención de nomenclatura persistentes. Para aplicaciones que requieren que exista una réplica en cada nodo o nodos seleccionados dentro de un clúster, el controlador de implementación no observa el modo en que las réplicas se distribuyen entre los nodos.

Hay dos recursos de Kubernetes que le permiten administrar estos tipos de aplicaciones:

- *StatefulSets*: mantenga el estado de las aplicaciones más allá de un ciclo de vida de pod individual, como el almacenamiento.
- *DaemonSets*: asegure una instancia en ejecución en cada nodo al principio del proceso de arranque de Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Con frecuencia, el desarrollo de aplicaciones modernas tiene como objetivo las aplicaciones sin estado, pero *StatefulSets* puede usarse para aplicaciones con estado, como las aplicaciones que incluyen componentes de base de datos. StatefulSet es similar a una implementación en la que se crean y administran uno o varios pods idénticos. Las réplicas de StatefulSet siguen un enfoque estable y secuencial para la implementación, el escalado, las actualizaciones y las finalizaciones. Con StatefulSet (a medida que las réplicas se vuelven a programar), la convención de nomenclatura, los nombres de red y el almacenamiento persisten.

El usuario define la aplicación en formato YAML mediante `kind: StatefulSet` y el controlador de StatefulSet, a continuación, controla la implementación y administración de las réplicas necesarias. Los datos se escriben en un almacenamiento persistente, con un almacenamiento subyacente que persiste incluso después de eliminar StatefulSet.

Para más información, consulte el [objeto StatefulSets de Kubernetes][kubernetes-statefulsets].

Las réplicas de StatefulSet se programan y se ejecutan en cualquier nodo disponible de un clúster de Azure Kubernetes Service en Azure Stack HCI. Si tiene que asegurarse de que al menos un pod del conjunto se ejecute en un nodo, puede usar DaemonSet en su lugar.

### <a name="daemonsets"></a>DaemonSets

Para la recopilación de registros específicos o las necesidades de supervisión, es posible que tenga que ejecutar un pod determinado en todos los nodos o en aquellos seleccionados. *DaemonSet* vuelve a usarse para implementar uno o varios pods idénticos, pero el controlador de DaemonSet garantiza que cada nodo especificado ejecute una instancia del pod.

El controlador de DaemonSet puede programar los pods en los nodos al principio del proceso de arranque del clúster, antes de que se haya iniciado el programador de Kubernetes predeterminado. Esta capacidad garantiza que los pods de DaemonSet se inicien antes de que se programen los pods tradicionales en una implementación o StatefulSet.

Como StatefulSets, DaemonSet se define como parte de una definición de YAML mediante `kind: DaemonSet`.

Para más información, consulte el [objeto DaemonSets de Kubernetes][kubernetes-daemonset].

### <a name="namespaces"></a>Espacios de nombres

Los recursos de Kubernetes, como los pods y las implementaciones, se agrupan lógicamente en un *espacio de nombres*. Estas agrupaciones proporcionan una manera de dividir de forma lógica un clúster de destino de Azure Kubernetes Service en Azure Stack HCI y de restringir el acceso para crear, ver o administrar los recursos. Puede crear espacios de nombres para separar grupos de negocios, por ejemplo. Los usuarios solo pueden interactuar con los recursos dentro de sus espacios de nombres asignados.

Al crear un clúster de Azure Kubernetes Service en Azure Stack HCI, están disponibles los siguientes espacios de nombres:

- *default*: este espacio de nombres es donde los pods y las implementaciones se crean de forma predeterminada cuando no se proporciona ninguno. En entornos más pequeños, puede implementar aplicaciones directamente en el espacio de nombres predeterminado sin crear separaciones lógicas adicionales. Al interactuar con la API de Kubernetes, como con `kubectl get pods`, el espacio de nombres predeterminado se utiliza cuando no se especifica ninguno.
- *kube-system*: este espacio de nombres es donde existen recursos principales, como las funciones de red como DNS y proxy o el panel de Kubernetes. Normalmente, el usuario no implementa sus propias aplicaciones en este espacio de nombres.
- *kube-public*: este espacio de nombres no se utiliza normalmente, pero puede usarse para que los recursos sean visibles en todo el clúster y para que puedan verlos todos los usuarios.

Para más información, consulte los [espacios de nombres de Kubernetes][kubernetes-namespaces].

[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[node-selectors]: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
[taints-tolerations]: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
