---
title: Guía de usuario de la herramienta de conmutación por recuperación de Azure Site Recovery
description: Aprenda a usar la herramienta de conmutación por recuperación de Azure Site Recovery para proteger máquinas virtuales.
author: sethmanheim
ms.author: sethm
ms.date: 11/19/2020
ms.topic: how-to
ms.reviewer: rtiberiu
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: 0cb3bccab11d337a8a8804578233edb95ac02dc6
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517232"
---
# <a name="azure-site-recovery-failback-tool"></a>Herramienta de conmutación por recuperación de Azure Site Recovery

En un entorno conectado, puede usar Azure Site Recovery para proteger las máquinas virtuales que se ejecutan en Azure Stack Hub. En [este artículo](/azure/site-recovery/azure-stack-site-recovery) se describe cómo configurar el entorno y cómo Site Recovery contribuye a la estrategia general de recuperación ante desastres y continuidad empresarial para estas cargas de trabajo.

En caso de una interrupción, el operador de Azure Stack Hub pasa por un procedimiento de *conmutación por error*; una vez que Azure Stack Hub vuelve a estar en funcionamiento, pasa por un proceso de *conmutación por recuperación*. El proceso de conmutación por error se describe en [este artículo de Site Recovery](/azure/site-recovery/azure-stack-site-recovery), pero el proceso de conmutación por recuperación implica varios pasos manuales:

1. Detenga la máquina virtual en ejecución en Azure.
2. Descargue los discos duros virtuales.
3. Cárguelos en Azure Stack Hub.
4. Vuelva a crear las máquinas virtuales.
5. Por último, inicie la máquina virtual que está en ejecución en Azure Stack Hub. 

Como este proceso puede ser proclive a errores y llevar mucho tiempo, hemos creado scripts para ayudar a acelerarlo y automatizarlo.

> [!Note]  
> La herramienta Azure Site Recovery requiere los módulos Az de Azure Stack Hub. Si ejecuta los módulos de AzureRM de Azure Stack Hub, tendrá que actualizar la estación de trabajo o usar la herramienta de conmutación por error de Azure Site Recovery en un entorno aislado con los módulos Az. Para obtener más información, consulte [Instalación del módulo Az de PowerShell para Azure Stack Hub](powershell-install-az-module.md).

## <a name="failback-procedure"></a>Procedimiento de conmutación por recuperación

El proceso de conmutación por recuperación automatizado contiene tres partes principales:

- **Copy-AzSiteRecoveryVmVHD**:
  - Apaga la máquina virtual de Azure.
  - Prepara la exportación del disco.
  - Copia el disco mediante AzCopy o StorageBlobCopy.
  - Carga el disco en una cuenta de almacenamiento de Azure Stack Hub.

- Una vez copiado el disco, se incluyen dos escenarios mediante **Prepare-AzSiteRecoveryVMFailBack**:
  - Se ha recuperado la instancia de Azure Stack Hub original. La máquina virtual original sigue existiendo y solo tiene que cambiar sus discos duros virtuales.
  - En caso de desastre, si se pierden las máquinas virtuales originales, deberá volver a generar toda la máquina virtual.

  En este procedimiento se incluyen ambos escenarios mediante la creación de la plantilla y el archivo de parámetros necesarios.

- La implementación real de la plantilla de Azure Resource Manager usa el archivo de parámetros e implementa o crea la máquina virtual en Azure Stack Hub.

### <a name="prerequisites"></a>Requisitos previos

Los siguientes requisitos previos son necesarios para realizar el procedimiento de conmutación por recuperación:

