---
title: Uso de plantillas de Administrador de recursos de Azure en Azure Stack Hub
description: Aprenda a usar plantillas de Azure Resource Manager en Azure Stack Hub para aprovisionar recursos.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 11/14/2019
ms.openlocfilehash: 41f83e1e7f1432e182473c3b090171d3c426d340
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111921"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack-hub"></a>Uso de plantillas de Administrador de recursos de Azure en Azure Stack Hub

Puede utilizar las plantillas de Azure Resource Manager para implementar y aprovisionar todos los recursos para su aplicación en una única operación coordinada. También se pueden volver a implementar plantillas para realizar cambios en los recursos de un grupo de recursos.

Estas plantillas se pueden implementar con el portal de Microsoft Azure Stack Hub, PowerShell, la línea de comandos y Visual Studio.

Las siguientes plantillas de inicio rápido están disponibles en [GitHub](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Implementación de SharePoint Server(sin implementación de alta disponibilidad)

Use la extensión DSC ([Desired State Configuration](/powershell/scripting/dsc/overview/overview)) de PowerShell para [crear una granja de SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) que incluya los recursos siguientes:

* Una red virtual
* Tres cuentas de almacenamiento
* Dos equilibradores de carga externos
* Una máquina virtual (VM) configurada como controlador de dominio para un nuevo bosque con un único dominio
* Una máquina virtual configurada como un servidor independiente de SQL Server 2014
* Una máquina virtual configurada como granja de SharePoint Server 2013 de una sola máquina

## <a name="deploy-ad-non-high-availability-deployment"></a>Implementación de AD (sin alta disponibilidad)

Use la extensión DSC de PowerShell para [crear un servidor de controlador de dominio de AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) que incluya los recursos siguientes:

* Una red virtual
* Una cuenta de almacenamiento
* Un equilibrador de carga externo
* Una máquina virtual (VM) configurada como controlador de dominio para un nuevo bosque con un único dominio

## <a name="deploy-adsql-non-high-availability-deployment"></a>Implementación de AD/SQL (sin alta disponibilidad)

Use la extensión DSC de PowerShell para [crear un servidor independiente de SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) que incluya los recursos siguientes:

* Una red virtual
* Dos cuentas de almacenamiento
* Un equilibrador de carga externo
* Una máquina virtual (VM) configurada como controlador de dominio para un nuevo bosque con un único dominio
* Una máquina virtual configurada como un servidor independiente de SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>Extensión DSC de máquina virtual para un servidor de extracción de Azure Automation

Utilice la extensión de DSC de PowerShell para configurar Administrador de configuración Local (LCM) de una máquina virtual existente y registrarlo en un servidor de extracción de DSC de la cuenta de Azure Automation.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Creación de una máquina virtual desde una imagen de usuario

[Cree una máquina virtual desde una imagen de usuario personalizada](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage). Esta plantilla también implementa una red virtual (con DNS), una dirección IP pública y una interfaz de red.

## <a name="basic-virtual-machine"></a>Máquina virtual básica

[Implemente una máquina virtual Windows](https://aka.ms/aa6zdzx) que incluya una red virtual (con DNS), una dirección IP pública y una interfaz de red.

## <a name="cancel-a-running-template-deployment"></a>Cancelación de una implementación de la plantilla en ejecución

Para cancelar la implementación de una plantilla en ejecución, use el [cmdlet](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) de PowerShell [Stop-AzureRmResourceGroupDeployment de PowerShell](/powershell/scripting/developer/cmdlet/cmdlet-overview).

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de plantillas con el portal](azure-stack-deploy-template-portal.md)
* [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
* [Implementación de plantillas con Visual Studio](azure-stack-deploy-template-visual-studio.md)
* [Información general sobre Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
