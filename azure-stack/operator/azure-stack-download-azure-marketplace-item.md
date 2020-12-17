---
title: Descarga de elementos de Marketplace desde Azure y publicación en Azure Stack Hub
description: Aprenda a descargar elementos de Marketplace desde Azure y a publicarlos en Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/16/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: f0aafd572aa50760a7b326d5ca699e3f1331b3e9
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620660"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Descarga de elementos de Marketplace en Azure Stack Hub

Los operadores en la nube pueden descargar elementos de Marketplace en Azure Stack Hub y hacer que estén disponibles para todos los usuarios mediante el entorno de Azure Stack Hub. Dichos elementos se pueden elegir de una lista de elementos de Azure Marketplace que se han probados con anterioridad y se sabe que son compatibles con Azure Stack Hub. Con frecuencia se agregan nuevos elementos a esta lista, así que es aconsejable consultar periódicamente si tiene contenido nuevo.

Existen dos escenarios para descargar productos de Marketplace:

- **Escenario sin conexión o con conexión parcial**: requiere que se acceda a Internet mediante la herramienta de redifusión de Marketplace para descargar los elementos de este. Después, los elementos descargados se transfieren a la instalación desconectada de Azure Stack Hub. Este escenario usa PowerShell.
- **Escenario conectado**: requiere que el entorno de Azure Stack Hub esté conectado a Internet. El portal de administración de Azure Stack Hub se usa para buscar y descargar elementos.

Consulte [Elementos de Azure Marketplace disponibles para Azure Stack Hub](azure-stack-marketplace-azure-items.md) para encontrar una lista completa de los elementos de Marketplace que se pueden descargar. Consulte el artículo [Cambios en Marketplace de Azure Stack Hub](azure-stack-marketplace-changes.md) para ver una lista de adiciones, eliminaciones y actualizaciones recientes en Marketplace de Azure Stack Hub.

> [!NOTE]
> El catálogo será diferente en función de la nube a la que esté conectado el sistema de Azure Stack Hub. El entorno de nube viene determinado por la suscripción de Azure que se usa para registrar la instancia de Azure Stack Hub.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

::: zone pivot="state-connected"
Una implementación con conexión le permite usar el portal de administración para descargar elementos de Marketplace.

## <a name="prerequisites"></a>Prerrequisitos

La implementación de Azure Stack Hub debe tener conectividad a Internet y estar registrada en Azure.

## <a name="use-the-portal-to-download-marketplace-items"></a>Uso del portal para descargar elementos de Marketplace

1. Inicie sesión en el portal de administración de Azure Stack Hub.

