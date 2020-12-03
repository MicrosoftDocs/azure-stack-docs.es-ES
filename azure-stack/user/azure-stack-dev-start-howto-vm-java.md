---
title: Implementación de un Java WAR en una máquina virtual en Azure Stack Hub
description: Implemente un archivo WAR de Java en una máquina virtual en Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.custom: conteperfq4
ms.openlocfilehash: 12aa4c0e666085b7692a55ff795c07d9b05aa96c
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525425"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack-hub"></a>Implementación de una aplicación web de Java en una máquina virtual en Azure Stack Hub

Puede crear una máquina virtual para hospedar la aplicación web de Java en Azure Stack Hub. En este artículo, va a instalar, abrir los puertos correctos y configurar un servidor Apache Tomcat en una máquina virtual Linux en Azure Stack Hub. Después, va a cargar un archivo de recursos de aplicación web de Java (WAR) en el servidor. Un archivo WAR se utiliza para distribuir una colección de archivos Java (JAR), archivos comprimidos que contienen recursos de Java como clases, texto, imágenes, XML y HTML, y otros recursos que se utilizan para entregar una aplicación web.

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack Hub mediante las instrucciones de [Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. En el panel de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | Descripción |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es el protocolo que se utiliza para entregar páginas web desde los servidores. Los clientes se conectan mediante HTTP con un nombre DNS o dirección IP. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTTPS) es una versión segura de HTTP que requiere un certificado de seguridad y permite la transmisión cifrada de información. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red cifrado para proteger las comunicaciones. Esta conexión se usa con un cliente SSH para configurar la VM e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto (RDP) permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario con su máquina.   |
    | 8080 | Personalizado | El puerto predeterminado para el servicio de Apache Tomcat. Para un servidor de producción, enruta el tráfico a través de los puertos 80 y 443. |

## <a name="install-java"></a>Instalar Java

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Conexión con SSH mediante PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

2. En el símbolo del sistema de bash en la máquina virtual, ejecute el siguiente comando:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, ejecute el siguiente comando:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Instalación y configuración de Tomcat

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Conexión con SSH mediante PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Cree un usuario de Tomcat haciendo lo siguiente:

    a. Cree un nuevo grupo de Tomcat mediante el comando siguiente:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Cree un nuevo usuario de Tomcat. Agregue este usuario al grupo de Tomcat con un directorio de inicio de *tomcat/opt/* . Va a implementar Tomcat en este directorio:

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. Instale Tomcat haciendo lo siguiente:

    a. Obtenga la dirección URL del archivo tar para la última versión de Tomcat 8 en la [página de descargas de Tomcat 8](http://tomcat.apache.org/download-80.cgi).

    b. Utilice cURL para descargar la versión más reciente mediante el vínculo. Ejecute los comandos siguientes:

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. Instale Tomcat en el directorio */opt/tomcat*. Cree la carpeta y, después, abra el archivo:

    ```bash  
        sudo mkdir /opt/tomcat
        sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
        sudo chown -R tomcat webapps/ work/ temp/ logs/
    ```

1. Actualice los permisos de Tomcat mediante los comandos siguientes:

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

1. Cree un archivo de servicio *systemd*, para poder ejecutar Tomcat como un servicio.

   a. Tomcat debe saber dónde está instalado Java. Esta ruta de acceso se conoce comúnmente como *JAVA_HOME*. Busque la ubicación mediante la ejecución de:

    ```bash  
        sudo update-java-alternatives -l
    ```

    Esto genera algo parecido a lo siguiente:

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    Para construir la variable *JAVA_HOME*, tome la ruta de acceso de la salida y agregue */jre*. Por ejemplo, con el ejemplo anterior, */usr/lib/jvm/java-1.8.0-openjdk-amd64/jre*.

    b. Utilice el valor del servidor para crear el archivo de servicio systemd:

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. Pegue el siguiente contenido en el archivo de servicio. Modifique el valor de *JAVA_HOME* si es necesario para que coincida con el valor que encontró en el sistema. También puede modificar la configuración de asignación de memoria que se especifica en CATALINA_OPTS:

    ```Text  
        [Unit]
        Description=Apache Tomcat Web Application Container
        After=network.target
        
        [Service]
        Type=forking
        
        Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
        Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
        Environment=CATALINA_HOME=/opt/tomcat
        Environment=CATALINA_BASE=/opt/tomcat
        Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
        Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
        
        ExecStart=/opt/tomcat/bin/startup.sh
        ExecStop=/opt/tomcat/bin/shutdown.sh
    
        User=tomcat
        Group=tomcat
        UMask=0007
        RestartSec=10
        Restart=always
        
        [Install]
        WantedBy=multi-user.target
    ```

    d. Guarde y cierre el archivo.

    e. Vuelva a cargar el demonio systemd para que conozca el archivo de servicio:

    ```bash  
        sudo systemctl daemon-reload
    ```

    f. Inicie el servicio Tomcat: 

    ```bash  
        sudo systemctl start tomcat
    ```

    g. Compruebe que se ha iniciado sin errores escribiendo lo siguiente:

    ```bash  
        sudo systemctl status tomcat
    ```

