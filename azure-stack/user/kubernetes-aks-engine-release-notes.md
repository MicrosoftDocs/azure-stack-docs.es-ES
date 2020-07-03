---
title: Notas de la versión del motor de Azure Kubernetes Service (AKS) en Azure Stack Hub
description: Conozca los pasos que debe llevar a cabo con la actualización del motor de AKS en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 06/29/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/25/2020
ms.openlocfilehash: d606e76a6c7bdaabc1828c26cd07fffba2d6fec7
ms.sourcegitcommit: bd775dfb298ba1dc67ac9ac7d591794179151026
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2020
ms.locfileid: "85764572"
---
# <a name="release-notes-for-the-aks-engine-on-azure-stack-hub"></a>Notas de la versión del motor de AKS en Azure Stack Hub
::: moniker range=">=azs-2002"
*Se aplica a la versión 0.51.0 del motor de AKS.*

En este artículo se describe el contenido del motor de Azure Kubernetes Service (AKS) en la actualización de Azure Stack Hub. La actualización incluye mejoras y correcciones de la versión más reciente del motor de AKS en la plataforma de Azure Stack Hub. Tenga en cuenta que esto no pretende documentar la información sobre la versión del motor de AKS para Azure global.

## <a name="update-planning"></a>Planeación de la actualización

El comando de actualización del motor de AKS automatiza completamente el proceso de actualización del clúster, se encarga de las máquinas virtuales y de las tareas de redes, almacenamiento, Kubernetes y orquestaciones. Antes de aplicar la actualización, asegúrese de revisar la información de este artículo.

### <a name="upgrade-considerations"></a>Consideraciones sobre la actualización

-   ¿Usa el elemento de Marketplace correcto, Distribución de imágenes de la imagen base de AKS en Ubuntu 16.04-LTS, para su versión del motor de AKS? Puede encontrar las versiones en la sección "Descarga de la nueva imagen y el motor de AKS".

