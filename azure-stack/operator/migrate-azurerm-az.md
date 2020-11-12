---
title: Migración de scripts de Azure PowerShell de AzureRM a Az en Azure Stack Hub
description: Obtenga información sobre los pasos y herramientas para migrar los scripts desde el módulo AzureRM al nuevo módulo Az en Azure Stack Hub.
author: mattbriggs
ms.author: mabrigg
ms.topic: conceptual
ms.date: 10/05/2020
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: bfb385e29a89ad8d47ace3b15776bd368e0cf521
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546178"
---
# <a name="migrate-from-azurerm-to-azure-powershell-az-in-azure-stack-hub"></a>Migración desde AzureRM al módulo Az de Azure PowerShell en Azure Stack Hub

El módulo Az incluye paridad de características con AzureRM, pero usa nombres de cmdlet más cortos y más coherentes.
Los scripts escritos para los cmdlets de AzureRM no funcionan automáticamente con el nuevo módulo. Para facilitar la transición, Az ofrece herramientas que le permiten ejecutar los scripts existentes con AzureRM. No es conveniente nunca una migración a un nuevo conjunto de comandos, pero este artículo le ayudará a empezar a trabajar en la transición al nuevo módulo.

Para ver la lista completa de los cambios importantes entre AzureRM y Az, consulte la [Guía de migración de Az 1.0.0](/powershell/azure/migrate-az-1.0.0).

## <a name="check-for-installed-versions-of-azurerm"></a>Consulte las versiones instaladas de AzureRM

Antes de llevar a cabo los pasos de la migración, compruebe qué versiones de AzureRM están instaladas en el sistema. Así podrá asegurarse de que ya se están ejecutando las últimas versiones de los scripts y saber si puede habilitar los alias de comandos sin desinstalar AzureRM.

Para comprobar qué versiones de AzureRM tiene instaladas, ejecute el comando:

```powershell-interactive
Get-InstalledModule -Name AzureRM -AllVersions
```

## <a name="check-current-scripts-work-with-azurerm"></a>Comprobación de que los scripts actuales funcionan con AzureRM

Este es el paso más importante. Ejecute los scripts existentes y asegúrese de que funcionan con la versión _más reciente_ de AzureRM ( __2.5.0__ ). Si los scripts no funcionan, no olvide leer la [Guía de migración de AzureRM](/powershell/azure/azurerm/migration-guide.6.0.0).

## <a name="install-the-azure-powershell-az-module"></a>Instalación del módulo Az de Azure PowerShell

El primer paso es instalar el módulo Az en la plataforma. Para instalar Az, se recomienda desinstalar AzureRM. En los pasos siguientes, obtendrá información sobre cómo seguir ejecutando los scripts existentes y habilitar la compatibilidad con los nombres de cmdlet antiguos.

Para instalar el módulo Az de Azure PowerShell, siga estos pasos:

* __RECOMENDACIÓN__ : [Desinstale el módulo AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module).
  Asegúrese de eliminar _todas_ las versiones instaladas de AzureRM, no solo la última versión.
* [Instalación del módulo Az](/powershell/azure/install-az-ps)

## <a name="enable-azurerm-compatibility-aliases"></a>Habilitación de los alias de compatibilidad de AzureRM 

> [!IMPORTANT]
>
> Habilite el modo de compatibilidad solo si ha desinstalado _todas_ versiones de AzureRM. Habilitar el modo de compatibilidad con los cmdlets de AzureRM aún disponibles puede provocar un comportamiento impredecible. Omita este paso si ha decidido mantener AzureRM instalado, pero tenga en cuenta que los cmdlets de AzureRM utilizarán los módulos anteriores y no llamarán a los cmdlets de Az.

Con AzureRM desinstalado y los scripts con la versión más reciente de AzureRM, es el momento de habilitar el modo de compatibilidad para el módulo Az. La compatibilidad se habilita con el comando:

```powershell-interactive
Enable-AzureRmAlias -Scope CurrentUser
```

Los alias permiten usar los nombres de cmdlet anteriores con el módulo Az instalado. Estos alias se escriben en el perfil de usuario para el ámbito seleccionado. Si no existe ningún perfil de usuario, se crea uno.

