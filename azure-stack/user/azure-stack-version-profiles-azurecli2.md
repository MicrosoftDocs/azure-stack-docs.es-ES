---
title: Administración de Azure Stack Hub con la CLI de Azure
description: Obtenga información sobre cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para administrar e implementar recursos en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 1fb17516e5ef0b4e3a670703a34e1e895c847b52
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2020
ms.locfileid: "88818851"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>Administración e implementación de recursos en Azure Stack Hub con la CLI de Azure

Siga los pasos de este artículo para configurar la Interfaz de la línea de comandos (CLI) de Azure a fin de administrar los recursos del Kit de desarrollo de Azure Stack (ASKD) de las plataformas de cliente de Linux, Mac y Windows.

## <a name="prepare-for-azure-cli"></a>Preparativos para la CLI de Azure

Si está usando el ASDK, necesitará el certificado raíz de CA de Azure Stack Hub para usar la CLI de Azure en la máquina de desarrollo. El certificado se usa para administrar recursos a través de la CLI.

 - **El certificado raíz de la entidad de certificación de Azure Stack Hub** es necesario si usa la CLI desde una estación de trabajo fuera del ASDK.  

 - **El punto de conexión de alias de máquina virtual** proporciona un alias, como "UbuntuLTS" o "Win2012Datacenter". Este alias hace referencia a un editor de imagen, una oferta, una SKU y una versión como un solo parámetro al implementar máquinas virtuales.  

En las secciones siguientes se explica cómo obtener estos valores.

### <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Exportación del certificado de raíz de la entidad de certificación de Azure Stack Hub

Si usa un sistema integrado, no es necesario exportar el certificado raíz de CA. Si está usando el ASDK, exporte el certificado raíz de CA en un ASDK.

Para exportar el certificado raíz del ASDK en formato PEM:

1. Obtenga el nombre del certificado raíz de Azure Stack Hub:
    - Inicie sesión en el portal de usuarios o del administrador de Azure Stack Hub.
    - Haga clic en **Seguro** cerca de la barra de direcciones.
    - En la ventana emergente, haga clic en **Válido**.
    - En la ventana Certificado, haga clic en la pestaña **Ruta de certificación**.
    - Anote el nombre del certificado raíz de Azure Stack Hub.

    ![Certificado raíz de Azure Stack Hub](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Cree una máquina virtual Windows en Azure Stack Hub](azure-stack-quick-windows-portal.md).

