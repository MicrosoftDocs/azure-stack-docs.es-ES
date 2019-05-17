---
title: Implementación de una aplicación web de Python en una máquina virtual en Azure Stack | Microsoft Docs
description: Implemente una aplicación web de Python en una máquina virtual en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b1a588dd084962c095a534f6569333b34e694bc6
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482257"
---
# <a name="how-to-deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Implementación de una aplicación web de Python en una máquina virtual en Azure Stack

Puede crear una máquina virtual para hospedar la aplicación web de Python en Azure Stack. En este artículo se examinan los pasos que debería seguir para ajustar los parámetros del servidor, configurarlo para hospedar su aplicación web de Python y, a continuación, implementar la aplicación.

Python es un lenguaje de programación interpretado, de alto nivel y destinado a fines genéricos. Creado por Guido van Rossum y publicado por primera vez en 1991, Python tiene una filosofía de diseño que enfatiza la legibilidad del código, especialmente al usar espacios en blanco significativos. Proporciona construcciones que hacen posible una programación clara en escalas pequeñas y grandes. Para conocer el lenguaje de programación de Python y consultar recursos adicionales para Python, consulte [Python.org](https://www.python.org).

Este artículo usará Python 3.x, que ejecuta Flask en un entorno virtual en un servidor Ngnix.

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack. Siga los pasos de [Deploy a Linux VM to host a web app in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) (Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack).

2. En la hoja de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | DESCRIPCIÓN |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es un protocolo de aplicación para los sistemas de información de hipermedia distribuidos y colaborativos. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTPPS) es una extensión del Protocolo de transferencia de hipertexto (HTTP). Se usa para establecer una comunicación segura a través de la red de un equipo. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red criptográfico que permite usar servicios de red de forma segura a través de una red no segura. Usará esta conexión con un cliente SSH para configurar la máquina virtual e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario de su equipo.   |
    | 5000, 8000 | Personalizado | Los puertos 5000, 8000 se usan por el marco web de Flask en desarrollo. Para un servidor de producción, querrá enrutar el tráfico a través de 80 y 443. |

## <a name="install-python"></a>Instalación de Python

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Connect via SSH with PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty) (Conexión mediante SSH con PuTTy).
2. En el símbolo de bash de la máquina virtual, escriba los siguientes comandos:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
        python -version
    ```


3. Instale NGINX. [Nginx](https://www.nginx.com/resources/wiki/) es un servidor web, que también puede usarse como proxy inverso, equilibrador de carga, proxy de correo y caché HTTP. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. Instale Git. [Git](https://git-scm.com) es un sistema de control de revisión y administración de código fuente (SCM) ampliamente distribuido. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación

1. Configure el repositorio de Git en la máquina virtual. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Cree un entorno virtual y rellénelo con todas las dependencias de paquete.  Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3.  Ahora, vaya ahora al nuevo servidor y debería ver la aplicación web en ejecución.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Actualización del servidor

1. Conéctese a la máquina virtual en la sesión SSH. Detenga el servidor escribiendo `CTRL+C`.
2. Escriba los comandos siguientes:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Activación de el entorno virtual e inicio de la aplicación

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de cómo [desarrollar para Azure Stack](azure-stack-dev-start.md)
- Obtenga información sobre las [implementaciones comunes para Azure Stack como IaaS](azure-stack-dev-start-deploy-app.md).
