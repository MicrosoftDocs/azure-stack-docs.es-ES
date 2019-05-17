---
title: Implementación de un archivo WAR de Java en una máquina virtual en Azure Stack | Microsoft Docs
description: Implemente un archivo WAR de Java en una máquina virtual en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: e788be6315078fccee020fefe6ad79a20485c382
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482389"
---
# <a name="how-to-deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Implementación de una aplicación web de Java en una máquina virtual en Azure Stack

Puede crear una máquina virtual para hospedar la aplicación web de Python en Azure Stack. En este artículo se examinan los pasos que debería seguir para ajustar los parámetros del servidor, configurarlo para hospedar su aplicación web de Python y, a continuación, implementar la aplicación.

Java es un lenguaje de programación de equipos destinado a fines genéricos que es simultáneo, se basa en clases, se orienta a objetos y está diseñado para tener la mínima cantidad posible de dependencias de implementación. Su propósito es permitir que los desarrolladores de aplicaciones "escriban una vez y ejecuten en cualquier lugar", lo que significa que el código compilado de Java puede ejecutarse en todas las plataformas que admiten Java sin necesidad de volver a compilar. Para conocer el lenguaje de programación de Java y consultar recursos adicionales para Java, consulte [Java.com](https://www.java.com).

Este artículo le guiará a través de la instalación y configuración de un servidor Apache Tomcat en una VM de Linux en Azure Stack y, a continuación, la carga de un archivo de recurso de aplicación web (WAR) de Java en el servidor. Un archivo WAR se utiliza para distribuir una colección de archivos JAR, páginas de JavaServer, servlets de Java, clases de Java, archivos XML, bibliotecas de etiquetas, páginas web estáticas (HTML y archivos relacionados) y otros recursos que juntos constituyen una aplicación web.

Apache Tomcat, a menudo denominado Tomcat Server, es un contenedor de servlet de Java de código abierto desarrollado por Apache Software Foundation. Tomcat implementa varias especificaciones de Java EE, como servlets de Java, páginas de JavaServer, Java EL y WebSocket, y proporciona un entorno de servidor web de Java HTTP "puramente de Java" en el que Java puede ejecutarse.

## <a name="create-a-vm"></a>Crear una VM

1. Configure la máquina virtual en Azure Stack. Siga los pasos de [Deploy a Linux VM to host a web app in Azure Stack](azure-stack-dev-start-howto-deploy-linux.md) (Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack).

2. En la hoja de red de la máquina virtual, asegúrese de que los siguientes puertos sean accesibles:

    | Port | Protocolo | DESCRIPCIÓN |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es un protocolo de aplicación para los sistemas de información de hipermedia distribuidos y colaborativos. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTPPS) es una extensión del Protocolo de transferencia de hipertexto (HTTP). Se usa para establecer una comunicación segura a través de la red de un equipo. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red criptográfico que permite usar servicios de red de forma segura a través de una red no segura. Usará esta conexión con un cliente SSH para configurar la máquina virtual e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario de su equipo.   |
    | 8080 | Personalizado | El puerto predeterminado para el servicio de Apache Tomcat es 8080. Para un servidor de producción, querrá enrutar el tráfico a través de 80 y 443. |

## <a name="install-java"></a>Instalar Java

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Connect via SSH with PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty) (Conexión mediante SSH con PuTTy).
2. En el símbolo de bash de la máquina virtual, escriba los siguientes comandos:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Valide la instalación. Aún conectado a la máquina virtual en la sesión SSH, escriba los siguientes comandos:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Instalación y configuración de Tomcat

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Connect via SSH with PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty) (Conexión mediante SSH con PuTTy).

2. Cree el usuario de Tomcat.
    - En primer lugar, cree un nuevo grupo de Tomcat:
        ```bash  
            sudo groupadd tomcat
        ```
     
    - En segundo lugar, cree un nuevo usuario de Tomcat y conviértalo en miembro del grupo de Tomcat con un directorio de inicio de `/opt/tomcat`, que es donde instalará Tomcat, y con un shell de `/bin/false` (para que nadie pueda iniciar sesión en la cuenta ):
        ```bash  
            sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
        ```

3. Instale Tomcat.
    - En primer lugar, obtenga la dirección URL del archivo tar para la última versión de Tomcat 8 en la página de descargas de Tomcat 8, en [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi).
    - En segundo lugar, use curl para descargar la versión más reciente mediante el vínculo.

        ```bash  
            cd /tmp 
            curl -O <URL for the tar for the latest version of Tomcat 8>
        ```

    - En tercer lugar, instale Tomcat en el directorio `/opt/tomcat`. Cree el directorio y, a continuación, extraiga el archivo mediante los siguientes comandos:

        ```bash  
            sudo mkdir /opt/tomcat
            sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
            sudo chown -R tomcat webapps/ work/ temp/ logs/
        ```

4. Actualice los permisos de Tomcat.

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

