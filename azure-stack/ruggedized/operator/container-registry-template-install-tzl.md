---
title: 'Adición de un registro de contenedor: Azure Stack Hub | Microsoft Docs'
titleSuffix: Azure Stack
description: Aprenda a agregar un registro de contenedor al marketplace de Azure Stack Hub (Ruggedized).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: a517fc2bef86584c6bdf5aff6f9b7907ac9c0b1e
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910574"
---
# <a name="add-a-container-registry-to-azure-stack-hub-ruggedized"></a>Adición de un registro de contenedor a Azure Stack Hub (Ruggedized)

Puede agregar el registro de contenedor a su Marketplace de Azure Stack Hub para que los usuarios puedan implementar y mantener su propio registro de contenedor. Esta plantilla de solución instala y configura el registro de contenedor de Docker de código abierto en una suscripción de usuario que se ejecuta en la imagen base de Ubuntu 16.04-LTS de AKS. La plantilla admite implementaciones conectadas y desconectadas (con separación aérea) y admite Azure Active Directory (AAD) e instancias de Azure Stack Hub implementadas en los servicios federados de Active Directory (AD FS).

## <a name="get-the-marketplace-item"></a>Obtención del elemento de Marketplace

Puede encontrar el elemento de Marketplace de la **plantilla de Container Registry** en el siguiente repositorio de GitHub: https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/registry-v1.0.2/Microsoft.AzureStackContainerRegistry.1.0.2.azpkg. El elemento de Marketplace está disponible en el portal en suscripciones seleccionadas.

También puede agregar el elemento (carga lateral) a Marketplace mediante Microsoft.AzureStackDockerContainerRegistry.1.0.2.azpkg. Se puede acceder a los scripts de este artículo mediante la descarga del repositorio de git como un paquete (zip) desde https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip y la extracción de los archivos. Puede encontrar el script en la carpeta `azurestack-gallery-master\registry\scripts`.

## <a name="prerequisites"></a>Requisitos previos

Deberá tener los elementos siguientes antes de agregar el elemento de Marketplace de Container Registry en Azure Stack Hub.

| Elemento | Tipo | Detalles |
| --- | --- | --- |
| Módulos de PowerShell de Azure Stack Hub (Azs.Gallery.Admin) | Módulos de PowerShell | Solo se requiere si se realiza la carga lateral del elemento de la galería de la plantilla del registro de contenedor, los módulos de PowerShell de Azure Stack Hub se usan para agregar y eliminar elementos de la galería.<br>[Instalación de los módulos de PowerShell de Azure Stack](../../operator/azure-stack-powershell-install.md) |
| Plantilla de registro de contenedor | Elemento de Marketplace | Para implementar el registro de contenedor como un usuario de Azure Stack Hub, el elemento de Marketplace de la plantilla del registro de contenedor debe estar disponible en su suscripción, o bien agregarse manualmente (carga lateral) en el Marketplace de Azure Stack Hub. En el caso de la carga lateral, siga las instrucciones para cargar el paquete del archivo `readme.md` del [repositorio de GitHub](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/registry-v1.0.1). |
| Imagen base de Ubuntu 16.04-LTS de AKS, versión de lanzamiento mínima de septiembre de 2019 | Elemento de Marketplace | Para que los usuarios de Azure Stack Hub implementen el registro de contenedor, debe hacer que la imagen base de AKS esté disponible en Marketplace. La plantilla de registro de contenedor usa la imagen cuando una máquina virtual Ubuntu hospeda los archivos binarios del registro de contenedor de Docker. |
| Extensión de script personalizado de Linux 2.0 | Elemento de Marketplace | Para que los usuarios de Azure Stack Hub implementen el registro de contenedor, debe hacer que la extensión de script personalizado de Linux esté disponible en Marketplace. La implementación de la plantilla del registro de contenedor utiliza la extensión para configurar el registro. |
| Certificado SSL | Certificado | Los usuarios que implementan la plantilla del registro de contenedor deben proporcionar un certificado PFX que se usa al configurar el cifrado SSL para el servicio de registro. Si usa el script, tendrá que ejecutar la sesión de PowerShell desde un símbolo del sistema con privilegios elevados. No se debe ejecutar en DVM ni HLH.<br>Para obtener instrucciones generales sobre los requisitos del certificado PKI para Azure Stack Hub con certificados públicos o privados o de empresa, consulte [Requisitos de certificados de la infraestructura de clave pública (PKI) de Azure Stack Hub](../../operator/azure-stack-pki-certs.md).<br>El FQDN del certificado debe seguir el patrón `<vmname>.<location>.cloudapp.<fqdn>` a menos que se use un dominio personalizado o una entrada DNS para el punto de conexión. El nombre debe empezar por una letra y contener al menos dos letras, usar solo letras minúsculas y tener al menos tres caracteres de longitud. |
| Entidad de servicio (SPN) | Registro de aplicaciones | Para implementar y configurar el registro de contenedor, se debe crear un registro de aplicación, también conocido como entidad de servicio (SPN). Este SPN se usa durante la configuración de la máquina virtual y el registro para acceder a los recursos de Microsoft Azure Key Vault y la cuenta de almacenamiento creados antes de implementar el elemento de Marketplace.<br>El SPN se debe crear en AAD dentro del inquilino con el que inicia sesión en el portal de usuarios de Azure Stack Hub. Si usa AD FS, se creará en el directorio local.<br>Para más información sobre cómo crear un SPN para los métodos de autenticación de AAD y AD FS, consulte [las siguientes instrucciones](../../operator/azure-stack-create-service-principals.md).<br> **Importante**: debe guardar el identificador de aplicación y el secreto del SPN para implementar las actualizaciones.<br> |
| Nombre de usuario y contraseña del registro | Credenciales | El registro de contenedor de Docker de código abierto se implementa y se configura con la autenticación básica habilitada. Para acceder al registro mediante los comandos de Docker para insertar y extraer imágenes, se requiere un nombre de usuario y una contraseña. El nombre de usuario y la contraseña se almacenan de forma segura en un almacén de Key Vault.<br>**Importante**: debe guardar el nombre de usuario y la contraseña del registro para iniciar sesión en el registro e insertar y extraer imágenes. |
| Clave pública y privada de SSH | Credenciales | Para solucionar problemas relacionados con la implementación o problemas en tiempo de ejecución con la máquina virtual, debe proporcionar una clave pública de SSH para la implementación y la clave privada correspondiente debe estar accesible. Se recomienda usar el formato de OpenSSH, ssh-keygen, para generar el par de claves privada y pública, ya que los scripts de diagnóstico para recopilar los registros requieren este formato.<br>**Importante**: debe tener acceso a las claves pública y privada para acceder a la máquina virtual implementada para la solución de problemas. |
| Acceso a los portales de administración y de usuarios y a los puntos de conexión de administración | Conectividad | En esta guía se da por supuesto que va a implementar y configurar el registro desde un sistema con conectividad con el sistema de Azure Stack Hub. |

