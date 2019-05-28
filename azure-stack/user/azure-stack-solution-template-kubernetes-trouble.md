---
title: Solución de problemas de implementación de Kubernetes en Azure Stack | Microsoft Docs
description: Aprenda a solucionar problemas de la implementación de Kubernetes en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabrigg
ms.date: 04/02/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 0e02489bc9750183754b27887fa701d1dd1a8567
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712418"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack"></a>Solución de problemas de implementación de Kubernetes en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!Note]  
> Kubernetes en Azure Stack está en versión preliminar. El escenario sin conexión de Azure Stack no es compatible actualmente con la versión preliminar.

En este artículo se indica cómo solucionar los problemas de cualquier clúster de Kubernetes. Para empezar a solucionar problemas, examine los elementos necesarios para la implementación. Puede que deba recopilar los registros de implementación de Azure Stack o las máquinas virtuales Linux que hospedan Kubernetes. Para recuperar los registros de un punto de conexión administrativo, póngase en contacto con el administrador de Azure Stack.

## <a name="overview-of-kubernetes-deployment"></a>Información general de la implementación de Kubernetes

Antes de solucionar los problemas del clúster, repase el proceso de implementación del clúster de Kubernetes en Azure Stack. La implementación usa una plantilla de soluciones de Azure Resource Manager para crear las máquinas virtuales e instalar el motor de AKS para el clúster.

### <a name="kubernetes-deployment-workflow"></a>Flujo de trabajo de implementación de Kubernetes

En el siguiente diagrama se muestra el proceso general para implementar el clúster.

