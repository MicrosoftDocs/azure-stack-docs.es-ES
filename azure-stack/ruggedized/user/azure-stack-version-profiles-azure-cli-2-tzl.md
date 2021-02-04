---
title: Administración de Azure Stack Hub con la CLI de Azure | Microsoft Docs
description: Obtenga información sobre cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para administrar e implementar recursos en Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 1/9/2020
ms.openlocfilehash: 77e42d1e97839be296f2aaf80aefddbef25ce989
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245813"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>Administración e implementación de recursos en Azure Stack Hub con la CLI de Azure

*Se aplica a: Sistemas integrados de Azure Stack Hub*

Siga los pasos de este artículo para configurar la Interfaz de la línea de comandos (CLI) de Azure a fin de administrar los recursos de Azure Stack Hub de las plataformas de cliente de Linux, Mac y Windows.

## <a name="prepare-for-azure-cli"></a>Preparativos para la CLI de Azure

**El punto de conexión de alias de máquina virtual** proporciona un alias, como "UbuntuLTS" o "Win2012Datacenter". Este alias hace referencia a un editor de imagen, una oferta, una SKU y una versión como un solo parámetro al implementar máquinas virtuales.  

En la siguiente sección se describe cómo configurar el punto de conexión de alias de máquinas virtuales.

### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configuración del punto de conexión de alias de máquina virtual

Puede configurar un punto de conexión accesible públicamente que hospede un archivo de alias de máquina virtual. El archivo de alias de máquina virtual es un archivo JSON que proporciona un nombre común para una imagen. Usará este nombre al implementar una máquina virtual como parámetro de la CLI de Azure.

1. Si publica una imagen personalizada, anote la información del publicador, la oferta, la SKU y la versión que especificó en la publicación. Si es una imagen de Marketplace, la información se puede ver mediante el cmdlet ```Get-AzureVMImage```.  

2. Descargue el [archivo de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) de GitHub.

3. Cree una cuenta de almacenamiento en Azure Stack Hub. Cuando haya terminado, cree un contenedor de blobs. Establezca la directiva de acceso en "público".  

4. Cargue el archivo JSON en el nuevo contenedor. Cuando esté listo, puede ver la dirección URL del blob. Seleccione el nombre del blob y, a continuación, seleccione la dirección URL desde las propiedades del blob.

### <a name="install-or-upgrade-cli"></a>Instalación o actualización de la CLI

Inicie sesión en la estación de trabajo de desarrollo e instale la CLI. Azure Stack Hub requiere la versión 2.0 o superior de la CLI de Azure. La versión más reciente de los perfiles de API requiere una versión actual de la CLI. Instale la CLI mediante los pasos descritos en el artículo [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). 

Para comprobar si la instalación se realizó correctamente, abra un terminal o una ventana del símbolo del sistema y ejecute el siguiente comando:

```shell
az --version
```

Debería ver la versión de la CLI de Azure y otras bibliotecas dependientes instaladas en su equipo.
    
![CLI de Azure en la ubicación de Python de Azure Stack Hub](media/azure-stack-version-profiles-azure-cli-2/cli-python-location.png)


## <a name="windowslinux-azure-ad"></a>Windows/Linux (Azure AD)

Esta sección le guiará a través de la configuración de la CLI si usa Azure AD como servicio de administración de identidades y utiliza la CLI en una máquina Windows o Linux.

### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

1. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

2. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`:

    | Valor | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Punto de conexión del documento de alias de imagen de VM | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento que contiene los alias de imagen de máquina virtual. Para obtener más información, consulte [Configuración del punto de conexión de alias de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Establezca el entorno activo mediante los comandos siguientes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack Hub. Para actualizar la configuración, ejecute el comando siguiente:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
1. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Inicie sesión en el entorno de Azure Stack Hub como un usuario o una entidad de servicio. 

   - Iniciar sesión como *usuario*: 

     puede especificar el nombre de usuario y la contraseña directamente en el comando `az login` o autenticarse utilizando un explorador. Debe hacer esto último si la cuenta tiene habilitada la autenticación multifactor:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Si su cuenta de usuario tiene la autenticación multifactor habilitada, use el comando `az login` sin proporcionar el parámetro `-u`. Al ejecutar este comando, se le proporciona una URL y un código que debe usar para la autenticación.

   - Inicie sesión como una *entidad de servicio*: 
    
     Antes de iniciar sesión, [cree una entidad de servicio en Azure Portal](../../operator/azure-stack-create-service-principals.md) o mediante la CLI y asígnele un rol. Ahora, inicie sesión con el siguiente comando:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image-1.png)

## <a name="windowslinux-ad-fs"></a>Windows/Linux (AD FS)

Esta sección le guiará a través de la configuración de la CLI si usa los Servicios de federación de Active Directory (AD FS) como servicio de administración de identidades y utiliza la CLI en una máquina Windows o Linux.


### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

1. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

2. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`:

    | Valor | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Para un sistema integrado, le recomendamos que utilice un punto de conexión del sistema.  |
    | Punto de conexión del documento de alias de imagen de VM | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento que contiene los alias de imagen de máquina virtual. Para obtener más información, consulte [Configuración del punto de conexión de alias de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Establezca el entorno activo mediante los comandos siguientes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack Hub. Para actualizar la configuración, ejecute el comando siguiente:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

1. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Puede iniciar sesión en el entorno de Azure Stack Hub como un usuario o una entidad de servicio. 

   - Iniciar sesión como *usuario*:

     puede especificar el nombre de usuario y la contraseña directamente en el comando `az login` o autenticarse utilizando un explorador. Debe hacer esto último si la cuenta tiene habilitada la autenticación multifactor:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Si su cuenta de usuario tiene la autenticación multifactor habilitada, use el comando `az login` sin proporcionar el parámetro `-u`. Al ejecutar este comando, se le proporciona una URL y un código que debe usar para la autenticación.

   - Inicie sesión como una *entidad de servicio*: 
    
     Prepare el archivo .pem que se va a usar para el inicio de sesión de la entidad de servicio.

     En el equipo cliente donde se ha creado la entidad de seguridad, exporte el certificado de la entidad de servicio como un archivo pfx con la clave privada ubicado en `cert:\CurrentUser\My`; el nombre del certificado es igual que el de la entidad de seguridad.

     Convierta el archivo pfx a pem (use la utilidad OpenSSL).

     Inicie sesión en la CLI:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image-1.png)


## <a name="known-issues"></a>Problemas conocidos

Existen problemas conocidos cuando se usa la CLI en Azure Stack Hub:

 - El modo interactivo de la CLI. Por ejemplo, el comando `az interactive` no se admite todavía en Azure Stack Hub.
 - Para obtener la lista de imágenes de máquinas virtuales disponibles en Azure Stack Hub, use el comando `az vm image list --all` en lugar del comando `az vm image list`. Al especificar la opción `--all`, se asegura de que la respuesta devuelve únicamente las imágenes que estén disponibles en el entorno de Azure Stack Hub.
 - Los alias de las imágenes de máquinas virtuales que están disponibles en Azure pueden no ser aplicables a Azure Stack Hub. Al utilizar imágenes de máquinas virtuales, debe utilizar todo el parámetro URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) en lugar del alias de la imagen. Este URN debe coincidir con las especificaciones de la imagen como se derivan del comando `az vm images list`.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de plantillas con la CLI de Azure](../../user/azure-stack-deploy-template-command-line.md)
- [Habilitación de la CLI de Azure para usuarios de Azure Stack Hub (operador)](../../operator/azure-stack-cli-admin.md)
- [Administración de permisos de usuario](../../user/azure-stack-manage-permissions.md)