El script `Pre-reqs` crea las otras entradas necesarias para implementar el elemento de Marketplace.

## <a name="installation-steps"></a>Pasos de instalación

La instalación de la plantilla de registro de contenedor requiere la creación de varios recursos antes de la implementación.

1. Conéctese a Azure Stack Hub como usuario con PowerShell y seleccione una suscripción con el cmdlet `Select-AzureRmSubscription –Subscription <subscription guid>`. Para más información sobre cómo conectarse como usuario a PowerShell de Azure Stack Hub, consulte [Conexión a Azure Stack Hub con PowerShell como usuario](../../user/azure-stack-powershell-configure-user.md).

2. Ejecute `Import-Modules .\\pre-reqs.ps1` para importar los módulos dentro del script `pre-reqs.ps1`. El script creará un grupo de recursos, una cuenta de almacenamiento, un contenedor de blobs, un almacén de Key Vault, asignará permisos de acceso al SPN y copiará los certificados y el nombre de usuario y la contraseña del registro en el almacén de Key Vault.

3. Ejecute el siguiente cmdlet desde un símbolo del sistema con privilegios elevados con los valores de su entorno para los parámetros:

    ```powershell  
         Set-ContainerRegistryPrerequisites -Location Shanghai `
         -ServicePrincipalId <spn app id> `
         -ResourceGroupName newregreq1 `
         -StorageAccountName newregsa1 `
         -StorageAccountBlobContainer newregct1 `
         -KeyVaultName newregkv1 `
         -CertificateSecretName containersecret2 `
         -CertificateFilePath C:\crinstall\shanghairegcert.pfx `
         -CertificatePassword <cert password> `
         -RegistryUserName admin `
         -RegistryUserPassword <password> 
    ```

    | Parámetro | Detalles |
    | --- | --- |
    | $Location | En ocasiones se hace referencia a este parámetro como el nombre de la región. |
    | $ResourceGroupName | Especifique el nombre del grupo de recursos en el que desea que se creen la cuenta de almacenamiento y el almacén de Key Vault. Especificará otro grupo de recursos al implementar el elemento de Marketplace. |
    | $storageAccountName | Especifique el nombre de la cuenta de almacenamiento que se va a crear para el registro de contenedor que se va a usar al almacenar las imágenes que se han insertado. |
    | $StorageAccountBlobContainer | Especifique el nombre del contenedor de blobs que se va a crear, que se usa para el almacenamiento de imágenes. |
    | $KeyVaultName | Especifique el nombre del almacén de Key Vault que se va a crear para almacenar el certificado y el valor del nombre de usuario y la contraseña. |
    | $CertificateSecretName | Proporcione el nombre del secreto creado en Key Vault para almacenar el certificado PFX. |
    | $CertificateFilePath | Proporcione la ruta de acceso al certificado PFX. |
    | $CertificatePassword | Proporcione la contraseña del certificado PFX. |
    | $ServicePrincipalId | Proporcione el valor de AppID del SPN. |
    | $RegistryUserName | Proporcione el nombre de usuario para acceder al servicio de registro mediante la autorización básica. |
    | $RegistryUserPassword | Proporcione la contraseña del usuario del registro. |

