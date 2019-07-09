---
title: Acceso al panel de Kubernetes en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo acceder al panel de Kubernetes en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: ecd0d3c79edc2359cf82aa9c52fb9021d7fc7a6f
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308685"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Acceso al panel de Kubernetes en Azure Stack 

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack* 
> [!Note]   
> Kubernetes en Azure Stack está en versión preliminar. Un escenario sin conexión de Azure Stack no es compatible actualmente con la versión preliminar. Solo use el elemento de Marketplace para escenarios de desarrollo y pruebas.

Kubernetes incluye un panel web que se puede usar para operaciones básicas de administración. Este panel le permite ver el estado de mantenimiento básico y las métricas para sus aplicaciones, crear e implementar servicios, y modificar las aplicaciones existentes. En este artículo se explica cómo configurar el panel de Kubernetes en Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Requisitos previos para el panel de Kubernetes

* Clúster de Kubernetes en Azure Stack

    Debe haber implementado un clúster de Kubernetes en Azure Stack. Para más información, vea [Implementación de Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Cliente SSH

    Necesitará un cliente SSH para conectarse con seguridad al nodo principal del clúster. Si usa Windows, puede usar [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Necesitará la clave privada utilizada cuando implementó el clúster de Kubernetes.

* FTP (PSCP)

    Puede que también necesite un cliente FTP que sea compatible con el protocolo de transferencia de archivos SSH y SSH para transferir los certificados desde el nodo principal a su equipo de administración de Azure Stack. Puede usar [FileZilla](https://filezilla-project.org/download.php?type=client). Necesitará la clave privada utilizada cuando implementó el clúster de Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Información general de los pasos para habilitar el panel

1.  Exporte los certificados de Kubernetes del nodo principal del clúster. 
2.  Importe los certificados en la máquina de administración de Azure Stack.
2.  Abra el panel web de Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Exportación del certificado desde el nodo principal 

Puede recuperar la dirección URL para el panel desde el nodo principal del clúster.

1. Obtenga la dirección IP pública y el nombre de usuario para el nodo principal del clúster desde el panel de Azure Stack. Para obtener esta información:

    - Inicie sesión en el [portal de Azure Stack](https://portal.local.azurestack.external/).
    - Seleccione **Todos los servicios** > **Todos los recursos**. Busque el nodo principal en el grupo de recursos de clúster. El nodo principal se denomina `k8s-master-<sequence-of-numbers>`. 

2. Abra el nodo principal en el portal. Copie la dirección **IP pública**. Haga clic en **Conectar** para obtener el nombre de usuario en el cuadro **Iniciar sesión con la cuenta local de VM**. Este es el mismo nombre de usuario que se establece al crear el clúster. Utilice la dirección IP pública en lugar de la dirección IP privada que se muestra en la hoja de conexión.

3.  Abra un cliente de SSH para conectarse al nodo principal. Si está trabajando en Windows, puede usar [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) para crear la conexión. Utilizará la dirección IP pública para el nodo principal y el nombre de usuario y agregará la clave privada que utilizó al crear el clúster.

4.  Cuando se conecte el terminal, escriba `kubectl` para abrir el cliente de la línea de comandos de Kubernetes.

5. Ejecute el siguiente comando:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Busque la dirección URL para el panel. Por ejemplo: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extraiga el certificado autofirmado y conviértalo al formato PFX. Ejecute el siguiente comando:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Obtenga la lista de secretos del espacio de nombres **kube-system**. Ejecute el siguiente comando:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Tome nota del valor kubernetes-dashboard-token-\<XXXXX>. 

8.  Obtenga el token y guárdelo. Actualice `kubernetes-dashboard-token-<####>` con el valor del secreto del paso anterior.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importación del certificado

1. Abra Filezilla y conéctese al nodo principal. Necesitará:

    - la dirección IP pública del nodo principal
    - el nombre de usuario
    - el secreto privado
    - Usar el **protocolo de transferencia de archivos SFTP - SSH**

2. Copie `/etc/kubernetes/certs/client.pfx` y `/etc/kubernetes/certs/ca.crt` en el equipo de administración de Azure Stack.

3. Tome nota de las ubicaciones del archivo. Actualice el script con las ubicaciones y, a continuación, abra PowerShell mediante un símbolo del sistema con privilegios elevados. Ejecute el script actualizado:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Apertura del panel de Kubernetes 

1. Deshabilite el bloqueador de elementos emergentes en el explorador web.

2. Apunte el explorador a la dirección URL anotada cuando ejecutó el comando `kubectl cluster-info`. Por ejemplo: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy 
3. Seleccione el certificado de cliente.
4. Escriba el token. 
5. Vuelva a conectarse a la línea de comandos Bash en el nodo principal y conceda permisos a `kubernetes-dashboard`. Ejecute el siguiente comando:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    El script otorga privilegios de administrador en la nube `kubernetes-dashboard`. Para más información, vea [Para clústeres con RBAC habilitado](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Puede usar el panel. Para más información sobre el panel de Kubernetes, vea la información sobre el [panel de la interfaz de usuario web de Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

![Panel de Kubernetes en Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Pasos siguientes 

[Implementación de Kubernetes en Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).  

[Agregar un clúster de Kubernetes a Marketplace (para el operador de Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes en Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
