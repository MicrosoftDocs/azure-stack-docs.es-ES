---
title: Uso de perfiles de la versión de la API con Python en Azure Stack Hub
description: Aprenda a usar los perfiles de la versión de la API con Python en Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 48df6dab5d93e4e9875dc422f90f3f823a4b2ee9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884231"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack-hub"></a>Uso de perfiles de la versión de la API con Python en Azure Stack Hub

El SDK de Python admite los perfiles de la versión de la API para su uso con diferentes plataformas en la nube, como Azure Stack Hub y Azure global. Utilice los perfiles de la API para crear soluciones para una nube híbrida.

Las instrucciones de este artículo requieren una suscripción de Microsoft Azure. Si no tiene una, puede obtener una [cuenta de evaluación gratuita](https://go.microsoft.com/fwlink/?LinkId=330212).

## <a name="python-and-api-version-profiles"></a>Python y Perfiles de la versión de la API

El SDK de Python es compatible con los siguientes perfiles de la API:

- **más reciente**  
    Este perfil tiene como destino las versiones de API más recientes de todos los proveedores de servicios de la plataforma Azure.
- **2019-03-01-hybrid**  
    Este perfil tiene como destino las versiones de API más recientes de todos los proveedores de recursos de la plataforma de Azure Stack Hub para la versión 1904 u otras posteriores.
- **2018-03-01-hybrid**  
    Este perfil tiene como destino las versiones de API más compatibles de todos los proveedores de recursos de la plataforma de Azure Stack Hub.
- **2017-03-09-profile**  
    Este perfil tiene como destino las versiones de API más compatibles de los proveedores de recursos admitidos por Azure Stack Hub.

   Para más información sobre los perfiles de la API y Azure Stack Hub, consulte [Administración de perfiles de versión de API en Azure Stack Hub](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Instalación del SDK de Azure para Python

1. Instale Git desde [el sitio oficial](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Para obtener instrucciones sobre la instalación del SDK de Python, consulte [Azure para desarrolladores de Python](/python/azure/python-sdk-azure-install?view=azure-python).
3. Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Para instrucciones sobre la creación de una suscripción, consulte [Creación de suscripciones para ofertas en Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Cree una entidad de servicio y guarde su identificador y su secreto. Para obtener instrucciones sobre la creación de una entidad de servicio para Azure Stack Hub, consulte [Proporcionar a las aplicaciones acceso a Azure Stack Hub](../operator/azure-stack-create-service-principals.md).
5. Asegúrese de que la entidad de servicio tenga rol de colaborador o propietario en la suscripción. Para obtener instrucciones sobre cómo asignar un rol a la entidad de servicio, consulte [Proporcionar a las aplicaciones acceso a Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prerequisites

Para usar el SDK de Azure para Python con Azure Stack Hub, debe proporcionar los siguientes valores y, luego, establecer valores con variables de entorno. Para establecer las variables de entorno, consulte las instrucciones bajo la tabla siguiente relativas a su sistema operativo específico.

| Value | Variables de entorno | Descripción |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Id. de inquilino | `AZURE_TENANT_ID` | [Identificador de inquilino](../operator/azure-stack-identity-overview.md)de su instancia de Azure Stack Hub. |
| Id. de cliente | `AZURE_CLIENT_ID` | Id. de la aplicación de la entidad de servicio que guardó al crear dicha entidad de servicio en la sección anterior de este artículo. |
| Id. de suscripción | `AZURE_SUBSCRIPTION_ID` | Use el [identificador de suscripción](../operator/service-plan-offer-subscription-overview.md#subscriptions) para acceder a las ofertas de Azure Stack Hub. |
| Secreto del cliente | `AZURE_CLIENT_SECRET` | Secreto de aplicación de la entidad de servicio que guardó al crear dicha entidad de servicio. |
| Punto de conexión de Resource Manager | `ARM_ENDPOINT` | Consulte el artículo [Punto de conexión de Resource Manager de Azure Stack Hub](azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint). |
| Ubicación de los recursos | `AZURE_RESOURCE_LOCATION` | Ubicación de los recursos en el entorno de Azure Stack Hub.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Confianza en el certificado raíz de la entidad de certificación de Azure Stack Hub

Si usa el ASDK, debe confiar explícitamente en el certificado raíz de la entidad de certificación en la máquina remota. No es necesario que confíe en el certificado raíz de la entidad de certificación con los sistemas integrados de Azure Stack Hub.

#### <a name="windows"></a>Windows

1. Busque la ubicación del almacén de certificados de Python en la máquina. La ubicación puede variar dependiendo de dónde haya instalado Python. Abra un símbolo del sistema o un símbolo del sistema de PowerShell con privilegios elevados y escriba el siguiente comando:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Anote la ubicación del almacén de certificados; por ejemplo, **~/lib/python3.5/site-packages/certifi/cacert.pem**. La ruta de acceso específica depende del sistema operativo y la versión de Python que haya instalado.

2. Para confiar en el certificado raíz de CA de Azure Stack Hub, anéxelo al certificado existente de Python:

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate; for ex: C:\Users\user1\Downloads\root.pem>"

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

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

> [!NOTE]  
> Si usa **virtualenv** para desarrollar con el SDK de Python como se menciona en la sección siguiente titulada [Ejecutar el ejemplo de Python](#run-the-python-sample), debe agregar el certificado anterior al almacén de certificados del entorno virtual. La ruta de acceso podría ser similar a la siguiente: `..\mytestenv\Lib\site-packages\certifi\cacert.pem`.

## <a name="python-samples-for-azure-stack-hub"></a>Ejemplos de Python para Azure Stack Hub

Algunos de los ejemplos de código disponibles para Azure Stack Hub que usan el SDK de Python son:

- [Administración de recursos y grupos de recursos](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [Administración de una cuenta de almacenamiento](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [Administración de máquinas virtuales](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/): Este ejemplo usa el perfil **2019-03-01-hybrid** que tiene como destino las versiones más recientes de API compatibles con Azure Stack Hub.

## <a name="manage-virtual-machine-sample"></a>Administración de una máquina virtual

Use el siguiente ejemplo de código de Python para realizar tareas de administración comunes en máquinas virtuales de Azure Stack Hub. El código de ejemplo muestra lo siguiente:

- Creación de máquinas virtuales:
  - Creación de una máquina virtual Linux
  - Creación de una máquina virtual Windows
- Actualización de una máquina virtual:
  - Expansión de una unidad
  - Etiquetado de una máquina virtual
  - Conexión de discos de datos
  - Desacoplamiento de discos de datos
- Funcionamiento de una máquina virtual:
  - Inicio de una máquina virtual
  - Detención de una máquina virtual
  - Reinicio de una máquina virtual
- Enumeración de máquinas virtuales
- Eliminación de una máquina virtual

Para revisar el código que realiza estas operaciones, consulte la función **run_example()** del script de Python **example.py** en el repositorio de GitHub [Hybrid-Compute-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Cada operación está claramente etiquetada con un comentario y una función de impresión. Los ejemplos no aparecen necesariamente en el mismo orden de esta lista.

## <a name="run-the-python-sample"></a>Ejecutar el ejemplo de Python

1. Si aún no lo tiene, [instale Python](https://www.python.org/downloads/). Este ejemplo (y el SDK) es compatible con Python 2.7, 3.4, 3.5 y 3.6.

2. Una recomendación general para el desarrollo de Python es usar un entorno virtual. Para más información, consulte la [documentación de Python](https://docs.python.org/3/tutorial/venv.html).

3. Instale e inicialice el entorno virtual con el módulo **venv** en Python 3 (debe instalar [virtualenv](https://pypi.python.org/pypi/virtualenv) para Python 2.7):

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

5. Instale las dependencias con **pip**:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Cree una [entidad de servicio](../operator/azure-stack-create-service-principals.md) para trabajar con Azure Stack Hub. Asegúrese de que la entidad de servicio tenga rol de [colaborador o propietario](../operator/azure-stack-create-service-principals.md#assign-a-role) en la suscripción.

7. Establezca las siguientes variables y exporte estas variables de entorno al shell actual:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Para ejecutar este ejemplo, las imágenes Ubuntu 16.04-LTS y WindowsServer 2012-R2-DataCenter deben estar presentes en Marketplace de Azure Stack Hub. Estas imágenes se pueden [descargar de Azure](../operator/azure-stack-download-azure-marketplace-item.md) o agregarse al [repositorio de imágenes de la plataforma](../operator/azure-stack-add-vm-image.md).

9. Ejecución del ejemplo:

    ```python
    python example.py
    ```

## <a name="next-steps"></a>Pasos siguientes

- [Centro para desarrolladores de Python de Azure](https://azure.microsoft.com/develop/python/)
- [Documentación de Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
- [Ruta de aprendizaje de Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
