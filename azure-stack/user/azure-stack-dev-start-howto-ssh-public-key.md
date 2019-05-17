---
title: Uso de una clave pública SSH con Azure Stack | Microsoft Docs
description: Uso de una clave pública SSH
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0f4133052022963e1ed0457dd479a00bcc5bb749
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490056"
---
# <a name="how-to-use-an-ssh-public-key"></a>Uso de una clave pública SSH

Es posible que deba crear un par de claves pública y privada de SSH para usar una conexión SSH abierta desde su máquina de desarrollo y la máquina virtual del servidor en la instancia de Azure Stack que aloja su aplicación web. Este artículo le guiará a través de los pasos necesarios para obtener las claves y utilizarlas para conectarse al servidor. Puede usar un cliente SSH para obtener un símbolo del sistema de bash en el servidor Linux o un cliente SFTP para mover archivos desde y hacia el servidor.

## <a name="create-an-ssh-public-key-on-windows"></a>Creación de una clave pública SSH en Windows

En esta sección, utilizará el generador de claves de PuTTY para crear una clave pública de SSH y un par de claves privadas para usar al crear una conexión segura a máquinas Linux en su Azure Stack. PuTTY es una implementación gratuita de SSH y Telnet para Windows y plataformas de Unix, junto con un emulador de terminal `xterm`.

1. [Descargue e instale PuTTY para su equipo](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Abra el generador de claves de PuTTY.

1. Configure **Parameters** (Parámetros) en **RSA**.

1. Establezca el número de bits en una clave generada en `2048`.  

    ![Uso de PuTTY para generar una clave pública SSH](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. Seleccione **Generar**. En el área **Key** (Clave), genere algo de aleatoriedad moviendo el cursor sobre el área en blanco.

1. Agregue una **frase de contraseña de clave** y confírmela en el cuadro **Confirm** (Confirmar). Anote la frase de contraseña.
    ![Uso de PuTTY para generar una clave pública SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Seleccione **Save the public key** (Guardar la clave pública) y guárdela en una ubicación donde pueda acceder a ella.

1. Seleccione **Save private key** (Guardar clave privada) y guárdela en una ubicación donde pueda acceder a ella y recordar que pertenece a la clave pública.

La clave pública se almacena en el archivo de texto que guardó. Si lo abre, contendrá texto similar al siguiente:

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

Cuando se usa la clave pública, se copian y pegan todos los contextos del cuadro de texto como valor cuando una aplicación solicita la clave.

<!-- 
## Create an SSH public key on Linux

ToDo: I need to write this section.

-->
## <a name="connect-with-ssh-using-putty"></a>Conexión con SSH mediante PuTTY

Si ha instalado PuTTY, tiene el generador de claves y un cliente SSH. Abra el cliente SSH, PuTTY, configure los valores de conexión y la clave SSH y, si se encuentra en la misma red que Azure Stack, conéctese a la máquina virtual.

Antes de conectarse, necesitará:
- PuTTY
- La dirección IP y el nombre de usuario de la máquina de Linux en la instancia de Azure Stack que usa una clave pública SSH como tipo de autenticación.
- El puerto 22 debe estar abierto para la máquina.
- La clave SSH pública que usó al crear la máquina.
- La máquina cliente que ejecuta PuTTY estará en la misma red que su instancia de Azure Stack.

### <a name="connect-via-ssh-with-putty"></a>Conexión a través de SSH con PuTTy

1. Abra PuTTY.

    ![Uso de PuTTY para conectarse](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Agregue el nombre de usuario y la dirección IP pública de la máquina. Por ejemplo, `username@192.XXX.XXX.XX` para el **nombre de host**. 
3. Valide que el **puerto** `22` está configurado y el **tipo de conexión** está establecido en `SSH`.
4. Expanda **SSH** > **Auth** (Autenticación) en el árbol **Category** (Categoría).

    ![Clave privada SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Seleccione **Browse** (Examinar) y busque el archivo de clave privada (nombrearchivo.ppk) de su par de claves pública y privada.
6. Seleccione Session (Sesión) en el árbol de categoría.

    ![Clave pública SSH con clave privada](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. Escriba un nombre para la sesión en **Saved Sessions** (Sesiones guardadas) y seleccione **Save** (Guardar).
8. Seleccione el nombre de la sesión y **Load** (Cargar).
9. seleccione **Open**(Abrir). Se abrirá la sesión SSH.

## <a name="connect-with-sftp-with-filezilla"></a>Conexión con SFTP con FileZilla

Puede usar Filezilla como cliente FTP que admite SFTP para mover los archivos hacia y desde su equipo Linux. FileZilla funciona en Windows 10, Linux y macOS. El cliente de FileZilla es compatible con FTP, pero también FTP a través de TLS (FTPS) y SFTP. Es software de código abierto distribuido de forma gratuita bajo los términos de la licencia pública general de GNU.

### <a name="set-your-connection"></a>Configuración de la conexión

1. [Descargue e instale FileZilla](https://filezilla-project.org/download.php).
1. Abra FileZilla.
1. Seleccione **Archivo** > **Gestor de sitios**.

    ![Clave pública SSH con clave privada](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Seleccione **SFTP - SSH File Transfer Protocol** en **Protocolo**.
1. Agregue la dirección IP pública del equipo en el cuadro **Host**.
1. Seleccione **Normal** para **Modo de acceso**.
1. Agregue su nombre de usuario y contraseña.
1. Seleccione **Aceptar**.
1. Seleccione **Edición** > **Opciones**.

    ![Clave pública SSH con clave privada](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Expanda **Conexión** en el árbol **Seleccione página**. Seleccione **SFTP**.
1. Seleccione **Add key file** (Agregar archivo de clave) y agregue el archivo de clave privada, por ejemplo, nombrearchivo.ppk.
1. Seleccione **Aceptar**.

### <a name="open-your-connection"></a>Apertura de la conexión

1. Abra FileZilla.
1. Seleccione **Archivo** > **Gestor de sitios**.
1. Seleccione el nombre del sitio y luego **Conectar**.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información acerca de cómo [desarrollar para Azure Stack](azure-stack-dev-start.md)