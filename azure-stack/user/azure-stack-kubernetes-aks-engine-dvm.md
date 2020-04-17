---
title: Traslado del clúster del elemento de Marketplace al motor de AKS en Azure Stack Hub
description: Aprenda a trasladar el clúster del elemento de Marketplace al motor de AKS en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: f15c870a1b256ffa546672a3abde2fc68f9baa4f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "80069003"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack-hub"></a>Traslado del clúster del elemento de Marketplace al motor de AKS en Azure Stack Hub

El elemento Kubernetes de Marketplace de Azure Stack Hub usa una plantilla de Azure Resource Manager para implementar una máquina virtual de implementación con el fin de descargar e instalar el motor de AKS y generar el modelo de API de entrada que se usa para describir el clúster, después de que el motor de AKS se ejecute en la máquina virtual y el clúster implementado. En este artículo se muestra cómo obtener acceso al motor de AKS y a los archivos correspondientes para que se pueda usar para realizar operaciones de actualización y escalado en el clúster de Kubernetes.

## <a name="access-aks-engine-in-the-dvm"></a>Acceso al motor de AKS en DVM

Cuando la implementación iniciada por el elemento Kubernetes de Marketplace de Azure Stack Hub finaliza correctamente, puede encontrar el motor de AKS que se usa para implementar el clúster instalado en la máquina virtual de implementación creada en el grupo de recursos que especificó para el clúster. Esta máquina virtual no forma parte del clúster de Kubernetes, se crea en su propia red virtual. Estos son los pasos para encontrar la máquina virtual y ubicar el motor de AKS dentro de ella:

1.  Abra el portal de usuarios de Azure Stack Hub y busque el grupo de recursos que ha especificado para el clúster de Kubernetes.
2.  En el grupo de recursos, busque la máquina virtual de implementación. El nombre empieza por el prefijo **vmd-** .
3.  Seleccione la máquina virtual de implementación. En Overview (Información general),** busque la dirección IP pública. Use esta dirección y la aplicación de consola, como Putty, para establecer una sesión de SSH en la máquina virtual.
4.  En la sesión de la máquina virtual de implementación, encontrará el motor de AKS en la siguiente ruta de acceso: `./var/lib/waagent/custom-script/download/0/bin/aks-engine`.
5.  Busque el archivo `.json` que describe los clústeres que se usan como entrada en aks-engine. El archivo se encuentra en `/var/lib/waagent/custom-script/download/0/bin/azurestack.json`. Tenga en cuenta que el archivo tiene las credenciales de la entidad de servicio que se usan para implementar el clúster. Si decide conservar el archivo, tenga cuidado de transferir el archivo a un almacén protegido.
6.  Busque el directorio de salida generado por el motor de AKS en `/var/lib/waagent/custom-script/download/0/_output/<resource group name>`. En este directorio, busque el archivo `apimodel.json` de salida en la ruta de acceso `/var/lib/waagent/custom-script/download/0/bin/apimodel.json`. El directorio y el archivo `apimodel.json` contienen todos los certificados, las claves y las credenciales generados que necesita para implementar el clúster de Kubernetes. Almacene estos recursos en una ubicación segura.
7.  Busque el archivo de configuración de Kubernetes, que se suele conocer como el archivo **kubeconfig**, en la ruta de acceso `$HOME/<output dir>/kubeconfig/kubeconfing.<location>.json`, donde **\<location>** es su identificador de ubicación de Azure Stack Hub. Este archivo es útil si piensa configurar **kubectl** para tener acceso al clúster de Kubernetes.

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Uso del motor de AKS con el clúster recién creado

Cuando haya localizado aks-engine, el archivo apimodel.json de entrada, el directorio de salida y el archivo apimodel.json de salida, y los haya guardado en una ubicación segura, puede usar el archivo `apimodel.json` de salida y binario del motor de AKS en cualquier máquina virtual Linux.

1.  Para seguir usando el motor de AKS para realizar operaciones como **Upgrade** y **Escale**, copie el archivo binario **aks-engine** en la máquina de destino. Si usa la misma máquina **vmd-** en un directorio.

2.  Cree un directorio con el nombre del clúster u otro nombre mnemotécnico que haga referencia al nuevo clúster y guarde el archivo apimodel.json de salida en él. Asegúrese de que se trata de un lugar protegido, ya que este archivo contiene credenciales. Después, puede ejecutar el archivo aks-engine para ejecutar operaciones como [Scale](azure-stack-kubernetes-aks-engine-scale.md) o [Upgrade](azure-stack-kubernetes-aks-engine-upgrade.md).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).  
- [Solución de problemas del motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

