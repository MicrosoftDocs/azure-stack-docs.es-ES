---
title: Implementación de una aplicación de Node.js en una máquina virtual en Azure Stack Hub | Microsoft Docs
description: Implementación de una aplicación de Node.js en Azure Stack Hub.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: b848678eab2b913331208e13882c3b8b125c51a8
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820600"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack-hub"></a>Implementación de una aplicación web de Node.js en una máquina virtual en Azure Stack Hub

Puede crear una máquina virtual para hospedar una aplicación web de Node.js en Azure Stack Hub. En este artículo, va a configurar un servidor, va a configurar el servidor para hospedar la aplicación web de Node.js y, después, implementar la aplicación en Azure Stack Hub.

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack Hub mediante las instrucciones de [Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. En el panel de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | Descripción |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es el protocolo que se utiliza para entregar páginas web desde los servidores. Los clientes se conectan mediante HTTP con un nombre DNS o dirección IP. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTTPS) es una versión segura de HTTP que requiere un certificado de seguridad y permite la transmisión cifrada de información. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red cifrado para proteger las comunicaciones. Esta conexión se usa con un cliente SSH para configurar la VM e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto (RDP) permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario con su máquina.   |
    | 1337 | Personalizado | El puerto que usa Node.js. Para un servidor de producción, enruta el tráfico a través de los puertos 80 y 443. |

## <a name="install-node"></a>Instalación de Node.js

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Conexión con SSH mediante PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. En el símbolo del sistema de bash en la máquina virtual, introduzca el siguiente comando:

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [Install NPM](https://www.npmjs.com/), un administrador de paquetes para los paquetes de Node.js, o módulos. Aún conectado a la máquina virtual en la sesión SSH, escriba el siguiente comando:

    ```bash  
       go version
    ```

3. [Install Git](https://git-scm.com), un sistema de control de revisión y administración de código fuente (SCM) ampliamente distribuido. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
       sudo apt-get -y install git
    ```

3. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación

1. Configure el repositorio de Git en la máquina virtual. Mientras sigue conectado a la máquina virtual en la sesión SSH, introduzca los siguientes comandos:

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. Inicie la aplicación. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
       sudo node app.js
    ```

3. Vaya al nuevo servidor. Debería ver la aplicación web en ejecución.

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [desarrollar para Azure Stack Hub](azure-stack-dev-start.md).
- Obtenga información sobre las [implementaciones comunes para Azure Stack Hub como IaaS](azure-stack-dev-start-deploy-app.md).
- Para conocer el lenguaje de programación de Node.js y consultar recursos adicionales para Node.js, consulte [Nodejs.org](https://nodejs.org).
