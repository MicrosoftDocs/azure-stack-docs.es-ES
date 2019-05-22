---
title: Uso de perfiles de la versión de la API con Python en Azure Stack | Microsoft Docs
description: Obtenga información sobre el empleo de perfiles de la versión de la API con Python en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
<!-- dev: viananth -->
ms.openlocfilehash: ee5906e825af59e3b67792e8370cc5d26901ff2b
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782593"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Usar perfiles de la versión de la API con Python en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="python-and-api-version-profiles"></a>Python y Perfiles de la versión de la API

El SDK de Python es compatible con los perfiles de la versión de la API para su uso con diferentes plataformas en la nube, como Azure Stack y Azure global. Puede utilizar los perfiles de la API para crear soluciones para una nube híbrida. El SDK de Python es compatible con los siguientes perfiles de la API:

- **más reciente**  
    Este perfil tiene como destino las versiones de API más recientes para todos los proveedores de servicios en la plataforma de Azure.
- **2018-03-01-hybrid**     
    Este perfil tiene como destino las versiones de API más recientes para todos los proveedores de recursos en la plataforma de Azure Stack.
- **2017-03-09-profile**  
    Este perfil tiene como destino las versiones de API más compatibles de los proveedores de recursos admitidos por Azure Stack.

   Para más información sobre los perfiles de API y Azure Stack, consulte [Administración de perfiles de versión de API en Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Instalación del SDK de Azure para Python

1. Instale Git desde [el sitio oficial](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Para instrucciones sobre la instalación del SDK de Python, consulte [Azure para desarrolladores de Python](/python/azure/python-sdk-azure-install?view=azure-python).
3. Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Para instrucciones sobre la creación de una suscripción, consulte [Creación de suscripciones para ofertas en Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Cree una entidad de servicio y guarde su identificador y su secreto. Para obtener instrucciones para crear una entidad de servicio para Azure Stack, consulte [Proporcionar a las aplicaciones acceso a Azure Stack](../operator/azure-stack-create-service-principals.md).
5. Asegúrese de que la entidad de servicio tenga rol de colaborador o propietario en la suscripción. Para obtener instrucciones sobre cómo asignar roles a la entidad de servicio, consulte [Proporcionar a las aplicaciones acceso a Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Requisitos previos

Para usar el SDK de Azure para Python con Azure Stack, debe proporcionar los siguientes valores y, luego, establecer valores con variables de entorno. Consulte las instrucciones bajo la tabla de su sistema operativo sobre cómo configurar las variables de entorno.

| Valor | Variables de entorno | DESCRIPCIÓN |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Id. de inquilino | AZURE_TENANT_ID | El valor de su [identificador de inquilino](../operator/azure-stack-identity-overview.md) de Azure Stack. |
| Id. de cliente | AZURE_CLIENT_ID | El identificador de aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este artículo. |
| Id. de suscripción | AZURE_SUBSCRIPTION_ID | El [identificador de suscripción](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) es su forma de tener acceso a las ofertas de Azure Stack. |
| Secreto del cliente | AZURE_CLIENT_SECRET | El secreto de aplicación de la entidad de servicio que guardó al crear esta última. |
| Punto de conexión de Resource Manager | ARM_ENDPOINT | Consulte el [punto de conexión de Resource Manager de Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Ubicación del recurso | AZURE_RESOURCE_LOCATION | Ubicación de los recursos en el entorno de Azure Stack.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar en el certificado de raíz de CA de Azure Stack

Si usa el ASDK, deberá confiar en el certificado raíz de CA en la máquina remota. No será necesario que lo haga con los sistemas integrados.

#### <a name="windows"></a> Windows

1. Busque la ubicación del almacén de certificados de Python en la máquina. La ubicación puede variar dependiendo de dónde haya instalado Python. Abra un símbolo del sistema cmd o un símbolo del sistema de PowerShell con privilegios elevados y escriba el siguiente comando:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Tome nota de la ubicación del almacén de certificados. Por ejemplo, *~/lib/python3.5/site-packages/certifi/cacert.pem*. La ruta de acceso particular dependerá del sistema operativo y de la versión de Python que esté instalada.

2. Para confiar en el certificado raíz de CA de Azure Stack, anéxelo al certificado existente de Python.

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
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"

    ```

> [!NOTE]  
> Si usa virtualenv para el desarrollo con el SDK de Python como se menciona a continuación, también deberá agregar el certificado anterior al almacén de certificados de su entorno virtual. La ruta de acceso podría ser similar a: "..\mytestenv\Lib\site-packages\certifi\cacert.pem".



## <a name="python-samples-for-azure-stack"></a>Ejemplos de Python para Azure Stack

Algunos de los ejemplos de código disponibles para Azure Stack que usan el SDK de Python son:

- [Administración de recursos y grupos de recursos](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/).
- [Administración de una cuenta de almacenamiento](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/).
- [Administración de máquinas virtuales](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/).

## <a name="python-manage-virtual-machine-sample"></a>Ejemplo de administración de una máquina virtual con Python

Puede usar los siguientes ejemplos de código para realizar tareas de administración comunes en máquinas virtuales en Azure Stack. El código de ejemplo muestra cómo:

- Creación de máquinas virtuales:
  - Creación de una máquina virtual con Linux
  - Creación de una máquina virtual Windows
- Actualización de una máquina virtual:
  - Expansión de una unidad
  - Etiquetado de una máquina virtual
  - Conexión de discos de datos
  - Desacoplamiento de discos de datos
- Manejo de una máquina virtual:
  - Inicio de una máquina virtual
  - Detención de una máquina virtual
  - Reinicio de una máquina virtual
- Enumeración de máquinas virtuales
- Eliminación de una máquina virtual

Para revisar el código que realiza estas operaciones, consulte la función **run_example()** del script de Python **example.py** en el repositorio de GitHub [Hybrid-Compute-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Cada operación está claramente etiquetada con un comentario y una función de impresión. Los ejemplos no aparecen necesariamente en el mismo orden de esta lista.

## <a name="run-the-python-sample"></a>Ejecutar el ejemplo de Python

1. Si aún no lo tiene, [instale Python](https://www.python.org/downloads/). Este ejemplo (y el SDK) son compatibles con Python 2.7, 3.4, 3.5 y 3.6.

2. La recomendación general para el desarrollo de Python es usar un entorno virtual. Para más información, consulte la [documentación de Python](https://docs.python.org/3/tutorial/venv.html).

3. Instale e inicie el entorno virtual con el módulo "venv" en Python 3 (debe instalar [virtualenv](https://pypi.python.org/pypi/virtualenv) para Python 2.7):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Clone el repositorio:

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Instale las dependencias con pip:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Cree una [entidad de servicio](../operator/azure-stack-create-service-principals.md) para trabajar con Azure Stack. Asegúrese de que la entidad de servicio tenga [rol de colaborador o propietario](../operator/azure-stack-create-service-principals.md#assign-a-role) en la suscripción.

7. Establezca las siguientes variables y exporte estas variables de entorno al shell actual:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Para ejecutar este ejemplo, las imágenes Ubuntu 16.04-LTS y WindowsServer 2012 R2-DataCenter deben estar presentes en Marketplace de Azure Stack. Estas imágenes se pueden [descargar de Azure](../operator/azure-stack-download-azure-marketplace-item.md) o [agregarse a Platform Image Repository](../operator/azure-stack-add-vm-image.md).

9. Ejecución del ejemplo:

    ```python
    python example.py
    ```


## <a name="next-steps"></a>Pasos siguientes

- [Centro para desarrolladores de Python de Azure](https://azure.microsoft.com/develop/python/)
- [Documentación de Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
- [Ruta de aprendizaje de Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Si no tiene una suscripción a Microsoft Azure, puede obtener una cuenta de evaluación GRATUITA [aquí](https://go.microsoft.com/fwlink/?LinkId=330212).
