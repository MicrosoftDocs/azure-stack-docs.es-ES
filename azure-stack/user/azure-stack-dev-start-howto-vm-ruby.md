---
title: Implementación de una aplicación de Ruby en una máquina virtual en Azure Stack | Microsoft Docs
description: Implemente una aplicación de Ruby en una máquina virtual en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: e54fc1cd1e8fe8588a9498b5af8476437880e988
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824182"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Implementación de una aplicación web de Ruby en una máquina virtual en Azure Stack

Puede crear una máquina virtual para hospedar la aplicación web de Ruby en Azure Stack. En este artículo, va a configurar un servidor, va a configurar el servidor para hospedar la aplicación web de Ruby y, después, implementar la aplicación en Azure Stack.

En este artículo se usa Ruby y la plataforma web Ruby on Rails.

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack. Para obtener instrucciones, consulte [Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. En el panel de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | DESCRIPCIÓN |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es el protocolo que se utiliza para entregar páginas web desde los servidores. Los clientes se conectan mediante HTTP con un nombre DNS o dirección IP. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTTPS) es una versión segura de HTTP que requiere un certificado de seguridad y permite la transmisión cifrada de información. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red cifrado para proteger las comunicaciones. Esta conexión se usa con un cliente SSH para configurar la VM e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto (RDP) permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario con su máquina.   |
    | 3000 | Personalizado | El puerto que utiliza la plataforma web Ruby on Rails en desarrollo. Para un servidor de producción, enruta el tráfico a través de los puertos 80 y 443. |

## <a name="install-ruby"></a>Instalación de Ruby

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Connect via SSH with PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty) (Conexión mediante SSH con PuTTy).

1. Instale el repositorio PPA. En el símbolo del sistema de bash en la máquina virtual, introduzca los siguientes comandos:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Instale Ruby y Ruby on Rails en la máquina virtual. Mientras sigue conectado a la máquina virtual en la sesión SSH, introduzca los siguientes comandos:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Instale las dependencias de Ruby on Rails. Mientras sigue conectado a la máquina virtual en la sesión SSH, introduzca los siguientes comandos:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > Mientras instala las dependencias de Ruby on Rails, es posible que deba ejecutar repetidamente `sudo gem install bundler`. Si se produce un error en la instalación, revise los registros de errores y resuelva los problemas.

4. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
        ruby -v
    ```

3. [Install Git](https://git-scm.com), un sistema de control de versiones y administración de código fuente (SCM) ampliamente distribuido. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Creación y ejecución de una aplicación

1. Mientras sigue conectado a la máquina virtual en la sesión SSH, introduzca los siguientes comandos:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. Vaya al nuevo servidor. Debería ver la aplicación web en ejecución.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [desarrollar para Azure Stack](azure-stack-dev-start.md).
- Obtenga información sobre las [implementaciones comunes para Azure Stack como IaaS](azure-stack-dev-start-deploy-app.md).
- Para conocer el lenguaje de programación de Ruby y consultar recursos adicionales para Ruby, consulte [Ruby-lang.org](https://www.ruby-lang.org).
