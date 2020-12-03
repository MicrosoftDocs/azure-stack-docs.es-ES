---
title: Configuración de certificados para la CLI de Azure en el Kit de desarrollo de Azure Stack (ASDK) | Microsoft Docs
description: Obtenga información acerca de cómo configurar los certificados para la CLI de Azure en el Kit de desarrollo de Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 7b408e28ff70e66c0460473f54304cbf50fa2c92
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524692"
---
# <a name="setting-up-certificates-for-azure-cli-on-azure-stack-development-kit"></a>Configuración de certificados para la CLI de Azure en el Kit de desarrollo de Azure Stack

Siga los pasos de este artículo para configurar la Interfaz de la línea de comandos (CLI) de Azure a fin de administrar los recursos del Kit de desarrollo de Azure Stack (ASKD) de las plataformas de cliente de Linux, Mac y Windows.

En este artículo se explica cómo obtener los certificados y confiar en ellos en el equipo de administración remoto. Para instalar la CLI de Azure y conectarse a su entorno, consulte [Instalación de la CLI de Azure en Azure Stack Hub](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="prepare-for-azure-cli"></a>Preparativos para la CLI de Azure

Para el ASDK, necesitará el certificado raíz de CA de Azure Stack Hub para usar la CLI de Azure en la máquina de desarrollo. El certificado se usa para administrar recursos a través de la CLI.

 - **El certificado raíz de la entidad de certificación de Azure Stack Hub** es necesario si usa la CLI desde una estación de trabajo fuera del ASDK.  

 - **El punto de conexión de alias de máquina virtual** proporciona un alias, como "UbuntuLTS" o "Win2012Datacenter". Este alias hace referencia a un editor de imagen, una oferta, una SKU y una versión como un solo parámetro al implementar máquinas virtuales.  

En las secciones siguientes se explica cómo obtener estos valores.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Exportación del certificado de raíz de la entidad de certificación de Azure Stack Hub

Para usar la CLI de Azure con el ASDK, exporte el certificado raíz de CA.

Para exportar el certificado raíz del ASDK en formato PEM:

1. Obtenga el nombre del certificado raíz de Azure Stack Hub:
    1. Inicie sesión en el portal de usuarios o del administrador de Azure Stack Hub.
    2. Seleccione **Seguro** cerca de la barra de direcciones.
    3. En la ventana emergente, seleccione **Válido**.
    4. En la ventana Certificado, seleccione la pestaña **Ruta de certificación**.
    5. Anote el nombre del certificado raíz de Azure Stack Hub.

    ![Certificado raíz de Azure Stack Hub](../user/media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Cree una máquina virtual Windows en Azure Stack Hub](../user/azure-stack-quick-windows-portal.md).

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

## <a name="set-up-the-virtual-machine-alias-endpoint"></a>Configuración del punto de conexión de alias de máquina virtual

Puede configurar un punto de conexión accesible públicamente que hospede un archivo de alias de máquina virtual. El archivo de alias de máquina virtual es un archivo JSON que proporciona un nombre común para una imagen. Usará este nombre al implementar una máquina virtual como parámetro de la CLI de Azure.

1. Si publica una imagen personalizada, anote la información del publicador, la oferta, la SKU y la versión que especificó en la publicación. Si es una imagen de Marketplace, la información se puede ver mediante el cmdlet ```Get-AzureVMImage```.  

2. Descargue el [archivo de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) de GitHub.

3. Cree una cuenta de almacenamiento en Azure Stack Hub. Cuando haya terminado, cree un contenedor de blobs. Establezca la directiva de acceso en "público".  

4. Cargue el archivo JSON en el nuevo contenedor. Cuando esté listo, puede ver la dirección URL del blob. Seleccione el nombre del blob y, a continuación, seleccione la dirección URL desde las propiedades del blob.


## <a name="trust-the-certificate"></a>Confiar en el certificado

Para usar la CLI de Azure con ASDK, debe confiar en el certificado raíz de CA en la máquina remota.

### <a name="windows"></a>[Windows](#tab/win)

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

Para obtener instrucciones sobre la instalación y conexión con la CLI de Azure, consulte [Administración e implementación de recursos en Azure Stack Hub con la CLI de Azure](/azure-stack/user/azure-stack-version-profiles-azurecli2).

### <a name="linux"></a>[Linux](#tab/lin)

Al configurar la CLI, confíe en el certificado raíz de CA de Azure Stack Hub; para ello, anéxelo al certificado de Python existente.

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

Para obtener instrucciones sobre la instalación y conexión con la CLI de Azure, consulte [Administración e implementación de recursos en Azure Stack Hub con la CLI de Azure](/azure-stack/user/azure-stack-version-profiles-azurecli2).

---

## <a name="next-steps"></a>Pasos siguientes

[Administración e implementación de recursos en Azure Stack Hub con la CLI de Azure](../user/azure-stack-version-profiles-azurecli2.md)