3. Inicie sesión en la máquina virtual, abra un símbolo del sistema de PowerShell con privilegios elevados y, luego, ejecute el siguiente script:

    ```powershell  
      $label = "<the name of your Azure Stack Hub root cert from Step 1>"
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

4. Copie el certificado en la máquina local.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configuración del punto de conexión de alias de máquina virtual

Puede configurar un punto de conexión accesible públicamente que hospede un archivo de alias de máquina virtual. El archivo de alias de máquina virtual es un archivo JSON que proporciona un nombre común para una imagen. Usará este nombre al implementar una máquina virtual como parámetro de la CLI de Azure.

1. Si publica una imagen personalizada, anote la información del publicador, la oferta, la SKU y la versión que especificó en la publicación. Si es una imagen de Marketplace, la información se puede ver mediante el cmdlet ```Get-AzureVMImage```.  

2. Descargue el [archivo de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) de GitHub.

3. Cree una cuenta de almacenamiento en Azure Stack Hub. Cuando haya terminado, cree un contenedor de blobs. Establezca la directiva de acceso en "público".  

4. Cargue el archivo JSON en el nuevo contenedor. Cuando esté listo, puede ver la dirección URL del blob. Seleccione el nombre del blob y, a continuación, seleccione la dirección URL desde las propiedades del blob.

### <a name="install-or-upgrade-cli"></a>Instalación o actualización de la CLI

Inicie sesión en la estación de trabajo de desarrollo e instale la CLI. Azure Stack Hub requiere la versión 2.0 o superior de la CLI de Azure. La versión más reciente de los perfiles de API requiere una versión actual de la CLI. Instale la CLI mediante los pasos descritos en el artículo [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). 

1. Para comprobar si la instalación se realizó correctamente, abra un terminal o una ventana del símbolo del sistema y ejecute el siguiente comando:

    ```shell
    az --version
    ```

    Debería ver la versión de la CLI de Azure y otras bibliotecas dependientes instaladas en su equipo.

    ![CLI de Azure en la ubicación de Python de Azure Stack Hub](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Tome nota de la ubicación de Python de la CLI. Si está ejecutando el ASDK, necesitará usar esta ubicación para agregar el certificado.


## <a name="windows-azure-ad"></a>Windows (Azure AD)

Esta sección le guiará a través de la configuración de la CLI si usa Azure AD como servicio de administración de identidades y utiliza la CLI en una máquina Windows.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Confianza en el certificado raíz de la entidad de certificación de Azure Stack Hub

Si usa el ASDK, deberá confiar en el certificado raíz de CA en la máquina remota. Este paso no es necesario en los sistemas integrados.

Para confiar en el certificado raíz de la entidad de certificación de Azure Stack Hub, debe anexarlo al almacén de certificados existente de Python correspondiente a la versión de Python instalada con la CLI de Azure. Puede que esté ejecutando su propia instancia de Python. La CLI de Azure incluye su propia versión de Python.

1. Busque la ubicación del almacén de certificados en la máquina.  Para encontrar la ubicación, puede ejecutar el comando `az --version`.

2. Navegue hasta la carpeta que contiene la aplicación Python de la CLI. Puede ejecutar esta versión de Python. Si ha configurado Python en la ruta de acceso del sistema, al iniciar Python, se ejecutará su propia versión. En su lugar, puede ejecutar la versión que usa la CLI y agregar su certificado a esta versión. Por ejemplo, su instancia de Python de la CLI puede estar en: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`.

    Use los comandos siguientes:

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Tome nota de la ubicación del certificado. Por ejemplo, `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. La ruta de acceso específica dependerá de su sistema operativo y de la instalación de la CLI.

2. Para confiar en el certificado raíz de la entidad de certificación de Azure Stack Hub, anéxelo al certificado existente de Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

1. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

2. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`:

    | Value | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | El valor de **ResourceManagerUrl** del ASDK es el siguiente: `https://management.local.azurestack.external/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | `local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | `.vault.local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
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

    >[!NOTE]  
    >Si ejecuta una versión de Azure Stack Hub anterior a la compilación 1808, debe usar el perfil de la versión de API **2017-03-09-profile** en lugar de **2019-03-01-hybrid**. Deberá usar una versión reciente de la CLI de Azure.
 
1. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Inicie sesión en el entorno de Azure Stack Hub como un usuario o una [entidad de servicio](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Iniciar sesión como *usuario*: 

     puede especificar el nombre de usuario y la contraseña directamente en el comando `az login` o autenticarse utilizando un explorador. Debe hacer esto último si la cuenta tiene habilitada la autenticación multifactor:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Si su cuenta de usuario tiene la autenticación multifactor habilitada, use el comando `az login` sin proporcionar el parámetro `-u`. Al ejecutar este comando, se le proporciona una URL y un código que debe usar para la autenticación.

   - Inicie sesión como una *entidad de servicio*: 
    
     Antes de iniciar sesión, [cree una entidad de servicio en Azure Portal](../operator/azure-stack-create-service-principals.md?view=azs-2002) o mediante la CLI y asígnele un rol. Ahora, inicie sesión con el siguiente comando:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Esta sección le guiará a través de la configuración de la CLI si usa los Servicios de federación de Active Directory (AD FS) como servicio de administración de identidades y utiliza la CLI en una máquina Windows.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Confianza en el certificado raíz de la entidad de certificación de Azure Stack Hub

Si usa el ASDK, deberá confiar en el certificado raíz de CA en la máquina remota. Este paso no es necesario en los sistemas integrados.

1. Busque la ubicación del certificado en la máquina. La ubicación puede variar dependiendo de dónde haya instalado Python. Abra un símbolo del sistema cmd o un símbolo del sistema de PowerShell con privilegios elevados y escriba el siguiente comando:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Tome nota de la ubicación del certificado. Por ejemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. La ruta de acceso particular dependerá del sistema operativo y de la versión de Python que esté instalada.

2. Para confiar en el certificado raíz de la entidad de certificación de Azure Stack Hub, anéxelo al certificado existente de Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

1. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

2. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`:

    | Value | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | El valor de **ResourceManagerUrl** del ASDK es el siguiente: `https://management.local.azurestack.external/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | `local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | `.vault.local.azurestack.external` es para el ASDK. Para un sistema integrado, le recomendamos que utilice un punto de conexión del sistema.  |
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

    >[!NOTE]  
    >Si ejecuta una versión de Azure Stack Hub anterior a la compilación 1808, debe usar el perfil de la versión de API **2017-03-09-profile** en lugar de **2019-03-01-hybrid**. Deberá usar una versión reciente de la CLI de Azure.

1. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Puede iniciar sesión en el entorno de Azure Stack Hub como un usuario o una [entidad de servicio](/azure/active-directory/develop/app-objects-and-service-principals). 

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

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Esta sección le guiará a través de la configuración de la CLI si usa Azure AD como servicio de administración de identidades y utiliza la CLI en una máquina Linux.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Confianza en el certificado raíz de la entidad de certificación de Azure Stack Hub

Si usa el ASDK, deberá confiar en el certificado raíz de CA en la máquina remota. Este paso no es necesario en los sistemas integrados.

Para confiar en el certificado raíz de la entidad de certificación de Azure Stack Hub, anéxelo al certificado existente de Python.

1. Busque la ubicación del certificado en la máquina. La ubicación puede variar dependiendo de dónde haya instalado Python. Debe tener PIP y el módulo certifi instalado. Use el siguiente comando de Python desde el símbolo del sistema de bash:

    ```bash  
    az --version
    ```

    Tome nota de la ubicación del certificado. Por ejemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. La ruta de acceso específica depende del sistema operativo y la versión de Python que haya instalado.

2. Ejecute el siguiente comando de bash con la ruta de acceso al certificado.

   - Para una máquina Linux remota:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Para una máquina Linux en el entorno de Azure Stack Hub:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

Use los pasos siguientes para conectarse a Azure Stack Hub:

1. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

2. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`:

    | Value | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | El valor de **ResourceManagerUrl** del ASDK es el siguiente: `https://management.local.azurestack.external/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | `local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | `.vault.local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Punto de conexión del documento de alias de imagen de VM | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento que contiene los alias de imagen de máquina virtual. Para obtener más información, consulte [Configuración del punto de conexión de alias de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Establezca el entorno activo. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack Hub. Para actualizar la configuración, ejecute el comando siguiente:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si ejecuta una versión de Azure Stack Hub anterior a la compilación 1808, debe usar el perfil de la versión de API **2017-03-09-profile** en lugar de **2019-03-01-hybrid**. Deberá usar una versión reciente de la CLI de Azure.

5. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Puede iniciar sesión en el entorno de Azure Stack Hub como un usuario o una [entidad de servicio](/azure/active-directory/develop/app-objects-and-service-principals). 

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
    
     Antes de iniciar sesión, [cree una entidad de servicio en Azure Portal](../operator/azure-stack-create-service-principals.md?view=azs-2002) o mediante la CLI y asígnele un rol. Ahora, inicie sesión con el siguiente comando:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
    az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Esta sección le guiará a través de la configuración de la CLI si usa los Servicios de federación de Active Directory (AD FS) como servicio de administración y utiliza la CLI en una máquina Linux.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Confianza en el certificado raíz de la entidad de certificación de Azure Stack Hub

Si usa el ASDK, deberá confiar en el certificado raíz de CA en la máquina remota. Este paso no es necesario en los sistemas integrados.

Para confiar en el certificado raíz de la entidad de certificación de Azure Stack Hub, anéxelo al certificado existente de Python.

