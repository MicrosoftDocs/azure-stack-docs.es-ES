---
title: Creación de una máquina virtual Windows con el portal de Azure Stack Hub
description: Obtenga información sobre cómo crear una máquina virtual (VM) Windows Server 2016 mediante el portal de Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: c9b2309e5c640d9f2cab79f93dc8a13b1344cea2
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/15/2020
ms.locfileid: "77703764"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>Inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack Hub

Obtenga información sobre cómo crear una máquina virtual (VM) Windows Server 2016 mediante el portal de Azure Stack Hub.

> [!NOTE]  
> Las capturas de pantalla de este artículo se han actualizado para que coincidan con la interfaz de usuario introducida con la versión 1808 de Azure Stack Hub. 1808 agrega compatibilidad para los *discos administrados* además de para los no administrados. Si usa una versión anterior, algunas imágenes como la selección de disco diferirán de las que se muestran en este artículo.  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Inicio de sesión en el portal de Azure Stack Hub

Inicie sesión en el portal de Azure Stack Hub. La dirección del portal de Azure Stack Hub depende del producto de Azure Stack al que se vaya a conectar:

* Para obtener el Kit de desarrollo de Azure Stack (ASDK), vaya a: https://portal.local.azurestack.external.
* Para un sistema integrado de Azure Stack Hub, vaya a la dirección URL que proporciona su operador de Azure Stack Hub.

## <a name="create-a-vm"></a>Crear una VM

1. Seleccione **Crear un recurso** > **Proceso**. Busque` Windows Server 2016 Datacenter – Pay as you use`.
    Si no ve la entrada **Windows Server 2016 Datacenter - Pay-as-you-use**, póngase en contacto con su operador de nube de Azure Stack Hub y pida que se agregue la imagen al Marketplace de Azure Stack Hub. Para obtener instrucciones, el operador de la nube puede consultar [Creación y publicación de un elemento personalizado de Marketplace de Azure Stack Hub](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Windows Server 2016 Datacenter – Pay as you use](./media/azure-stack-quick-windows-portal/image1.png)

1. Seleccione **Crear**.

    ![Creación de un recurso](./media/azure-stack-quick-windows-portal/image2.png)

1. Escriba un **nombre**, **tipo de disco**, **nombre de usuario** y **contraseña** en **Aspectos básicos**. Elija una **suscripción**. Cree un **grupo de recursos**, o seleccione uno ya existente, seleccione una **ubicación** y, después, seleccione **Aceptar**.

    ![Creación de una máquina virtual: Aspectos básicos](./media/azure-stack-quick-windows-portal/image3.png)

1. Seleccione **D1_v2** en **Tamaño** y, después, elija **Seleccionar**.

    ![Creación de una máquina virtual: Tamaño](./media/azure-stack-quick-windows-portal/image4.png)

1. En la página **Configuración**, realice los cambios necesarios en los valores predeterminados. Debe configurar los puertos de entrada públicos deseados en el menú desplegable relacionado. Cuando haya terminado, seleccione **Aceptar**.

    ![Creación de una máquina virtual: Configuración](./media/azure-stack-quick-windows-portal/image5.png)

1. Seleccione **Aceptar** en **Resumen** para crear la máquina virtual.

    ![Creación de una máquina virtual: Resumen](./media/azure-stack-quick-windows-portal/image6.png)

1. Seleccione **Virtual Machines** para revisar la nueva máquina virtual. Busque el nombre de la máquina virtual y, a continuación, seleccione la máquina virtual en los resultados de la búsqueda.

![Creación de una máquina virtual: Búsqueda de máquinas virtuales](./media/azure-stack-quick-windows-portal/image7.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado de usar la máquina virtual, elimínela junto con sus recursos. Para ello, seleccione el grupo de recursos en la página de la máquina virtual y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha implementado una máquina virtual básica con Windows Server. Para más información sobre las máquinas virtuales de Azure Stack Hub, continúe con el artículo [Consideraciones sobre máquinas virtuales en Azure Stack Hub](azure-stack-vm-considerations.md).
