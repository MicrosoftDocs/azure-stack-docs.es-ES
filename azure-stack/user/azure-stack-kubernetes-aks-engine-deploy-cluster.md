---
title: Implementación de un clúster de Kubernetes con AKS-Engine en Azure Stack | Microsoft Docs
description: Implementación de un clúster de Kubernetes en Azure Stack desde una máquina virtual de cliente que ejecute el motor de AKS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: e4f10cb3e5d96942e5fe32b0d8fe3a04cf921521
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595198"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack"></a>Implementación de un clúster de Kubernetes con AKS-Engine en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede implementar un clúster de Kubernetes en Azure Stack desde una máquina virtual de cliente que ejecute el motor de AKS. En este artículo, vamos a escribir una especificación de clúster, implementar un clúster con el archivo `apimodel.json` y comprobar el clúster mediante la implementación de MySQL con Helm.

## <a name="define-a-cluster-specification"></a>Definición de una especificación de clúster

Puede indicar una especificación de clúster en un archivo de documento mediante el formato JSON denominado [modelo de API](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram). El motor de AKS usa una especificación de clúster en el modelo de API para crear el clúster. 

### <a name="update-the-api-model"></a>Actualización del modelo de API

En esta sección se examina la creación de un modelo de API para el clúster.

1.  Empiece a usar un archivo del modelo de API de [ejemplo](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) de Azure Stack y realice una copia local para su implementación. En la máquina donde ha instalado el motor de AKS, ejecute:

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

    | Campo | DESCRIPCIÓN |
    | --- | --- |
    | dnsPrefix | Escriba una cadena única que sirva para identificar el nombre de host de las máquinas virtuales. Por ejemplo, un nombre basado en el nombre del grupo de recursos. |
    | count |  Escriba el número de maestros que desea para la implementación. El mínimo para una implementación de alta disponibilidad es tres, pero se permite uno para implementaciones que no son de alta disponibilidad. |
    | vmSize |  Escriba [un tamaño admitido por Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), ejemplo `Standard_D2_v2`. |
    | Distribuciones | Escriba `aks-ubuntu-16.04`. |

8.  En la matriz `agentPoolProfiles`, actualice:

    | Campo | DESCRIPCIÓN |
    | --- | --- |
    | count | Escriba el número de agentes que desea para la implementación. |
    | vmSize | Escriba [un tamaño admitido por Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), ejemplo `Standard_D2_v2`. |
    | Distribuciones | Escriba `aks-ubuntu-16.04`. |

9.  En la matriz `linuxProfile`, actualice:

    | Campo | DESCRIPCIÓN |
    | --- | --- |
    | adminUsername | Escriba el nombre de usuario administrador para la máquina virtual. |
    | ssh | Escriba la clave pública que se usará para la autenticación SSH con máquinas virtuales. |

### <a name="more-information-about-the-api-model"></a>Más información sobre el modelo de API

- Para obtener una referencia completa de todas las opciones disponibles en el modelo de API, consulte [Definiciones de clúster](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Para obtener información destacada sobre opciones específicas para Azure Stack, consulte los [detalles específicos de la definición de clúster de Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Implementación de un clúster de Kubernetes

Cuando haya recopilado todos los valores necesarios en el modelo de API, puede crear el clúster. En este momento, debe tener:

Solicite al operador de Azure Stack que:

- Compruebe el estado del sistema, sugiera la ejecución de `Test-AzureStack` y la herramienta de supervisión de hardware del proveedor de OEM.
- Compruebe la capacidad del sistema, incluidos los recursos como la memoria, el almacenamiento y las direcciones IP públicas.
- Proporcione detalles de la cuota asociada a su suscripción para que pueda comprobar que todavía hay suficiente espacio para el número de máquinas virtuales que piensa usar.

Continúe con la implementación de un clúster:

1.  Revise los parámetros disponibles para el motor de AKS en las [marcas de la CLI](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags) de Azure Stack.

    | Parámetro | Ejemplo | DESCRIPCIÓN |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Para indicar al motor de AKS que la plataforma de destino es Azure Stack, utilice `AzureStackCloud`. |
    | identity-system | adfs | Opcional. Especifique la solución de administración de identidad si usa los Servicios de federación de Active Directory (AD FS). |
    | location | local | El nombre de la región de la instancia de Azure Stack. Para ASDK, la región se establece en `local`. |
    | resource-group | kube-rg | Escriba el nombre del nuevo grupo de recursos o seleccione uno existente. El nombre del recurso debe ser alfanumérico y estar en minúsculas. |
    | api-model | ./kubernetes-azurestack.json | Ruta de acceso al archivo de configuración del clúster o modelo de API. |
    | output-directory | kube-rg | Escriba el nombre del directorio que va a contener el archivo de salida `apimodel.json`, así como otros archivos generados. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Escriba el GUID de la entidad de servicio. El id. de cliente identificado como identificador de la aplicación cuando el administrador de Azure Stack creó la entidad de servicio. |
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

1. Obtenga la dirección IP pública de uno de los nodos maestros con el portal de Azure Stack.

2. Desde una máquina con acceso a la instancia de Azure Stack, conéctese mediante SSH al nuevo nodo maestro mediante un cliente como PuTTY o MobaXterm. 

3. Para el nombre de usuario de SSH, use "azureuser" y el archivo de clave privada del par de claves que proporcionó para la implementación del clúster.

4.  Ejecute los comandos siguientes:

    ```bash
    sudo snap install helm --classic
    kubectl -n kube-system create serviceaccount tiller
    kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
    helm init --service-account=tiller
    helm repo update
    helm install stable/mysql
    ```

5.  Para limpiar la prueba, busque el nombre usado para la implementación de MySQL. En el ejemplo siguiente, el nombre es `wintering-rodent`. Después, elimínela. 

    Ejecute los comandos siguientes:

    ```bash
    helm ls
    NAME REVISION UPDATED STATUS CHART APP VERSION NAMESPACE
    wintering-rodent 1 Thu Oct 18 15:06:58 2018 DEPLOYED mysql-0.10.1 5.7.14 default
    helm delete wintering-rodent
    ```

    La CLI mostrará:
    ```bash
    release "wintering-rodent" deleted
    ```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Solución de problemas del motor de AKS en Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)