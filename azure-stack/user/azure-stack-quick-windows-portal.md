---
title: Creación de una máquina virtual Windows con el portal de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo crear una máquina virtual (VM) Windows Server 2016 mediante el portal de Azure Stack.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cc9a6baa3c71e58c2671b1f1b221e18a0c4f38c1
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816164"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>Inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Obtenga información sobre cómo crear una máquina virtual (VM) Windows Server 2016 mediante el portal de Azure Stack.

> [!NOTE]  
> Las capturas de pantalla de este artículo se han actualizado para que coincidan con la interfaz de usuario introducida con la versión de Azure Stack 1808. 1808 agrega compatibilidad para los *discos administrados* además de para los no administrados. Si usa una versión anterior, algunas imágenes como la selección de disco diferirán de las que se muestran en este artículo.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Inicio de sesión en el portal de Azure Stack

Inicie sesión en el portal de Azure Stack. La dirección del portal de Azure Stack depende del producto de Azure Stack al que se vaya a conectar:

* Para obtener el Kit de desarrollo de Azure Stack (ASDK), vaya a: https://portal.local.azurestack.external.
* Para un sistema integrado de Azure Stack, vaya a la dirección URL que proporciona su operador de Azure Stack.

## <a name="create-a-vm"></a>Crear una VM

1. Haga clic en **+ Crear un recurso** > **Proceso** > **Windows Server 2016 Datacenter – Pago por uso** > **Crear**. <br> Si no ve la entrada **Windows Server 2016 Datacenter - Pago por uso**, póngase en contacto con el operador de Azure Stack y pídale que la agregue a Marketplace, tal como se explica en el artículo [Agregar la máquina virtual de Windows Server 2016 a Marketplace de Azure Stack](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Pasos para crear una máquina virtual Windows en el portal](media/azure-stack-quick-windows-portal/image01.png)

2. En **Aspectos básicos**, escriba un **nombre**, un **nombre de usuario** y una **contraseña**. Elija una **suscripción**. Cree un **grupo de recursos**, o seleccione uno ya existente, seleccione una **ubicación** y luego haga clic en **Aceptar**.

    ![Configuración básica](media/azure-stack-quick-windows-portal/image02.png)

3. En **Tamaño**, seleccione **D1 Estándar** y después haga clic en **Seleccionar**.  

    ![Elección del tamaño de la máquina virtual](media/azure-stack-quick-windows-portal/image03.png)

4. En la página **Configuración**, realice los cambios necesarios en los valores predeterminados.
   - A partir de la versión de Azure Stack 1808, puede configurar **Almacenamiento**, donde podrá elegir los *discos administrados*. En las versiones anteriores a la 1808, solo se pueden usar discos no administrados.  

   ![Configuración de la máquina virtual](media/azure-stack-quick-windows-portal/image04.png)  

   Cuando las configuraciones estén preparadas, seleccione **Aceptar** para continuar.

5. En **Resumen**, haga clic en **Aceptar** para crear la máquina virtual.
    ![Visualización del resumen y creación de la máquina virtual](media/azure-stack-quick-windows-portal/image05.png)

6. Para ver la nueva máquina virtual, haga clic en **Todos los recursos**, busque el nombre de la máquina virtual y, a continuación, selecciónela en los resultados de la búsqueda.

    ![Consulta de máquinas virtuales](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado de usar la máquina virtual, elimínela junto con sus recursos. Para ello, seleccione el grupo de recursos en la página de la máquina virtual y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha implementado una máquina virtual básica con Windows Server. Para más información sobre las máquinas virtuales de Azure Stack, continúe con el artículo [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Consideraciones acerca de máquinas virtuales de Azure Stack).
