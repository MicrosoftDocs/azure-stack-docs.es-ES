---
title: Creación de una máquina virtual Linux mediante Azure Stack Hub
description: Cree una máquina virtual de servidor Linux mediante Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/06/2020
ms.openlocfilehash: 026eca2d69dfbbcebbdba00b52f8d8ec57f3c2a5
ms.sourcegitcommit: 2d2ae0b6db2e4f43f8496b184f30cddbb08b2cbd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2020
ms.locfileid: "91815292"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-stack-hub-portal"></a>Inicio rápido: Creación de una máquina virtual de servidor Linux mediante el portal de Azure Stack Hub

Puede crear una máquina virtual en Ubuntu Server 16.04 LTS mediante el portal de Azure Stack Hub. Siga los pasos que se describen en este artículo para crear y usar una máquina virtual. En este artículo también se muestra cómo:

* Conectarse a la máquina virtual con un cliente remoto.
* Instalar a un servidor web NGINX.
* Realizar la limpieza de los recursos.

> [!NOTE]  
> Las imágenes de este artículo se han actualizado para reflejar los cambios introducidos en la versión 1808 de Azure Stack Hub. La versión 1808 presenta compatibilidad con los *discos administrados*, además de los no administrados. Si usa una versión anterior, las imágenes para algunas tareas, como la selección de disco, diferirán de lo que se muestra en la interfaz de usuario.  

## <a name="prerequisites"></a>Requisitos previos

* Una imagen de Linux en el Marketplace de Azure Stack Hub

   De forma predeterminada, el Marketplace de Azure Stack Hub no tiene ninguna imagen de Linux. Solicite al operador de Azure Stack Hub que le proporcione la imagen de Ubuntu Server 16.04 LTS que necesita. El operador puede seguir las instrucciones del artículo [Descarga de elementos de Marketplace desde Azure a Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md).

* Acceso a un cliente SSH

   Si usa el Kit de desarrollo de Azure Stack (ASDK), es posible que no tenga acceso a un cliente de Secure Shell (SSH). Si necesita un cliente, existen varios paquetes que incluyen un cliente SSH. Por ejemplo, PuTTY incluye un cliente SSH y el generador de claves SSH (puttygen.exe). Para más información acerca de los paquetes disponibles, consulte [Uso de una clave pública SSH](azure-stack-dev-start-howto-ssh-public-key.md).

