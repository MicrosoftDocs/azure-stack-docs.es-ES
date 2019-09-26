---
title: Adición de trabajos e infraestructura en App Service en Azure Stack | Microsoft Docs
description: Instrucciones detalladas de escalado de App Services en Azure Stack
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: b01199bfe96c39fe79aac65eca219a065f39375c
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70975006"
---
# <a name="add-workers-and-infrastructure-in-app-service-on-azure-stack"></a>Adición de trabajos e infraestructura en App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*  

Este documento proporciona instrucciones acerca de cómo escalar la infraestructura y los roles de trabajo de App Service en Azure Stack. Se incluyen todos los pasos necesarios para crear roles de trabajo adicionales que permitan admitir aplicaciones de cualquier tamaño.

> [!NOTE]
> Si el entorno de Azure Stack no tiene más de 96 GB de RAM, puede encontrar dificultades al agregar capacidad adicional.

De forma predeterminada, App Service en Azure Stack es compatible con los niveles de trabajo gratuito y compartido. Para agregar otros niveles de trabajo, debe agregar más roles de trabajo.

Si no está seguro de qué se implementó con la instalación predeterminada de App Service en Azure Stack, puede revisar la información de [Introducción a App Service en Azure Stack](azure-stack-app-service-overview.md).

Azure App Service en Azure Stack implementa todos los roles mediante conjuntos de escalado de máquinas virtuales y así aprovecha las ventajas de las funcionalidades de escalado de esta carga de trabajo. Por lo tanto, el escalado de los niveles de trabajo se realiza mediante el administrador de App Service.

## <a name="add-additional-workers-with-powershell"></a>Adición de roles de trabajo adicionales con PowerShell

1. [Configure el entorno de administración de Azure Stack con PowerShell](azure-stack-powershell-configure-admin.md).

2. Use este ejemplo para escalar horizontalmente el conjunto de escalado:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Este paso puede tardar algunas horas en completarse en función del tipo de rol y del número de instancias.
   >
   >

3. Supervise el estado de las nuevas instancias de rol en la administración de App Service. Para comprobar el estado de una instancia de rol individual, haga clic en el tipo de rol en la lista.

## <a name="add-additional-workers-using-the-administrator-portal"></a>Adición de trabajos adicionales mediante el portal del administrador

1. Inicie sesión en el portal de administración de Azure Stack como administrador del servicio.

2. Vaya a **App Services**.

    ![Portal de administración de App Service en Azure Stack](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Haga clic en **Roles**. Aquí verá el desglose de los roles de App Service implementados.

4. Haga clic con el botón derecho en la fila del tipo que desea escalar y, a continuación, haga clic en **ScaleSet**.

    ![Conjunto de escalado de roles de App Service en el portal de administración de Azure Stack](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Haga clic en **Escalado**, seleccione el número de instancias a las que desea escalar y, a continuación, haga clic en **Guardar**.

    ![Establecimiento de instancias para escalar en los roles de App Service del portal de administración de Azure Stack](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service en Azure Stack ahora incorporará máquinas virtuales adicionales, las configurará, instalará el software necesario y las marcará como listas cuando finalice el proceso, que puede tardar unos 80 minutos.

7. Puede supervisar el progreso de la disponibilidad de los nuevos roles desde la hoja **Roles**.

## <a name="result"></a>Resultado

Cuando estén totalmente implementados y listos, los roles de trabajo estarán disponibles para que los usuarios implementen su carga de trabajo en ellos. La siguiente captura de pantalla muestra un ejemplo de los distintos niveles de precios predeterminados disponibles. Si no hay ningún rol de trabajo disponible para un nivel de trabajo concreto, la opción para elegir el nivel de precios correspondiente tampoco lo estará.

![Planes de tarifa para el nuevo plan de App Service en el portal de administración de Azure Stack](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Para escalar horizontalmente los roles de administración, front-end o publicador, siga los mismos pasos seleccionando el tipo de rol adecuado. Los controladores no se implementan como conjuntos de escalado y, por tanto, dos deben implementarse en tiempo de instalación para todas las implementaciones de producción.

### <a name="next-steps"></a>Pasos siguientes

[Configuración de orígenes de implementación](azure-stack-app-service-configure-deployment-sources.md)
