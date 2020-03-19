---
title: Solución de problemas del motor de AKS en Azure Stack Hub
description: Este artículo contiene los pasos necesarios para la solución de problemas del motor de AKS en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: aa41ddde0986716e49073d571e967a050ef660f6
ms.sourcegitcommit: 4301e8dee16b4db32b392f5979dfec01ab6566c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79313030"
---
# <a name="troubleshoot-the-aks-engine-on-azure-stack-hub"></a>Solución de problemas del motor de AKS en Azure Stack Hub

Cuando se implementa o se trabaja con el motor de AKS en Azure Stack Hub pueden surgir problemas. En este artículo se examinan los pasos necesarios para solucionar los problemas que puedan aparecer al implementar el motor de AKS, recopilar información acerca del motor de AKS, recopilar registros de Kubernetes, examinar códigos de error de extensiones de scripts personalizados e instrucciones para abrir un problema de GitHub para el motor de AKS.

## <a name="troubleshoot-the-aks-engine-install"></a>Solución de problemas de la instalación del motor de AKS

### <a name="try-gofish"></a>Prueba de GoFish

Si se produjeron errores en los pasos de instalación anteriores, puede instalar el motor de AKS mediante el administrador de paquetes de GoFish. [GoFish](https://gofi.sh) se describe a sí mismo como un Homebrew multiplataforma.

#### <a name="install-the-aks-engine-with-gofish-on-linux"></a>Instalación del motor de AKS con GoFish en Linux

Instale GoFish desde la página [Install](https://gofi.sh/#install) (Instalar).

1. En una secuencia de comandos de Bash, ejecute el siguiente comando:

    ```bash
    curl -fsSL https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.sh | bash
    ```

2.  Ejecute el siguiente comando para instalar el motor de AKS con GoFish:

    ```bash
    Run "gofish install aks-engine"
    ```

#### <a name="install-the-aks-engine-with-gofish-on-windows"></a>Instalación del motor de AKS con GoFish en Windows

Instale GoFish desde la página [Install](https://gofi.sh/#install) (Instalar).

1. En un símbolo de sistema de PowerShell con privilegios elevados y ejecute el siguiente comando:

    ```PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force
    iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.ps1'))
    ```

2.  Ejecute el siguiente comando en la misma sesión para instalar el motor de AKS con GoFish:

    ```PowerShell
    gofish install aks-engine
    ```

### <a name="checklist-for-common-deployment-issues"></a>Lista de comprobación de problemas comunes en la implementación

Si aparecen errores al implementar un clúster de Kubernetes mediante el motor de AKS, puede comprobar lo siguiente:

1.  ¿Usa las credenciales de entidad de servicio (SPN) correctas?
2.  ¿Tiene el SPN tiene un rol "Colaboradores" en la suscripción a Azure Stack Hub?
3. ¿Tiene una cuota suficientemente grande en el plan de Azure Stack Hub?
4.  ¿Se aplica la instancia de Azure Stack Hub que tiene una revisión o una actualización?

Para más información, consulte el artículo [Solución de problemas ](https://github.com/Azure/aks-engine/blob/master/docs/howto/troubleshooting.md)en el **repositorio de GitHub** Azure/aks-engine.

## <a name="collect-aks-engine-logs"></a>Recopilación de registros del motor de AKS

Puede acceder a la información de revisión que crea AKS-Engine. El motor de AKS informa acerca del estado y los errores mientras se ejecuta la aplicación. La salida se puede canalizar a un archivo de texto, o bien se puede copiar directamente desde la consola de la línea de comandos. Consulte una lista de códigos de error desencadenados por el motor de AKS en [Examen de los códigos de error de extensión de script personalizado](#review-custom-script-extension-error-codes).

1.  Recopile la salida estándar y los errores de la información que se muestra en la herramienta de línea de comandos del motor de AKS.

2. Obtenga los registros de un archivo local. Puede establecer el directorio de salida con el parámetro **--output-directory**.

    Para establecer la ruta de acceso local para los registros:

    ```bash  
    aks-engine --output-directory <path to the directory>
    ```

## <a name="collect-kubernetes-logs"></a>Recopilación de registros de Kubernetes

Además de los registros del motor de AKS, los componentes de Kubernetes generan mensajes de estado y de error. Estos registros se pueden recopilar mediante el script de Bash, [getkuberneteslogs.sh](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/diagnosis-v0.1.3).

Este script automatiza el proceso de recopilación de los siguientes registros: 

 - Registros del agente Linux de Microsoft Azure (waagent)
 - Registros de la extensión de script personalizado
 - Ejecución de metadatos de contenedor de kube-system
 - Ejecución de registros de contenedor de kube-system
 - Diario y estado del servicio Kubelet
 - Diario y estado del servicio Etcd
 - Registros de DVM del elemento de la galería
 - Instantánea de kube-system

Sin este script, necesitaría conectarse a todos los nodos del clúster para buscar y descargar los registros manualmente. Además, el script también puede cargar los registros recopilados en una cuenta de almacenamiento que se puede usar para compartir los registros con otros usuarios.

Requisitos:

 - Una máquina virtual Linux, Git Bash o Bash en Windows.
 - La [CLI de Azure](azure-stack-version-profiles-azurecli2.md) instalada en la máquina desde donde se ejecutará el script.
 - Identidad de la entidad de servicio que ha iniciado una sesión de la CLI de Azure en Azure Stack Hub. Como el script tiene la funcionalidad de detectar y crear recursos ARM para realizar su trabajo, requiere la CLI de Azure y una identidad de la entidad de servicio.
 - Una cuenta de usuario (suscripción) en la que el clúster de Kubernetes ya está seleccionado en el entorno. 
1. Descargue la versión más reciente del archivo tar del script en la máquina virtual del cliente, una máquina que tenga acceso al clúster de Kubernetes o a la misma máquina que usó para implementar el clúster con AKS-Engine.

    Ejecute los comandos siguientes:

    ```bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    wget https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.1/diagnosis-v0.1.1.tar.gz
    tar xvf diagnosis-v0.1.1.tar.gz -C ./
    ```

2. Busque los parámetros que requiere el script `getkuberneteslogs.sh`. El script usará los siguientes parámetros:

    | Parámetro | Descripción | Obligatorio | Ejemplo |
    | --- | --- | --- | --- |
    | -h, --help | Uso de comandos de impresión. | no | 
    -u,--user | El nombre de usuario del administrador de las máquinas virtuales del clúster | sí | azureuser<br>(valor predeterminado) |
    | -i, --identity-file | Clave privada RSA vinculada a la clave pública usada para crear el clúster de Kubernetes (a veces se denomina "id_rsa")  | sí | `./rsa.pem` (Putty)<br>`~/.ssh/id_rsa` (SSH) |
    |   -g, --resource-group    | Grupo de recursos de clúster de Kubernetes | sí | k8sresourcegroup |
    |   -n, --user-namespace               | Recopilar registros de contenedores en los espacios de nombres especificados (los registros de kube-system siempre se recopilan) | no |   monitoring |
    |       --api-model                    | Conserva el archivo apimodel.json en una cuenta de Azure Stack Hub Storage. La carga del archivo apimodel.json en la cuenta de almacenamiento se produce cuando también se proporciona el parámetro--upload-logs. | no | `./apimodel.json` |
    | --all-namespaces               | Recopile registros de los contenedores de todos los espacios de nombres. Invalida --user-namespace | no | |
    | --upload-logs                  | Conserva los registros recuperados en una cuenta de Azure Stack Hub Storage. Los registros se pueden encontrar en el grupo de recursos KubernetesLogs | no | |
    --disable-host-key-checking    | Establece la opción StrictHostKeyChecking de SSH en "no" mientras se ejecuta el script. Utilícela solo en entornos seguros. | no | |

3. Ejecute cualquiera de los siguientes comandos de ejemplo con su información:

    ```bash
    ./getkuberneteslogs.sh -u azureuser -i private.key.1.pem -g k8s-rg
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --disable-host-key-checking
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg -n default -n monitoring
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs --api-model clusterDefinition.json
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs
    ```

## <a name="review-custom-script-extension-error-codes"></a>Examen de los códigos de error de extensión de script personalizado

Puede consultar una lista de los códigos de error que crea la extensión de script personalizado (CSE) al ejecutar su clúster. El error de CSE puede ser útil para diagnosticar la causa principal del problema. El CSE del servidor Ubuntu que se usa en el clúster de Kubernetes admite muchas de las operaciones del motor de AKS. Para más información sobre los códigos de salida de CSE, consulte [cse_helpers.sh](https://github.com/Azure/aks-engine/blob/master/parts/k8s/cloud-init/artifacts/cse_helpers.sh).

### <a name="providing-kubernetes-logs-to-a-microsoft-support-engineer"></a>Entrega de registros de Kubernetes a un ingeniero del servicio de soporte técnico de Microsoft

Si después de recopilar y examinar los registros sigue sin poder resolver el problema, puede que desee iniciar el proceso de creación de una incidencia de soporte técnico y proporcionar los registros recopilados mediante la ejecución de `getkuberneteslogs.sh` con el conjunto de parámetros `--upload-logs`. 

Póngase en contacto con un operador de Azure Stack Hub. El operador usa la información de los registros para crear el caso de soporte técnico.

Durante el proceso de solución de problemas de soporte técnico, un ingeniero de soporte técnico de Microsoft puede solicitar que el operador recopile los registros del sistema de Azure Stack Hub. Es posible que tenga que proporcionar a su operador la información de la cuenta de almacenamiento donde cargó los registros de Kubernetes mediante la ejecución de `getkuberneteslogs.sh`.

El operador puede ejecutar el cmdlet de PowerShell en **Get-AzureStackLog**. Este comando usa un parámetro (`-InputSaSUri`) que especifica la cuenta de almacenamiento donde almacenó los registros de Kubernetes.

El operador puede combinar los registros que ha creado junto con cualquier otro registro del sistema que el servicio de soporte técnico de Microsoft pueda necesitar y ponerlos a disposición de Microsoft.

## <a name="open-github-issues"></a>Apertura de incidencias de GitHub

Si no puede resolver un error de implementación, puede abrir una incidencia de GitHub. 

1. Abra una [incidencia de GitHub](https://github.com/Azure/aks-engine/issues/new) en el repositorio del motor de AKS.
2. Agregue un título con el siguiente formato: C`SE error: exit code <INSERT_YOUR_EXIT_CODE>`.
3. Incluya la siguiente información en la incidencia:

    - El archivo de configuración del clúster, `apimodel json`, que se usa para implementar el clúster. Quite todos los secretos y claves antes de publicarlo en GitHub.  
     - La salida del siguiente comando **kubectl** `get nodes`.  
     - El contenido de `/var/log/azure/cluster-provision.log` y `/var/log/cloud-init-output.log`

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
