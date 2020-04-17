---
title: Creación de una máquina virtual Linux mediante la CLI de Azure en Azure Stack Hub
description: Cree una máquina virtual Linux mediante la CLI de Azure en Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 49b2a950b523ca76143f50784efb048880d1ac93
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/15/2020
ms.locfileid: "77701945"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack-hub"></a>Inicio rápido: Creación de una máquina virtual de servidor Linux mediante la CLI de Azure en Azure Stack Hub

Puede crear una máquina virtual (VM) Ubuntu Server 16.04 LTS mediante la CLI de Azure. Siga los pasos que se describen en este artículo para crear y usar una máquina virtual. En este artículo también se muestra cómo:

* Conectarse a la máquina virtual con un cliente remoto.
* Instalar un servidor web NGINX y ver la página principal predeterminada.
* Borrar los recursos que no use.

## <a name="prerequisites"></a>Prerrequisitos

* Una imagen de Linux en el Marketplace de Azure Stack Hub

   El Marketplace de Azure Stack Hub no contiene la imagen de Linux de forma predeterminada. Solicite al operador de Azure Stack Hub que le proporcione la imagen de Ubuntu Server 16.04 LTS que necesita. El operador puede seguir las instrucciones del artículo [Descarga de elementos de Marketplace desde Azure a Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md).

* Azure Stack Hub requiere una versión específica de la CLI de Azure para crear y administrar sus recursos. Si no tiene la CLI de Azure configurada para Azure Stack Hub, inicie sesión en el [Kit de desarrollo de Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (o en un cliente externo basado en Windows si está [conectado mediante VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)) y siga los pasos para [instalar y configurar la CLI de Azure](azure-stack-version-profiles-azurecli2.md).

* Se guardó una clave pública de Secure Shell (SSH) denominada *id_rsa.pub* en el directorio *.ssh* del perfil de usuario de Windows. Para obtener más información sobre la creación de claves SSH, consulte [Uso de una clave pública SSH](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que puede implementar y administrar recursos de Azure Stack Hub. Desde el kit de desarrollo o el sistema integrado de Azure Stack Hub, ejecute el comando [az group create](/cli/azure/group#az-group-create) para crear un grupo de recursos.

> [!NOTE]
> En los siguientes ejemplos de código, hemos asignado valores para todas las variables. Sin embargo, puede asignar sus propios valores.

En el ejemplo siguiente, se crea un grupo de recursos denominado myResourceGroup en la ubicación local: 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Cree una máquina virtual con el comando [az vm create](/cli/azure/vm#az-vm-create). En el ejemplo siguiente se crea una máquina virtual denominada myVM. En este ejemplo se usa *Demouser* como nombre de usuario administrador y *Demouser@123* como contraseña de administrador. Actualice estos valores a un valor apropiado para su entorno.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

La dirección IP pública se devuelve en el parámetro **PublicIpAddress**. Anote la dirección para usarla más adelante con la máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Apertura del puerto 80 para el tráfico web

Dado que en esta máquina virtual se va a ejecutar el servidor web IIS, debe abrir el puerto 80 para el tráfico de Internet. Para abrir el puerto, use el comando [az vm open-port](/cli/azure/vm): 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Uso de SSH para conectarse a la máquina virtual

En un equipo cliente con SSH instalado, conéctese a la máquina virtual. Si está trabajando en un cliente Windows, puede usar [PuTTY](https://www.putty.org/) para crear la conexión. Para conectarse a la máquina virtual, use el comando siguiente:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalación del servidor web NGINX

Ejecute el siguiente script para actualizar los recursos del paquete e instalar el paquete NGINX más reciente:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visualización de la página de bienvenida de NGINX

Con el servidor web NGINX instalado y el puerto 80 abierto en la máquina virtual, puede acceder al servidor web mediante la dirección IP pública de la máquina virtual. Para hacerlo, abra un explorador y vaya a ```http://<public IP address>```.

![Página principal del servidor web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Borre los recursos que ya no necesite. Puede usar el comando [az group delete](/cli/azure/group#az-group-delete) para eliminarlos. Ejecute el siguiente comando:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha implementado una máquina virtual de servidor Linux básica con un servidor web. Para más información sobre las máquina virtuales de Azure Stack Hub, consulte el artículo [Consideraciones sobre máquinas virtuales en Azure Stack Hub](azure-stack-vm-considerations.md).
