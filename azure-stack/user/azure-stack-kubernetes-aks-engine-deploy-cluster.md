---
title: Implementación de un clúster de Kubernetes con el motor de AKS en Azure Stack Hub
description: Implementación de un clúster de Kubernetes en Azure Stack Hub desde una máquina virtual de cliente que ejecute el motor de AKS.
author: mattbriggs
ms.topic: article
ms.date: 01/10/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: f7d9b735a3b4936a5bb97e60e4c84f4b35b29baa
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492298"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>Implementación de un clúster de Kubernetes con el motor de AKS en Azure Stack Hub

Puede implementar un clúster de Kubernetes en Azure Stack Hub desde una máquina virtual de cliente que ejecute el motor de AKS. En este artículo, vamos a escribir una especificación de clúster, implementar un clúster con el archivo `apimodel.json` y comprobar el clúster mediante la implementación de MySQL con Helm.

## <a name="define-a-cluster-specification"></a>Definición de una especificación de clúster

Puede indicar una especificación de clúster en un archivo de documento mediante el formato JSON denominado [modelo de API](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram). El motor de AKS usa una especificación de clúster en el modelo de API para crear el clúster. 

### <a name="update-the-api-model"></a>Actualización del modelo de API

En esta sección se examina la creación de un modelo de API para el clúster.

1.  Empiece a usar un archivo del modelo de API de [ejemplo](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) de Azure Stack Hub y realice una copia local para su implementación. En la máquina donde ha instalado el motor de AKS, ejecute:

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > Si está desconectado, puede descargar el archivo y copiarlo manualmente en la máquina desconectada en la que piensa editarlo. Puede copiar el archivo en la máquina Linux con herramientas como [PuTTY o WinSCP](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html).

2.  Para abrirlo en un editor, puede usar nano:

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > Si no tiene nano instalado, puede instalar nano en Ubuntu: `sudo apt-get install nano`.

3.  En el archivo kubernetes-azurestack.json, busque `orchestratorRelease`. Seleccione una de las versiones admitidas de Kubernetes. Por ejemplo, 1.14, 1.15. Las versiones se suelen actualizar. Especifique la versión como x.xx en lugar de x.xx.x. Para obtener una lista de las versiones actuales, consulte [Versiones admitidas de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Puede averiguar la versión compatible mediante la ejecución del siguiente comando del motor de AKS:

    ```bash
    aks-engine get-versions
    ```

4.  Busque `customCloudProfile` y proporcione la dirección URL al portal del inquilino. Por ejemplo, `https://portal.local.azurestack.external`. 

5. Agregue `"identitySystem":"adfs"` si utiliza AD FS. Por ejemplo,

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!Note]  
    > Si usa Azure AD para su sistema de identidades, no es necesario agregar el campo **identitySystem**.

6. Busque `portalURL` y proporcione la dirección URL al portal del inquilino. Por ejemplo, `https://portal.local.azurestack.external`.

7.  En la matriz `masterProfile`, establezca los campos siguientes:

    | Campo | Descripción |
    | --- | --- |
    | dnsPrefix | Escriba una cadena única que sirva para identificar el nombre de host de las máquinas virtuales. Por ejemplo, un nombre basado en el nombre del grupo de recursos. |
    | count |  Escriba el número de maestros que desea para la implementación. El mínimo para una implementación de alta disponibilidad es tres, pero se permite uno para implementaciones que no son de alta disponibilidad. |
    | vmSize |  Escriba [un tamaño admitido por Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), ejemplo `Standard_D2_v2`. |
    | Distribuciones | Escriba `aks-ubuntu-16.04`. |

8.  En la matriz `agentPoolProfiles`, actualice:

    | Campo | Descripción |
    | --- | --- |
    | count | Escriba el número de agentes que desea para la implementación. |
    | vmSize | Escriba [un tamaño admitido por Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), ejemplo `Standard_D2_v2`. |
    | Distribuciones | Escriba `aks-ubuntu-16.04`. |

9.  En la matriz `linuxProfile`, actualice:

    | Campo | Descripción |
    | --- | --- |
    | adminUsername | Escriba el nombre de usuario administrador para la máquina virtual. |
    | ssh | Escriba la clave pública que se usará para la autenticación SSH con máquinas virtuales. Si usa Putty, abra el generador de claves de PuTTy para cargar la clave privada y la clave pública que comienza con ssh-rsa, como en el ejemplo siguiente. Puede usar la clave generada al crear el cliente de Linux, pero **necesita copiar la clave pública para que sea un texto de una sola línea, tal como se muestra en el ejemplo**.|

    ![Generador de claves de PuTTY](media/azure-stack-kubernetes-aks-engine-deploy-cluster/putty-key-generator.png)

### <a name="more-information-about-the-api-model"></a>Más información sobre el modelo de API

