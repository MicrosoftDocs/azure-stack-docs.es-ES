---
title: Implementación de una aplicación de Node.js en una máquina virtual en Azure Stack | Microsoft Docs
description: Implementación de una aplicación de Node.js en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: bebf3b349a994379d5f54bd387533b8d4a63ccdd
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482293"
---
# <a name="how-to-deploy-a-nodejs-web-app-to-a-vm-in-azure-stack"></a>Implementación de una aplicación web de Node.js en una máquina virtual en Azure Stack

Puede crear una máquina virtual para hospedar la aplicación web de Node.js en Azure Stack. En este artículo se examinan los pasos que debería seguir para ajustar los parámetros del servidor, configurarlo para hospedar su aplicación web de Node.js y, a continuación, implementar la aplicación.

Node.js es un entorno de tiempo de ejecución de JavaScript integrado en el motor de V8 JavaScript de Chrome. Como entorno de ejecución JavaScript controlado por eventos asincrónicos, Node.js está diseñado para compilar aplicaciones de red escalables. Para conocer el lenguaje de programación de Node.js y consultar recursos adicionales para Node.js, consulte [Nodejs.org](https://nodejs.org).

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack. Siga los pasos de [Deploy a Linux VM to host a web app in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) (Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack).

2. En la hoja de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | DESCRIPCIÓN |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es un protocolo de aplicación para los sistemas de información de hipermedia distribuidos y colaborativos. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTPPS) es una extensión del Protocolo de transferencia de hipertexto (HTTP). Se usa para establecer una comunicación segura a través de la red de un equipo. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red criptográfico que permite usar servicios de red de forma segura a través de una red no segura. Usará esta conexión con un cliente SSH para configurar la máquina virtual e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario de su equipo.   |
    | 1337 | Personalizado | El puerto 1337 se usa por Node.js. Para un servidor de producción, querrá enrutar el tráfico a través de 80 y 443. |

## <a name="install-node"></a>Instalación de Node.js

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Connect via SSH with PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty) (Conexión mediante SSH con PuTTy).
1. En el símbolo de bash de la máquina virtual, escriba los siguientes comandos:

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. Instale NPM. [NPM](https://www.npmjs.com/) es un administrador de paquetes para los paquetes de Node.js, o módulos. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
       go version
    ```

3. Instale Git. [Git](https://git-scm.com) es un sistema de control de revisión y administración de código fuente (SCM) ampliamente distribuido. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
       sudo apt-get -y install git
    ```

3. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación

1. Configure el repositorio de Git en la máquina virtual. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. Inicie la aplicación. Aún conectado a la máquina virtual en la sesión SSH, escriba el siguiente comando:

    ```bash  
       sudo node app.js
    ```

3.  Ahora, vaya ahora al nuevo servidor y debería ver la aplicación web en ejecución.

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de cómo [desarrollar para Azure Stack](azure-stack-dev-start.md)
- Obtenga información sobre las [implementaciones comunes para Azure Stack como IaaS](azure-stack-dev-start-deploy-app.md).