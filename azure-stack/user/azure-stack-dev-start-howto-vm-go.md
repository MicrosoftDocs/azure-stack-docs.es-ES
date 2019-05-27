---
title: Implementación de una aplicación web de Go en una máquina virtual en Azure Stack | Microsoft Docs
description: Implementación de una aplicación web de Go en una máquina virtual en Azure Stack
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 8b1f207929e018b27bb3f20db8d2b7d5abe46088
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838359"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>Implementación de una aplicación web de Go en una máquina virtual en Azure Stack

Puede crear una máquina virtual para hospedar una aplicación web de Go en Azure Stack. En este artículo, va a configurar un servidor, va a configurar el servidor para hospedar la aplicación web de Go y, después, implementar la aplicación en Azure Stack.

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack mediante las instrucciones de [Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. En el panel de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | DESCRIPCIÓN |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es el protocolo que se utiliza para entregar páginas web desde los servidores. Los clientes se conectan mediante HTTP con un nombre DNS o dirección IP. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTTPS) es una versión segura de HTTP que requiere un certificado de seguridad y permite la transmisión cifrada de información. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red cifrado para proteger las comunicaciones. Esta conexión se usa con un cliente SSH para configurar la VM e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto (RDP) permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario con su máquina.   |
    | 3000 | Personalizado | El puerto 3000 se usa por el marco web Go en desarrollo. Para un servidor de producción, enruta el tráfico a través de los puertos 80 y 443. |

## <a name="install-go"></a>Instalación de Go

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Conexión con SSH mediante PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. En el símbolo del sistema de bash en la máquina virtual, introduzca los siguientes comandos:

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Configure el entorno de G0 en la máquina virtual. Mientras sigue conectado a la máquina virtual en la sesión SSH, introduzca los siguientes comandos:

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
        go version
    ```

3. [Install Git](https://git-scm.com), un sistema de control de versiones y administración de código fuente (SCM) ampliamente distribuido. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación

1. Configure el repositorio de Git en la máquina virtual. Mientras sigue conectado a la máquina virtual en la sesión SSH, introduzca los siguientes comandos:

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Inicie la aplicación. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
       go run hello-world.go
    ```

3. Vaya al nuevo servidor. Debería ver la aplicación web en ejecución.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [desarrollar para Azure Stack](azure-stack-dev-start.md).
- Obtenga información sobre las [implementaciones comunes para Azure Stack como IaaS](azure-stack-dev-start-deploy-app.md).
- Para conocer el lenguaje de programación de Go y consultar recursos adicionales para Go, consulte [Golang.org](https://golang.org).