> [!WARNING]
>
> Puede usar otro valor de `-Scope` para este comando, pero no se recomienda. Los alias se escriben en el perfil de usuario para el ámbito seleccionado, por lo tanto, mantenga su habilitación en un ámbito tan limitado como sea posible. Habilitar los alias en todo el sistema también podría causar problemas para otros usuarios que tengan instalado AzureRM en su ámbito local.

Una vez activado el modo de alias, ejecute los scripts para confirmar que siguen funcionando según lo previsto. 

## <a name="change-module-and-cmdlet-names"></a>Cambio de nombres de los módulos y cmdlets

En general, los nombres de módulo se han cambiado para que `AzureRM` y `Azure` se conviertan en `Az` e igual para los cmdlets.
Por ejemplo, el nombre del módulo `AzureRM.Compute` ha cambiado a `Az.Compute`. `New-AzureRMVM` se ha convertido en `New-AzVM` y `Get-AzureStorageBlob` ahora es `Get-AzStorageBlob`.

Hay excepciones a este cambio de nomenclatura que debe tener en cuenta. Algunos módulos se han cambiado de nombre o se han combinado con los módulos existentes sin que esto afecte al sufijo de sus cmdlets, aparte de cambiar de `AzureRM` o `Azure` a `Az`. En los demás, se ha cambiado el sufijo completo del cmdlet para reflejar el nuevo nombre de módulo.

| Módulo AzureRM | Módulo Az | ¿Ha cambiado el sufijo del cmdlet? |
|----------------|-----------|------------------------|
| AzureRM.Profile | Az.Accounts | Sí |
| AzureRM.Insights | Az.Monitor | Sí |
| AzureRM.Tags | Az.Resources | No |
| AzureRM.UsageAggregates | Az.Billing | No |
| AzureRM.Consumption | Az.Billing | No |

## <a name="summary"></a>Resumen

Con estos pasos puede actualizar todos los scripts existentes para que utilicen el nuevo módulo. Si tiene alguna pregunta o problemas con estos pasos que dificultan la migración, comente este artículo para que podamos mejorar las instrucciones.

## <a name="breaking-changes-for-az-100"></a>Cambios importantes en Az 1.0.0

Este documento proporciona información detallada sobre los cambios entre AzureRM 6.x y el nuevo módulo Az versión 1.x y versiones posteriores. La tabla de contenido le guiará por la ruta de migración completa, incluidos los cambios específicos del módulo que pueden afectar a los scripts.

## <a name="general-breaking-changes"></a>Cambios importantes generales

En esta sección se describen los cambios importantes generales que forman parte de este nuevo diseño del módulo Az.

### <a name="cmdlet-noun-prefix-changes"></a>Cambios de prefijo en el nombre de los cmdlets

En el módulo AzureRM, los cmdlets usan `AzureRM` o `Azure` como un prefijo de sustantivo.  Para simplificar y normalizar los nombres de los cmdlets, todos usan "Az" como prefijo del nombre. Por ejemplo:

```powershell  
Get-AzureRMVM
Get-AzureKeyVaultSecret
```

Ha cambiado a:

```powershell  
Get-AzVM
Get-AzKeyVaultSecret
```

Para facilitar la transición a estos nuevos nombres de cmdlet, Az incorpora dos nuevos cmdlets, [Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias) y [Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias).  `Enable-AzureRmAlias` crea un alias para los nombres de cmdlet antiguos en AzureRM que se asignan a los nombres de cmdlet de Az más recientes. El argumento `-Scope` con `Enable-AzureRmAlias` le permite elegir dónde se habilitan los alias.

Por ejemplo, el siguiente script de AzureRM:

```powershell  
#Requires -Modules AzureRM.Storage
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Se puede ejecutar con cambios mínimos con `Enable-AzureRmAlias`:

```powershell  
#Requires -Modules Az.Storage
Enable-AzureRmAlias -Scope Process
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Al ejecutar `Enable-AzureRmAlias -Scope CurrentUser`, se habilitarán los alias para todas las sesiones de PowerShell que abra por lo que, después de ejecutar este cmdlet, no sería necesario realizar ningún cambio en un script como este:

