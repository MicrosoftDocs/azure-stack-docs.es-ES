---
title: Puesta en disponibilidad de conjuntos de escalado de máquinas virtuales en Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo un operador en la nube puede agregar conjuntos de escalado de máquinas virtuales a la plataforma Marketplace de Azure Stack
services: azure-stack
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.topic: article
origin.date: 02/21/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 7237437ca19ee6f5580661683ea82569d9d84b64
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309363"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Puesta en disponibilidad de conjuntos de escalado de máquinas virtuales en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*
  
Los conjuntos de escalado de máquinas virtuales son un recurso de proceso de Azure Stack. Puede utilizarlos para implementar y administrar un conjunto de máquinas virtuales idénticas. Si todas las máquinas virtuales tienen la misma configuración, los conjuntos de escalado no requieren un aprovisionamiento previo de las máquinas virtuales. Esto facilita la creación de servicios a gran escala cuyo objetivo son las cargas de trabajo en contenedores, de macroproceso y macrodatos.

Este artículo le guía por el proceso de poner a disposición de los usuarios los conjuntos de escalado en la plataforma Marketplace de Azure Stack. Después de completar este procedimiento, los usuarios pueden agregar conjuntos de escalado de máquinas virtuales a sus suscripciones.

Los conjuntos de escalado de máquinas virtuales en Azure Stack son similares a los conjuntos de escalado de máquinas virtuales en Azure. Para obtener más información, consulte los siguientes vídeos:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich habla sobre los conjuntos de escalado de Azure)
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

En Azure Stack, los conjuntos de escalado de máquinas virtuales no admiten el escalado automático. Puede agregar más instancias a un conjunto de escalado con plantillas de Resource Manager, la CLI o PowerShell.

## <a name="prerequisites"></a>Requisitos previos

* **Marketplace:** Registre Azure Stack con Azure global para habilitar la disponibilidad de los artículos en Marketplace. Siga las instrucciones de [Registro de Azure Stack con Azure](azure-stack-registration.md).
* **Imagen del sistema operativo:** Para poder crear un conjunto de escalado de máquinas virtuales (VMSS), primero debe descargar las imágenes de máquina virtual para usarlas en el VMSS desde [Azure Stack Marketplace](azure-stack-download-azure-marketplace-item.md). Las imágenes ya deben existir antes de que un usuario pueda crear un nuevo VMSS.

## <a name="use-the-azure-stack-portal"></a>Uso del portal de Azure Stack

>[!IMPORTANT]  
> La información de esta sección se aplica cuando se usa la versión 1808 de Azure Stack o una posterior. Si tiene la versión 1807 o una anterior, vea [Add the Virtual Machine Scale Set (Prior to 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808) (Agregar el conjunto de escalado de máquinas virtuales [anterior a 1808]).

1. Inicie sesión en el portal de Azure Stack. Después vaya a **Todos los servicios**, luego **Conjuntos de escalado de máquinas virtuales** y, en **COMPUTE**, seleccione **Conjuntos de escalado de máquinas virtuales**.
   ![Selección de conjuntos de escalado de máquinas virtuales](media/azure-stack-compute-add-scalesets/all-services.png)

2. Seleccione Crear ***conjuntos de escalado de máquinas virtuales***.
   ![Creación de un conjunto de escalado de máquinas virtuales](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Rellene los campos vacíos y elija valores de las listas desplegables **Imagen del disco del sistema operativo**, **Suscripción** y **Tamaño de instancia**. Seleccione **Sí** para **Usar discos administrados**. A continuación, haga clic en **Crear**.
    ![Configurar y crear](media/azure-stack-compute-add-scalesets/create.png)

4. Para ver el nuevo conjunto de escalado de máquinas virtuales, vaya a **Todos los recursos**, busque el nombre del conjunto de escalado de máquinas virtuales y, después, seleccione su nombre en la búsqueda.
   ![Visualizar el conjunto de escalado](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Adición del conjunto de escalado de máquinas virtuales (antes de la versión 1808)

>[!IMPORTANT]  
> La información de esta sección se aplica cuando se usa una versión de Azure Stack anterior a 1808. Si usa la versión 1808 o una posterior, vea [Usar el portal de Azure Stack](#use-the-azure-stack-portal).

1. Abra Azure Stack Marketplace y conéctese a Azure. Seleccione **Administración de Marketplace** y haga clic en **+ Agregar desde Azure**.

    ![Administración de Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Agregue y descargue el elemento de Marketplace Conjunto de escalado de máquinas virtuales.

    ![Conjunto de escalado de máquinas virtuales](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Actualización de imágenes en un conjunto de escalado de máquinas virtuales

Después de crear un conjunto de escalado de máquinas virtuales, los usuarios pueden actualizar imágenes en él sin tener que volver a crearlo. El proceso para actualizar una imagen depende de los siguientes escenarios:

1. La plantilla de implementación del conjunto de escalado de máquinas virtuales especifica **latest** para **version**:  

   Cuando `version` se establece en **latest** en la sección `imageReference` de la plantilla de un conjunto de escalado, las operaciones de escalado vertical de este usarán la versión más reciente disponible de la imagen para las instancias del conjunto de escalado. Una vez finalizado el escalado vertical, puede eliminar las instancias más antiguas de los conjuntos de escalado de máquinas virtuales. Los valores de `publisher`, `offer` y `sku` permanecen sin cambios.

   En el siguiente ejemplo de JSON se especifica `latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Antes de que el escalado vertical pueda usar una nueva imagen, debe descargar esa imagen:  

   * Si la imagen de Marketplace es una versión más reciente que la imagen del conjunto de escalado, descargue la imagen nueva que reemplaza a la anterior. Una vez reemplazada la imagen, un usuario puede continuar con el escalado vertical.

   * Si la versión de la imagen de Marketplace es la misma que la de la imagen del conjunto de escalado, elimine la imagen en uso en el conjunto de escalado y, a continuación, descargue la nueva. Durante el tiempo que transcurre entre la eliminación de la imagen original y la descarga de la nueva imagen, no se puede escalar verticalmente.

     Este proceso es necesario para redistribuir las imágenes que usan el formato de archivo disperso que se introdujo con la versión 1803.

2. La plantilla de implementación del conjunto de escalado de máquinas virtuales **no especifica latest** para **version** y, en su lugar, especifica un número de versión:  

    Si descarga una imagen con una versión más reciente (que cambia la versión disponible), el conjunto de escalado no se podrá escalar verticalmente. Esto es así por diseño, ya que la versión de la imagen especificada en la plantilla del conjunto de escalado debe estar disponible.  

Para más información, consulte [Imágenes y discos del sistema operativo](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Escalar un conjunto de escalado de máquinas virtuales

Puede escalar el tamaño de un *conjunto de escalado de máquinas virtuales* para que sea más grande o más pequeño.  

1. En el portal, seleccione el conjunto de escalado y luego elija **Escalado**.

2. Use la barra de desplazamiento para establecer el nuevo nivel de escalado para este conjunto de escalado de máquinas virtuales y, luego, haga clic en **Guardar**.

     ![Escalar el conjunto](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Eliminación de un conjunto de escalado de máquinas virtuales

Para eliminar un elemento de la galería del conjunto de escalado de máquinas virtuales, ejecute el siguiente comando de PowerShell:

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> Es posible que el elemento de la galería no se quite de forma inmediata. Es posible que deba actualizar el portal varias veces para que el elemento se muestre como eliminado de Marketplace.

## <a name="next-steps"></a>Pasos siguientes

* [Descarga de elementos de Marketplace desde Azure a Azure Stack](azure-stack-download-azure-marketplace-item.md)