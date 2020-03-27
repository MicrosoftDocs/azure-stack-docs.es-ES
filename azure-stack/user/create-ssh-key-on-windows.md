---
title: Implementación de un clúster de Kubernetes en una red virtual personalizada en Azure Stack Hub
description: Aprenda a implementar un clúster de Kubernetes en una red virtual personalizada en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: e82ddb48b3858acdf25163976854f538400da54b
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2020
ms.locfileid: "80069196"
---
# <a name="create-an-ssh-key-for-linux-on-azure-stack-hub"></a>Creación de una clave SSH para Linux en Azure Stack Hub

En una máquina Windows se puede crear una clave SSH (Secure Shell) para una máquina Linux. Use la clave pública que se generará si se siguen los pasos de este artículo para autenticar SSH con máquinas virtuales. Si usa una máquina Windows, instale Ubuntu en Windows para poder tener un terminal con utilidades como bash, ssh y git, y con Ubuntu en Windows. Ejecute **ssh-keygen** para crear la clave.

## <a name="open-bash-on-windows"></a>Apertura de bash en Windows

1. Si el subsistema de Windows para Linux no está instalado en la máquina, instale "[Ubuntu en Windows](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).  
    Para más información sobre el uso del Subsistema de Windows para Linux, consulte el artículo [Documentación del subsistema de Windows para Linux](https://docs.microsoft.com/windows/wsl/about).

2. Escriba **Ubuntu** en la barra de y seleccione **Open** (Abrir).

## <a name="create-a-key-with-ssh-keygen"></a>Creación de una clave con ssh-keygen

1. Escriba el siguiente comando en el indicador de bash:

    ```bash  
    ssh-keygen -t rsa
    ```

    Bash muestra el siguiente indicador:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

2. Escriba el nombre de archivo y la frase de contraseña. Vuelva a escribir la frase de contraseña.

    Bash muestra lo siguiente:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa): key.txt
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in key.txt.
    Your public key has been saved in key.txt.pub.
    The key fingerprint is:
    SHA256:xanotrealoN6z1/KChqeah0CYVeyhL50/0rq37qgy6Ik username@machine
    The key's randomart image is:
    +---[RSA 2048]----+
    |   o.     .      |
    |  . o.   +       |
    | + o .+ o o      |
    |o o .  O +       |
    | . o .o S .      |
    |  o +. .         |
    |.  o +..o. .     |
    |= . ooB +o+ .    |
    |E=..*X=*.. +.    |
    +----[SHA256]-----+
    ```

3. Para ver el indicador y la clave SSH pública:

    ```bash
    cat /home/<username>/<filename>
    ```

    Bash muestra algo parecido a esto:

    ```bash
    ssh-rsa AAAAB3NzaC1ycTHISISANEXAMPLEDITqEJRNrf6tXy9c0vKnMhiol1BFzHFV3
    +suXk6NDeFcA9uI58VdD/CuvG826R+3OPnXutDdl2MLyH3DGG1fJAHObUWQxmDWluhSGb
    JMHiw2L9Wnf9klG6+qWLuZgjB3TQdus8sZI8YdB4EOIuftpMQ1zkAJRAilY0p4QxHhKbU
    IkvWqBNR+rd5FcQx33apIrB4LMkjd+RpDKOTuSL2qIM2+szhdL5Vp5Y6Z1Ut1EpOrkbg1
    cVw7oW0eP3ROPdyNqnbi9m1UVzB99aoNXaepmYviwJGMzXsTkiMmi8Qq+F8/qy7i4Jxl0
    aignia880qOtQrvNEvyhgZOM5oDhgE3IJ username@machine
    ```

4. Copie el texto `ssh-rsa [...]` hasta `username@machinename`. Asegúrese de que el texto no incluye retornos de carro. Este texto se puede usar al crear una máquina virtual o un clúster de Kubernetes mediante el motor AKS.

5. En una máquina Windows, para acceder a los archivos Linux se debe usar **\\\\wsl$** .

    1. Escriba `\\wsl$` en la barra de herramientas. Se abre la ventana predeterminada de la distribución.

    2. Vaya a: `\\wsl$\Ubuntu\home\<username>` y busque las claves privada y pública, y guárdelas en una ubicación segura.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de un clúster de Kubernetes con el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
- [Inicio rápido: Creación de una máquina virtual de servidor Linux mediante el portal de Azure Stack Hub](azure-stack-quick-linux-portal.md)