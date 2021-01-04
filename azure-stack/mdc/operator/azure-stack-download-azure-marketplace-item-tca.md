---
title: Descarga de elementos de Marketplace desde Azure y publicación en Azure Stack
description: Aprenda a descargar elementos de Marketplace desde Azure y a publicarlos en Azure Stack para centros de datos modulares (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 2a3dec7699911988c4c9a958c31386c127f70694
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598239"
---
# <a name="download-marketplace-items-to-azure-stack-hub---modular-data-center-mdc"></a>Descarga de elementos de Marketplace en Azure Stack Hub: centro de datos modular (MDC) 

Los operadores en la nube pueden descargar elementos de Marketplace en Azure Stack Hub y hacer que estén disponibles para todos los usuarios mediante el entorno de Azure Stack Hub. Dichos elementos se pueden elegir en una lista de elementos de Azure Marketplace que se han probados con anterioridad y se sabe que son compatibles con Azure Stack. Con frecuencia se agregan nuevos elementos a esta lista, así que es aconsejable consultar periódicamente si tiene contenido nuevo.

Existen dos escenarios para descargar productos de Marketplace:

- **Escenario conectado**: requiere que el entorno de Azure Stack Hub esté conectado a Internet. El portal de administración de Azure Stack Hub se usa para buscar y descargar elementos.
- **Escenario sin conexión o con conexión parcial**: requiere que se acceda a Internet mediante la herramienta de redifusión de Marketplace para descargar los elementos de este. Después, los elementos descargados se transfieren a la instalación desconectada de Azure Stack. Este escenario usa PowerShell.

Consulte [Elementos de Azure Marketplace disponibles para Azure Stack](../../operator/azure-stack-marketplace-azure-items.md) para encontrar una lista completa de los elementos de Marketplace que se pueden descargar. Consulte el artículo [Cambios en Marketplace de Azure Stack](../../operator/azure-stack-marketplace-changes.md) para obtener una lista de adiciones, eliminaciones y actualizaciones recientes en Marketplace de Azure Stack.

> [!NOTE]
> El catálogo será diferente en función de la nube a la que esté conectado el sistema de Azure Stack Hub. El entorno de nube viene determinado por la suscripción de Azure que se usa para registrar la instancia de Azure Stack Hub.

## <a name="connected-scenario"></a>Escenario conectado

Si Azure Stack Hub se conecta a Internet, se puede usar el portal de administración para descargar los elementos de Marketplace.

### <a name="prerequisites"></a>Requisitos previos

La implementación de Azure Stack Hub debe tener conectividad a Internet y [estar registrada en Azure](registration-tzl.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Uso del portal para descargar elementos de Marketplace

1. Inicie sesión en el portal de administración de Azure Stack Hub.

2. Antes de empezar a descargar elementos de Marketplace vea el espacio de almacenamiento que tiene disponible. Después, cuando seleccione los elementos que desea descargar, puede comparar el tamaño de la descarga con la capacidad de almacenamiento disponible. Si hay un límite de capacidad, considere distintas opciones para [administrar el espacio disponible](../../operator/azure-stack-manage-storage-shares.md#manage-available-space).

   Para examinar el espacio disponible, en **Region management** (Administración de regiones), seleccione la región que desea explorar y vaya a **Resource Providers** > **Storage** (Proveedores de recursos > Almacenamiento):

   ![Revisión del espacio de almacenamiento en el portal de administración de Azure Stack](media/azure-stack-download-azure-marketplace-item-tzl/storage.png)

3. Abra Marketplace de Azure Stack Hub y conéctese a Azure. Para ello, seleccione el servicio **Marketplace management** (Administración de Marketplace), elija **Elementos de Marketplace** y, por último, seleccione **Add from Azure** (Agregar desde Azure):

   ![Descarga de elementos de Marketplace desde Azure](media/azure-stack-download-azure-marketplace-item-tzl/marketplace.png)

4. Cada elemento de línea también muestra la versión disponible actualmente. Si hay más de una versión de un elemento de Marketplace disponible, la columna **Version** (Versión) muestra **Multiple** (Múltiples). Puede hacer clic en cada elemento para ver su descripción, así como información adicional, incluido su tamaño de descarga:

   ![Captura de pantalla que muestra la página "Add from Azure" (Agregar desde Azure).](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure-1.png)

5. Si la versión de un elemento se muestra como **Multiple** (Múltiples), puede seleccionar ese elemento y, a continuación, elegir una versión específica en la lista desplegable de selección de versión que aparece:

   ![Captura muestra la página de administración con la lista desplegable del selector de versiones resaltada.](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure-3.png)

6. Seleccione el elemento que desee y, después, seleccione **Download** (Descargar). Los tiempos de descarga varían y dependen de la conectividad de red. Una vez finalizada la descarga, el nuevo elemento de Marketplace se puede implementar como usuario u operador de Azure Stack.

7. Para implementar el elemento descargado, seleccione **+Crear un recurso** y busque el nuevo elemento de Marketplace entre las distintas categorías. Luego, seleccione el elemento para comenzar el proceso de implementación. El proceso no es el mismo para todos los artículos de Marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Escenario sin conexión o con conexión parcial

En un entorno sin conexión, no se pueden descargar elementos de Marketplace desde Azure. Debe usar la herramienta de redifusión sin conexión para descargar los elementos de Marketplace en una unidad local y, a continuación, cargar esos elementos en Azure Stack Hub desde allí.

Puede [descargar las herramientas de redifusión sin conexión aquí](https://aka.ms/azsSyndicationtool).

### <a name="download-marketplace-items-from-azure"></a>Descarga de elementos de Marketplace desde Azure

#### <a name="prerequisites"></a>Requisitos previos

- Azure PowerShell instalado en la máquina.

- El identificador de la suscripción de Azure, el grupo de recursos y el nombre del registro que utilizó para registrar su instancia de Azure Stack Hub. Puede encontrar esta información en la pestaña **Propiedades** de la hoja **Region Management** (Administración de regiones) del portal del operador de Azure Stack Hub.

- El nombre de usuario y la contraseña de la cuenta que tiene privilegios de propietario o colaborador en el identificador de la suscripción de Azure.

- Una unidad local o una ruta de acceso de red en la que se pueden escribir los archivos sin conexión.

#### <a name="download-items"></a>Descarga de elementos



### <a name="az-modules"></a>[Modules de Az](#tab/az1)

1. Abra PowerShell y vaya a la carpeta extraída.

2. Ejecute el script **Invoke-AzSMarketplaceDownload.ps1** de PowerShell:

    ```powershell
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationSubscriptionId '<subscription ID>' ` 
       -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' `
       -TenantName mytenant.onmicrosoft.com -DownloadFolder 'F:\offlineSyndication'
    ```

    Como alternativa, si ya ha iniciado sesión mediante Azure PowerShell, puede pasar el contexto de Azure:

    ```powershell
    Add-AzAccount -Environment AzureCloud -Tenant mytenant.onmicrosoft.com 
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' -DownloadFolder 'F:\offlineSyndication' -AzureContext $(Get-AzureRMContext)
    ```
    Si no pasa el contexto de Azure, le pedirá que inicie sesión.

3. Aparece una ventana en la que puede seleccionar el producto que desea descargar. Puede usar Ctrl + clic para seleccionar varios elementos.

4. Seleccione **Aceptar**. Esto descarga el elemento de Marketplace y sus dependencias, si las hay.
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)

1. Abra PowerShell y vaya a la carpeta extraída.

2. Ejecute el script **Invoke-AzSMarketplaceDownload.ps1** de PowerShell:

    ```powershell
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationSubscriptionId '<subscription ID>' ` 
       -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' `
       -TenantName mytenant.onmicrosoft.com -DownloadFolder 'F:\offlineSyndication'
    ```

    Como alternativa, si ya ha iniciado sesión mediante Azure PowerShell, puede pasar el contexto de Azure:

    ```powershell
    Add-AzureRmAccount -Environment AzureCloud -Tenant mytenant.onmicrosoft.com 
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' -DownloadFolder 'F:\offlineSyndication' -AzureContext $(Get-AzureRMContext)
    ```
    Si no pasa el contexto de Azure, le pedirá que inicie sesión.

3. Aparece una ventana en la que puede seleccionar el producto que desea descargar. Puede usar Ctrl + clic para seleccionar varios elementos.

4. Seleccione **Aceptar**. Esto descarga el elemento de Marketplace y sus dependencias, si las hay.

---

### <a name="upload-marketplace-items-to-azure-stack-hub"></a>Carga de elementos de Marketplace en Azure Stack Hub

#### <a name="prerequisites"></a>Requisitos previos

- Un punto de conexión de Resource Manager de administración de Azure Stack y un inquilino de directorio.

- Acceso a los elementos sin conexión de Marketplace.

#### <a name="upload-items"></a>Carga de elementos

### <a name="az-modules"></a>[Modules de Az](#tab/az2)

1. Abra PowerShell y vaya a la carpeta extraída.

2. Ejecute el script **Invoke-AzSMarketplaceUpload.ps1** de PowerShell:

    ```powershell
    .\Invoke-AzsMarketplaceUpload.ps1 -AzureStackCloudName "AzureStack-Admin" -AzureStackAdminARMEndpoint https://adminmanagement.<region>.<fqdn> -TenantName mytenant.onmicrosoft.com -DownloadFolder F:\offlineSyndication
    ```

    Como alternativa, puede configurar el entorno de Azure Stack usted mismo en Azure PowerShell, autenticarse en el punto de conexión de Resource Manager de administración y pasar el contexto al script:

    ```powershell
    Add-AzEnvironment -Name Redmond-Admin -ARMEndpoint https://adminmanagement.redmond.azurestack.corp.microsoft.com

    Add-AzAccount -Environment Redmond-Admin

    .\Invoke-AzsMarketplaceUpload.ps1 -DownloadFolder F:\Downloads\offlining -AzureContext $(GetAzContext)
    ```

    Este procedimiento permite cargar los elementos de Marketplace en la instancia de Azure Stack Hub especificada.

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm2)

1. Abra PowerShell y vaya a la carpeta extraída.

2. Ejecute el script **Invoke-AzSMarketplaceUpload.ps1** de PowerShell:

    ```powershell
    .\Invoke-AzsMarketplaceUpload.ps1 -AzureStackCloudName "AzureStack-Admin" -AzureStackAdminARMEndpoint https://adminmanagement.<region>.<fqdn> -TenantName mytenant.onmicrosoft.com -DownloadFolder F:\offlineSyndication
    ```

    Como alternativa, puede configurar el entorno de Azure Stack usted mismo en Azure PowerShell, autenticarse en el punto de conexión de Resource Manager de administración y pasar el contexto al script:

    ```powershell
    Add-AzureRmEnvironment -Name Redmond-Admin -ARMEndpoint https://adminmanagement.redmond.azurestack.corp.microsoft.com

    Add-AzureRmAccount -Environment Redmond-Admin

    .\Invoke-AzsMarketplaceUpload.ps1 -DownloadFolder F:\Downloads\offlining -AzureContext $(Get-AzureRmContext)
    ```

    Este procedimiento permite cargar los elementos de Marketplace en la instancia de Azure Stack Hub especificada.

---
