---
title: Descarga de elementos de Marketplace desde Azure y publicación en Azure Stack Hub | Microsoft Docs
description: Aprenda a descargar elementos de Marketplace desde Azure y a publicarlos en Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: 80cf9d192be07f951ee959c7a83419bb16bd2bbb
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022953"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Descarga de elementos de Marketplace en Azure Stack Hub 

Los operadores en la nube pueden descargar elementos de Marketplace en Azure Stack Hub y hacer que estén disponibles para todos los usuarios mediante el entorno de Azure Stack Hub. Dichos elementos se pueden elegir de una lista de elementos de Azure Marketplace que se han probados con anterioridad y se sabe que son compatibles con Azure Stack Hub. Con frecuencia se agregan nuevos elementos a esta lista, así que es aconsejable consultar periódicamente si tiene contenido nuevo.

Existen dos escenarios para descargar productos de Marketplace:

- **Escenario conectado**: requiere que el entorno de Azure Stack Hub esté conectado a Internet. El portal de administración de Azure Stack Hub se usa para buscar y descargar elementos.
- **Escenario sin conexión o con conexión parcial**: requiere que se acceda a Internet mediante la herramienta de redifusión de Marketplace para descargar los elementos de este. Después, los elementos descargados se transfieren a la instalación desconectada de Azure Stack Hub. Este escenario usa PowerShell.

Consulte [Elementos de Azure Marketplace disponibles para Azure Stack Hub](azure-stack-marketplace-azure-items.md) para encontrar una lista completa de los elementos de Marketplace que se pueden descargar. Consulte el artículo [Cambios en Marketplace de Azure Stack Hub](azure-stack-marketplace-changes.md) para ver una lista de adiciones, eliminaciones y actualizaciones recientes en Marketplace de Azure Stack Hub.

> [!NOTE]
> El catálogo será diferente en función de la nube a la que esté conectado el sistema de Azure Stack Hub. El entorno de nube viene determinado por la suscripción de Azure que se usa para registrar la instancia de Azure Stack Hub.

## <a name="connected-scenario"></a>Escenario conectado

Si Azure Stack Hub se conecta a Internet, se puede usar el portal de administración para descargar los elementos de Marketplace.

### <a name="prerequisites"></a>Prerequisites

La implementación de Azure Stack Hub debe tener conectividad a Internet y estar registrada en Azure.

### <a name="use-the-portal-to-download-marketplace-items"></a>Uso del portal para descargar elementos de Marketplace

1. Inicie sesión en el portal de administración de Azure Stack Hub.