2. Antes de empezar a descargar elementos de Marketplace vea el espacio de almacenamiento que tiene disponible. Después, cuando seleccione los elementos que desea descargar, puede comparar el tamaño de la descarga con la capacidad de almacenamiento disponible. Si hay un límite de capacidad, considere distintas opciones para [administrar el espacio disponible](azure-stack-manage-storage-shares.md#manage-available-space).

   Para examinar el espacio disponible, en **Region management** (Administración de regiones), seleccione la región que desea explorar y vaya a **Resource Providers** > **Storage** (Proveedores de recursos > Almacenamiento):

   ![Revisión del espacio de almacenamiento en el portal del administrador de Azure Stack Hub](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Abra Marketplace de Azure Stack Hub y conéctese a Azure. Para ello, seleccione el servicio **Marketplace management** (Administración de Marketplace), elija **Elementos de Marketplace** y, por último, seleccione **Add from Azure** (Agregar desde Azure):

   ![Descarga de elementos de Marketplace desde Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Cada elemento de línea también muestra la versión disponible actualmente. Si hay más de una versión de un elemento de Marketplace disponible, la columna **Version** (Versión) muestra **Multiple** (Múltiples). Puede hacer clic en cada elemento para ver su descripción, así como información adicional, incluido su tamaño de descarga:

   ![Captura de pantalla que muestra las versiones disponibles de un elemento de Marketplace.](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Si la versión de un elemento se muestra como **Multiple** (Múltiples), puede seleccionar ese elemento y, a continuación, elegir una versión específica en la lista desplegable de selección de versión que aparece. Tenga en cuenta que Microsoft ahora puede agregar atributos para impedir que los administradores descarguen productos de Marketplace que no son compatibles con sus instancias de Azure Stack, mediante diversas propiedades como la versión de Azure Stack o el modelo de facturación. Solo Microsoft puede agregar estos atributos:

   [![Agregar desde Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3sm.png "Varias versiones")](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png#lightbox)

6. Seleccione el elemento que desee y, después, seleccione **Download** (Descargar). Los tiempos de descarga varían y dependen de la conectividad de red. Una vez finalizada la descarga, el nuevo elemento de Marketplace se puede implementar como usuario u operador de Azure Stack Hub.

7. Para implementar el elemento descargado, seleccione **+Crear un recurso** y busque el nuevo elemento de Marketplace entre las distintas categorías. Luego, seleccione el elemento para comenzar el proceso de implementación. El proceso no es el mismo para todos los artículos de Marketplace.
::: zone-end

::: zone pivot="state-disconnected"
Si Azure Stack Hub tiene poca conectividad a Internet o ninguna, use PowerShell y la *herramienta de redifusión de Marketplace* para descargar los elementos de Marketplace en una máquina que sí disponga de conectividad. Luego, transfiera los elementos al entorno de Azure Stack Hub. En un entorno sin conexión, no se pueden descargar elementos de Marketplace desde el portal de Azure Stack Hub.

La herramienta de redifusión de Marketplace también se puede usar en un escenario con conexión.

Este escenario tiene dos partes:

- **Parte 1**: descarga de elementos de Marketplace. En el equipo con acceso a Internet, configure PowerShell, descargue la herramienta de redifusión y, después, descargue los elementos desde Azure Marketplace.
- **Parte 2**: carga y publicación en Marketplace de Azure Stack Hub. Mueva los archivos que ha descargado a su entorno de Azure Stack Hub y, después, publíquelos en Marketplace de Azure Stack Hub.

## <a name="prerequisites"></a>Prerrequisitos

- Un entorno conectado (no tiene que ser Azure Stack Hub). Necesita conectividad para obtener la lista de productos de Azure con sus detalles y descargar todo localmente. Una vez hecho esto, el resto del procedimiento no requiere ninguna conectividad a Internet. Crea un catálogo de elementos que se han descargado previamente para su uso en el entorno desconectado.

- Medios extraíbles para conectarse al entorno desconectado y transferir todos los artefactos necesarios.

- Un entorno de Azure Stack Hub desconectado con los siguientes requisitos previos:

  - La implementación de Azure Stack Hub se debe registrar en Azure.

  - El equipo que tenga conectividad a Internet debe tener la **versión 1.2.11 del paquete de Azure Stack PowerShell Module** u otra versión posterior. Si no están, [instale los módulos de PowerShell específicos de Azure Stack Hub](powershell-install-az-module.md).

  - Para habilitar la importación de un elemento de Marketplace descargado, debe estar configurado el [entorno de PowerShell para el operador de Azure Stack Hub](azure-stack-powershell-configure-admin.md).
  - .NET Framework 4.7 o superior.

Descargue el módulo **Azs.Syndication.Admin** de la Galería de PowerShell mediante el comando siguiente:

### <a name="az-modules"></a>[Modules de Az](#tab/az1)

  ```powershell
  Install-Module -Name Azs.Syndication.Admin -AllowPrerelease -PassThru
  ```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)

  ```powershell
  Install-Module -Name Azs.Syndication.Admin -RequiredVersion 0.1.140
  ```

---

Una vez registrado Azure Stack, puede ignorar el mensaje siguiente que aparece en la hoja de administración de Marketplace, ya que no es aplicable en un caso de uso sin conexión:

![Administración de Marketplace](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

## <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Uso de la herramienta de redifusión de Marketplace para descargar elementos de Marketplace

> [!IMPORTANT]
> Asegúrese de descargar la herramienta de redifusión de Marketplace cada vez que descargue elementos de Marketplace en un escenario desconectado. Se realizan cambios frecuentes en esta herramienta y se debe usar la versión más reciente en cada descarga.

### <a name="az-modules"></a>[Modules de Az](#tab/az2)

1. En un equipo con conexión a Internet, abra una consola de PowerShell como administrador.

2. Inicie sesión en la nube de Azure adecuada y en el inquilino de Azure AD con la cuenta de Azure que ha usado para registrar Azure Stack Hub. Para agregar la cuenta, en PowerShell ejecute `Login-AzAccount`:

   ```powershell  
   Login-AzAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```

   Se le solicita que escriba las credenciales de su cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases en función de la configuración de la cuenta.

   > [!NOTE]
   > Si la sesión expira, la contraseña ha cambiado o desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión mediante `Add-AzRmAccount`: `RemoveAzAccount -Scope Process`.

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la que ha usado para el registro:

   ```powershell  
   Get-AzSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzSubscription
   ```

4. Si aún no lo ha hecho en el paso de requisitos previos, descargue la versión más reciente de la herramienta de redifusión de Marketplace:

   ```powershell
   Install-Module -Name Azs.Syndication.Admin -AllowPrerelease -PassThru
   ```

5. Para seleccionar elementos de Marketplace como imágenes de máquinas virtuales, extensiones o plantillas de soluciones para descargar, ejecute el siguiente comando:

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   Esto hará que aparezca una tabla que muestra todos los registros de Azure Stack disponibles en la suscripción seleccionada. Elija el registro que coincida con el entorno de Azure Stack para el que está descargando los elementos de Marketplace y seleccione **Aceptar**.

     ![Captura de pantalla que muestra una lista de todos los registros de Azure Stack disponibles en la suscripción seleccionada.](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Ahora debería ver una segunda tabla en la que se enumeran todos los elementos de Marketplace que están disponibles para su descarga. Seleccione el elemento que desea descargar y tome nota de la **versión**. Puede mantener presionada la tecla **Ctrl** para seleccionar varias imágenes.
     ![Captura de pantalla que muestra otra lista de todos los registros de Azure Stack disponibles en la suscripción seleccionada.](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   También puede filtrar la lista de imágenes mediante la opción **Agregar criterios**.
   ![Selección de registros de Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Cuando termine las selecciones, seleccione Aceptar.

6. Los identificadores de los elementos de Marketplace que ha seleccionado para su descarga se guardan en la variable `$products`. Use el comando siguiente para empezar a descargar los elementos seleccionados. Reemplace la ruta de acceso de la carpeta de destino por una ubicación para almacenar los archivos que descargue desde Azure Marketplace:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. El tiempo que tarda la descarga depende del tamaño del elemento. Una vez que se completa la descarga, el elemento está disponible en la carpeta que se especificó en el script. La descarga incluye un archivo VHD (para máquinas virtuales) o un archivo .zip (para extensiones de máquina virtual y proveedores de recursos). También puede incluir un paquete de galería en el formato *.azpkg*, que es un archivo .zip.

8. Si se produce un error en la descarga, puede intentarlo de nuevo volviendo a ejecutar el siguiente cmdlet de PowerShell:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. También debe exportar el módulo **Azs.Syndication.Admin** de forma local para que pueda copiarlo en la máquina desde la que va a importar los elementos de Marketplace hacia Azure Stack Hub.

   > [!NOTE]
   > La carpeta de destino para la exportación de este módulo debe ser diferente de la ubicación a la que ha exportado los elementos de Marketplace.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm2)

1. En un equipo con conexión a Internet, abra una consola de PowerShell como administrador.

2. Inicie sesión en la nube de Azure adecuada y en el inquilino de Azure AD con la cuenta de Azure que ha usado para registrar Azure Stack Hub. Para agregar la cuenta, en PowerShell ejecute `Add-AzureRMureRmAccount`:

   ```powershell  
   Login-AzureRMAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```

   Se le solicita que escriba las credenciales de su cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases en función de la configuración de la cuenta.

   > [!NOTE]
   > Si la sesión expira, la contraseña ha cambiado o desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión mediante `Add-AzureRMRmAccount`: `RemoveAzAccount -Scope Process`.

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la que ha usado para el registro:

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRMSubscription
   ```

4. Si aún no lo ha hecho en el paso de requisitos previos, descargue la versión más reciente de la herramienta de redifusión de Marketplace:

   ```powershell
   Install-Module -Name Azs.Syndication.Admin -RequiredVersion 0.1.140
   ```

5. Para seleccionar elementos de Marketplace como imágenes de máquinas virtuales, extensiones o plantillas de soluciones para descargar, ejecute el siguiente comando:

   ```powershell
   $products = Select-AzureRMsMarketplaceItem
   ```

   Esto hará que aparezca una tabla que muestra todos los registros de Azure Stack disponibles en la suscripción seleccionada. Elija el registro que coincida con el entorno de Azure Stack para el que está descargando los elementos de Marketplace y seleccione **Aceptar**.

     ![Captura de pantalla que muestra una lista de todos los registros de Azure Stack disponibles en la suscripción seleccionada.](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Ahora debería ver una segunda tabla en la que se enumeran todos los elementos de Marketplace que están disponibles para su descarga. Seleccione el elemento que desea descargar y tome nota de la **versión**. Puede mantener presionada la tecla **Ctrl** para seleccionar varias imágenes.
     ![Captura de pantalla que muestra otra lista de todos los registros de Azure Stack disponibles en la suscripción seleccionada.](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   También puede filtrar la lista de imágenes mediante la opción **Agregar criterios**.
   ![Selección de registros de Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Cuando termine las selecciones, seleccione Aceptar.

6. Los identificadores de los elementos de Marketplace que ha seleccionado para su descarga se guardan en la variable `$products`. Use el comando siguiente para empezar a descargar los elementos seleccionados. Reemplace la ruta de acceso de la carpeta de destino por una ubicación para almacenar los archivos que descargue desde Azure Marketplace:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. El tiempo que tarda la descarga depende del tamaño del elemento. Una vez que se completa la descarga, el elemento está disponible en la carpeta que se especificó en el script. La descarga incluye un archivo VHD (para máquinas virtuales) o un archivo .zip (para extensiones de máquina virtual y proveedores de recursos). También puede incluir un paquete de galería en el formato *.azpkg*, que es un archivo .zip.

8. Si se produce un error en la descarga, puede intentarlo de nuevo volviendo a ejecutar el siguiente cmdlet de PowerShell:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. También debe exportar el módulo **Azs.Syndication.Admin** de forma local para que pueda copiarlo en la máquina desde la que va a importar los elementos de Marketplace hacia Azure Stack Hub.

   > [!NOTE]
   > La carpeta de destino para la exportación de este módulo debe ser diferente de la ubicación a la que ha exportado los elementos de Marketplace.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

---



## <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Importación de la descarga y publicación en Marketplace de Azure Stack Hub con PowerShell

1. Debe mover los archivos que [descargó anteriormente](#use-the-marketplace-syndication-tool-to-download-marketplace-items) de forma local a una máquina que tenga conexión al entorno de Azure Stack Hub. La herramienta de redifusión de Marketplace debe estar disponible también en su entorno de Azure Stack Hub, ya que necesita usarla para realizar la operación de importación.

   En la siguiente imagen muestra un ejemplo de estructura de carpeta. **D:\downloadfolder** contiene todos los elementos de Marketplace descargados. Cada subcarpeta es un elemento de Marketplace (por ejemplo, **microsoft.custom-script-linux-arm-2.0.3**) y tienen como nombre el identificador del producto. Dentro de cada subcarpeta se encuentra el contenido descargado del elemento de Marketplace.

   ![Estructura de directorios de descarga de Marketplace](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Siga las instrucciones de [este artículo](azure-stack-powershell-configure-admin.md) para configurar la sesión de PowerShell para un operador de Azure Stack Hub.

3. Inicie sesión en Azure Stack Hub con una identidad que tenga acceso de propietario a la "Suscripción de proveedor predeterminada".

4. Importe el módulo de redifusión e inicie la herramienta de redifusión de Marketplace mediante la ejecución del script siguiente:

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. Después de que el script se complete correctamente, el elemento de Marketplace debe estar disponible en el Marketplace de Azure Stack Hub.
::: zone-end