-   ¿Está usando la especificación de clúster correcta (`apimodel.json`) y el grupo de recursos para el clúster de destino? Cuando implementó originalmente el clúster, este archivo se generó en el directorio de salida. Consulte los parámetros del comando `deploy` en [Implementación de un clúster de Kubernetes](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-cluster#deploy-a-kubernetes-cluster).

-   ¿Está usando una máquina confiable para ejecutar el motor de AKS desde el que realiza las operaciones de actualización?

-   Si va a actualizar un clúster operativo con cargas de trabajo activas, puede aplicar la actualización sin que esto les afecte, siempre que el clúster tenga una carga normal. Sin embargo, debe tener un clúster de copia de seguridad en caso de que haya necesidad de redirigir a los usuarios a este. Es muy recomendable disponer de un clúster de copia de seguridad.

-   Si es posible, ejecute el comando desde una máquina virtual en el entorno de Azure Stack Hub para disminuir los saltos de red y los posibles errores de conectividad.

-   Asegúrese de que la suscripción tiene una cuota suficiente para todo el proceso. El proceso asigna nuevas máquinas virtuales durante el proceso. El número resultante de máquinas virtuales sería el mismo que el original, pero planee la creación de un par de máquinas virtuales más durante el proceso.

-   No se prevén actualizaciones del sistema ni tareas programadas.

-   Configure una actualización por fases en un clúster que esté configurado con los mismos valores que el clúster de producción y pruebe la actualización allí antes de hacerlo en el clúster de producción.

### <a name="use-the-upgrade-command"></a>Uso del comando "upgrade"

Se le pedirá que use el comando `upgrade` del motor de AKS como se indica en el siguiente artículo [Actualización de un clúster de Kubernetes en Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-upgrade).

### <a name="upgrade-interruptions"></a>Interrupciones de la actualización

En algunas ocasiones se producen factores inesperados que interrumpen la actualización del clúster. Se puede producir una interrupción cuando el motor de AKS notifica un error o algo sucede en el proceso de ejecución de este. Examine el motivo de la interrupción, soluciónelo y envíe de nuevo el mismo comando "upgrade" para continuar con el proceso de actualización. El comando **upgrade** es idempotente y debería reanudar la actualización del clúster una vez que se vuelva a enviar el comando. Normalmente, las interrupciones pueden aumentar el tiempo para terminar la actualización, pero no deberían afectar a la finalización de la misma.

### <a name="estimated-upgrade-time"></a>Tiempo de actualización estimado

El tiempo estimado se sitúa entre los 12 y los 15 minutos por cada máquina virtual del clúster. Por ejemplo, un clúster de 20 nodos puede tardar aproximadamente cinco (5) horas en actualizarse.

## <a name="download-new-image-and-aks-engine"></a>Descarga de la nueva imagen y el motor de AKS

Descargue las nuevas versiones de la imagen base de Ubuntu de AKS y el motor de AKS.

Como se ha indicado en la documentación del motor de AKS para Azure Stack Hub, la implementación de un clúster de Kubernetes requiere dos componentes principales:

-   El binario aks-engine

-   Distribución de imágenes de la imagen base de AKS en Ubuntu 16.04-LTS

Hay nuevas versiones de estas disponibles con esta actualización:

-   El operador de Azure Stack Hub tendrá que descargar una nueva imagen base de AKS en Ubuntu en el marketplace de stamps:

    -   Nombre: Distribución de la imagen base de AKS en Ubuntu 16.04-LTS, mayo de 2020 (2020.05.13)

    -   Versión: 2020.05.13

    -   Siga las instrucciones que se indican en el artículo [Incorporación de requisitos previos del motor de Azure Kubernetes Service (AKS) al Marketplace de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-aks-engine).

-   El administrador del clúster de Kubernetes deberá descargar la nueva versión 0.51.0 de aks-engine. Consulte las instrucciones del siguiente artículo, [Instalación del motor de AKS en Linux para Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux). Puede seguir el mismo proceso que se usa para el instalar el clúster la primera vez. La actualización sobrescribirá el binario anterior. Por ejemplo, si utilizó el script get-akse.sh, siga los mismos pasos que se indicaron en la sección [Instalación en un entorno conectado](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux#install-in-a-connected-environment). Se puede aplicar el mismo proceso si va a instalar en un sistema con Windows. Consulte el artículo [Instalación del motor de AKS en Windows para Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-windows).

## <a name="aks-engine-and-azure-stack-version-mapping"></a>Asignación de versiones del motor de AKS y de Azure Stack

| Versión de Azure Stack Hub | Versión del motor de AKS |
| ----------------------------- | ------------------------ |
| 1910 | 0.43.0, 0.43.1 |
| 2002 | 0.48.0, 0.51.0 |

## <a name="kubernetes-version-upgrade-path-in-aks-engine-0510"></a>Ruta de actualización de la versión de Kubernetes en el motor de AKS 0.51.0

Puede encontrar la versión actual y la versión de actualización en la tabla siguiente de Azure Stack Hub. No siga el comando get-versions de aks-engine ya que incluye también las versiones admitidas de Azure global. La siguiente tabla de versiones y actualizaciones se aplica al clúster del motor de AKS en Azure Stack Hub.

| Versión actual | Actualización disponible |
| ------------------------- | ----------------------- |
| 1.14.7, 1.14.8 | 1.15.12 |
| 1.15.4, 1.15.5, 1.15.10 | 1.15.12, 1.16.9 |
| 1.15.12 | 1.16.9 |
| 1.16.8 | 1.16.9 |

En el archivo JSON del modelo de API, especifique los valores de versión secundaria y versión principal en la sección `orchestratorProfile`, por ejemplo, si desea implementar Kubernetes 1.16.9, se deben establecer los dos valores siguientes (consulte el ejemplo [kubernetes-azurestack.json](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json)):

```json  
    -   "orchestratorRelease": "1.16",
    -   "orchestratorVersion": "1.16.9"
```

## <a name="whats-new"></a>Novedades

-   Se ha agregado compatibilidad con Kubernetes 1.15.12 ([#3212](https://github.com/Azure/aks-engine/issues/3212))
-   Se ha agregado compatibilidad con Kubernetes 1.16.8 y 1.16.9 ([#3157](https://github.com/Azure/aks-engine/issues/3157) y [#3087](https://github.com/Azure/aks-engine/issues/3087))
-   Complemento del panel de Kubernetes v2.0.0 ([#3140](https://github.com/Azure/aks-engine/issues/3140))
-   Mejora de la confiabilidad de las operaciones mediante la eliminación de los bloqueos apt sin retención** Una vez que se han completado las operaciones apt ([#3049](https://github.com/Azure/aks-engine/issues/3049))
-   Mejora de la confiabilidad de las operaciones mediante la finalización de la ejecución de scripts incluso después de obtener un error al conectarse al servidor de API K8s ([#3022](https://github.com/Azure/aks-engine/issues/3022))
-   Mejora de la confiabilidad de la operación mediante la comprobación del estado de la configuración de DNS antes de conectarse ([#3002](https://github.com/Azure/aks-engine/issues/3002))
-   Corrección del problema de aprovisionamiento de los nodos de Windows con kubenet ([#3300](https://github.com/Azure/aks-engine/issues/3300))
-   Corrección del inicio de systemd y etcd para garantizar los procesos idempotentes ([#3126](https://github.com/Azure/aks-engine/issues/3126))
-   Se ha eliminado la compatibilidad con Kubernetes 1.13 y 1.14 ([#3310](https://github.com/Azure/aks-engine/issues/3310) y [#3059](https://github.com/Azure/aks-engine/issues/3059))
-   Garantía de que pod-security-policy es el primer complemento cargado ([#3313](https://github.com/Azure/aks-engine/issues/3313))
-   Actualización de la versión de Azure CNI a v1.1.0 ([#3075](https://github.com/Azure/aks-engine/issues/3075)) (versión preliminar)
-   Se han agregado características y correcciones para que se admitan contenedores de Windows en Azure Stack Hub (versión preliminar):
    -   Corrección de la recopilación de versiones de Windows ([#2954](https://github.com/Azure/aks-engine/issues/2954))
    -   Actualización del nombre de los componentes binarios de Windows en Azure Stack ([#3231](https://github.com/Azure/aks-engine/issues/3231))
    -   Actualización de la validación de imágenes de Windows en Azure Stack Hub ([#3260](https://github.com/Azure/aks-engine/issues/3260))
    -   Actualización de los discos duros virtuales de Windows para que incluyan las revisiones de mayo ([#3263](https://github.com/Azure/aks-engine/issues/3263))
    -   Actualización de los discos duros virtuales de Windows con las revisiones 4B ([#3115](https://github.com/Azure/aks-engine/issues/3115))
    -   Actualización del disco duro virtual de Windows para que incluya las revisiones de abril ([#3101](https://github.com/Azure/aks-engine/issues/3101))

## <a name="known-issues"></a>Problemas conocidos

-   La implementación de varios servicios de Kubernetes en paralelo dentro de un único clúster puede producir un error en la configuración del equilibrador de carga básico. Implemente un servicio cada vez si es posible.
-   No se admite Kubernetes 1.17 en esta versión. Aunque hay solicitudes de incorporación de cambios que aluden a la versión 1.17, en realidad no se admite esta versión.
-   La ejecución del comando aks-engine get-versions producirá información aplicable en Azure y Azure Stack Hub. Sin embargo, no hay una manera explícita de diferenciar lo que corresponde a Azure Stack Hub. No utilice este comando para averiguar qué versiones están disponibles en el área para su actualización. Use la tabla de referencia que se ha descrito anteriormente.
-   La herramienta de aks-engine es un repositorio de código fuente de uso compartido entre Azure y Azure Stack Hub. Si examina las numerosas notas de la versión y las solicitudes de incorporación de cambios puede llegar a pensar que la herramienta es compatible con otras versiones de Kubernetes y la plataforma de SO además de las mencionadas anteriormente. Ignórelas y use la tabla de versiones anterior como la guía oficial de esta actualización.
- Durante la actualización (actualización de aks-engine) de un clúster de Kubernetes de la versión 1.15.x a 1.16.x, el componente **kube-proxy** no se actualiza:
  - En entornos conectados, es posible que este problema pase desapercibido ya que no hay ningún signo en el clúster de que **kube-proxy** no se haya actualizado. Todo parece funcionar según lo previsto.
  - En entornos desconectados, puede ver este problema al ejecutar la siguiente consulta del estado de los pods del sistema y ver que los pods de **kube-proxy** no están en el estado **Listo**: `kubectl get pods -n kube-system`.
  - Para solucionar este problema, elimine el daemonset de **kube-proxy** para que Kubernetes lo reinicie con la versión correcta. Ejecute el siguiente comando: `kubectl delete ds kube-proxy -n kube-system`.

## <a name="reference"></a>Referencia

Este es el conjunto completo de notas de la versión de Azure y Azure Stack Hub combinadas:

-   https://github.com/Azure/aks-engine/releases/tag/v0.49.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.50.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.51.0
::: moniker-end
::: moniker range="<=azs-1910"
*Se aplica a la versión 0.48.0 del motor de AKS o a otras anteriores.*

En este artículo se describe el contenido del motor de Azure Kubernetes Service (AKS) en la actualización de Azure Stack Hub. La actualización incluye mejoras y correcciones de la versión más reciente del motor de AKS en la plataforma de Azure Stack Hub. Tenga en cuenta que esto no pretende documentar la información sobre la versión del motor de AKS para Azure global.

## <a name="update-planning"></a>Planeación de la actualización

El comando de actualización del motor de AKS automatiza completamente el proceso de actualización del clúster, se encarga de las máquinas virtuales y de las tareas de redes, almacenamiento, Kubernetes y orquestaciones. Antes de aplicar la actualización, asegúrese de revisar la información de este artículo.

### <a name="upgrade-considerations"></a>Consideraciones sobre la actualización

-   ¿Usa el elemento de Marketplace correcto, Distribución de imágenes de la imagen base de AKS en Ubuntu 16.04-LTS, para su versión del motor de AKS? Puede encontrar las versiones en la sección [Descarga de las nuevas versiones de la imagen base de Ubuntu de AKS y del motor de AKS](#download-new-image-and-aks-engine).

-   ¿Está usando la especificación de clúster (apimodel.json) y el grupo de recursos correctos para el clúster de destino? Cuando implementó originalmente el clúster, este archivo se generó en el directorio de salida. Consulte los parámetros del comando "deploy" en [Implementación de un clúster de Kubernetes](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-cluster#deploy-a-kubernetes-cluster).

-   ¿Está usando una máquina confiable para ejecutar el motor de AKS desde el que realiza las operaciones de actualización?

-   Si va a actualizar un clúster operativo con cargas de trabajo activas, puede aplicar la actualización sin que esto les afecte, siempre que el clúster tenga una carga normal. Sin embargo, debe tener un clúster de copia de seguridad en caso de que haya necesidad de redirigir a los usuarios a este.

-   Si es posible, ejecute el comando desde una máquina virtual en el entorno de Azure Stack Hub para disminuir los saltos de red y los posibles errores de conectividad.

-   Asegúrese de que la suscripción tiene una cuota suficiente para todo el proceso. El proceso asigna nuevas máquinas virtuales durante el proceso. El número resultante de máquinas virtuales sería el mismo que el original, pero planee la creación de un par de máquinas virtuales más durante el proceso.

-   No se prevén actualizaciones del sistema ni tareas programadas.

-   Configure una actualización por fases en un clúster que esté configurado con los mismos valores que el clúster de producción y pruebe la actualización allí antes de hacerlo en el clúster de producción.

### <a name="use-the-upgrade-command"></a>Uso del comando "upgrade"

Se le pedirá que use el comando "upgrade" del motor de AKS como se indica en el siguiente artículo [Actualización de un clúster de Kubernetes en Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-upgrade).

### <a name="upgrade-interruptions"></a>Interrupciones de la actualización

En algunas ocasiones se producen factores inesperados que interrumpen la actualización del clúster. Se puede producir una interrupción cuando el motor de AKS notifica un error o algo sucede en el proceso de ejecución de este. Examine el motivo de la interrupción, soluciónelo y envíe de nuevo el mismo comando "upgrade" para continuar con el proceso de actualización. El comando **upgrade** es idempotente y debería reanudar la actualización del clúster una vez que se vuelva a enviar el comando. Normalmente, las interrupciones pueden aumentar el tiempo para terminar la actualización, pero no deberían afectar a la finalización de la misma.

### <a name="estimated-upgrade-time"></a>Tiempo de actualización estimado

El tiempo estimado se sitúa entre los 12 y los 15 minutos por cada máquina virtual del clúster. Por ejemplo, un clúster de 20 nodos puede tardar aproximadamente cinco (5) horas en actualizarse.

## <a name="download-new-image-and-aks-engine"></a>Descarga de la nueva imagen y el motor de AKS

Descargue las nuevas versiones de la imagen base de Ubuntu de AKS y el motor de AKS.

Como se ha indicado en la documentación del motor de AKS para Azure Stack Hub, la implementación de un clúster de Kubernetes requiere dos componentes principales: 
- El binario aks-engine
- Distribución de imágenes de la imagen base de AKS en Ubuntu 16.04-LTS

Hay nuevas versiones de estas disponibles con esta actualización:

-   El operador de Azure Stack Hub tendrá que descargar una nueva imagen base de AKS en Ubuntu:

    -   Nombre: `AKS Base Ubuntu 16.04-LTS Image Distro, March 2020`
    -   Versión: `2020.03.19`
    -   Siga las instrucciones que se indican en el artículo [Incorporación de requisitos previos del motor de Azure Kubernetes Service (AKS) al Marketplace de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-aks-engine).

-   El administrador del clúster de Kubernetes deberá descargar la nueva versión 0.48.0 de aks-engine. Consulte las instrucciones de esta en el siguiente artículo, [Instalación del motor de AKS en Linux para Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux). Puede seguir el mismo proceso que se usa para el instalar el clúster la primera vez. La actualización sobrescribirá el binario anterior. Por ejemplo, si utilizó el script `get-akse.sh`, siga los mismos pasos que se indicaron en el artículo [Instalación en un entorno conectado](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux#install-in-a-connected-environment). Se puede aplicar el mismo proceso si va a instalar en un sistema con Windows. Consulte el artículo [Instalación del motor de AKS en Windows para Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-windows).

## <a name="kubernetes-version-upgrade-path"></a>Ruta de actualización de la versión de Kubernetes

Puede encontrar la versión actual y la versión de actualización en la tabla siguiente de Azure Stack Hub. No siga el comando `get-versions` de aks-engine ya que incluye también las versiones admitidas de Azure global. La siguiente tabla de versiones y actualizaciones se aplica al clúster del motor de AKS en Azure Stack Hub.

| **Versión actual** | **Actualización disponible** |
| --------------------| ----------------------|
| 1.14.7 | 1.15.10 |
| 1.14.8 | 1.15.10 |
| 1.15.4 | 1.15.10 |
| 1.15.5 | 1.15.10 |

## <a name="whats-new"></a>Novedades 

-   Soporte técnico para versión 1.15.10 de Kubernetes ([\#2834](https://github.com/Azure/aks-engine/issues/2834)). Al implementar un nuevo clúster, asegúrese de especificar en el archivo JSON del modelo de API (anteriormente conocido como archivo de definiciones del clúster) el número de la versión principal así como el número de la versión secundaria. Puede encontrar un ejemplo: [kubernetes-azurestack.json](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json):

    - `"orchestratorRelease": "1.15`,

    - `"orchestratorVersion": "1.15.10"`

    > [!Note]  
    > Si no se proporciona la versión de Kubernetes explícitamente en el archivo JSON del modelo de API, se utilizará la versión `1.15` ([\#2932](https://github.com/Azure/aks-engine/issues/2932)) y la versión del orquestador se establecerá de forma predeterminada en ` 1.15.11`, lo cual provocará un error durante la implementación del clúster.

-   Con aks-engine v0.43.1, la configuración de la frecuencia predeterminada del proveedor de nube para realizar su bucle de control y otras tareas no funciona correctamente con los límites de Resource Manager para Azure Stack Hub para las solicitudes entrantes. Esta actualización cambia los valores predeterminados de Azure Stack Hub para reducir la carga de reintentos de Resource Manager en Azure Stack Hub ([\#2861](https://github.com/Azure/aks-engine/issues/2861)).

-   El nuevo paso de comprobación en aks-engine hará que se detenga la ejecución o que aparezcan avisos si el archivo JSON del modelo de API contiene propiedades que no admite Azure Stack Hub ([\#2717](https://github.com/Azure/aks-engine/issues/2717)).

-   Con una nueva sincronización de comprobación, aks-engine comprobará la disponibilidad de la versión de la imagen base de AKS necesaria para la versión de aks-engine que ejecuta ([\#2342](https://github.com/Azure/aks-engine/issues/2342)). Esto se producirá después de analizar el archivo del modelo de API y antes de llamar a Resource Manager de Azure Stack Hub.

-   La nueva opción de aks-engine "--control-plane-only" del comando "upgrade" permite al usuario actualizar las operaciones para que solo tengan como destino las máquinas virtuales maestras ([\#2635](https://github.com/Azure/aks-engine/issues/2635)).

-   Actualizaciones de la versión 4.15.0-1071-azure del kernel de Linux para Ubuntu 16.04-LTS. Consulte "[Paquete: linux-image-4.15.0-1071-azure (4.15.0-1071.76) \[seguridad\]](https://packages.ubuntu.com/xenial/linux-image-4.15.0-1071-azure)" para más información.

-   Nuevas actualizaciones de hyperkube compatibles con las versiones 1.14.8 y 1.15.10 de Kubernetes.

-   Actualización de kubectl para que coincida con la versión de Kubernetes para el clúster. Este componente está disponible en los nodos maestros del clúster de Kubernetes. Puede ejecutarlo mediante SSH en un nodo maestro.

-   Actualizaciones del complemento de supervisión de Azure Container con la última [versión de febrero de 2020](https://github.com/microsoft/Docker-Provider/blob/ci_feature_prod/README.md) ([\#2850](https://github.com/Azure/aks-engine/issues/2850)).

-   Actualización de `coredns` a la versión v1.6.6 ([\#2555](https://github.com/Azure/aks-engine/issues/2555)).

-   Actualice `etcd` a la versión 3.3.18 ([\#2462](https://github.com/Azure/aks-engine/issues/2462)).

-   Actualice `moby` a la versión 3.0.11 ([\#2887](https://github.com/Azure/aks-engine/issues/2887)).

-   Con esta versión, el motor de AKS elimina su dependencia de `k8s.gcr.io` para usar ahora el `Kubernetes MCR registry @ mcr.microsoft.com` oficial al crear sus imágenes ([\#2722](https://github.com/Azure/aks-engine/issues/2722)).

## <a name="known-issues"></a>Problemas conocidos

-  La implementación de varios servicios de Kubernetes en paralelo dentro de un único clúster puede producir un error en la configuración del equilibrador de carga básico. Implemente un servicio cada vez si es posible.

-  La ejecución del comando aks-engine get-versions producirá información aplicable en Azure y Azure Stack Hub. Sin embargo, no hay una manera explícita de diferenciar lo que corresponde a Azure Stack Hub. No utilice este comando para averiguar qué versiones están disponibles en el área para su actualización. Use la tabla de referencia que se ha descrito anteriormente.

-  La herramienta de aks-engine es un repositorio de código fuente de uso compartido entre Azure y Azure Stack Hub. Si examina las numerosas notas de la versión y las solicitudes de incorporación de cambios puede llegar a pensar que la herramienta es compatible con otras versiones de Kubernetes y la plataforma de SO además de las mencionadas anteriormente. Ignórelas y use la tabla de versiones anterior como la guía oficial de esta actualización.

## <a name="reference"></a>Referencia

La siguiente es una lista de algunos de los errores solucionados así como todo el conjunto de notas de las versiones 0.44.0 a la 0.48.0. Tenga en cuenta que en la última lista se incluirán elementos de Azure y Azure Stack Hub.

### <a name="bug-fixes"></a>Corrección de errores

-   Faltan comillas para `userAssignedIdentityId` en el archivo `azure.json` de Windows ([\#2327](https://github.com/Azure/aks-engine/issues/2327))

-   Los complementos de `update config` son solo de actualización ([\#2282](https://github.com/Azure/aks-engine/issues/2282))

-   Incremento del tiempo de expiración para obtener la dirección IP de administración en los nodos de Windows ([\#2284](https://github.com/Azure/aks-engine/issues/2284))

-   Incorporación del archivo ZIP de Azure CNI 1.0.28 en el disco duro virtual de Windows ([\#2268](https://github.com/Azure/aks-engine/issues/2268))

-   Orden predeterminado correcto para establecer IPAddressCount ([\#2358](https://github.com/Azure/aks-engine/issues/2358))

-   Se ha actualizado para utilizar un único archivo YAML de omsagent para todas las versiones k8s para evitar los errores manuales y conseguir un mantenimiento fácil ([\#2692](https://github.com/Azure/aks-engine/issues/2692))

### <a name="release-notes"></a>Notas de la versión

Este es el conjunto completo de notas de la versión de Azure y Azure Stack Hub combinadas

-   https://github.com/Azure/aks-engine/releases/tag/v0.44.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.45.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.3
-   https://github.com/Azure/aks-engine/releases/tag/v0.47.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.48.0
::: moniker-end
## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