- Para obtener una referencia completa de todas las opciones disponibles en el modelo de API, consulte [Definiciones de clúster](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Para obtener información destacada sobre opciones específicas para Azure Stack Hub, consulte los [detalles específicos de la definición de clúster de Azure Stack Hub](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Implementación de un clúster de Kubernetes

Cuando haya recopilado todos los valores necesarios en el modelo de API, puede crear el clúster. En este momento, debe tener:

Solicite al operador de Azure Stack Hub que:

- Compruebe el estado del sistema, sugiera la ejecución de `Test-AzureStack` y la herramienta de supervisión de hardware del proveedor de OEM.
- Compruebe la capacidad del sistema, incluidos los recursos como la memoria, el almacenamiento y las direcciones IP públicas.
- Proporcione detalles de la cuota asociada a su suscripción para que pueda comprobar que todavía hay suficiente espacio para el número de máquinas virtuales que piensa usar.

Continúe con la implementación de un clúster:

1.  Revise los parámetros disponibles para el motor de AKS en las [marcas de la CLI](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags) de Azure Stack Hub.

    | Parámetro | Ejemplo | Descripción |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Para indicar al motor de AKS que la plataforma de destino es Azure Stack Hub, utilice `AzureStackCloud`. |
    | identity-system | adfs | Opcional. Especifique la solución de administración de identidad si usa los Servicios de federación de Active Directory (AD FS). |
    | ubicación | local | El nombre de la región de la instancia de Azure Stack Hub. Para ASDK, la región se establece en `local`. |
    | resource-group | kube-rg | Escriba el nombre del nuevo grupo de recursos o seleccione uno existente. El nombre del recurso debe ser alfanumérico y estar en minúsculas. |
    | api-model | ./kubernetes-azurestack.json | Ruta de acceso al archivo de configuración del clúster o modelo de API. |
    | output-directory | kube-rg | Escriba el nombre del directorio que va a contener el archivo de salida `apimodel.json`, así como otros archivos generados. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Escriba el GUID de la entidad de servicio. El identificador de cliente indicado como identificador de la aplicación cuando el administrador de Azure Stack Hub creó la entidad de servicio. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Escriba el secreto de la entidad de servicio. Este es el secreto de cliente que configuró al crear el servicio. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | escriba el identificador de la suscripción. Para más información, consulte [Suscripción a una oferta](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |

    Este es un ejemplo:

    ```bash  
    aks-engine deploy \
    --azure-env AzureStackCloud \
    --location <for asdk is local> \
    --resource-group kube-rg \
    --api-model ./kubernetes-azurestack.json \
    --output-directory kube-rg \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

2.  Si por algún motivo se produce un error en la ejecución después de que se haya creado el directorio de salida, puede corregir el problema y volver a ejecutar el comando. Si va a volver a ejecutar la implementación y ha usado el mismo directorio de salida antes, el motor de AKS devolverá un error que indica que el directorio ya existe. Puede sobrescribir el directorio existente mediante el uso de la marca `--force-overwrite`.

3.  Guarde la configuración de clúster del motor de AKS en una ubicación segura y cifrada.

    Busque el archivo `apimodel.json`. Cópielo en una ubicación segura. Este archivo se usará como entrada en todas las demás operaciones del motor de AKS.

    El objeto `apimodel.json` generado contiene la entidad de seguridad de servicio, el secreto y la clave pública SSH que se usa en el modelo de API de entrada. También tiene todos los demás metadatos necesarios para que el motor de AKS realice todas las demás operaciones. Si lo pierde, el motor de AKS no podrá configurar el clúster.

    Los secretos están **sin cifrar**. Mantenga el archivo en un lugar seguro y cifrado. 

## <a name="verify-your-cluster"></a>Comprobación del clúster

Compruebe el clúster mediante la implementación de MySQL con Helm.

1. Obtenga la dirección IP pública de uno de los nodos maestros con el portal de Azure Stack Hub.

2. Desde una máquina con acceso a la instancia de Azure Stack Hub, conéctese mediante SSH al nuevo nodo maestro mediante un cliente como PuTTY o MobaXterm. 

3. Para el nombre de usuario de SSH, use "azureuser" y el archivo de clave privada del par de claves que proporcionó para la implementación del clúster.

4. Ejecute los siguientes comandos para crear una implementación de ejemplo de un nodo maestro de Redis (solo para marcas conectadas):

   ```bash
   kubectl apply -f https://k8s.io/examples/application/guestbook/redis-master-deployment.yaml
   ```

    1. Consulte la lista de pods:

       ```bash
       kubectl get pods
       ```

    2. La respuesta debe ser similar a la siguiente:

       ```shell
       NAME                            READY     STATUS    RESTARTS   AGE
       redis-master-1068406935-3lswp   1/1       Running   0          28s
       ```

    3. Vea los registros de implementación:

       ```shell
       kubectl logs -f <pod name>
       ```

    Para una implementación completa de una aplicación PHP de ejemplo que incluya el nodo maestro de Redis, siga [las instrucciones que se indican aquí](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/).

5. En el caso de una marca desconectada, los siguientes comandos deben ser suficientes:

    1. En primer lugar, compruebe que los puntos de conexión del clúster se están ejecutando:

       ```bash
       kubectl cluster-info
       ```

       La salida debe tener una apariencia similar a la siguiente:

       ```shell
       Kubernetes master is running at https://democluster01.location.domain.com
       CoreDNS is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
       kubernetes-dashboard is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy
       Metrics-server is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
       ```

    2. A continuación, revise los estados del nodo:

       ```bash
       kubectl get nodes
       ```

       La salida debe ser similar a la siguiente:

       ```shell
       k8s-linuxpool-29969128-0   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-1   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-2   Ready      agent    9d    v1.15.5
       k8s-master-29969128-0      Ready      master   9d    v1.15.5
       k8s-master-29969128-1      Ready      master   9d    v1.15.5
       k8s-master-29969128-2      Ready      master   9d    v1.15.5
       ```

6. Para eliminar la implementación del POD de Redis del paso anterior, ejecute el siguiente comando:

    ```bash
    kubectl delete deployment -l app=redis
    ```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Solución de problemas del motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-troubleshoot.md)