1. Una vez completado el script, observe que al final del script se incluyen los parámetros que se van a usar en la implementación de la plantilla. Al copiar y pegar estos valores, se puede haber introducido algún espacio si el valor se ajusta.

    ```powershell  
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/<subcription id>/resourceGroups/newr
    egreg1/providers/Microsoft.KeyVault/vaults/newregkv1
    PFX KeyVaultSecretUrl        : https://newregkv1.vault.shanghai.azurestack.corp.microsoft.com:443/secr
    ets/containersecret1/37cc2f7ea1c44ad7b930e2c237a14949
    PFX Certificate Thumbprint   : 64BD5F3BC41DCBC6495998900ED322D8110DE25E
    ----------------------------------------------------------------
    StorageAccountResourceId     : /subscriptions/<subcription id>/resourcegroups/newr
    egreg1/providers/Microsoft.Storage/storageAccounts/newregsa1
    Blob Container               : newregct1
    ----------------------------------------------------------------
    
    Skus : aks-ubuntu-1604-201909
    
    
    Skus : aks-ubuntu-1604-201910
    
    ---------------------------------------------------------------- 
    
    ```

1. Abra el portal de usuarios de Azure Stack Hub.

2. Seleccione **Create** > **Compute** > **Container Registry Template** (Crear > Compute > Plantilla de registro de contenedor).

    ![Captura de pantalla en la que se muestra la página Nuevo del panel, con la opción de proceso seleccionada y la selección de la plantilla de Container Registry a la vista.](./media/container-registry-template-install-tzl/image1.png)

3. Seleccione la suscripción, el grupo de recursos y la ubicación para implementar la plantilla de registro de contenedor.

    ![Captura de pantalla en la que se muestra la página de conceptos básicos para crear una plantilla de Container Registry.](./media/container-registry-template-install-tzl/image2.png)

4. Complete los detalles de configuración de la máquina virtual. La SKU de la imagen tiene como valor predeterminado **aks-ubuntu-1604-201909**; sin embargo, la salida de la función `Set-ContainerRegistryPrerequisites` incluye una lista de las SKU disponibles que se usarán para la implementación. Si existe más de una SKU, elija la SKU más reciente para la implementación.

    ![Captura de pantalla en la que se muestra la página de configuración de máquina virtual para crear una plantilla de Container Registry.](./media/container-registry-template-install-tzl/image3.png)

    | Parámetro | Detalles |
    | --- | --- |
    | Nombre de usuario | Proporcione el nombre de usuario para iniciar sesión en la máquina virtual. |
    | Clave pública SSH | Proporcione la clave pública SSH que se usa para autenticarse con la máquina virtual mediante el protocolo SSH. |
    | Size | Seleccione el tamaño de la máquina virtual que va a implementar. |
    | Dirección IP pública | Especifique el nombre y el tipo de dirección IP (dinámica o estática) para esta máquina virtual. El nombre de dominio no es válido. Solo puede contener minúsculas, números y guiones. El primer carácter debe ser una letra. El último carácter debe ser una letra o un número. El valor debe tener entre 3 y 63 caracteres de longitud.  |
    | Etiqueta de nombre de dominio | Especifique el prefijo DNS del registro. El FQDN completo debe coincidir con el valor de CN del certificado PFX creado para el registro. |
    | Réplicas | Especifique el número de réplicas de contenedor que se van a iniciar. |
    | SKU de la imagen | Especifique la SKU de la imagen que se va a usar para la implementación. El script `Set-ContainerRegistryPrerequisites` muestra las SKU disponibles para la imagen base de AKS. |
    | Identificador de cliente del SPN | Especifique el identificador de aplicación del SPN. |
    | Contraseña del SPN y Confirmar contraseña | Especifique el secreto del identificador de aplicación del SPN. |