5. Cree un archivo de servicio `systemd`. para que pueda ejecutar Tomcat como un servicio.

    - Tomcat debe saber dónde está instalado Java. Esta ruta de acceso se conoce comúnmente como **JAVA_HOME**. Busque la ubicación mediante la ejecución de:

        ```bash  
            sudo update-java-alternatives -l
        ```

        El resultado se parecerá a lo siguiente:

        ```Text  
            Output
            java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
        ```

        La variable **JAVA_HOME** se puede construir tomando la ruta de acceso de la salida y agregando `/jre`. Por ejemplo, utilizando el ejemplo anterior: `/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre`.

    - Utilice el valor de su servidor para crear el archivo de servicio systemd.

        ```bash  
            sudo nano /etc/systemd/system/tomcat.service
        ```

    - Pegue el siguiente contenido en el archivo de servicio. Modifique el valor de **JAVA_HOME** si es necesario para que coincida con el valor que encuentra en el sistema. También puede modificar la configuración de asignación de memoria que se especifica en CATALINA_OPTS:

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

    - Guarde y cierre el archivo.
    - Vuelva a cargar el demonio systemd para que conozca el archivo de servicio:

        ```bash  
            sudo systemctl daemon-reload
        ```

    - Inicie el servicio Tomcat. 

        ```bash  
            sudo systemctl start tomcat
        ```

    - Compruebe que se inició sin errores escribiendo lo siguiente:

        ```bash  
            ssudo systemctl status tomcat
        ```

6. Compruebe el servidor Tomcat. Tomcat utiliza el puerto 8080 para aceptar solicitudes convencionales. Permita el tráfico a ese puerto mediante la ejecución del comando siguiente:

    ```bash  
        sudo ufw allow 8080
    ```

    Si no ha agregado las **reglas del puerto de entrada** de la VM de Azure Stack, hágalo ahora. Para obtener más información, consulte [Create a VM](#create-a-vm) (Creación de una VM).

7. Abra un explorador en la misma red que Azure Stack y abra el servidor: `yourmachine.local.cloudapp.azurestack.external:8080`

    ![Apache Tomcat en una máquina virtual de Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Se carga la página de Apache Tomcat en el servidor. A continuación, configurará el servidor para tener acceso al estado del servidor y las aplicaciones Manager y Host Manager.

8. Habilite el archivo de servicio para que Tomcat se inicie automáticamente cuando se reinicie el servidor:

    ```bash  
        sudo systemctl enable tomcat
    ```

9. Configure el servidor de Tomcat para tener acceso a la interfaz de administración web. Edite `tomcat-users.xml` y defina un rol y un usuario de modo que pueda iniciar sesión. Defina el usuario para tener acceso a `manager-gui` y `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

    - Agregue los siguientes elementos en la sección `<tomcat-users>`:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    - Por ejemplo, el archivo final podría ser similar a lo siguiente:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
         <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    - Guarde y cierre el archivo.


10. Tomcat restringe el acceso a las aplicaciones **Manager** y **Host Manager** a las conexiones que proceden del servidor. Puesto que está instalando Tomcat en una máquina virtual en Azure Stack, querrá quitar esta restricción. Cambie las restricciones de las direcciones IP en estas aplicaciones mediante la edición de los archivos `context.xml` adecuados.

    - Actualice `context.xml` en la aplicación Manager:

        ```bash  
            sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
        ```

    - Marque como comentario la restricción de la dirección IP para permitir conexiones desde cualquier lugar, o agregue la dirección IP de la máquina que está utilizando para conectarse a Tomcat.

        ```XML  
        <Context antiResourceLocking="false" privileged="true" >
          <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                 allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
        </Context>
        ```

    - Guarde y cierre el archivo.

    - Actualice `context.xml` en la aplicación Host Manager con una actualización similar:

        ```bash  
            sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
        ```

    - Guarde y cierre el archivo.

11. Reinicie el servicio Tomcat para actualizar el servidor con los cambios:

    ```bash  
        sudo systemctl restart tomcat
    ```

12. Abra un explorador en la misma red que Azure Stack y abra el servidor: `yourmachine.local.cloudapp.azurestack.external:8080`.

    - Seleccione Server Status (Estado del servidor) para examinar el estado del servidor de Tomcat y compruebe que tiene acceso.
    - Inicie sesión con las credenciales de Tomcat.

![Apache Tomcat en una máquina virtual de Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Creación de una aplicación

Deberá crear un archivo WAR para implementar en Tomcat. Si simplemente desea comprobar el entorno, puede encontrar un ejemplo de archivo WAR en el sitio de Apache TomCat: [aplicación de ejemplo](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Para obtener instrucciones sobre cómo desarrollar aplicaciones de Java en Azure, consulte [Compile e implemente aplicaciones Java en Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación

1. Conéctese a la máquina virtual mediante un cliente SSH. Para obtener instrucciones, consulte [Connect via SSH with PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty) (Conexión mediante SSH con PuTTy).
1. Reinicie el servicio Tomcat para actualizar el servidor con el paquete de la aplicación:

    ```bash  
        sudo systemctl stop tomcat
    ```

2.  Agregue su usuario FTP al grupo de Tomcat para que pueda escribir en la carpeta de aplicaciones web. El usuario FTP es el usuario que define al crear la máquina virtual en Azure Stack.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

3. Conéctese a la máquina virtual con FileZilla para borrar la carpeta de aplicaciones web y, a continuación, cargue su archivo WAR nuevo o actualizado. Para obtener instrucciones sobre el uso de FileZila, consulte [Connect with SFTP with FileZilla](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla) (Conexión con SFTP con FileZilla).
    - Borre `TOMCAT_HOME/webapps`.
    - Agregue su archivo WAR a ` TOMCAT_HOME/webapps`; por ejemplo `/opt/tomcat/webapps/`.

4.  Tomcat se expande automáticamente e implementa la aplicación. Puede verlo con el nombre DNS que creó anteriormente. Por ejemplo: 

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [Develop for Azure Stack](azure-stack-dev-start.md)
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).