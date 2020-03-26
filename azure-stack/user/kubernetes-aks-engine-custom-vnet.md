---
title: Implementación de un clúster de Kubernetes en una red virtual personalizada en Azure Stack Hub
description: Aprenda a implementar un clúster de Kubernetes en una red virtual personalizada en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: aac2f9a0991bdae7f15d7fc54517a880ab384785
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2020
ms.locfileid: "80068941"
---
# <a name="deploy-a-kubernetes-cluster-to-a-custom-virtual-network-on-azure-stack-hub"></a>Implementación de un clúster de Kubernetes en una red virtual personalizada en Azure Stack Hub 

Los clústeres de Kubernetes se pueden implementar mediante el motor de Azure Kubernetes Service (AKS) en una red virtual personalizada. En este artículo se examina la búsqueda de la información que se necesita en una red virtual. Puede encontrar los pasos necesarios para calcular las direcciones IP que usa el clúster y establecer los valores en el modelo de API, así como establecer la tabla de rutas y el grupo de seguridad de red.

En Azure Stack Hub, el clúster de Kubernetes que usa el motor de AKS usa el complemento de red kubenet. Para ver una explicación acerca de las redes kubenet en Azure, consulte [Uso de redes kubenet con intervalos de direcciones IP propios en Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-kubenet).

## <a name="create-custom-virtual-network"></a>Creación de una red virtual personalizada