```powershell  
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Para obtener información detallada sobre el uso de los cmdlets de alias, consulte la [Referencia de Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias).

Cuando esté listo para deshabilitar los alias, `Disable-AzureRmAlias` quita los alias creados. Para obtener información detallada, consulte la [Referencia de Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias).

> [!IMPORTANT]
> Al deshabilitar los alias, asegúrese de que se deshabilitan para _todos_ los ámbitos en los que han estado habilitados.

### <a name="module-name-changes"></a>Cambio de nombre de los módulos

Los nombres de los módulo han cambiado de `AzureRM.*` a `Az.*`, excepto los siguientes módulos:

| Módulo AzureRM | Módulo Az |
|----------------|-----------|
| Azure.Storage | Az.Storage |
| Azure.AnalysisServices | Az.AnalysisServices |
| AzureRM.Profile | Az.Accounts |
| AzureRM.Insights | Az.Monitor |
| AzureRM.RecoveryServices.Backup | Az.RecoveryServices |
| AzureRM.RecoveryServices.SiteRecovery | Az.RecoveryServices |
| AzureRM.Tags | Az.Resources |
| AzureRM.MachineLearningCompute | Az.MachineLearning |
| AzureRM.UsageAggregates | Az.Billing |
| AzureRM.Consumption | Az.Billing |

El cambio de nombre de los módulos implica que todos los scripts que usen `#Requires` o `Import-Module` para cargar módulos específicos deben cambiarse para que usen el nuevo módulo en su lugar. En el caso de los módulos en los que no ha cambiado el sufijo del cmdlet, esto significa que, aunque ha cambiado el nombre del módulo, _no ha cambiado_ el sufijo que indica el espacio de la operación.

#### <a name="migrating-requires-and-import-module-statements"></a>Migración de las instrucciones #Requires e Import-Module

Es necesario actualizar los scripts que usan `#Requires` o `Import-Module` para declarar una dependencia en los módulos AzureRM para que usen los nuevos nombres de módulos. Por ejemplo:

```powershell  
#Requires -Module AzureRM.Compute
```

Se debe cambiar a:

```powershell  
#Requires -Module Az.Compute
```

Para `Import-Module`:

```powershell  
Import-Module -Name AzureRM.Compute
```

Se debe cambiar a:

```powershell  
Import-Module -Name Az.Compute
```

### <a name="migrating-fully-qualified-cmdlet-invocations"></a>Migración de las invocaciones de cmdlet completas

Los scripts que usan las invocaciones de cmdlets calificadas para el módulo, como esta:

```powershell  
AzureRM.Compute\Get-AzureRmVM
```

Se deben cambiar para que usen los nuevos nombres de módulos y cmdlets:

```powershell  
Az.Compute\Get-AzVM
```

### <a name="migrating-module-manifest-dependencies"></a>Migración de las dependencias de manifiesto de módulo

Los módulos que expresan dependencias en módulos AzureRM mediante un archivo de manifiesto de módulo (. psd1) deben actualizar los nombres de los módulos en su sección `RequiredModules`:

```powershell
RequiredModules = @(@{ModuleName="AzureRM.Profile"; ModuleVersion="5.8.2"})
```

Se deben cambiar a:

```powershell
RequiredModules = @(@{ModuleName="Az.Accounts"; ModuleVersion="1.0.0"})
```

### <a name="removed-modules"></a>Módulos eliminados

Se han eliminado los siguientes módulos:

- `AzureRM.Backup`
- `AzureRM.Compute.ManagedService`
- `AzureRM.Scheduler`

Las herramientas de estos servicios ya no tiene un soporte técnico activo.  Se recomienda a los clientes que cambien a otros servicios tan pronto como les sea posible.

### <a name="windows-powershell-51-and-net-472"></a>Windows PowerShell 5.1 y .NET 4.7.2

Para usar Az con PowerShell 5.1 para Windows, es necesario instalar .NET Framework 4.7.2. El uso de PowerShell Core 6.x o versiones posteriores no requiere .NET Framework.

### <a name="temporary-removal-of-user-login-using-pscredential"></a>Eliminación temporal del inicio de sesión de usuario con PSCredential

