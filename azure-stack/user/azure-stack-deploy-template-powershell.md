---
title: Implementación de una plantilla mediante PowerShell en Azure Stack Hub
description: Implemente una plantilla mediante PowerShell en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: a5bd582cd93a95f662a8acc2094e6a62a7ecdf50
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546912"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Implementación de una plantilla mediante PowerShell en Azure Stack Hub

Puede usar PowerShell para implementar plantillas de Azure Resource Manager en Azure Stack Hub. En este artículo, se describe cómo usar PowerShell para implementar una plantilla.

## <a name="run-az-powershell-cmdlets"></a>Ejecución de cmdlets de PowerShell Az

En este ejemplo, se utilizan cmdlets de PowerShell **Az** y una plantilla almacenada en GitHub. La plantilla crea una máquina virtual del centro de datos para Windows Server 2012 R2.

>[!NOTE]
> Antes de probar este ejemplo, asegúrese de que se haya [configurado PowerShell](azure-stack-powershell-configure-user.md) para un usuario de Azure Stack Hub.

1. Examine el [repositorio AzureStack-QuickStart-Templates](https://aka.ms/AzureStackGitHub) y busque la plantilla **101-simple-windows-vm**. Guarde la plantilla en esta ubicación: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Abra un símbolo del sistema de PowerShell con privilegios elevados.
3. En el siguiente script, sustituya `username` y `password` por su nombre de usuario y contraseña y, a continuación, ejecute el script:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Incremente el valor del parámetro `$myNum` cada vez que ejecute este script para evitar sobrescribir la implementación.

4. Abra el portal de Azure Stack Hub, seleccione **Examinar**, luego seleccione **Máquinas virtuales** y busque su nueva máquina virtual (**myDeployment001**).

## <a name="cancel-a-running-template-deployment"></a>Cancelación de una implementación de la plantilla en ejecución

Para cancelar la implementación de una plantilla en ejecución, use el cmdlet `Stop-AzResourceGroupDeployment` de PowerShell.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de una plantilla con Visual Studio](azure-stack-deploy-template-visual-studio.md)