* Esta guía de inicio rápido usa PuTTY para generar las claves SSH y para conectarse a la VM de servidor Linux. [Descargue e instale PuTTY](https://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Creación de un par de claves SSH

Para finalizar todos los pasos de este artículo, necesita un par de claves SSH. Si ya tiene un par de claves SSH, puede omitir este paso.

Para crear un par de claves SSH:

1. Vaya a la carpeta de instalación de PuTTY (la ubicación predeterminada es *C:\Program Files\PuTTY*) y ejecute:

    `puttygen.exe`

1. En la ventana de **Putty Key Generator**, defina **Type of key to generate**(Tipo de clave que se va a generar) como **RSA** y **Number of bits in a generated key** (Número de bits de una clave generada), como **2048**.

   ![Configuración del generador de claves de PuTTY](media/azure-stack-quick-linux-portal/Putty01a.png)

1. Seleccione **Generar**.

1. Para generar una clave, en el cuadro **Key** (Clave), mueva el puntero de forma aleatoria.

1. Una vez finalizada la generación de claves, seleccione **Save public key** (Guardar clave pública) y, a continuación, seleccione **Save private key** (Guardar clave privada) para guardar las claves en archivos.

   ![Resultados del generador de claves de PuTTY](media/azure-stack-quick-linux-portal/Putty02a.png)

## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Inicio de sesión en el portal de Azure Stack Hub

La dirección del portal de Azure Stack Hub depende del producto de Azure Stack al que se vaya a conectar:

* Para el ASDK, vaya a `https://portal.local.azurestack.external`.

* Para un sistema integrado de Azure Stack Hub, vaya a la dirección URL que proporciona su operador de Azure Stack Hub.

## <a name="create-the-vm"></a>Creación de la máquina virtual

1. Seleccione **Crear un recurso** > **Proceso**. Busque `Ubuntu Server 16.04 LTS`. Seleccione el nombre.

   ![Crear servidor Linux](media/azure-stack-quick-linux-portal/image1.png)

1. Seleccione **Crear**.

   ![Crear servidor Linux: Crear](media/azure-stack-quick-linux-portal/image2.png)

1. Escriba la información de la máquina virtual. En Tipo de autenticación, seleccione **Clave pública SSH**, pegue la clave pública SSH que ha guardado y, a continuación, seleccione **Aceptar**.

    > [!NOTE]  
    > Asegúrese de eliminar los espacios en blanco iniciales y finales de la clave.

   ![Authentication](media/azure-stack-quick-linux-portal/image3.png)

1. Seleccione **D1_v2** como tamaño de la máquina virtual.

   ![Crear servidor Linux: Tamaño](media/azure-stack-quick-linux-portal/image4.png)

1. Especifique los cambios en los valores predeterminados en la hoja **Configuración**. Use discos administrados. Si tiene que permitir el acceso de SSH, seleccione **SSH (22)** para abrir el puerto. Cuando las configuraciones estén preparadas, seleccione **Aceptar**.

   ![Crear servidor Linux: Configuración](media/azure-stack-quick-linux-portal/image5.png)

1. Seleccione **Aceptar** en Resumen para iniciar la implementación de la máquina virtual. Seleccione **Virtual Machines** para ver la nueva máquina virtual, busque el nombre de esta y, finalmente, seleccione la máquina virtual en los resultados de la búsqueda.

![Crear servidor Linux: Resumen](media/azure-stack-quick-linux-portal/image5.png)

## <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

1. Seleccione **Connect** (Conectar) en la página de la VM. Podrá encontrar la cadena de conexión SSH que necesita para conectarse a la máquina virtual. 

1. En la página **PuTTY Configuration** (Configuración de PuTTY), en el panel **Category** (Categoría), desplácese hacia abajo y expanda **SSH**. A continuación, seleccione **Auth** (Autenticación). 

   ![Conexión de la VM](media/azure-stack-quick-linux-portal/putty03a.png)

1. Seleccione **Browse** (Examinar) y, a continuación, seleccione el archivo de clave privada que guardó.

1. En el panel **Category** (Categoría), desplácese hacia arriba y seleccione **Session** (Sesión).

1. En el cuadro **Host Name (Nombre de host) o IP address (Dirección IP)** , pegue la cadena de conexión que se muestra en el portal de Azure Stack Hub. En este ejemplo, la cadena es *asadmin@192.168.102.34* .

1. Seleccione **Open** (Abrir) para abrir una sesión para la VM.

   ![Sesión de Linux](media/azure-stack-quick-linux-portal/Putty05a.png)

## <a name="install-the-nginx-web-server"></a>Instalación del servidor web NGINX

Para actualizar los orígenes de paquetes e instalar el paquete de NGINX más reciente en la VM, escriba los siguientes comandos bash:

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Cuando haya finalizado la instalación de NGINX, cierre la sesión SSH y abra la página **Información general** de la máquina virtual en el portal de Azure Stack Hub.

## <a name="open-port-80-for-web-traffic"></a>Apertura del puerto 80 para el tráfico web

Los grupos de seguridad de red (NSG) protegen el tráfico entrante y saliente. Cuando se crea una máquina virtual en el portal de Azure Stack Hub, se crea una regla de entrada en el puerto 22 para las conexiones SSH. Dado que esta máquina virtual hospeda un servidor web, es preciso crear una regla de NSG para permitir el tráfico web en el puerto 80.

1. En la página **Información general** de la VM, seleccione el nombre del **Grupo de recursos**.

1. Seleccione el **grupo de seguridad de red** de la máquina virtual. Para identificar el NSG, use la columna **Tipo**.

1. En el panel izquierdo, en **Configuración**, seleccione **Reglas de seguridad de entrada**.

1. Seleccione **Agregar**.

1. En el cuadro **Nombre**, escriba **http**. 

1. Asegúrese de que **Intervalo de puertos** esté establecido en 80 y **Acción**, en **Permitir**.

1. Seleccione **Aceptar**.

## <a name="view-the-welcome-to-nginx-page"></a>Ver la página de bienvenida para NGINX

Con NGINX instalado y el puerto 80 abierto en la VM, puede acceder al servidor web mediante la dirección IP pública de la VM. (La dirección IP pública se muestra en la página **Información general** de la VM).

Abra un explorador web y vaya a *http://\<public IP address>* .

![Página principal del servidor web NGINX](media/azure-stack-quick-linux-portal/linux-05a.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Borre los recursos que ya no necesite. Para eliminar la VM y sus recursos, seleccione el grupo de recursos en la página de la VM y, a continuación, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha implementado una máquina virtual básica en un servidor Linux con un servidor web. Para más información sobre las máquinas virtuales de Azure Stack Hub, continúe con el artículo [Consideraciones sobre máquinas virtuales en Azure Stack Hub](azure-stack-vm-considerations.md).
