---
title: Adición de imágenes de Linux al Marketplace de Azure Stack Hub
description: Aprenda a agregar imágenes de Linux al Marketplace de Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2019
ms.openlocfilehash: 532f61b2b306dab833c35dab403226e70950d43a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699344"
---
# <a name="add-linux-images-to-the-azure-stack-hub-marketplace"></a>Adición de imágenes de Linux al Marketplace de Azure Stack Hub

Puede implementar máquinas virtuales Linux en Azure Stack Hub mediante la adición de una imagen basada en Linux a Marketplace de Azure Stack Hub. La manera más fácil de agregar una imagen de Linux a Azure Stack Hub es mediante la administración de Marketplace. Estas imágenes se han preparado y probado para que sean compatibles con Azure Stack Hub.

## <a name="marketplace-management"></a>Administración de Marketplace

Para descargar imágenes de Linux desde Azure Marketplace, consulte [Descarga de elementos de Marketplace de Azure a Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). Seleccione las imágenes de Linux que quiera ofrecer a los usuarios en Azure Stack Hub.

Hay actualizaciones frecuentes de estas imágenes, por lo que se recomienda comprobar Marketplace Management a menudo para estar al día.

## <a name="prepare-your-own-image"></a>Preparación de su propia imagen

Siempre que sea posible, descargue las imágenes disponibles a través de Marketplace Management. Dichas imágenes se han preparado y probado para Azure Stack Hub.

### <a name="azure-linux-agent"></a>Agente Linux de Azure

El agente Linux de Azure (que suele llamarse **WALinuxAgent** o **walinuxagent**) es obligatorio, pero no todas las versiones del agente funcionan en Azure Stack Hub. No se admiten las versiones entre 2.2.21 y 2.2.34 (ambas inclusive) en Azure Stack Hub. Para usar las versiones del agente más recientes, superiores a la 2.2.35, aplique las revisiones 1901 o 1902, o actualice Azure Stack Hub a la versión 1903 (o cualquier versión superior). Tenga en cuenta que [cloud-init](https://cloud-init.io/) se admite en versiones de Azure Stack Hub posteriores a la versión 1910.

| Compilación de Azure Stack Hub | Compilación del agente Linux de Azure |
| ------------- | ------------- |
| 1.1901.0.99 o versiones anteriores | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 o más reciente |
| 1.1902.2.73  | 2.2.35 o más reciente |
| 1.1903.0.35  | 2.2.35 o más reciente |
| Compilaciones posteriores a 1903 | 2.2.35 o más reciente |
| No compatible | 2.2.21-2.2.34 |
| Compilaciones posteriores a 1910 | Todas las versiones del agente WALA de Azure|

Puede preparar su propia imagen de Linux mediante las siguientes instrucciones:

* [Distribuciones basadas en CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES y openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>Cloud-Init

[Cloud-init](https://cloud-init.io/) se admite en versiones de Azure Stack Hub posteriores a la versión 1910. Para usar cloud-init en la personalización de la máquina virtual Linux, puede utilizar las siguientes instrucciones de PowerShell.

### <a name="step-1-create-a-cloud-inittxt-file-with-your-cloud-config"></a>Paso 1: Creación de un archivo cloud-init.txt con la configuración de la nube

Cree un archivo denominado cloud-init.txt y pegue la siguiente configuración de la nube:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
  ```
  
### <a name="step-2-reference-the-cloud-inittxt-during-the-linux-vm-deployment"></a>Paso 2: Referencia a cloud-init.txt durante la implementación de máquinas virtuales Linux

Cargue el archivo en una cuenta de almacenamiento de Azure, una cuenta de almacenamiento de Azure Stack Hub o un repositorio de GitHub al que pueda acceder la máquina virtual Linux con Azure Stack Hub.
Actualmente, el uso de cloud-init para la implementación de máquinas virtuales solo se admite en REST, PowerShell y la CLI, y no tiene una interfaz de usuario de portal asociada en Azure Stack Hub.

Puede seguir [estas](../user/azure-stack-quick-create-vm-linux-powershell.md) instrucciones para crear la máquina virtual Linux con PowerShell, pero asegúrese de hacer referencia a cloud-init.txt como parte de la marca `-CustomData`:

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>Adición de la imagen a Marketplace

Consulte [Incorporación de la imagen a Marketplace](azure-stack-add-vm-image.md). Asegúrese de que el `OSType` parámetro está establecido en `Linux`.

Después de agregar la imagen a Marketplace, se crea un elemento de Marketplace y los usuarios podrán implementar una máquina virtual Linux.

## <a name="next-steps"></a>Pasos siguientes

* [Descarga de elementos de Marketplace en Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
* [Introducción al Marketplace de Azure Stack Hub](azure-stack-marketplace.md)
