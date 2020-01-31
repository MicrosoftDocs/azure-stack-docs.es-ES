---
title: Implemente una aplicación web de Python en una máquina virtual en Azure Stack Hub
description: Implemente una aplicación web de Python en una máquina virtual en Azure Stack Hub.
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: c0455b2e6ab971ae73df14e9986753339544051e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884851"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack-hub"></a>Implementación de una aplicación web de Python en una máquina virtual en Azure Stack Hub

Puede crear una máquina virtual para hospedar la aplicación web de Python en Azure Stack Hub. En este artículo, va a configurar un servidor, preparar el servidor para hospedar la aplicación web de Python y, después, implementar la aplicación en Azure Stack Hub.

Este artículo usa Python 3.x, que ejecuta Flask en un entorno virtual en un servidor Ngnix.

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack Hub mediante las instrucciones de [Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. En el panel de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | Descripción |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es el protocolo que se utiliza para entregar páginas web desde los servidores. Los clientes se conectan mediante HTTP con un nombre DNS o dirección IP. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTTPS) es una versión segura de HTTP que requiere un certificado de seguridad y permite la transmisión cifrada de información. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red cifrado para proteger las comunicaciones. Esta conexión se usa con un cliente SSH para configurar la VM e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto (RDP) permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario con su máquina.   |
    | 5000, 8000 | Personalizado | Los puertos que usan por el marco web de Flask en desarrollo. Para un servidor de producción, enruta el tráfico a través de los puertos 80 y 443. |

## <a name="install-python"></a>Instalación de Python

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Connect via SSH with PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty) (Conexión mediante SSH con PuTTy).
2. En el símbolo del sistema de bash de la máquina virtual, introduzca el siguiente comando:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
        python -version
    ```

3. [Install Nginx](https://www.nginx.com/resources/wiki/), un servidor web ligero. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [Install Git](https://git-scm.com), un sistema de control de versiones y administración de código fuente (SCM) ampliamente distribuido. Aún conectado a la máquina virtual en la sesión SSH, introduzca el siguiente comando:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación

1. Configure el repositorio de Git en la máquina virtual. Mientras sigue conectado a la máquina virtual en la sesión SSH, introduzca los siguientes comandos:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Cree un entorno virtual y rellénelo con todas las dependencias de paquete. Mientras sigue conectado a la máquina virtual en la sesión SSH, introduzca los siguientes comandos:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. Vaya al nuevo servidor. Debería ver la aplicación web en ejecución.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Actualización del servidor

1. Conéctese a la máquina virtual en la sesión SSH. Para detener el servidor, escriba Ctrl+C.

2. Escriba los siguientes comandos:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Activación del entorno virtual e inicio de la aplicación:

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [desarrollar para Azure Stack Hub](azure-stack-dev-start.md).
- Obtenga información sobre las [implementaciones comunes para Azure Stack Hub como IaaS](azure-stack-dev-start-deploy-app.md).
- Para conocer el lenguaje de programación de Python y consultar recursos adicionales para Python, consulte [Python.org](https://www.python.org).