Debido a los cambios en el flujo de autenticación de .NET Standard, hemos quitando temporalmente el inicio de sesión de usuario mediante PSCredential. Esta funcionalidad se volverá a incorporar a PowerShell 5.1 para Windows en la versión del 15/1/2019. En [esta incidencia de GitHub](https://github.com/Azure/azure-powershell/issues/7430) se trata este asunto detalladamente.

### <a name="default-device-code-login-instead-of-web-browser-prompt"></a>Inicio de sesión predeterminado con código del dispositivo en lugar del símbolo del sistema del explorador web

Debido a los cambios en el flujo de autenticación de .NET Standard, estamos usando el inicio de sesión de dispositivo como flujo de inicio de sesión predeterminado durante el inicio de sesión interactivo. El inicio de sesión basado en el explorador web se volverá a incorporar en PowerShell 5.1 para Windows como configuración predeterminada en la versión del 15/1/2019. En ese momento, los usuarios podrán elegir iniciar sesión de dispositivo con un parámetro Switch.

## <a name="module-breaking-changes"></a>Cambios importantes en los módulos

En esta sección se describen cambios importantes específicos para módulos y cmdlets individuales.

### <a name="azapimanagement-previously-azurermapimanagement"></a>Az.ApiManagement (anteriormente AzureRM.ApiManagement)

- Se han eliminado los siguientes cmdlets:
  - New-AzureRmApiManagementHostnameConfiguration
  - Set-AzureRmApiManagementHostnames
  - Update-AzureRmApiManagementDeployment
  - Import-AzureRmApiManagementHostnameCertificate
  - En su lugar, use el cmdlet **Set-AzApiManagement** para establecer estas propiedades
- Se han eliminado las siguientes propiedades:
  - Se han quitado las propiedades `PortalHostnameConfiguration`, `ProxyHostnameConfiguration`, `ManagementHostnameConfiguration` y `ScmHostnameConfiguration` del tipo `PsApiManagementHostnameConfiguration` de `PsApiManagementContext`. En su lugar, use `PortalCustomHostnameConfiguration`, `ProxyCustomHostnameConfiguration`, `ManagementCustomHostnameConfiguration` y `ScmCustomHostnameConfiguration` del tipo `PsApiManagementCustomHostNameConfiguration`.
  - Se ha quitado la propiedad `StaticIPs` de PsApiManagementContext. La propiedad se ha dividido en `PublicIPAddresses` y `PrivateIPAddresses`.
  - Se ha quitado la propiedad necesaria `Location` del cmdlet New-AzureApiManagementVirtualNetwork.

### <a name="azbilling-previously-azurermbilling-azurermconsumption-and-azurermusageaggregates"></a>Az.Billing (anteriormente AzureRM.Billing, AzureRM.Consumption y AzureRM.UsageAggregates)

- Se ha quitado el parámetro `InvoiceName` del cmdlet `Get-AzConsumptionUsageDetail`.  Los scripts debe usar otros parámetros de la identidad para la factura.

### <a name="azcompute-previously-azurermcompute"></a>Az.Compute (anteriormente AzureRM.Compute)

- Se han quitado `IdentityIds` de la propiedad `Identity` en los objetos `PSVirtualMachine` y `PSVirtualMachineScaleSet`. Los scripts ya no deberán usar el valor de este campo para tomar decisiones de procesamiento.
- El tipo de la propiedad `InstanceView` del objeto `PSVirtualMachineScaleSetVM` ha cambiado de `VirtualMachineInstanceView` a `VirtualMachineScaleSetVMInstanceView`.
- Se han quitado las propiedades `AutoOSUpgradePolicy` y `AutomaticOSUpgrade` de la propiedad `UpgradePolicy`.
- El tipo de la propiedad `Sku` del objeto `PSSnapshotUpdate` ha cambiado de `DiskSku` a `SnapshotSku`.
- `VmScaleSetVMParameterSet` se ha quitado del cmdlet `Add-AzVMDataDisk`; ya no se puede agregar un disco de datos individualmente a una máquina virtual de un conjunto de escalado.

### <a name="azkeyvault-previously-azurermkeyvault"></a>Az.KeyVault (anteriormente AzureRM.KeyVault)

- Se ha quitado la propiedad `PurgeDisabled` de los objetos `PSKeyVaultKeyAttributes`, `PSKeyVaultKeyIdentityItem` y `PSKeyVaultSecretAttributes`. Los scripts ya no deben hacer referencia a la propiedad ```PurgeDisabled``` para tomar decisiones de procesamiento.

### <a name="azmonitor-previously-azurerminsights"></a>Az.Monitor (anteriormente AzureRM.Insights)

- Se han quitado los nombres en plural de los parámetros `Categories` y `Timegrains` en favor de los nombres en singular en el cmdlet `Set-AzDiagnosticSetting`. Los scripts que usen:
  ```powershell  
  Set-AzureRmDiagnosticSetting -Timegrains PT1M -Categories Category1, Category2
  ```

  Se deben cambiar a:
  ```powershell  
  Set-AzDiagnosticSetting -Timegrain PT1M -Category Category1, Category2
  ```

### <a name="aznetwork-previously-azurermnetwork"></a>Az.Network (anteriormente AzureRM.Network)

- Se ha quitado el parámetro en desuso `ResourceId` del cmdlet `Get-AzServiceEndpointPolicyDefinition`.
- Se ha quitado la propiedad en desuso `EnableVmProtection` del objeto `PSVirtualNetwork`.
- Se ha quitado el cmdlet en desuso `Set-AzVirtualNetworkGatewayVpnClientConfig`.

Los scripts no deben tomar decisiones de procesamiento en función de los valores de estos campos.

### <a name="azresources-previously-azurermresources"></a>Az.Resources (anteriormente AzureRM.Resources)

- Se ha quitado el parámetro `Sku` del cmdlet `New/Set-AzPolicyAssignment`.
- Se ha quitado el parámetro `Password` de los cmdlets `New-AzADServicePrincipal` y `New-AzADSpCredential`. Las contraseñas se generan automáticamente. Los scripts que proporcionaban la contraseña:

  ```powershell  
  New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476 -Password $secPassword
  ```

  Se deben cambiar para que tomen la contraseña de la salida:

  ```powershell  
  $credential = New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476
  $secPassword = $credential.Secret
  ```


### <a name="azstorage-previously-azurestorage-and-azurermstorage"></a>Az.Storage (anteriormente Azure.Storage y AzureRM.Storage)

- Para poder crear un contexto de almacenamiento de Oauth solo con el nombre de la cuenta de almacenamiento, se ha cambiado el conjunto de parámetros predeterminados a `OAuthParameterSet`.
  - Ejemplo: `$ctx = New-AzureStorageContext -StorageAccountName $accountName`
- El parámetro `Location` ahora es obligatorio en el cmdlet `Get-AzStorageUsage`.
- Los métodos de Storage API ahora usan el patrón asincrónico basado en tareas (TAP), en lugar de llamadas de API sincrónicas. Los ejemplos siguientes muestran los nuevos comandos asincrónicos:

#### <a name="blob-snapshot"></a>Instantánea de blob

AzureRM:

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$b.ICloudBlob.Snapshot()
```

Az:

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$task = $b.ICloudBlob.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="share-snapshot"></a>Compartir instantánea

AzureRM:

```powershell  
$Share = Get-AzureStorageShare -Name $containerName -Context $ctx
$snapshot = $Share.Snapshot()
```

Az:

```powershell  
$Share = Get-AzStorageShare -Name $containerName -Context $ctx
$task = $Share.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="undelete-soft-deleted-blob"></a>Recuperar los blobs eliminados temporalmente

AzureRM:

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$b.ICloudBlob.Undelete()
```

Az:

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$task = $b.ICloudBlob.UndeleteAsync()
$task.Wait()
```

#### <a name="set-blob-tier"></a>Establecer nivel de blob

AzureRM:

```powershell  
$blockBlob = Get-AzureStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$blockBlob.ICloudBlob.SetStandardBlobTier("hot")

$pageBlob = Get-AzureStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$pageBlob.ICloudBlob.SetPremiumBlobTier("P4")
```

Az:

```powershell  
$blockBlob = Get-AzStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$task = $blockBlob.ICloudBlob.SetStandardBlobTierAsync("hot")
$task.Wait()

$pageBlob = Get-AzStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$task = $pageBlob.ICloudBlob.SetPremiumBlobTierAsync("P4")
$task.Wait()
```

### <a name="azwebsites-previously-azurermwebsites"></a>Az.Websites (anteriormente AzureRM.Websites)

- Se han quitado las propiedades en desuso de los objetos `PSAppServicePlan`, `PSCertificate`, `PSCloningInfo` y `PSSite`.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre PowerShell en Azure Stack Hub, consulte [Introducción a PowerShell en Azure Stack Hub](../user/azure-stack-powershell-overview.md).
- Para más información sobre la instalación del módulo Az de PowerShell, consulte [Instalación del módulo Az de PowerShell para Azure Stack Hub](powershell-install-az-module.md).
