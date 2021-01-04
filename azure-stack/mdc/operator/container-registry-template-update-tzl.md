---
title: 'Actualización del registro de contenedor en Azure Stack Hub: MDC'
titleSuffix: Azure Stack
description: Obtenga información sobre cómo actualizar el registro de contenedor en Azure Stack Hub para centros de datos modulares (MDC).
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
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: dafd9d485125d7c8da1524b71fddb75af7a4ebba
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598579"
---
# <a name="update-the-container-registry-in-azure-stack-hub---modular-data-center-mdc"></a>Actualización del registro de contenedor en Azure Stack Hub: centro de datos modular (MDC)

Los usuarios de Azure Stack Hub pueden actualizar su implementación del registro de contenedor a una SKU de imagen base de AKS más reciente mediante las instrucciones siguientes. La máquina virtual y el servicio de la plantilla del registro de contenedor no tienen estado, ya que el estado y todas las imágenes de contenedor se almacenan en Blob Storage. Una actualización es tan sencilla como implementar la plantilla del registro de contenedor con una versión más reciente del VHD de la imagen base de AKS y redirigir DNS a la nueva máquina virtual. La acción de actualizar el valor de DNS de las máquinas virtuales de la plantilla del registro de contenedor anterior y la nueva dará lugar a una pequeña ventana de conectividad intermitente del registro mientras se propagan los valores.

## <a name="prerequisites"></a>Requisitos previos

### <a name="operator"></a>Operator

- Distribuya la imagen base de AKS más reciente desde el Marketplace de Azure Stack. La imagen base de AKS se actualiza con una cadencia mensual.

  ![Imagen base de AKS](./media/container-registry-template-updating-tzl/aks-base-image.png)

### <a name="user"></a>Usuario

1.  Compruebe la SKU de la imagen base de AKS que se usó para implementar la plantilla del registro de contenedor; para ello, vaya al registro de implementación en el grupo de recursos y seleccione **Entradas**.

    ![Captura de pantalla que muestra la página "Entradas".](./media/container-registry-template-updating-tzl/inputs.png)

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

    ![Instalación de una nueva plantilla del registro de contenedor](./media/container-registry-template-updating-tzl/new-instance.png)

2.  Especifique la salida de SKU más reciente del script `Get-VMImage` y use el parámetro único **dnsname** de la instalación inicial en la configuración de la máquina virtual, use la misma entidad de servicio y el mismo secreto que la instalación inicial.

    ![Especificar SKU](./media/container-registry-template-updating-tzl/sku.png)

3.  Utilice los mismos parámetros de almacenamiento y Key Vault que la instalación inicial para el almacenamiento y la configuración de Key Vault.

    ![Agregar almacenamiento](./media/container-registry-template-updating-tzl/storage.png)

1.  Una vez implementada la nueva plantilla del registro de contenedor, vaya al grupo de recursos inicial y seleccione el recurso de dirección IP pública.

    ![Seleccionar dirección IP](./media/container-registry-template-updating-tzl/ip.png)

1.  En el recurso de dirección IP pública, vaya a Configuración y modifique la etiqueta de nombre DNS para que se pueda usar para el recurso recién implementado. Tenga en cuenta que una vez que modifique la etiqueta de nombre DNS y seleccione **Guardar**, las llamadas al registro de contenedor comenzarán a producir errores.

    ![Cambiar etiqueta DNS](./media/container-registry-template-updating-tzl/dns.png)
    
    ![Guardar cambio](./media/container-registry-template-updating-tzl/save.png)

2.  Vaya al nuevo grupo de recursos utilizado para implementar la nueva instancia de la plantilla del registro de contenedor, seleccione el recurso de dirección IP pública, la configuración y actualice la etiqueta de nombre DNS al nombre correcto usado en la implementación original, que en este ejemplo es `myreg` y seleccione **Guardar**.

    ![Selección de configuración](./media/container-registry-template-updating-tzl/select-configuration.png)
    
    ![Guardar configuración](./media/container-registry-template-updating-tzl/save-configuration.png)

3.  Durante los próximos 30 minutos, habrá acceso intermitente al registro de contenedor a medida que se propaga el registro DNS. Para validar la conexión, inicie sesión en Docker Registry y extraiga o inserte una imagen.

## <a name="next-steps"></a>Pasos siguientes

[Información general de Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
