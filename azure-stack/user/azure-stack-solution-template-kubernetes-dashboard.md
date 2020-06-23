---
title: Acceso al panel de Kubernetes en Azure Stack Hub
description: Obtenga información sobre cómo acceder al panel de Kubernetes en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 0efcac6635a94b3fdc5551f9aa61b5c774e93905
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631185"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack-hub"></a>Acceso al panel de Kubernetes en Azure Stack Hub 

> [!Note]   
> Use únicamente el elemento Kubernetes de Marketplace de Azure Stack para implementar clústeres como prueba de concepto. Para los clústeres de Kubernetes admitidos en Azure Stack, utilice el [motor de AKS](azure-stack-kubernetes-aks-engine-overview.md).

Kubernetes incluye un panel web que se puede usar para operaciones básicas de administración. Este panel le permite ver el estado de mantenimiento básico y las métricas para sus aplicaciones, crear e implementar servicios, y modificar las aplicaciones existentes. En este artículo se explica cómo configurar el panel de Kubernetes en Azure Stack Hub.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Requisitos previos para el panel de Kubernetes

* Clúster de Kubernetes en Azure Stack Hub

    Debe haber implementado un clúster de Kubernetes en Azure Stack Hub. Para más información, vea [Implementación de Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Cliente SSH

    Necesitará un cliente SSH para conectarse con seguridad al nodo principal del clúster. Si usa Windows, puede usar [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Necesitará la clave privada utilizada cuando implementó el clúster de Kubernetes.

* FTP (PSCP)

    Puede que también necesite un cliente FTP que sea compatible con el protocolo de transferencia de archivos SSH y SSH para transferir los certificados desde el nodo principal a su equipo de administración de Azure Stack Hub. Puede usar [FileZilla](https://filezilla-project.org/download.php?type=client). Necesitará la clave privada utilizada cuando implementó el clúster de Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Información general de los pasos para habilitar el panel

1.  Exporte los certificados de Kubernetes del nodo principal del clúster. 
2.  Importe los certificados en la máquina de administración de Azure Stack Hub.
2.  Abra el panel web de Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Exportación del certificado desde el nodo principal 

Puede recuperar la dirección URL para el panel desde el nodo principal del clúster.

1. Obtenga la dirección IP pública y el nombre de usuario para el nodo principal del clúster desde el panel de Azure Stack Hub. Para obtener esta información:

    - Inicie sesión en el portal de Azure Stack Hub `https://portal.local.azurestack.external/`.
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

2. Copie `/etc/kubernetes/certs/client.pfx` y `/etc/kubernetes/certs/ca.crt` en el equipo de administración de Azure Stack Hub.

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

![Panel de Kubernetes en Azure Stack Hub](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="troubleshooting"></a>Solución de problemas

### <a name="custom-virtual-networks"></a>Redes virtuales personalizadas

Si experimenta problemas de conectividad al acceder al panel de Kubernetes después de implementar Kubernetes en una [red virtual personalizada](https://docs.microsoft.com/azure-stack/user/kubernetes-aks-engine-custom-vnet), asegúrese de que las subredes de destino están vinculadas a los recursos de la tabla de enrutamiento y del grupo de seguridad de red creados por el motor de AKS.

Asegúrese de que las reglas del grupo de seguridad de red permitan la comunicación entre los nodos principales y la dirección IP del pod del panel de Kubernetes. Esto se puede confirmar mediante el comando ping desde un nodo maestro.

## <a name="next-steps"></a>Pasos siguientes 

[Implementación de Kubernetes en Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md)  

[Adición de un clúster de Kubernetes a Marketplace (para el operador de Azure Stack Hub)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes en Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