1. Busque la ubicación del certificado en la máquina. La ubicación puede variar dependiendo de dónde haya instalado Python. Debe tener PIP y el módulo certifi instalado. Use el siguiente comando de Python desde el símbolo del sistema de bash:

    ```bash  
    az --version 
    ```

    Tome nota de la ubicación del certificado. Por ejemplo, `~/lib/python3.5/site-packages/certifi/cacert.pem`. La ruta de acceso específica depende del sistema operativo y la versión de Python que haya instalado.

2. Ejecute el siguiente comando de bash con la ruta de acceso al certificado.

   - Para una máquina Linux remota:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Para una máquina Linux en el entorno de Azure Stack Hub:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>Conexión a Azure Stack Hub

Use los pasos siguientes para conectarse a Azure Stack Hub:

1. Registre el entorno de Azure Stack Hub. Para ello, ejecute el comando `az cloud register`.

2. Registre su entorno. Utilice los siguientes parámetros cuando ejecute `az cloud register`.

    | Value | Ejemplo | Descripción |
    | --- | --- | --- |
    | Nombre del entorno | AzureStackUser | Use `AzureStackUser` para el entorno de usuario. Si es operador, especifique `AzureStackAdmin`. |
    | Punto de conexión de Resource Manager | `https://management.local.azurestack.external` | El valor de **ResourceManagerUrl** del ASDK es el siguiente: `https://management.local.azurestack.external/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/` Si tiene alguna pregunta sobre el punto de conexión del sistema integrado, póngase en contacto con su operador de nube. |
    | Punto de conexión de Storage | local.azurestack.external | `local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Sufijo de Key Vault | .vault.local.azurestack.external | `.vault.local.azurestack.external` es para el ASDK. Para un sistema integrado, utilice un punto de conexión del sistema.  |
    | Punto de conexión del documento de alias de imagen de VM | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI del documento que contiene los alias de imagen de máquina virtual. Para obtener más información, consulte [Configuración del punto de conexión de alias de máquina virtual](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Establezca el entorno activo. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Actualice la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack Hub. Para actualizar la configuración, ejecute el comando siguiente:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Si ejecuta una versión de Azure Stack Hub anterior a la compilación 1808, debe usar el perfil de la versión de API **2017-03-09-profile** en lugar de **2019-03-01-hybrid**. Deberá usar una versión reciente de la CLI de Azure.

5. Inicie sesión en el entorno de Azure Stack Hub. Para ello, use el comando `az login`. Puede iniciar sesión en el entorno de Azure Stack Hub como un usuario o una [entidad de servicio](/azure/active-directory/develop/app-objects-and-service-principals). 

6. Inicie sesión: 

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

### <a name="test-the-connectivity"></a>Prueba de la conectividad

Con todo configurado, vamos a usar la CLI para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```azurecli
  az group create -n MyResourceGroup -l local
```

Si el grupo de recursos se crea correctamente, el comando anterior da como resultado las siguientes propiedades del recurso recién creado:

![Resultado de creación del grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problemas conocidos

Existen problemas conocidos cuando se usa la CLI en Azure Stack Hub:

 - El modo interactivo de la CLI. Por ejemplo, el comando `az interactive` no se admite todavía en Azure Stack Hub.
 - Para obtener la lista de imágenes de máquinas virtuales disponibles en Azure Stack Hub, use el comando `az vm image list --all` en lugar del comando `az vm image list`. Al especificar la opción `--all`, se asegura de que la respuesta devuelve únicamente las imágenes que estén disponibles en el entorno de Azure Stack Hub.
 - Los alias de las imágenes de máquinas virtuales que están disponibles en Azure pueden no ser aplicables a Azure Stack Hub. Al utilizar imágenes de máquinas virtuales, debe utilizar todo el parámetro URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) en lugar del alias de la imagen. Este URN debe coincidir con las especificaciones de la imagen como se derivan del comando `az vm images list`.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)
- [Habilitación de la CLI de Azure para usuarios de Azure Stack Hub (operador)](../operator/azure-stack-cli-admin.md)
- [Administración de permisos de usuario](azure-stack-manage-permissions.md) 