1. Complete la configuración del almacenamiento y Key Vault.

    ![Captura de pantalla en la que se muestra la página de configuración de almacenamiento y Key Vault para crear una plantilla de Container Registry.](./media/container-registry-template-install-tzl/image4.png)

    | Parámetro | Detalles |
    | --- | --- |
    | Identificador de recurso de la cuenta de almacenamiento extendida existente | Especifique el identificador de recurso de la cuenta de almacenamiento devuelto por el script `pre-reqs`. |
    | Contenedor de blobs de back-end existente | Especifique el nombre del contenedor de blobs, que se muestra en la salida del script de requisitos previos. |
    | Identificador de recurso de Key Vault del certificado PFX | Especifique el identificador de recurso de Key Vault devuelto por el script `pre-reqs`. |
    | Dirección URL del secreto de Key Vault del certificado PFX | Especifique la dirección URL del certificado devuelta por el script de requisitos previos. |
    | Huella digital del certificado PFX | Especifique la huella digital del certificado devuelta por el script de requisitos previos. |

1. Una vez que se proporcionen todos los valores y se inicie la implementación de la plantilla de la solución, la máquina virtual tardará de 10 a 15 minutos en implementar y configurar el servicio de registro.

    ![Plantilla de registro de contenedor](./media/container-registry-template-install-tzl/image5.png)

2. Para probar el registro, abra una instancia de la CLI de Docker desde un equipo o una máquina virtual que tenga acceso a la dirección URL del registro.

    > [!Note]
    >  Si ha usado un certificado autofirmado o un certificado no conocido por la máquina virtual que usa para acceder al registro, tendrá que instalar el certificado en la máquina virtual y reiniciar Docker.

## <a name="pushing-and-pulling-images-from-container-registry"></a>Inserción y extracción de imágenes desde el registro de contenedor

1. Inicie sesión con `docker login –u \<username> -p \<password>`.
2. Extraiga una imagen de un registro conocido.
3. Etiquete la imagen para que tenga como destino el registro de contenedor de Docker recién implementado.
4. Inserte la imagen en el nuevo registro de destino.

Por ejemplo:

```powershell  
PS C:\> docker pull mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
1.0: Pulling from azureiotedge-simulated-temperature-sensor
5d20c808ce19: Already exists
656de8e592c3: Already exists
1e1868d1f676: Already exists
f3fb1b0d620f: Pulling fs layer
26224c4fc11a: Pulling fs layer
c459a69d65b2: Pulling fs layer
c459a69d65b2: Verifying Checksum
c459a69d65b2: Download complete
f3fb1b0d620f: Download complete
f3fb1b0d620f: Pull complete
26224c4fc11a: Verifying Checksum
26224c4fc11a: Pull complete
c459a69d65b2: Pull complete
Digest: sha256:dd64ff0918459184574e840ee97aa9f1bacd40aa37c972984ea10f0ecd719d5f
Status: Downloaded newer image for mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker tag mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0    myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp.microsoft.com
docker : WARNING! Using --password via the CLI is insecure. Use --password-stdin.
At line:1 char:1
+ docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (WARNING! Using ...password-stdin.:String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
 
Login Succeeded

PS C:\> docker push myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
The push refers to repository [myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor]
d377c212e567: Preparing
0481685b758f: Preparing
15474c03a0b6: Preparing
8cdec5be5964: Preparing
79116d3fb0bf: Preparing
3fc64803ca2d: Preparing
3fc64803ca2d: Waiting
79116d3fb0bf: Mounted from azureiotedge-agent
8cdec5be5964: Mounted from azureiotedge-agent
15474c03a0b6: Pushed
d377c212e567: Pushed
3fc64803ca2d: Mounted from azureiotedge-agent
0481685b758f: Pushed
1.0: digest: sha256:f5fbc4a5c6806e12cafe1c363fea2b6cbd98a211b8153c5b19aca1386bfa6ecb size: 1576 

```

## <a name="known-issues"></a>Problemas conocidos

La versión del servicio de registro de contenedor de Docker implementada por esta plantilla es la 2.7. Esta versión tiene un problema conocido que impide la inserción y extracción de imágenes de un contenedor Windows. Hay un seguimiento del problema con el siguiente elemento de GitHub: [https://github.com/docker/distribution-library-image/issues/89](https://github.com/docker/distribution-library-image/issues/89).

## <a name="next-steps"></a>Pasos siguientes

[Información general de Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
