---
title: Conexión de un clúster de Azure Kubernetes Service en Azure Stack HCI a Azure Arc para Kubernetes
description: Conexión de un clúster de Azure Kubernetes Service en Azure Stack HCI a Azure Arc para Kubernetes
author: abha
ms.topic: how-to
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 24dc2efdc591404db1bbfc30cf9c1bc83e2ed356
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949331"
---
# <a name="connect-an-azure-kubernetes-service-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes"></a>Conexión de un clúster de Azure Kubernetes Service en Azure Stack HCI a Azure Arc para Kubernetes

Cuando un clúster de Azure Kubernetes Service en Azure Stack HCI esté asociado a Azure Arc, aparecerá en Azure Portal. Tendrá un identificador de Azure Resource Manager y una identidad administrada. Los clústeres se asocian a suscripciones estándar de Azure, viven en un grupo de recursos y pueden recibir etiquetas como cualquier otro recurso de Azure.

Para conectar un clúster de Kubernetes a Azure, es preciso que el administrador de clústeres implemente agentes. Estos agentes se ejecutan en un espacio de nombres de Kubernetes denominado `azure-arc` y son implementaciones estándar de Kubernetes. Los agentes son responsables de la conectividad con Azure, de la recopilación de registros y métricas de Azure Arc y de la supervisión de solicitudes de configuración.

Kubernetes habilitado para Azure Arc admite SSL estándar del sector para proteger los datos en tránsito. Además, los datos se almacenan cifrados en reposo en una base de datos de Azure Cosmos DB para garantizar la confidencialidad de los datos.

Los pasos siguientes proporcionan un tutorial sobre la incorporación de clústeres de Azure Kubernetes Service en Azure Stack HCI a Azure Arc. **Puede omitir estos pasos si ya ha incorporado el clúster de Kubernetes a Azure Arc mediante Windows Admin Center.**

## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene listos los requisitos siguientes:

* Un clúster de Azure Kubernetes Service en Azure Stack HCI que tenga al menos un nodo de trabajo de Linux en funcionamiento. 

* Necesitará un archivo kubeconfig para acceder al clúster y a al rol administrador del clúster en el clúster para la implementación de los agentes de Kubernetes habilitados para Arc.
* Tener instalado el módulo de PowerShell correspondiente a Azure Kubernetes Service en Azure Stack HCI.
* Se requiere la versión 2.3 o posteriores de la CLI de Azure para instalar las extensiones de la CLI de Kubernetes habilitado para Azure Arc. [Instalación de la CLI de Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). También puede actualizar a la última versión para asegurarse de que tiene la versión 2.3 o posterior de la CLI de Azure.
* Una suscripción de Azure de la que sea propietario o colaborador. 
* Ejecute los comandos de este documento en una ventana administrativa de PowerShell.


## <a name="network-requirements"></a>Requisitos de red

Los agentes de Azure Arc requieren que funcionen los siguientes protocolos, puertos y direcciones URL de salida.

* TCP en el puerto 443--> `https://:443`
* TCP en el puerto 9418--> `git://:9418`

| Punto de conexión (DNS)                                                                                               | Descripción                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Necesario para que el agente se conecte a Azure y registre el clúster                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Punto de conexión de plano de datos para que el agente inserte información de la configuración de estado y recuperación de cambios                                      |
| `https://docker.io`                                                                                            | Necesario para extraer imágenes de contenedor                                                                                         |
| `https://github.com`, git://github.com                                                                         | Los repositorios de GitOps de ejemplo se hospedan en GitHub. El agente de configuración requiere conectividad con el punto de conexión de Git que se especifique. |
| `https://login.microsoftonline.com`                                                                            | Necesario para capturar y actualizar tokens de Azure Resource Manager                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Necesario para extraer imágenes de contenedor para agentes de Azure Arc                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Necesario para extraer certificados de identidad administrados que haya asignado el sistema                                                                  |

## <a name="step-1-log-in-to-azure"></a>Paso 1: Inicio de sesión en Azure

Inicie sesión en Azure y después de iniciar sesión, establezca una suscripción de Azure de la que sea propietario o colaborador como suscripción predeterminada.

```console
az login
az account set --subscription "00000000-aaaa-bbbb-cccc-000000000000"
```

## <a name="step-2-register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Paso 2: Registre los dos proveedores para Kubernetes habilitado para Azure Arc:

Puede omitir este paso si ya ha registrado los dos proveedores para el servicio Kubernetes habilitado para Azure Arc en la suscripción. El registro es un proceso asincrónico y se debe realizar una vez por cada suscripción. Puede tardar 10 minutos, aproximadamente. 

```console
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.KubernetesConfiguration
```

Puede comprobar si está registrado con los siguientes comandos:

```console
az provider show -n Microsoft.Kubernetes -o table
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="step-3-create-a-resource-group"></a>Paso 3: Crear un grupo de recursos

Necesita un grupo de recursos que contenga el recurso de clúster conectado. Puede usar un grupo de recursos existente en las ubicaciones Este de EE. UU. u Oeste de Europa. Si no tiene ningún grupo de recursos existente en las ubicaciones Este de EE. UU. u Oeste de Europa, use el siguiente comando para crear un nuevo grupo de recursos:

```console
az group create --name AzureArcTest -l EastUS -o table
```

## <a name="step-4-create-a-new-service-principal"></a>Paso 4: Creación de una entidad de servicio


Puede omitir este paso si ya ha creado una entidad de servicio con el rol `contributor` y conoce el appID, la contraseña y los valores de inquilino de la entidad de servicio.

Cree una nueva entidad de servicio con un nombre descriptivo. Este nombre debe ser único en el inquilino de Azure Active Directory. El rol predeterminado de una entidad de servicio es `Contributor`. Este rol tiene permiso total para leer y escribir en una cuenta de Azure. También puede volver a usar esta entidad de servicio para incorporar varios clústeres en Azure Arc. Establezca el ámbito de la entidad de servicio en *suscripciones/grupo de recursos*. *Asegúrese de guardar los valores de appID, contraseña e inquilino de la entidad de servicio, ya que necesitará estos detalles en los pasos siguientes.*

```console
az ad sp create-for-RBAC --name "azure-arc-for-k8s" --scope /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}
```

**Salida:**

```
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-arc-for-k8s",
  "name": "https://azure-arc-for-k8s",
  "password": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
  "tenant": "ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj"
}
```
## <a name="step-5-save-service-principal-details"></a>Paso 5: Guardar los detalles de la entidad de servicio
Guarde los valores de appId, contraseña e inquilino de la entidad de servicio creada y el nombre del clúster, el identificador de la suscripción de Azure, el nombre del grupo de recursos y la ubicación en las variables de PowerShell. Así se asegurará de que puede reutilizar los detalles en otros tutoriales. Asegúrese de que también guarda estos valores en un bloc de notas en caso de que desee cerrar la sesión de PowerShell.

```PowerShell
$clusterName = #<name of your Kubernetes cluster>
$resourceGroup = #<Azure resource group to store your connected Kubernetes cluster in Azure Arc>
$location = #<Azure resource group location. This can only be eastus or westeurope for Azure Arc for Kubernetes>
$subscriptionId = #<Azure subscription Id>
$appId = #<appID from the service principal created above>
$password = #<password from the service principal created above>
$tenant = #<tenant from the service principal created above>
```
Asegúrese de que ha asignado los valores correctos a las variables mediante la ejecución de:

```PowerShell
echo $clusterName 
echo $resourceGroup
echo $location 
echo $subscriptionId 
echo $appId 
echo $password 
echo $tenant 
```

## <a name="step-6-connect-to-azure-arc-using-service-principal-and-the-aks-hci-powershell-module"></a>Paso 6: Conexión a Azure Arc mediante la entidad de servicio y el módulo Aks-Hci de PowerShell

A continuación, vamos a conectar el clúster de Kubernetes a Azure mediante la entidad de servicio y el módulo Aks-Hci de PowerShell. En este paso se implementan agentes de Azure Arc para Kubernetes en el espacio de nombres `azure-arc`.

Haga referencia a la entidad de servicio recién creada y ejecute el comando `Install-AksHciArcOnboarding` disponible en el módulo Aks-Hci de PowerShell.

```PowerShell
Install-AksHciArcOnboarding -clusterName $clusterName -resourcegroup $resourceGroup -location $location -subscriptionid $subscriptionId -clientid $appId -clientsecret $password -tenantid $tenant
```
## <a name="verify-connected-cluster"></a>Comprobación del clúster conectado

Puede ver el recurso de clúster de Kubernetes en [Azure Portal](https://portal.azure.com/). Una vez que tenga el portal abierto en el explorador, navegue hasta el grupo de recursos y el recurso Kubernetes habilitado para Azure Arc según las entradas de nombre de recurso y nombre del grupo de recursos que se usaron anteriormente en el comando `Install-AksHciArcOnboarding` de PowerShell.

> [!NOTE]
> Después de la incorporación del clúster, los metadatos del clúster (versión del clúster, versión del agente, número de nodos) tardan entre 5 y 10 minutos en aparecer en la página de información general del recurso de Kubernetes compatible con Azure Arc en Azure Portal.

Para eliminar el clúster, o conectarlo si está detrás de un servidor proxy de salida, visite [Conexión de un clúster de Kubernetes habilitado para Azure Arc](/azure/azure-arc/kubernetes/connect-cluster).

## <a name="azure-arc-agents-for-kubernetes"></a>Agentes de Azure Arc para Kubernetes

Kubernetes habilitado para Azure Arc implementa algunos operadores en el espacio de nombres `azure-arc`. Puede ver estas implementaciones y pods aquí:

```console
kubectl -n azure-arc get deployments,pods
```

Kubernetes habilitado para Azure Arc consta de algunos agentes (operadores) que se ejecutan en el clúster implementado en el espacio de nombres `azure-arc`.

* `deployment.apps/config-agent`: busca en el clúster conectado los recursos de configuración de control de código fuente aplicados en el clúster y actualiza el estado de cumplimiento.
* `deployment.apps/controller-manager`: es un operador de operadores y organiza las interacciones entre los componentes de Azure Arc.
* `deployment.apps/metrics-agent`: recopila métricas de otros agentes de Arc para asegurarse de que presentan un rendimiento óptimo.
* `deployment.apps/cluster-metadata-operator`: Recopila metadatos del clúster (la versión del clúster, el número de nodos y la versión del agente de Azure Arc).
* `deployment.apps/resource-sync-agent`: sincroniza con Azure los metadatos de clúster mencionados anteriormente.
* `deployment.apps/clusteridentityoperator`: Kubernetes habilitado para Azure Arc admite actualmente la identidad asignada por el sistema. clusteridentityoperator mantiene el certificado de Managed Service Identity (MSI) que usan otros agentes para la comunicación con Azure.
* `deployment.apps/flux-logs-agent`: recopila registros de los operadores de Flux implementados como parte de la configuración de control de código fuente.

## <a name="next-steps"></a>Pasos siguientes

* [Uso de GitOps en un clúster conectado](/azure/azure-arc/kubernetes/use-gitops-connected-cluster)
* [Uso de Azure Policy para controlar la configuración del clúster](/azure/azure-arc/kubernetes/use-azure-policy)
* [Habilitar Azure Monitor en un clúster de Kubernetes habilitado para Azure Arc](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)
