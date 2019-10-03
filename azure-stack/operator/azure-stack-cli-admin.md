---
title: Habilitación de la CLI de Azure para usuarios de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo habilitar la interfaz de la línea de comandos (CLI) multiplataforma para administrar e implementar recursos en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: eed0626458d6186b651801d5bda29c4cf77cef9a
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829053"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Habilitación de la CLI de Azure para usuarios de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede proporcionar el certificado de CA raíz a los usuarios de Azure Stack para que puedan habilitar la CLI de Azure en sus máquinas de desarrollo. Los usuarios necesitan el certificado para administrar recursos a través de la CLI.

 - **El certificado raíz de la entidad de certificación de Azure Stack** es necesario si los usuarios usan la CLI desde una estación de trabajo fuera del Kit de desarrollo de Azure Stack (ASDK).  

 - **El punto de conexión de los alias de máquina virtual** proporciona un alias, como "UbuntuLTS" o "Win2012Datacenter", que hace referencia a un publicador de imágenes, una oferta, una SKU y una versión como parámetro único al implementar máquinas virtuales.  

En las secciones siguientes se explica cómo obtener estos valores.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportación del certificado raíz de CA de Azure Stack

Si usa un sistema integrado, no es necesario exportar el certificado raíz de CA. Debe exportar el certificado raíz de CA en el ASDK.

Inicie sesión y ejecute el script siguiente para exportar el certificado raíz de ASDK en formato PEM:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-vm-aliases-endpoint"></a>Configuración del punto de conexión de alias de máquina virtual

Los operadores de Azure Stack deben configurar un punto de conexión accesible públicamente que hospede un archivo de alias de máquina virtual. El archivo de alias de máquina virtual es un archivo JSON que proporciona un nombre común para una imagen. Usará este nombre al implementar una máquina virtual como parámetro de la CLI de Azure.  

Antes de agregar una entrada a un archivo de alias, asegúrese de [descargar las imágenes de Azure Marketplace](azure-stack-download-azure-marketplace-item.md) o de que ha [publicado su propia imagen personalizada](azure-stack-add-vm-image.md). Si publica una imagen personalizada, anote la información del publicador, la oferta, la SKU y la versión que especificó en la publicación. Si es una imagen de Marketplace, la información se puede ver mediante el cmdlet `Get-AzureVMImage`.  

Hay un [archivo de alias de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) con muchos alias de imágenes comunes disponible, puede usarlo como punto de partida. Hospede este archivo en un espacio al que puedan acceder los clientes de la CLI. Una forma de hacerlo es hospedar el archivo en una cuenta de Blob Storage y compartir la dirección URL con los usuarios:

1. Descargue el [archivo de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) de GitHub.
2. Cree una cuenta de almacenamiento en Azure Stack. Cuando haya terminado, cree un contenedor de blobs. Establezca la directiva de acceso en "público".  
3. Cargue el archivo JSON en el nuevo contenedor. Cuando esté listo, puede ver la dirección URL del blob. Seleccione el nombre del blob y, a continuación, seleccione la dirección URL desde las propiedades del blob.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de plantillas con la CLI de Azure](../user/azure-stack-deploy-template-command-line.md )
- [Conexión con PowerShell](azure-stack-powershell-install.md)
- [Administración de permisos de usuario](azure-stack-manage-permissions.md)