- Copie la [herramienta de conmutación por recuperación de Azure Site Recovery](https://aka.ms/azshasr).

- Importe el módulo FailbackTool.psm1 en PowerShell.

- Siga el procedimiento descrito en [este artículo para instalar el Módulo Az para Azure Stack Hub](powershell-install-az-module.md).

- (Opcional) [Descargue la versión 10 de AzCopy](/azure/storage/common/storage-use-azcopy-v10).

  - Copiar el blob con **AzCopy** es más rápido, pero requiere espacio adicional en el disco local para almacenar temporalmente el archivo de blob.
  - Si no se utiliza **AzCopy**, la copia del disco duro virtual se realiza mediante **AzStorageBlobCopy**. Esto significa que no se requiere almacenamiento local, pero el proceso tarda más tiempo.

- Acceda a los recursos en Azure Portal y acceda para crear estos recursos en Azure Stack Hub.

## <a name="step-1-copy-blob-from-azure-to-azure-stack-hub"></a>Paso 1: Copia de blobs de Azure en Azure Stack Hub

Llame al cmdlet **Copy-AzSiteRecoveryVmVHD** de PowerShell para detener la máquina virtual de Azure, descargar los discos duros virtuales de Azure y cargarlos en Azure Stack Hub. Por ejemplo:

```powershell
$uris = Copy-AzSiteRecoveryVmVHD `
        -SourceVM $vmOnAzure `
        -TargetStorageAccountName "targetaccountName" `
        -TargetStorageEndpoint "redmond.ext-v.masd.stbtest.microsoft.com" `
        -TargetStorageAccountKey $accountKey `
        -AzCopyPath "C:\azcopy_v10\azcopy.exe" `
        -VhdLocalFolder "C:\tempfolder"
```

Tenga en cuenta las siguientes consideraciones:

- En este ejemplo se usa `$uris` para contener el valor `SourceDiskVhdUris` que se utiliza en el paso 2.

- El parámetro `-SourceVM` es un objeto de máquina virtual recuperado por `Get-AzVM`.
  - Esta es la máquina virtual protegida de Azure Stack Hub que se ha conmutado por error en Azure.
  - No importa si la máquina virtual se está ejecutando, ya que el script la apagará. Sin embargo, se recomienda apagarla explícitamente y detener, por consiguiente, los servicios de la máquina virtual.

- Puede proporcionar una clave de cuenta (mediante `TargetStorageAccountKey`) o el token de SAS (mediante `TargetStorageAccountSasToken`) de la cuenta de almacenamiento en el lado de Azure Stack Hub. El token de SAS se debe crear en el nivel de la cuenta de almacenamiento, con al menos los siguientes permisos:

   :::image type="content" source="media/site-recovery-failback/sasperms.png" alt-text="Permisos de token de SAS":::

- Puede proporcionar el punto de conexión de almacenamiento, que incluye la región y el FQDN como, por ejemplo, `regionname.azurestack.microsoft.com`, o un nombre de entorno de Azure Stack Hub, como `AzureStackTenant`. Si se usa el nombre del entorno, debe aparecer mediante **Get-AzEnvironment**.

- Puede optar por usar **AzCopy** o **AzStorageBlobCopy** para copiar el disco duro virtual de Azure en Azure Stack Hub. **AzCopy** es más rápido, pero primero debe descargar los archivos del disco duro virtual en una carpeta local:
  - Para usar **AzCopy**, proporcione los parámetros `-AzCopyPath` y `-VhdLocalFolder` (la ruta de acceso donde se copiarán los discos duros virtuales).
  - Si no hay espacio suficiente localmente, puede elegir copiar el disco duro virtual directamente, sin **AzCopy**, omitiendo los parámetros `-AzCopyPath` y `-VhdLocalFolder`. De forma predeterminada, este comando usa **AzStorageBlobCopy** para copiar directamente en la cuenta de almacenamiento de Azure Stack Hub.

## <a name="step-2-generate-resource-manager-templates"></a>Paso 2: 1 - Generación de plantillas de Azure Resource Manager

Una vez copiado el disco, use el cmdlet **Prepare-AzSiteRecoveryVMFailBack** para crear el `$templateFile` y `$parameterFile` necesarios para implementar la máquina virtual en Azure Stack Hub:

```powershell
$templateFile, $parameterFile = Prepare-AzSiteRecoveryVMFailBack `
                                -SourceContextName "PublicAzure" `
                                -SourceVM $vmOnAzure `
                                -SourceDiskVhdUris $uris `
                                -TargetResourceLocation "redmond" `
                                -ArmTemplateDestinationPath "C:\ARMtemplates" `
                                -TargetVM $vmOnHub `
                                -TargetContextName "AzureStack"

```

Tenga en cuenta las siguientes consideraciones:

- En este ejemplo se usa `-SourceDiskVhdUris` como valor devuelto del paso 1 (mediante `$uris`).

- Este cmdlet admite dos escenarios:
  - Al especificar `-TargetVM`, se supone que la máquina virtual está activa en el lado de Azure Stack Hub y desea reemplazar sus discos por los más recientes copiados de Azure.
  - El script genera una plantilla de Resource Manager para implementar esta máquina virtual y elimina la máquina virtual existente de Azure Stack Hub.
  
  > [!NOTE]
  > La eliminación de la propia máquina virtual de Azure Stack Hub no quita los demás objetos (como VNET, el grupo de recursos o los grupos de seguridad de red). Solo quita el recurso de la máquina virtual en sí y, a continuación, la plantilla se implementa con el parámetro `-incremental`.

  - Al no proporcionar el parámetro `-TargetVM`, el script da por supuesto que la máquina virtual ya no existe en el lado de Azure Stack Hub y crea una plantilla de Resource Manager para implementar una máquina virtual completamente nueva.

- Los archivos de la plantilla de Resource Manager generados se colocan en `-ArmTemplateDestinationPath` y se devuelve la ruta de acceso completa del archivo de plantilla o el archivo de parámetros.

- Si se proporciona el parámetro `-TargetVM`, el cmdlet elimina la máquina virtual, por lo que puede continuar con los pasos siguientes.

## <a name="step-3-deploy-the-resource-manager-template"></a>Paso 3: Implementación de la plantilla de Resource Manager

En este momento, el disco duro virtual se carga en Azure Stack Hub y se crea la plantilla de Resource Manager y los archivos de parámetros respectivos. Lo único que queda es implementar la máquina virtual en Azure Stack Hub.

En algunos escenarios, es posible que quiera editar esta plantilla y agregar, quitar o cambiar algunos nombres o recursos. Esto está permitido, ya que puede editar y ajustar la plantilla según sea necesario.

Cuando esté listo, y después de confirmar que los recursos de la plantilla de Resource Manager son los esperados, puede llamar al cmdlet **New-AzResourceGroupDeployment** para implementar los recursos. Por ejemplo:

```powershell
New-AzResourceGroupDeployment `
  -Name "Failback" `
  -ResourceGroupName "failbackrg" `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile `
  -Mode Incremental
```

Tenga en cuenta las siguientes consideraciones:

- El parámetro `-ResourceGroupName` debe ser un grupo de recursos existente.
- Los parámetros `-TemplateFile` y `-TemplateParameterFile` provienen de los valores devueltos en el paso 2.

## <a name="next-steps"></a>Pasos siguientes

- [Características de las máquinas virtuales de Azure Stack Hub](../user/azure-stack-vm-considerations.md)
- [Adición y eliminación de una imagen de máquina virtual personalizada a Azure Stack Hub](azure-stack-add-vm-image.md)
- [Creación de una máquina virtual Windows mediante PowerShell en Azure Stack Hub](../user/azure-stack-quick-create-vm-windows-powershell.md)