2. Antes de empezar a descargar elementos de Marketplace vea el espacio de almacenamiento que tiene disponible. Después, cuando seleccione los elementos que desea descargar, puede comparar el tamaño de la descarga con la capacidad de almacenamiento disponible. Si hay un límite de capacidad, considere distintas opciones para [administrar el espacio disponible](azure-stack-manage-storage-shares.md#manage-available-space).

   Para examinar el espacio disponible, en **Region management** (Administración de regiones), seleccione la región que desea explorar y vaya a **Resource Providers** > **Storage** (Proveedores de recursos > Almacenamiento):

   ![Revisión del espacio de almacenamiento en el portal del administrador de Azure Stack Hub](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Abra Marketplace de Azure Stack Hub y conéctese a Azure. Para ello, seleccione el servicio **Marketplace management** (Administración de Marketplace), elija **Elementos de Marketplace** y, por último, seleccione **Add from Azure** (Agregar desde Azure):

   ![Descarga de elementos de Marketplace desde Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Cada elemento de línea también muestra la versión disponible actualmente. Si hay más de una versión de un elemento de Marketplace disponible, la columna **Version** (Versión) muestra **Multiple** (Múltiples). Puede hacer clic en cada elemento para ver su descripción, así como información adicional, incluido su tamaño de descarga:

   ![Adición desde Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Si la versión de un elemento se muestra como **Multiple** (Múltiples), puede seleccionar ese elemento y, a continuación, elegir una versión específica en la lista desplegable de selección de versión que aparece:

   ![Adición desde Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Seleccione el elemento que desee y, después, seleccione **Descargar**. Los tiempos de descarga varían y dependen de la conectividad de red. Una vez finalizada la descarga, el nuevo elemento de Marketplace se puede implementar como usuario u operador de Azure Stack Hub.

7. Para implementar el elemento descargado, seleccione **+Crear un recurso** y busque el nuevo elemento de Marketplace entre las distintas categorías. Luego, seleccione el elemento para comenzar el proceso de implementación. El proceso no es el mismo para todos los artículos de Marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Escenario sin conexión o con conexión parcial

Si Azure Stack Hub tiene poca conectividad a Internet o ninguna, puede usar PowerShell y la *herramienta de redifusión de Marketplace* para descargar los elementos de Marketplace en una máquina que sí disponga de conectividad. Luego, transfiera los elementos al entorno de Azure Stack Hub. En un entorno sin conexión, no se pueden descargar elementos de Marketplace desde el portal de Azure Stack Hub.

La herramienta de redifusión de Marketplace también se puede usar en un escenario con conexión.

Este escenario tiene dos partes:

- **Parte 1**: descarga de elementos de Marketplace. En el equipo con acceso a Internet, configure PowerShell, descargue la herramienta de redifusión y, después, descargue los elementos desde Azure Marketplace.
- **Parte 2**: carga y publicación en Marketplace de Azure Stack Hub. Mueva los archivos que ha descargado a su entorno de Azure Stack Hub, impórtelos en Azure Stack Hub y, después, publíquelos en Marketplace de Azure Stack Hub.

### <a name="prerequisites"></a>Prerequisites

- Un entorno conectado (no tiene que ser Azure Stack Hub). Necesita conectividad para obtener la lista de productos de Azure con sus detalles y descargar todo localmente. Una vez hecho esto, el resto del procedimiento no requiere ninguna conectividad a Internet. Crea un catálogo de elementos que se han descargado previamente para su uso en el entorno desconectado.

- Medios extraíbles para conectarse al entorno desconectado y transferir todos los artefactos necesarios.

- Un entorno de Azure Stack Hub desconectado con los siguientes requisitos previos:

  - La implementación de Azure Stack Hub se debe registrar en Azure.

  - El equipo que tenga conectividad a Internet debe tener la **versión 1.2.11 del módulo de PowerShell de Azure Stack Hub** , o cualquier versión posterior. Si no están, [instale los módulos de PowerShell específicos de Azure Stack Hub](azure-stack-powershell-install.md).

  - Para habilitar la importación de un elemento de Marketplace descargado, debe estar configurado el  [entorno de PowerShell para el operador de Azure Stack Hub](azure-stack-powershell-configure-admin.md) .

  - Clone el repositorio de [herramientas de Azure Stack Hub](https://github.com/Azure/AzureStack-Tools) de GitHub.

- Debe tener una [cuenta de almacenamiento](azure-stack-manage-storage-accounts.md) en Azure Stack Hub que tenga un contenedor al que se pueda acceder de forma pública (que es un blob de almacenamiento). Utilice el contenedor como almacenamiento temporal para los archivos de la galería de elementos de Marketplace. Si no está familiarizado con las cuentas de almacenamiento y los contenedores, consulte [Carga, descarga y enumeración de blobs mediante Azure Portal](/azure/storage/blobs/storage-quickstart-blobs-portal) en la documentación de Azure.

- La herramienta de redifusión de Marketplace se descarga en el primer procedimiento.

- Puede instalar [AzCopy](/azure/storage/common/storage-use-azcopy) para que la descarga se realice de forma óptima, pero no es necesario.

Una vez registrado, puede omitir el mensaje siguiente que aparece en la hoja de administración de Marketplace, ya que no es aplicable en un caso de uso sin conexión:

![Administración de Marketplace](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Uso de la herramienta de redifusión de Marketplace para descargar elementos de Marketplace

> [!IMPORTANT]
> Asegúrese de descargar la herramienta de redifusión de Marketplace cada vez que descargue elementos de Marketplace en un escenario desconectado. Se realizan cambios frecuentes en este script y se debe usar la versión más reciente en cada descarga.

1. En un equipo con conexión a Internet, abra una consola de PowerShell como administrador.

2. Agregue la cuenta de Azure que ha usado para registrar Azure Stack Hub. Para agregar la cuenta, en PowerShell ejecute **Add-AzureRmAccount** sin parámetros. Se le solicita que escriba las credenciales de su cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases en función de la configuración de la cuenta.

   > [!NOTE]
   > Si la sesión expira, la contraseña ha cambiado o simplemente desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión con **Add-AzureRmAccount**: **Remove-AzureRmAccount-Scope Process**.

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la que ha usado para el registro:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Descargue la versión más reciente de la herramienta de redifusión de Marketplace mediante el siguiente script:

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master
   ```

5. Importe el módulo de redifusión e inicie la herramienta mediante la ejecución de los comandos siguientes:

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1
   ```

6. Para exportar elementos de Marketplace, como imágenes de máquinas virtuales, extensiones o plantillas de soluciones, ejecute el siguiente comando. Reemplace la ruta de acceso de la carpeta de destino por una ubicación para almacenar los archivos que descargue desde Azure Marketplace:

   ```powershell
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" -azCopyDownloadThreads "[optional - AzCopy threads number]" -azureContext $AzureContext
   ```

   Para exportar proveedores de recursos o servicios PaaS, ejecute el siguiente comando:

   ```powershell
   Export-AzSOfflineResourceProvider -destination "Destination folder path" -azCopyDownloadThreads "AzCopy threads number" -azureContext $AzureContext
   ```

   El `-azCopyDownloadThreads` es opcional. Solo se debe usar si tiene una red de ancho de banda bajo y se usa la descarga Premium. Esta opción especifica el número de operaciones simultáneas en AzCopy. Si utiliza una red con un ancho de banda bajo, puede especificar un número bajo con el fin de evitar errores provocados por la competencia de recursos. Puede encontrar más información en [este artículo de Azure](/previous-versions/azure/storage/storage-use-azcopy#specify-the-number-of-concurrent-operations-to-start).

   El parámetro `-azureContext` también es opcional. Si no especifica el contexto de Azure, el cmdlet usará el contexto predeterminado de Azure.

7. Cuando la herramienta se ejecuta, verá una pantalla similar a la de la siguiente imagen, con la lista de elementos disponibles en Azure Marketplace:

   ![Elementos de Marketplace](media/azure-stack-download-azure-marketplace-item/tool1.png)

8. Si hay más de una versión de un elemento de Marketplace disponible, la columna **Version** (Versión) muestra  **Multiple versions** (Múltiples versiones). Si aparece  **Multiple versions** (Múltiples versiones), puede seleccionar ese elemento y después elegir una versión específica en la ventana de selección de versión que aparece.

9. Seleccione el elemento que desea descargar y tome nota de la **versión**. Puede mantener presionada la tecla **Ctrl** para seleccionar varias imágenes. Hará referencia a la *versión* cuando importe el elemento en el siguiente procedimiento.

    También puede filtrar la lista de imágenes mediante la opción **Agregar criterios** .

10. Si no ha instalado las herramientas de Azure Storage, obtendrá el siguiente mensaje. Para instalar estas herramientas, descargue [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy):

    ![Herramientas de Azure Storage](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

11. Seleccione **Aceptar**, lea los términos legales y acéptelos.

12. El tiempo que tarda la descarga depende del tamaño del elemento. Una vez que se completa la descarga, el elemento está disponible en la carpeta que se especificó en el script. La descarga incluye un archivo VHD (para máquinas virtuales) o un archivo .zip (para extensiones de máquina virtual y proveedores de recursos). También puede incluir un paquete de galería en el formato *.azpkg* , que es simplemente un archivo .zip.

13. Si se produce un error en la descarga, puede intentarlo de nuevo volviendo a ejecutar el siguiente cmdlet de PowerShell:

   ```powershell
   # for Marketplace items
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"

   # for Resource providers
   Export-AzSOfflineResourceProvider -Destination "Destination folder path in quotes"
   ```

   Antes de volver a intentarlo, quite la carpeta de producto en la que se produjo el error de descarga. Por ejemplo, si se produce un error en el script de descarga al descargar en **D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1**, quite la carpeta **D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1**  y, a continuación, vuelva a ejecutar el cmdlet.

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Importación de la descarga y publicación en Marketplace de Azure Stack Hub con PowerShell

1. Debe mover los archivos que [descargó anteriormente](#use-the-marketplace-syndication-tool-to-download-marketplace-items) de forma local para que estén disponibles en su entorno de Azure Stack Hub. La herramienta de redifusión de Marketplace debe estar disponible también en su entorno de Azure Stack Hub, ya que necesita usarla para realizar la operación de importación.

   En la siguiente imagen se muestra un ejemplo de estructura de carpetas. **D:\downloadfolder** contiene todos los elementos de Marketplace descargados. Cada subcarpeta es un elemento de Marketplace (por ejemplo,  **microsoft.custom-script-linux-arm-2.0.3**) y tienen como nombre el identificador del producto. Dentro de cada subcarpeta se encuentra el contenido descargado del elemento de Marketplace.

   ![Estructura de directorios de descarga de Marketplace](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Siga las instrucciones de [este artículo](azure-stack-powershell-configure-admin.md) para configurar la sesión de PowerShell para un operador de Azure Stack Hub.

3. Importe el módulo de redifusión e inicie la herramienta de redifusión de Marketplace mediante la ejecución del script siguiente:

   ```powershell
   $credential = Get-Credential -Message "Enter the Azure Stack Hub operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   El parámetro `-origin` especifica la carpeta de nivel superior que contiene todos los productos descargados; por ejemplo,  `"D:\downloadfolder"`.

   El parámetro `-AzsCredential` es opcional. Sirve para renovar el token de acceso, si ha expirado. Si el parámetro `-AzsCredential` no se especifica y el token expira, se le pedirá que escriba las credenciales del operador.

   > [!NOTE]
   > AD FS solo admite la autenticación interactiva con identidades de usuario. Si se requiere un objeto de credencial, debe usar una entidad de servicio (SPN). Para más información sobre cómo configurar una entidad de servicio con Azure Stack Hub y AD FS como servicio de administración de identidades, consulte [Administración de una entidad de servicio de AD FS](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. Después de que el script se complete correctamente, el elemento debe estar disponible en Marketplace de Azure Stack Hub.
