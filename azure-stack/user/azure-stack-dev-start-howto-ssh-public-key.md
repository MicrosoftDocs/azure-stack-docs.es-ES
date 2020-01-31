---
title: Uso de una clave pública SSH con Azure Stack Hub
description: Uso de una clave pública SSH
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: c9a37dab12f3a96e2f44aa9ccb92eaea295a0d0b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883807"
---
# <a name="use-an-ssh-public-key"></a>Uso de una clave pública SSH

Para usar una conexión SSH abierta desde la máquina de desarrollo a la máquina virtual del servidor en la instancia de Azure Stack Hub que hospeda la aplicación web, es posible que deba crear un par de claves, pública y privada, de Secure Shell (SSH). 

En este artículo, va a crear las claves y, después, las va a utilizar para conectarse al servidor. Puede usar un cliente SSH para obtener un símbolo del sistema de bash en el servidor Linux o utilice un cliente SFTP seguro (SFTP) para mover archivos desde y hacia el servidor.

## <a name="create-an-ssh-public-key-on-windows"></a>Creación de una clave pública SSH en Windows

En esta sección, va a usar PuTTY Key Generator para crear una clave pública de SSH y un par de claves privadas para usar al crear una conexión segura a máquinas Linux en la instancia de Azure Stack Hub. PuTTTY es un emulador de terminal gratuito que le permite conectarse a un servidor a través de SSH y Telnet.

1. [Descargue e instale PuTTY para su equipo](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Abra PuTTY Key Generator.

    ![PuTTY Key Generator con un cuadro de claves en blanco](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. En **Parameters** (Parámetros), seleccione **RSA**.

1. En el cuadro **Number of bits in a generated key** (Número de bits en una clave generada), escriba **2048**.  

1. Seleccione **Generar**.

1. En el área **Key** (Clave), genere algún carácter aleatorio al mover el cursor sobre el área en blanco.

    ![PuTTY Key Generator con un cuadro de claves rellenado](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Escriba una **frase de contraseña de la clave** y confírmela en el cuadro **Confirm passphrase** (Confirmar frase de contraseña). Anote la frase de contraseña para su uso posterior.

1. Seleccione **Save public key** (Guardar clave pública) y guárdela en una ubicación donde pueda acceder a ella.

1. Seleccione **Save private key** (Guardar clave privada) y guárdela en una ubicación donde pueda acceder a ella. Recuerde que pertenece a la clave pública.

La clave pública se almacena en el archivo de texto que guardó. El texto será similar al siguiente:

```text  
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "rsa-key-20190330"
THISISANEXAMPLEDONOTUSE AAAAB3NzaC1yc2EAAAABJQAAAQEAthW2CinpqhXq
9uSa8/lSH7tLelMXnFljSrJIcpxp3MlHlYVbjHHoKfpvQek8DwKdOUcFIEzuStfT
Z8eUI1s5ZXkACudML68qQT8R0cmcFBGNY20K9ZMz/kZkCEbN80DJ+UnWgjdXKLvD
Dwl9aQwNc7W/WCuZtWPazee95PzAShPefGZ87Jp0OCxKaGYZ7UXMrCethwfVumvU
aj+aPsSThXncgVQUhSf/1IoRtnGOiZoktVvt0TIlhxDrHKHU/aZueaFXYqpxDLIs
BvpmONCSR3YnyUtgWV27N6zC7U1OBdmv7TN6M7g01uOYQKI/GQ==
---- END SSH2 PUBLIC KEY ----
```

Cuando una aplicación solicita la clave, se copia y pega todo el contenido del archivo de texto.

## <a name="connect-with-ssh-by-using-putty"></a>Conexión con SSH mediante PuTTY

Al instalar PuTTY, tendrá PuTTY Key Generator y un cliente SSH. En esta sección, va a abrir el cliente SSH, PuTTY y va a configurar los valores de conexión y la clave SSH. Si está en la misma red que la instancia de Azure Stack Hub, se conecta a la máquina virtual.

Antes de conectarse, necesitará:
- PuTTY
- La dirección IP y el nombre de usuario de la máquina Linux en la instancia de Azure Stack Hub que usa una clave pública SSH como tipo de autenticación.
- El puerto 22 que se va a abrir para la máquina.
- La clave SSH pública que usó al crear la máquina.
- La máquina cliente que ejecuta PuTTY estará en la misma red que la instancia de Azure Stack Hub.

1. Abra PuTTY.

    ![Panel de configuración de PuTTY](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. En el cuadro **Host Name (or IP address)** (Nombre de host [o dirección IP]), escriba el nombre de usuario y la dirección IP pública de la máquina (por ejemplo, **username@192.XXX.XXX.XX** ). 
3. Compruebe que **Port** (Puerto) es **22** y que **Connection type** (Tipo de conexión) es **SSH**.
4. En el árbol **Category** (Categoría), expanda **SSH** y **Auth**.

    ![Panel de configuración de PuTTY: clave privada SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Junto al cuadro **Private key file for authentication** (Archivo de clave privada para autenticación), seleccione **Browse** (Examinar) y, a continuación, busque el archivo de clave privada ( *\<nombreArchivo>.ppk*) del par de claves pública y privada.
6. En el árbol **Category** (Categoría), seleccione **Session** (Sesión).

    ![Cuadro "Saved Sessions" (Sesiones guardadas) del panel de configuración de PuTTY](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. En **Saved Sessions** (Sesiones guardadas), escriba el nombre de la sesión y seleccione **Save** (Guardar).
8. En la lista **Saved Sessions** (Sesiones guardadas), seleccione sucesivamente el nombre de la sesión y **Load** (Cargar).
9. seleccione **Open**(Abrir). Se abre la sesión SSH.

## <a name="connect-with-sftp-with-filezilla"></a>Conexión con SFTP con FileZilla

Para mover los archivos hacia y desde su máquina Linux, puede usar Filezilla como cliente FTP, que admite FTP seguro (SFTP). FileZilla funciona en Windows 10, Linux y macOS. El cliente de FileZilla es compatible con FTP, con FTP a través de TLS (FTPS) y SFTP. Es un software de código abierto que se distribuye de forma gratuita bajo los términos de la licencia pública general de GNU.

### <a name="set-your-connection"></a>Configuración de la conexión

1. [Descargue e instale FileZilla](https://filezilla-project.org/download.php).
1. Abra FileZilla.
1. Seleccione **Archivo** > **Gestor de sitios**.

    ![Panel Gestor de sitios de FileZilla](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. En la lista desplegable **Protocolo**, seleccione **SFTP - SSH File Transfer Protocol**.
1. En el cuadro **Servidor**, introduzca la dirección IP pública de la máquina.
1. En el cuadro **Modo de acceso**, seleccione **Normal**.
1. Escriba su nombre de usuario y contraseña.
1. Seleccione **Aceptar**.
1. Seleccione **Edición** > **Opciones**.

    ![Panel de configuración de FileZilla](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. En el árbol **Seleccione página**, expanda **Conexión** y, a continuación, seleccione **SFTP**.
1. Seleccione **Añadir archivo de clave** y, a continuación, especifique el archivo de clave privada (por ejemplo, *\<nombreArchivo>.ppk*).
1. Seleccione **Aceptar**.

### <a name="open-your-connection"></a>Apertura de la conexión

1. Abra FileZilla.
1. Seleccione **Archivo** > **Gestor de sitios**.
1. Seleccione el nombre del sitio y, después, **Conectar**.

## <a name="next-steps"></a>Pasos siguientes

Consulte [Configurar un entorno de desarrollo en Azure Stack Hub](azure-stack-dev-start.md) para conocer el procedimiento.