Debe tener una red virtual personalizada en su instancia de Azure Stack Hub. Para más información, consulte [Inicio rápido: Creación de una red virtual mediante Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

Cree una subred en la red virtual. Tendrá que obtener el identificador de recurso de la subred y el intervalo de direcciones IP. Tanto el identificador de recurso como el intervalo los usará en el modelo de API al implementar el clúster.

1. Abra el portal de usuarios de Azure Stack Hub en la instancia de Azure Stack Hub.
2. Seleccione **Todos los recursos**.
3. Escriba el nombre de la red virtual en el cuadro de búsqueda.
4. Seleccione **Subredes** >  **+ Subredes** para agregar una subred.
5. Especifique los valores correspondientes en los campos **Nombre** e **Intervalo de direcciones**, para lo que debe usar la notación CIDR. Seleccione **Aceptar**.
4. Seleccione **Propiedades** en la hoja **Redes virtuales**. Copie el valor del campo **Id. de recurso** y agregue `/subnets/<nameofyoursubnect>`. Este valor se usará para la clave `vnetSubnetId` en el modelo de API del clúster. El identificador de recurso de la subred usa el siguiente formato:<br>`/subscriptions/SUB_ID/resourceGroups/RG_NAME/providers/Microsoft.Network/virtualNetworks/VNET_NAME/subnets/SUBNET_NAME`

    ![Identificador de recurso de red virtual](media/kubernetes-aks-engine-custom-vnet/virtual-network-id.png)

5. Seleccione **Subredes** en la hoja **Redes virtuales**. Seleccione el nombre de la subred, por ejemplo, default.
    
    ![Bloque CIDR de la red virtual](media/kubernetes-aks-engine-custom-vnet/virtual-network-cidr-block.png)
    
6. En la hoja de la subred, anote el intervalo de direcciones y el bloque CIDR de la red virtual, por ejemplo: `10.1.0.0 - 10.1.0.255 (256 addresses)` y `10.1.0.0/24`.



## <a name="get-the-ip-address-block"></a>Obtención del bloque de direcciones IP

El motor de AKS admite la implementación en una red virtual existente. Al realizar la implementación en una subred existente, el clúster usará un bloque de direcciones IP consecutivas para los agentes y otro para los maestros.

Necesitará establecer dos valores. Necesitará saber el número de direcciones IP que tendrá que reservar para el clúster y la primera IP estática consecutiva dentro del espacio de IP de la subred.

Si se usan varios nodos maestros, el motor de AKS requiere un intervalo de hasta 16 direcciones IP no utilizadas. El clúster usará una dirección IP por cada nodo maestro, hasta un máximo de cinco maestros. El motor de AKS necesitará también las 10 siguientes direcciones IP después del último nodo maestro como direcciones IP de reserva para la capacidad de aumento. Por último, el equilibrador de carga usará una dirección IP adicional después de los maestros y de la reserva de la capacidad de aumento para hacer un total de 16.

Al colocar su bloque de direcciones IP, la subred requiere las siguientes asignaciones de las direcciones IP existentes:
 - Tanto las cuatro primeras direcciones IP como la última están reservadas y no se pueden usar en ninguna subred de Azure
 - Debe dejarse abierto un búfer de 16 direcciones IP.
 - El valor de la primera dirección IP del clúster debe estar hacia el final del espacio de direcciones para evitar conflictos de IP. Si es posible, asigne la propiedad `firstConsecutiveStaticIP` a una dirección IP cerca del *final* del espacio de direcciones IP disponible en la subred.

En el ejemplo siguiente, puede ver cómo se rellena el intervalo IP siguiendo estas consideraciones en una subred. Es para tres nodos maestros. Si usa una subred con 256 direcciones, por ejemplo 10.1.0.0/24, tendrá que establecer su primera dirección IP estática consecutiva en 207. En la tabla siguiente se muestran las direcciones y consideraciones:

| Intervalo para la subred /24 | Number | Nota: |
| --- | --- | --- |
| 10.1.0.0  - 10.1.03 | 4 | Reservado en la subred de Azure. |
| **10.1.0.224**-10.1.0.238 | 14 | Recuento de direcciones IP de un clúster definido por el motor de AKS.<br><br> 3 direcciones IP para 3 nodos maestros<br>10 direcciones IP para la capacidad de aumento<br>1 dirección IP para el equilibrador de carga |
| 10.1.0.239 - 10.1.0.255 | 16 | Búfer de 16 direcciones IP. |
| 10.1.0.256 | 1 | Reservado en la subred de Azure. |

En este ejemplo, la propiedad `firstConsecutiveStaticIP` sería `10.1.0.224`.

En el caso de subredes mayores, por ejemplo /16 (con más de 60 000 direcciones), es posible que no resulte práctico establecer las asignaciones de direcciones IP estáticas al final del espacio de red. Establezca el intervalo de direcciones IP estáticas del clúster fuera de las 24 primeras direcciones del espacio de IP, con el fin de que el clúster pueda ser resistente al reclamar direcciones.


## <a name="update-the-api-model"></a>Actualización del modelo de API

Actualice el modelo de API que se usa para implementar el clúster desde el motor AKS en su red virtual personalizada.

En **masterProfile**, establezca los valores siguientes:

| Campo | Ejemplo | Descripción |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Especifique el identificador de recurso de la subred.  |
| firstConsecutiveStaticIP | 10.1.0.224 | Asigne a la propiedad de configuración `firstConsecutiveStaticIP` una dirección IP que se encuentre cerca del *final* del espacio de direcciones IP disponible en la subred deseada. `firstConsecutiveStaticIP` solo se aplica al conjunto maestro. |

En **agentPoolProfiles**, establezca los valores siguientes:

| Campo | Ejemplo | Descripción |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Especifique el identificador de la ruta de acceso de Azure Resource Manager de la subred.  |

Por ejemplo:

```json
"masterProfile": {
  ...
  "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
  "firstConsecutiveStaticIP": "10.1.0.224",
  ...
},
...
"agentPoolProfiles": [
  {
    ...
    "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
    ...
  },

```

## <a name="deploy-your-cluster"></a>Implementación del clúster

Después de agregar los valores al modelo de API puede implementar el clúster desde la máquina cliente mediante el comando `deploy` que usa el motor AKS. Para ver las instrucciones, consulte la sección [Implementación de un clúster de Kubernetes](azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster).

## <a name="set-the-route-table-and-network-security-group"></a>Establecimiento de la tabla de rutas y del grupo de seguridad de red

Después de implementar el clúster, vuelva a la red virtual en el portal de usuario de Azure Stack. Establezca tanto la tabla de rutas como el grupo de seguridad de red en la hoja de la subred. Si no usa Azure CNI, por ejemplo, `networkPlugin`: `kubenet` en el objeto de configuración del modelo de API `kubernetesConfig`. Una vez que haya implementado correctamente un clúster en su red virtual personalizada, obtenga el identificador del recurso Tabla de rutas en la hoja **Network** (Red) del grupo de recursos del clúster.

1. Abra el portal de usuarios en la instancia de Azure Stack Hub.
2. Seleccione **Todos los recursos**.
3. Escriba el nombre de la red virtual en el cuadro de búsqueda.
4. Seleccione **Subredes** y, después, seleccione el nombre de la subred que contiene el clúster.
    
    ![Tabla de rutas y grupo de seguridad de red](media/kubernetes-aks-engine-custom-vnet/virtual-network-rt-nsg.png)
    
5. Seleccione **Tabla de rutas** y, después, seleccione la tabla de rutas del clúster.
6. Seleccione **Grupo de seguridad de red** y, después, seleccione el grupo del clúster.

> [!Note]  
> La red virtual personalizada de un clúster Windows de Kubernetes tiene un [problema conocido](https://github.com/Azure/aks-engine/issues/371).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).  
- Consulte [Introducción a Azure Monitor para contenedores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).