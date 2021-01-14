---
title: Administración de Azure Stack Hub con la CLI de Azure
description: Obtenga información sobre cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para administrar e implementar recursos en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/16/2020
ms.openlocfilehash: 2e3c25e282a84a0e41d6c69d0af6e2f352bbfba5
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974020"
---
# <a name="install-azure-cli-on-azure-stack-hub"></a>Instalación de la CLI de Azure en Azure Stack Hub

Puede instalar la CLI de Azure para administrar Azure Stack Hub con una máquina Windows o Linux. Este artículo le guía por los pasos necesarios para instalar y configurar la CLI de Azure.

## <a name="install-azure-cli"></a>Instalación de la CLI de Azure

1. Inicie sesión en la estación de trabajo de desarrollo e instale la CLI. Azure Stack Hub requiere la versión 2.0 o superior de la CLI de Azure. 

2. Puede instalar la CLI mediante los pasos descritos en el artículo [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). 

3. Para comprobar si la instalación se realizó correctamente, abra un terminal o una ventana del símbolo del sistema y ejecute el siguiente comando:

    ```shell
    az --version
    ```

    Debería ver la versión de la CLI de Azure y otras bibliotecas dependientes instaladas en su equipo.

    ![CLI de Azure en la ubicación de Python de Azure Stack Hub](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Tome nota de la ubicación de Python de la CLI. Si está ejecutando el ASDK, necesitará usar esta ubicación para agregar el certificado. Para obtener instrucciones sobre la configuración de certificados para la instalación de la CLI en ASDK, consulte [Configuración de certificados para la CLI de Azure en el Kit de desarrollo de Azure Stack](../asdk/asdk-cli.md).

## <a name="connect-with-azure-cli"></a>Conexión con la CLI de Azure

### <a name="azure-ad-on-windows"></a>[Azure AD en Windows](#tab/ad-win)

Esta sección le guiará a través de la configuración de la CLI si usa Azure AD como servicio de administración de identidades y utiliza la CLI en una máquina Windows.

#### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

1. Si va a utilizar el ASDK, confíe en el certificado raíz de la entidad de certificación de Azure Stack Hub. Para obtener instrucciones, consulte [Confiar en el certificado](../asdk/asdk-cli.md#trust-the-certificate).

2. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

3. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`:

      | Valor | Ejemplo | Descripción |
      | --- | --- | --- |
      | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
      | Punto de conexión de Resource Manager | `https://management.contoso.onmicrosoft.com` | El valor de **ResourceManagerUrl** del ASDK es el siguiente: `https://management.contoso.onmicrosoft.com/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
      | Punto de conexión de Storage | local.contoso.onmicrosoft.com | `local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
      | Sufijo de Key Vault | .vault.contoso.onmicrosoft.com | `.vault.local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
      | Identificador de recurso de grafo de Active Directory de punto de conexión | https://graph.windows.net/ | El identificador de recurso de Active Directory. |
    
      ```azurecli  
      az cloud register `
          -n <environmentname> `
          --endpoint-resource-manager "https://management.<region>.<fqdn>" `
          --suffix-storage-endpoint "<fqdn>" `
          --suffix-keyvault-dns ".vault.<fqdn>" `
          --endpoint-active-directory-graph-resource-id "https://graph.windows.net/"
      ```

    Puede encontrar una referencia del [ register](https://docs.microsoft.com/cli/azure/cloud?view=azure-cli-latest#az_cloud_register) en la documentación de referencia de la CLI de Azure.


4. Establezca el entorno activo mediante los comandos siguientes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack Hub. Para actualizar la configuración, ejecute el comando siguiente:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
6. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`.

    Puede iniciar sesión en el entorno de Azure Stack Hub con sus credenciales de usuario o con una [entidad de servicio](/azure/active-directory/develop/app-objects-and-service-principals) (SPN) proporcionada por el operador en la nube. 

   - Iniciar sesión como *usuario*: 

     puede especificar el nombre de usuario y la contraseña directamente en el comando `az login` o autenticarse utilizando un explorador. Debe hacer esto último si la cuenta tiene habilitada la autenticación multifactor:

     ```azurecli
     az login -u "user@contoso.onmicrosoft.com" -p 'Password123!' --tenant contoso.onmicrosoft.com
     ```

     > [!NOTE]
     > Si su cuenta de usuario tiene la autenticación multifactor habilitada, use el comando `az login` sin proporcionar el parámetro `-u`. Al ejecutar este comando, se le proporciona una URL y un código que debe usar para la autenticación.

   - Inicie sesión como una *entidad de servicio*: 
    
        Antes de iniciar sesión, [cree una entidad de servicio en Azure Portal](../operator/azure-stack-create-service-principals.md) o mediante la CLI y asígnele un rol. Ahora, inicie sesión con el siguiente comando:
    
        ```azurecli  
        az login `
          --tenant <Azure Active Directory Tenant name. `
                    For example: myazurestack.onmicrosoft.com> `
        --service-principal `
          -u <Application Id of the Service Principal> `
          -p <Key generated for the Service Principal>
        ```
    
7. Compruebe que el entorno esté configurado correctamente y que el entorno sea la nube activa.

      ```azurecli
          az cloud list --output table
      ```

Debería ver que el entorno aparece en la lista y que el valor de **IsActive** es `true`. Por ejemplo:

```azurecli  
IsActive    Name               Profile
----------  -----------------  -----------------
False       AzureCloud         2019-03-01-hybrid
False       AzureChinaCloud    latest
False       AzureUSGovernment  latest
False       AzureGermanCloud   latest
True        AzureStackUser     2019-03-01-hybrid
```

#### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-on-windows"></a>[AD FS en Windows](#tab/adfs-win)

Esta sección le guiará a través de la configuración de la CLI si usa los Servicios de federación de Active Directory (AD FS) como servicio de administración de identidades y utiliza la CLI en una máquina Windows.

#### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub


1. Si va a utilizar el ASDK, confíe en el certificado raíz de la entidad de certificación de Azure Stack Hub. Para obtener instrucciones, consulte [Confiar en el certificado](../asdk/asdk-cli.md#trust-the-certificate).

2. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

3. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`:

    | Valor | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | El valor de **ResourceManagerUrl** del ASDK es el siguiente: `https://management.local.azurestack.external/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | `local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | `.vault.local.azurestack.external` es para el ASDK. Para un sistema integrado, le recomendamos que utilice un punto de conexión del sistema.  |
    | Punto de conexión del documento de alias de imagen de VM | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento que contiene los alias de imagen de máquina virtual. Para más información, consulte [Configuración del punto de conexión de alias de máquina virtual](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Establezca el entorno activo mediante los comandos siguientes.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack Hub. Para actualizar la configuración, ejecute el comando siguiente:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si ejecuta una versión de Azure Stack Hub anterior a la compilación 1808, debe usar el perfil de la versión de API **2017-03-09-profile** en lugar de **2019-03-01-hybrid**. Deberá usar una versión reciente de la CLI de Azure.

6. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Puede iniciar sesión en el entorno de Azure Stack Hub como un usuario o una [entidad de servicio](/azure/active-directory/develop/app-objects-and-service-principals). 

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

#### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)

### <a name="azure-ad-on-linux"></a>[Azure AD en Linux](#tab/ad-lin)

Esta sección le guiará a través de la configuración de la CLI si usa Azure AD como servicio de administración de identidades y utiliza la CLI en una máquina Linux.

#### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

Use los pasos siguientes para conectarse a Azure Stack Hub:


1. Si va a utilizar el ASDK, confíe en el certificado raíz de la entidad de certificación de Azure Stack Hub. Para obtener instrucciones, consulte [Confiar en el certificado](../asdk/asdk-cli.md#trust-the-certificate).

2. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

3. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`:

    | Valor | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | El valor de **ResourceManagerUrl** del ASDK es el siguiente: `https://management.local.azurestack.external/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | `local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | `.vault.local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Punto de conexión del documento de alias de imagen de VM | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento que contiene los alias de imagen de máquina virtual. Para más información, consulte [Configuración del punto de conexión de alias de máquina virtual](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Establezca el entorno activo. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack Hub. Para actualizar la configuración, ejecute el comando siguiente:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si ejecuta una versión de Azure Stack Hub anterior a la compilación 1808, debe usar el perfil de la versión de API **2017-03-09-profile** en lugar de **2019-03-01-hybrid**. Deberá usar una versión reciente de la CLI de Azure.

6. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Puede iniciar sesión en el entorno de Azure Stack Hub como un usuario o una [entidad de servicio](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Iniciar sesión como *usuario*:

     puede especificar el nombre de usuario y la contraseña directamente en el comando `az login` o autenticarse utilizando un explorador. Debe hacer esto último si la cuenta tiene habilitada la autenticación multifactor:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Si su cuenta de usuario tiene la autenticación multifactor habilitada, puede usar el comando `az login` sin proporcionar el parámetro `-u`. Al ejecutar este comando, se le proporciona una URL y un código que debe usar para la autenticación.
   
   * Iniciar sesión como una *entidad de servicio*
    
     Antes de iniciar sesión, [cree una entidad de servicio en Azure Portal](../operator/azure-stack-create-service-principals.md) o mediante la CLI y asígnele un rol. Ahora, inicie sesión con el siguiente comando:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
    az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-linux"></a>[AD FS Linux](#tab/adfs-lin)

Esta sección le guiará a través de la configuración de la CLI si usa los Servicios de federación de Active Directory (AD FS) como servicio de administración y utiliza la CLI en una máquina Linux.

#### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

Use los pasos siguientes para conectarse a Azure Stack Hub:

1. Si va a utilizar el ASDK, confíe en el certificado raíz de la entidad de certificación de Azure Stack Hub. Para obtener instrucciones, consulte [Confiar en el certificado](../asdk/asdk-cli.md#trust-the-certificate).

2. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

3. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`.

    | Valor | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | El valor de **ResourceManagerUrl** del ASDK es el siguiente: `https://management.local.azurestack.external/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | `local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | `.vault.local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Punto de conexión del documento de alias de imagen de VM | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento que contiene los alias de imagen de máquina virtual. Para más información, consulte [Configuración del punto de conexión de alias de máquina virtual](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Establezca el entorno activo. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack Hub. Para actualizar la configuración, ejecute el comando siguiente:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si ejecuta una versión de Azure Stack Hub anterior a la compilación 1808, debe usar el perfil de la versión de API **2017-03-09-profile** en lugar de **2019-03-01-hybrid**. Deberá usar una versión reciente de la CLI de Azure.

6. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Puede iniciar sesión en el entorno de Azure Stack Hub como un usuario o una [entidad de servicio](/azure/active-directory/develop/app-objects-and-service-principals). 

7. Inicie sesión: 

   *  Como **usuario** mediante un explorador web con un código de dispositivo:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Al ejecutar el comando, se le proporciona una URL y un código que debe usar para la autenticación.

   * Como entidad de servicio:
        
     Prepare el archivo .pem que se va a usar para el inicio de sesión de la entidad de servicio.

      * En el equipo cliente donde se ha creado la entidad de seguridad, exporte el certificado de la entidad de servicio como un archivo pfx con la clave privada ubicado en `cert:\CurrentUser\My`; el nombre del certificado es igual que el de la entidad de seguridad.
  
      * Convierta el archivo pfx a pem (use la utilidad OpenSSL).

     Inicie sesión en la CLI:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

#### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
  az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)

### <a name="known-issues"></a>Problemas conocidos

Existen problemas conocidos cuando se usa la CLI en Azure Stack Hub:

 - El modo interactivo de la CLI. Por ejemplo, el comando `az interactive` no se admite todavía en Azure Stack Hub.
 - Para obtener la lista de imágenes de máquinas virtuales disponibles en Azure Stack Hub, use el comando `az vm image list --all` en lugar del comando `az vm image list`. Al especificar la opción `--all`, se asegura de que la respuesta devuelve únicamente las imágenes que estén disponibles en el entorno de Azure Stack Hub.
 - Los alias de las imágenes de máquinas virtuales que están disponibles en Azure pueden no ser aplicables a Azure Stack Hub. Al utilizar imágenes de máquinas virtuales, debe utilizar todo el parámetro URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) en lugar del alias de la imagen. Este URN debe coincidir con las especificaciones de la imagen como se derivan del comando `az vm images list`.

---

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)
- [Habilitación de la CLI de Azure para usuarios de Azure Stack Hub (operador)](../operator/azure-stack-cli-admin.md)
- [Administración de permisos de usuario](azure-stack-manage-permissions.md) 