![Implementación del proceso Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Pasos de implementación

1. Recopile los parámetros de entrada del elemento de Marketplace.

    Escriba los valores necesarios para configurar el clúster de Kubernetes, por ejemplo:
    -  **Nombre de usuario**: nombre de usuario de las máquinas virtuales de Linux que forman parte del clúster de Kubernetes y DVM.
    -  **Clave pública SSH**: clave que se usa para la autorización de todas las máquinas Linux creadas como parte del clúster de Kubernetes y DVM.
    -  **Entidad de servicio**: identificador que usa el proveedor de nube de Azure Kubernetes. El identificador de cliente identificado como identificador de la aplicación cuando se creó la entidad de servicio. 
    -  **Secreto del cliente**: la clave que creó al generar la entidad de servicio.

2. Cree la máquina virtual de implementación y la extensión de script personalizada.
    -  Crea la máquina virtual Linux de implementación mediante la imagen de Linux de Marketplace, **Ubuntu Server 16.04-LTS**.
    -  Descargue y ejecute la extensión de script de cliente de Marketplace. El script es **Script personalizado para Linux 2.0**.
    -  Ejecute el script personalizado de DVM. El script realiza las siguientes tareas:
        1. Obtiene el punto de conexión de la galería del punto de conexión de los metadatos de Azure Resource Manager.
        2. Obtiene el identificador de recurso del directorio activo del punto de conexión de los metadatos de Azure Resource Manager.
        3. Carga el modelo de API para el motor de AKS.
        4. Implementa el motor de AKS en el clúster de Kubernetes y guarda el perfil de la nube de Azure Stack en `/etc/kubernetes/azurestackcloud.json`.
3. Cree las máquinas virtuales maestras.

4. Descargue y ejecute las extensiones de script de cliente.

5. Ejecute el script maestro.

    El script realiza las siguientes tareas:
    - Instala los recursos de etcd, Docker y Kubernetes como kubelet. etcd es un almacén de pares valor-clave distribuido que proporciona una manera de almacenar datos en un clúster de máquinas. Docker es compatible con virtualizaciones básicas de nivel del sistema operativo, conocidas como contenedores. Kubelet es el agente de nodo que se ejecuta en cada nodo de Kubernetes.
    - Configura el servicio **etcd**.
    - Configura el servicio **kubelet**.
    - Inicia kubelet. Esta tarea implica los pasos siguientes:
        1. Inicia el servicio de API.
        2. Inicia el servicio de controlador.
        3. Inicia el servicio de programador.
6. Cree las máquinas virtuales de agente.

7. Descargue y ejecute las extensiones de script de cliente.

7. Ejecute el script de agente. El script personalizado de agente realiza las siguientes tareas:
    - Instala **etcd**.
    - Configura el servicio **kubelet**.
    - Una el clúster de Kubernetes.

## <a name="steps-to-troubleshoot-kubernetes"></a>Pasos para la solución de problemas de Kubernetes

Puede recopilar y examinar los registros de implementación de las máquinas virtuales que admiten el clúster de Kubernetes. Hable con el administrador de Azure Stack para comprobar la versión de Azure Stack que debe usar y obtener los registros de Azure Stack relacionados con su implementación.

1. Revise el [estado de implementación](#review-deployment-status) y recupere los registros del nodo maestro en el clúster de Kubernetes.
2. Asegúrese de que usa la versión más reciente de Azure Stack. Si no está seguro de qué versión usa, póngase en contacto con el administrador de Azure Stack.
3.  Revise los archivos de creación de la máquina virtual. Puede que haya tenido los siguientes problemas:  
    - Es posible que la clave pública no sea válida. Revise la clave que ha creado.  
    - Puede que la creación de máquinas virtuales haya desencadenado un error interno o un error de creación. Los errores pueden deberse a diversos factores, como las limitaciones de capacidad de la suscripción de Azure Stack.
    - Asegúrese de que el nombre de dominio completo (FQDN) de la máquina virtual comienza con un prefijo duplicado.
4.  Si la máquina virtual es **correcta**, evalúe la instancia de DVM. Si la instancia de DVM tiene un mensaje de error:

    - Es posible que la clave pública no sea válida. Revise la clave que ha creado.  
    - Debe ponerse en contacto con el administrador de Azure Stack para recuperar los registros de Azure Stack mediante los puntos de conexión con privilegios. Para más información, consulte [Registros de diagnóstico de Azure Stack](../operator/azure-stack-diagnostics.md).
5. Si tiene preguntas acerca de la implementación, puede publicarlas o ver si alguien ya ha respondido a la pregunta en el [foro de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Revisar el estado de implementación

Al implementar el clúster de Kubernetes, puede revisar el estado de implementación para comprobar si hay problemas.

1. Abra el [portal de Azure Stack](https://portal.local.azurestack.external).
2. Seleccione **Grupos de recursos** y, después, seleccione el nombre del grupo de recursos que usó al implementar el clúster de Kubernetes.
3. Seleccione **Implementaciones** y, después, el **nombre de la implementación**.

    ![Solución de problemas de Kubernetes: selección de implementación](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Consulte la ventana de solución de problemas. Cada uno de los recursos implementados ofrece la siguiente información:
    
    | Propiedad | DESCRIPCIÓN |
    | ----     | ----        |
    | Recurso | Nombre del recurso. |
    | Type | Proveedor de recursos y tipo de recurso. |
    | Status | Estado del elemento. |
    | TimeStamp | Marca de tiempo UTC de la hora. |
    | Detalles de la operación | Detalles de la operación, como el proveedor de recursos implicado en la operación, el punto de conexión del recurso y el nombre del recurso. |

    Cada elemento tiene un icono de estado de color verde o rojo.

## <a name="review-deployment-logs"></a>Revisión de los registros de implementación

Si el portal de Azure Stack no proporciona suficiente información para solucionar un error de implementación, el siguiente paso es profundizar en los registros del clúster. Para recuperar manualmente los registros de implementación, normalmente deberá conectarse a una de las máquinas virtuales principales del clúster. Una alternativa más sencilla sería descargar y ejecutar el siguiente [script de Bash](https://aka.ms/AzsK8sLogCollectorScript) proporcionado por el equipo de Azure Stack. Este script se conecta a la instancia de DVM y a las máquinas del clúster, recopila los registros pertinentes del sistema y del clúster y los descarga en la estación de trabajo.

### <a name="prerequisites"></a>Requisitos previos

Necesita un símbolo del sistema de Bash en la máquina que usa para administrar Azure Stack. En un equipo Windows, puede obtener un símbolo del sistema de Bash mediante la instalación de [GIT para Windows](https://git-scm.com/downloads). Una vez instalado, busque _Git Bash_ en el menú Inicio.

### <a name="retrieving-the-logs"></a>Recuperación de los registros

Siga estos pasos para recopilar y descargar los registros del clúster:

1. Abra un símbolo del sistema de Bash. Desde un equipo Windows, abra _Git Bash_ o ejecute: `C:\Program Files\Git\git-bash.exe`.

2. Descargue el script del recopilador de registros mediante la ejecución de los siguientes comandos en el símbolo del sistema de Bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Busque la información requerida por el script y ejecútelo:

    | Parámetro           | DESCRIPCIÓN                                                                                                      | Ejemplo                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | La dirección IP pública o el nombre de dominio completo (FQDN) de la instancia de DVM. El nombre de la máquina virtual empieza por `vmd-`. | IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Uso de comandos de impresión. | |
    | -i, --identity-file | El archivo de clave privada RSA pasado al elemento de marketplace al crear el clúster de Kubernetes. Se necesita para iniciar sesión de forma remota en los nodos de Kubernetes. | C:\data\id_rsa.pem (Putty)<br>~/.ssh/id_rsa (SSH)
    | -m, --master-host   | La dirección IP pública o el nombre de dominio completo (FQDN) de un nodo principal de Kubernetes. El nombre de la máquina virtual empieza por `k8s-master-`. | IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | El nombre de usuario pasado al elemento de marketplace al crear el clúster de Kubernetes. Se necesita para iniciar sesión de forma remota en los nodos de Kubernetes. | azureuser (valor predeterminado) |


   Al agregar los valores de los parámetros, el comando podría presentar un aspecto similar a este:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Después de unos minutos, el script generará los registros recopilados en un directorio denominado `KubernetesLogs_{{time-stamp}}`. Allí encontrará un directorio para cada máquina virtual que pertenezca al clúster.

    El script del recopilador de registros también buscará errores en los archivos de registro e incluirá los pasos necesarios para solucionar cualquier problema, en caso de que sea conocido. Asegúrese de que ejecuta la versión más reciente del script para aumentar las probabilidades de encontrar problemas conocidos.

> [!Note]  
> Consulte este [repositorio](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) de GitHub para obtener más detalles sobre el script del recopilador de registros.

## <a name="next-steps"></a>Pasos siguientes

[Implementación de Kubernetes en Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

[Agregar un clúster de Kubernetes a Marketplace (para el operador de Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes en Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
