---
title: Implementación de una aplicación de Ruby en una máquina virtual en Azure Stack | Microsoft Docs
description: Implemente una aplicación de Ruby en una máquina virtual en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 7744d1adcdcb1dde53c6ef887498a9a3978f4513
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482299"
---
# <a name="how-to-deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Implementación de una aplicación web de Ruby en una máquina virtual en Azure Stack

Puede crear una máquina virtual para hospedar la aplicación web de Ruby en Azure Stack. En este artículo se examinan los pasos que debería seguir para ajustar los parámetros del servidor, configurarlo para hospedar su aplicación web de Ruby y, a continuación, implementar la aplicación.

Ruby es un lenguaje con un buen equilibrio. Su creador, Yukihiro "Matz" Matsumoto, combinó partes de sus lenguajes favoritos (Perl, Smalltalk, Eiffel, Ada y Lisp) para formar un nuevo lenguaje que equilibra la programación funcional con la programación imperativa. Para conocer el lenguaje de programación de Ruby y consultar recursos adicionales para Ruby, consulte [Ruby-lang.org](https://www.ruby-lang.org).

En este artículo se usará Ruby y el marco web Ruby on Rails.

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack. Siga los pasos de [Deploy a Linux VM to host a web app in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) (Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack).

2. En la hoja de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | DESCRIPCIÓN |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es un protocolo de aplicación para los sistemas de información de hipermedia distribuidos y colaborativos. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTPPS) es una extensión del Protocolo de transferencia de hipertexto (HTTP). Se usa para establecer una comunicación segura a través de la red de un equipo. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red criptográfico que permite usar servicios de red de forma segura a través de una red no segura. Usará esta conexión con un cliente SSH para configurar la máquina virtual e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario de su equipo.   |
    | 3000 | Personalizado | El puerto 3000 se usa por el marco web Ruby-on-rails en desarrollo. Para un servidor de producción, querrá enrutar el tráfico a través de 80 y 443. |

## <a name="install-ruby"></a>Instalación de Ruby

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Connect via SSH with PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty) (Conexión mediante SSH con PuTTy).
1. Instale el repositorio PPA. En el símbolo de bash de la máquina virtual, escriba los siguientes comandos:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Instale Ruby y Ruby-on-rails en la máquina virtual. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Instale las dependencias de Ruby-on-rails. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > En la instalación, es posible que deba ejecutar repetidamente `sudo gem install bundler`. Si las dependencias intentan volver a instalarse y se produce un error, revise los registros de errores y resuelva los problemas.

4. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
        ruby -v
    ```

3. Instale Git. [Git](https://git-scm.com) es un sistema de control de revisión y administración de código fuente (SCM) ampliamente distribuido. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Creación y ejecución de una aplicación

1. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2.  Ahora, vaya ahora al nuevo servidor y debería ver la aplicación web en ejecución.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de cómo [desarrollar para Azure Stack](azure-stack-dev-start.md)
- Obtenga información sobre las [implementaciones comunes para Azure Stack como IaaS](azure-stack-dev-start-deploy-app.md).