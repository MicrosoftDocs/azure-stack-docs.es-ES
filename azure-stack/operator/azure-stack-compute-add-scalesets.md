---
title: Disponibilidad de los conjuntos de escalado de máquinas virtuales en Azure Stack Hub
description: Conozca cómo un operador de la nube puede agregar conjuntos de escalado de máquinas virtuales a Marketplace de Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2019
ms.openlocfilehash: f0b23df25ef2e9fffe9c5f43ee649ecfe5f74092
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695298"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>Disponibilidad de los conjuntos de escalado de máquinas virtuales en Azure Stack Hub

Los conjuntos de escalado de máquinas virtuales son un recurso de proceso de Azure Stack Hub. Puede utilizarlos para implementar y administrar un conjunto de máquinas virtuales idénticas. Si todas las máquinas virtuales tienen la misma configuración, los conjuntos de escalado no requieren un aprovisionamiento previo de las máquinas virtuales. Esto facilita la creación de servicios a gran escala cuyo objetivo son las cargas de trabajo en contenedores, de macroproceso y macrodatos.

Este artículo le guía por el proceso de permitir la disponibilidad de los conjuntos de escalado de Marketplace de Azure Stack Hub. Después de completar este procedimiento, los usuarios pueden agregar conjuntos de escalado de máquinas virtuales a sus suscripciones.

Los conjuntos de escalado de máquinas virtuales de Azure Stack Hub son similares a los conjuntos de escalado de máquinas virtuales de Azure. Para obtener más información, consulte los siguientes vídeos:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich habla sobre los conjuntos de escalado de Azure)
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) (Conjunto de escalado de máquinas virtuales con Guy Bowerman)

En Azure Stack Hub, los conjuntos de escalado de máquinas virtuales no admiten la escalabilidad automática. Puede agregar más instancias a un conjunto de escalado con plantillas de Resource Manager, la CLI o PowerShell.

## <a name="prerequisites"></a>Prerrequisitos

* **Marketplace de Azure Stack Hub** Registre Azure Stack Hub en Azure global para permitir la disponibilidad de los elementos en Marketplace de Azure Stack Hub. Siga las instrucciones que se indican en [Registro de Azure Stack Hub en Azure](azure-stack-registration.md).
* **Imagen del sistema operativo:** Para poder crear un conjunto de escalado de máquinas virtuales, antes es preciso descargar las imágenes de máquina virtual para usar en el conjunto de escalado desde [Marketplace de Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). Para que un usuario pueda crear un conjunto de escalado, deben existir las imágenes.

## <a name="use-the-azure-stack-hub-portal"></a>Uso del portal de Azure Stack Hub

>[!IMPORTANT]  
> La información de esta sección se aplica cuando se usa la versión 1808 de Azure Stack Hub o una posterior.

1. Inicie sesión en el portal de Azure Stack Hub. Después vaya a **Todos los servicios**, luego **Conjuntos de escalado de máquinas virtuales** y, en **COMPUTE**, seleccione **Conjuntos de escalado de máquinas virtuales**.
   ![Selección de conjuntos de escalado de máquinas virtuales](media/azure-stack-compute-add-scalesets/all-services.png)

2. Seleccione ***Create Virtual machine scale sets*** (Crear conjuntos de escalado de máquinas virtuales).
   ![Creación de un conjunto de escalado de máquinas virtuales](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Rellene los campos vacíos y elija valores de las listas desplegables **Imagen del disco del sistema operativo**, **Suscripción** y **Tamaño de instancia**. Seleccione **Sí** para **Usar discos administrados**. A continuación, haga clic en **Crear**.
    ![Configuración y creación de escalado de máquinas virtuales](media/azure-stack-compute-add-scalesets/create.png)

4. Para ver el nuevo conjunto de escalado de máquinas virtuales, vaya a **Todos los recursos**, busque el nombre del conjunto de escalado de máquinas virtuales y, después, seleccione su nombre en la búsqueda.
   ![Visualización del conjunto de escalado de máquinas virtuales](media/azure-stack-compute-add-scalesets/search.png)

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

2. La plantilla de implementación del conjunto de escalado de máquinas virtuales **no especifica latest** para **version** y, en su lugar, especifica un número de versión:  

    Si el operador de Azure Stack Hub descarga una imagen con una versión más reciente (y elimina la versión anterior), el conjunto de escalado no se puede escalar verticalmente. Esto es así por diseño, ya que la versión de la imagen especificada en la plantilla del conjunto de escalado debe estar disponible.  

Para más información, consulte [Imágenes y discos del sistema operativo](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Escalar un conjunto de escalado de máquinas virtuales

Puede escalar el tamaño de un conjunto de escalado de máquinas virtuales para que sea más grande o más pequeño.

1. En el portal, seleccione el conjunto de escalado y luego elija **Escalado**.

2. Use la barra de desplazamiento para establecer el nuevo nivel de escalado para este conjunto de escalado de máquinas virtuales y, luego, haga clic en **Guardar**.

     ![Escalado del conjunto de máquinas virtuales](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="next-steps"></a>Pasos siguientes

* [Descarga de elementos de Marketplace en Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