1. Compruebe el servidor Tomcat. Tomcat utiliza el puerto 8080 para aceptar solicitudes convencionales. Permita el tráfico a ese puerto mediante la ejecución del comando siguiente:

    ```bash  
        sudo ufw allow 8080
    ```

    Si no ha agregado las *reglas del puerto de entrada* para la máquina virtual de Azure Stack Hub, hágalo ahora. Para obtener más información, consulte [Create a VM](#create-a-vm) (Creación de una VM).

1. Abra un explorador en la misma red que Azure Stack Hub y abra el servidor *suMáquina.local.cloudapp.azurestack.external:8080*.

    ![Captura de pantalla de la página de Apache Tomcat.](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Se carga la página de Apache Tomcat en el servidor. A continuación, configura el servidor para tener acceso al estado del servidor y las aplicaciones Manager y Host Manager.

1. Habilite el archivo de servicio para que Tomcat se inicie automáticamente cuando se reinicie el servidor:

    ```bash  
        sudo systemctl enable tomcat
    ```

1. Para tener acceso a la interfaz de administración web, configure el servidor de Tomcat. 

   a. Modifique el archivo *tomcat-users.xml* y defina un rol y un usuario de modo que pueda iniciar sesión. Defina el usuario para tener acceso a `manager-gui` y `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. Agregue los siguientes elementos en la sección `<tomcat-users>`:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    Por ejemplo, el archivo final podría ser similar al siguiente:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
        <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    c. Guarde y cierre el archivo.

1. Tomcat restringe el acceso a las aplicaciones *Manager* y *Host Manager* a las conexiones que proceden del servidor. Como está instalando Tomcat en una máquina virtual en Azure Stack Hub, querrá quitar esta restricción. Cambie las restricciones de las direcciones IP en estas aplicaciones mediante la modificación de los archivos *context.xml* adecuados.

    a. Actualice *context.xml* en la aplicación Manager:

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. Convierta en comentario la restricción de la dirección IP para permitir conexiones desde cualquier lugar o agregue la dirección IP de la máquina que está utilizando para conectarse a Tomcat.

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. Guarde y cierre el archivo.

    d. Actualice *context.xml* en la aplicación Host Manager con una actualización similar:

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    e. Guarde y cierre el archivo.

1. Para actualizar el servidor con los cambios, reinicie el servicio Tomcat:

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Abra un explorador en la misma red que Azure Stack Hub y abra el servidor *suMáquina.local.cloudapp.azurestack.external:8080*.

    a. Para examinar el estado del servidor de Tomcat y comprobar que tiene acceso, seleccione **Server Status** (Estado del servidor).

    b. Inicie sesión con las credenciales de Tomcat.

    ![Apache Tomcat en una máquina virtual de Azure Stack Hub](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Creación de una aplicación

Deberá crear un archivo WAR para implementar en Tomcat. Si simplemente desea comprobar el entorno, puede encontrar un ejemplo de archivo WAR en el [sitio de Apache Tomcat](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Para obtener instrucciones sobre cómo desarrollar aplicaciones de Java en Azure, consulte [Compile e implemente aplicaciones Java en Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Conexión con SSH mediante PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Para actualizar el servidor con el paquete de la aplicación, detenga el servicio Tomcat:

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Para poder escribir en la carpeta de aplicaciones web, agregue su usuario FTP al grupo de Tomcat. El usuario FTP es el usuario que define al crear la máquina virtual en Azure Stack Hub.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. Para borrar la carpeta de aplicaciones web y cargar el archivo WAR nuevo o actualizado, conéctese a la máquina virtual con FileZilla. Para obtener instrucciones, consulte [Conexión con SFTP con FileZilla](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    a. Borre *TOMCAT_HOME/webapps*.

    b. Agregue el archivo WAR a *TOMCAT_HOME/webapps* (por ejemplo, */opt/tomcat/webapps/* ).

1.  Tomcat se expande automáticamente e implementa la aplicación. Puede verlo mediante el nombre DNS que ha creado anteriormente. Por ejemplo:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample
    ```
    
## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [desarrollar para Azure Stack Hub](azure-stack-dev-start.md).
- Obtenga información sobre las [implementaciones comunes para Azure Stack Hub como IaaS](azure-stack-dev-start-deploy-app.md).
- Para conocer el lenguaje de programación de Java y consultar recursos adicionales para Java, consulte [Java.com](https://www.java.com).
