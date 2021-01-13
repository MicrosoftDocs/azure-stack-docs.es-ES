---
title: Actualización del registro de contenedor en Azure Stack Hub | Microsoft Docs
titleSuffix: Azure Stack
description: Aprenda a actualizar el registro de contenedor en Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: f63f0d550a841902e1d7c27d9c7688a8b5373149
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910591"
---
# <a name="update-the-container-registry-in-azure-stack-hub"></a>Actualización del registro de contenedor en Azure Stack Hub

Los usuarios de Azure Stack Hub pueden actualizar su implementación del registro de contenedor a una SKU de imagen base de AKS más reciente mediante las instrucciones siguientes. La máquina virtual y el servicio de la plantilla del registro de contenedor no tienen estado, ya que el estado y todas las imágenes de contenedor se almacenan en Blob Storage. Una actualización es tan sencilla como implementar la plantilla del registro de contenedor con una versión más reciente del VHD de la imagen base de AKS y redirigir DNS a la nueva máquina virtual. La acción de actualizar el valor de DNS de las máquinas virtuales de la plantilla del registro de contenedor anterior y la nueva dará lugar a una pequeña ventana de conectividad intermitente del registro mientras se propagan los valores.

## <a name="prerequisites"></a>Requisitos previos

### <a name="operator"></a>Operator

1.  Distribuya la imagen base de AKS más reciente desde el Marketplace de Azure Stack. La imagen base de AKS se actualiza con una cadencia mensual.

> ![Captura de pantalla en la que se muestra la página para agregar desde Azure, con los resultados de la búsqueda de "AKS Base Ubu".](./media/container-registry-template-updating-tzl/image1.png)

### <a name="user"></a>Usuario

1.  Compruebe la SKU de la imagen base de AKS que se usó para implementar la plantilla del registro de contenedor; para ello, vaya al registro de implementación en el grupo de recursos y seleccione **Entradas**.

    ![Captura de pantalla que muestra la página "Entradas".](./media/container-registry-template-updating-tzl/image2.png)

2.  Determine si hay SKU más recientes de la imagen base de AKS disponibles con la función **Get-VMImageSku**, que requiere el archivo `Import-Module .\pre-reqs.ps1` de los scripts de la plantilla del registro de contenedor.

    ```powershell  
    PS C:\azurestack-galler-master\registry\Scripts> Get-VMImageSku -Location Shanghai
    
    Skus                  
    ----                  
    aks-ubuntu-1604-201909
    aks-ubuntu-1604-201910 
    ```

## <a name="parameters-required"></a>Parámetros necesarios

| Parámetro | Detalles |
| --- | --- |
| Nombre de usuario | Proporcione el nombre de usuario para iniciar sesión en la máquina virtual. |
| Clave pública SSH | Proporcione la clave pública SSH que se usa para autenticarse con la máquina virtual mediante el protocolo SSH. |
| Size | Seleccione el tamaño de la máquina virtual que va a implementar. |
| Dirección IP pública | Especifique el nombre y el tipo de dirección IP (dinámica o estática) para esta máquina virtual. |
| Etiqueta de nombre de dominio | Especifique el prefijo DNS del registro. Este FQDN completo debe coincidir con el valor de CN del certificado PFX creado para el registro. |
| Réplicas | Especifique el número de réplicas de contenedor que se van a iniciar. |
| SKU de la imagen | Especifique la SKU de la imagen que se va a usar para la implementación. El cmdlet de PowerShell **Get-VMImageSku** enumera las SKU disponibles para la imagen base de AKS. |
| Identificador de cliente de la entidad de servicio | Especifique el identificador de aplicación de la entidad de servicio (SPN) tal como se usó en la implementación anterior. |
| Contraseña de la entidad de servicio y Confirmar contraseña | Especifique el secreto del identificador de aplicación del SPN tal como se usó en la implementación anterior. |
| Identificador de recurso de la cuenta de almacenamiento extendida existente | Especifique el identificador de recurso de la cuenta de almacenamiento tal como se usó en la implementación anterior. |
| Contenedor de blobs de back-end existente | Especifique el nombre del contenedor de blobs tal como se usó en la implementación anterior. |
| Identificador de recurso de Key Vault del certificado PFX | Especifique el identificador de recurso de Microsoft Azure Key Vault tal como se usó en la implementación anterior. |
| Dirección URL del secreto de Key Vault del certificado PFX | Especifique la dirección URL del certificado tal como se usó en la implementación anterior. |
| Huella digital del certificado PFX | Especifique la huella digital del certificado tal como se usó en la implementación anterior. |

## <a name="installation"></a>Instalación

1.  Instale una nueva instancia de la plantilla del registro de contenedor en un nuevo grupo de recursos.

    ![Captura de pantalla en la que se muestra la página de conceptos básicos para crear una plantilla de Container Registry.](./media/container-registry-template-updating-tzl/image3.png)

2.  Especifique la salida de SKU más reciente del script `Get-VMImage` y use el parámetro único **dnsname** de la instalación inicial en la configuración de la máquina virtual, use la misma entidad de servicio y el mismo secreto que la instalación inicial.

    ![Captura de pantalla en la que se muestra la página de configuración de máquina virtual para crear una plantilla de Container Registry.](./media/container-registry-template-updating-tzl/image4.png)

3.  Utilice los mismos parámetros de almacenamiento y Key Vault que la instalación inicial para el almacenamiento y la configuración de Key Vault.

    ![Captura de pantalla en la que se muestra la página de configuración de almacenamiento y Key Vault para crear una plantilla de Container Registry.](./media/container-registry-template-updating-tzl/image5.png)

1.  Una vez implementada la nueva plantilla del registro de contenedor, vaya al grupo de recursos inicial y seleccione el recurso de dirección IP pública.

    ![Captura de pantalla en la que se muestra una lista de recursos de dirección IP pública.](./media/container-registry-template-updating-tzl/image6.png)

1.  En el recurso de dirección IP pública, vaya a Configuración y modifique la etiqueta de nombre DNS para que se pueda usar para el recurso recién implementado. Tenga en cuenta que una vez que modifique la etiqueta de nombre DNS y seleccione **Guardar**, las llamadas al registro de contenedor comenzarán a producir errores.

    ![Captura de pantalla en la que se muestra la página de recursos de la dirección IP pública con la opción "Estático" seleccionada.](./media/container-registry-template-updating-tzl/image7.png)
    
    ![Captura de pantalla en la que se muestra la página de recursos de la dirección IP pública con la configuración de etiqueta de nombre DNS (opcional) seleccionada.](./media/container-registry-template-updating-tzl/image8.png)

2.  Vaya al nuevo grupo de recursos utilizado para implementar la nueva instancia de la plantilla del registro de contenedor, seleccione el recurso de dirección IP pública, la configuración y actualice la etiqueta de nombre DNS al nombre correcto usado en la implementación original, que en este ejemplo es `myreg` y seleccione **Guardar**.

    ![Captura de pantalla en la que se muestra la página de recursos de la dirección IP pública con la etiqueta de nombre DNS original especificada.](./media/container-registry-template-updating-tzl/image9.png)
    
    ![Plantilla de registro de contenedor](./media/container-registry-template-updating-tzl/image10.png)

3.  Durante los próximos 30 minutos, habrá acceso intermitente al registro de contenedor a medida que se propaga el registro DNS. Para validar la conexión, inicie sesión en Docker Registry y extraiga o inserte una imagen.

## <a name="next-steps"></a>Pasos siguientes

[Información general de Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
