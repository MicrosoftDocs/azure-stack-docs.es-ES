---
title: Preguntas frecuentes sobre Windows Server en el Marketplace de Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Lista de preguntas frecuentes del Marketplace de Azure Stack Hub para Windows Server.
author: sethmanheim
ms.topic: article
ms.date: 12/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 72b21675e97a436499f53f00e4c7d25dfc1beb54
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696080"
---
# <a name="windows-server-in-azure-stack-hub-marketplace-faq"></a>Preguntas frecuentes sobre Windows Server en el Marketplace de Azure Stack Hub

En este artículo se responden algunas preguntas frecuentes sobre las imágenes de Windows Server en el [Marketplace de Azure Stack Hub](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Elementos de Marketplace

### <a name="how-do-i-update-to-a-newer-windows-image"></a>¿Cómo actualizo a una imagen de Windows más reciente?

En primer lugar, determine si las plantillas de Azure Resource Manager hacen referencia a alguna versión específica. Si es así, actualice las plantillas o mantenga versiones de imágenes antiguas. Es mejor usar **version: latest**.

A continuación, si los conjuntos de escalado de máquinas virtuales hacen referencia a una versión específica, debería pensar si será necesario escalarlas más tarde y decidir si conservar las versiones anteriores. Si ninguna de estas condiciones es aplicable, elimine las imágenes antiguas de Marketplace de Azure Stack Hub antes de descargar las más recientes. Use la administración de Marketplace para eliminarlas si es así cómo se descargó el original. A continuación, descargue la versión más reciente.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack-hub"></a>¿Qué opciones hay para obtener licencias de imágenes de Windows Server en el Marketplace de Azure Stack Hub?

Microsoft ofrece dos versiones de imágenes de Windows Server a través de Marketplace de Azure Stack Hub. Solo una versión de esta imagen puede usarse en un entorno de Azure Stack Hub.  

- **Pago por uso**: estas imágenes ejecutan los medidores de precio completo de Windows.
   Quién debe usar esta opción: Clientes con contratos Enterprise (EA) que usan el *modelo de facturación de consumo*; o CSP que no desean usar licencias de SPLA.
- **Traiga su propia licencia (BYOL)** : Estas imágenes ejecutan medidores básicos.
   Quién debe usar esta opción: Clientes con EA y con una licencia de Windows Server; o para CSP que usan licencias de SPLA.

Ventaja de uso híbrido de Azure no se admite en Azure Stack Hub. Los clientes que obtienen licencias a través del modelo "Capacidad" deben usar la imagen BYOL. Si va a realizar pruebas con el Kit de desarrollo de Azure Stack (ASDK), puede usar cualquiera de estas opciones.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>¿Qué ocurre si descargué la versión incorrecta para mis inquilinos o usuarios?

Primero, debe eliminar la versión incorrecta a través de Administración de Marketplace. Espere a que la eliminación finalice (consulte las notificaciones que indican la finalización, no la hoja **Administración de Marketplace**). A continuación, descargue la versión correcta.

Si descarga ambas versiones de la imagen, solo la versión más reciente es visible para los clientes finales en Marketplace de Azure Stack Hub.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>¿Qué ocurre si mi usuario marco incorrectamente el cuadro "Tengo una licencia" en compilaciones anteriores de Windows y no tiene ninguna licencia?

Puede cambiar el atributo del modelo de licencia para cambiar de traiga su propia licencia (BYOL) al modelo de pago por uso (PAYG) mediante la ejecución del siguiente script:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

Puede comprobar el tipo de licencia de su VM mediante la ejecución de los siguientes comandos. Si el modelo de licencia indica **Windows_Server**, se le cobrará el precio según el modelo de traiga su propia licencia. De lo contrario, se le cobrará por el medidor de Windows según el modelo de pago por uso:

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>¿Qué ocurre si tengo una imagen anterior y el usuario olvidó marcar el cuadro "Tengo una licencia"? ¿Y si usamos nuestras propias imágenes y tenemos un Contrato Enterprise?

Puede cambiar el atributo del modelo de licencia al modelo de traiga su propia licencia. Para ello, ejecute los siguientes comandos:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>¿Qué ocurre con otras máquinas virtuales que usan Windows Server, como SQL o Machine Learning Server?

Estas imágenes se aplican al parámetro **licenseType**, por lo que el modelo que se sigue es el de pago por uso. Puede establecer este parámetro (consulte la pregunta anterior). Esto solo se aplica al software de Windows Server, y no a productos en capas, como SQL, que tienen que aportar su propia licencia. Las licencias de pago por uso no se aplican a productos de software en capas.

Solo puede cambiar la propiedad **licenseType** para imágenes de SQL Server de Marketplace de Azure Stack Hub si la versión es XX.X.20190410 o superior. Si está ejecutando una versión anterior de las imágenes de SQL Server de Marketplace de Azure Stack Hub, no puede cambiar el atributo **licenseType** y debe volver a implementarlo con las imágenes de SQL Server más recientes de Marketplace.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Tengo un contrato Enterprise (EA) y usaré mi licencia EA Windows Server, ¿cómo puedo asegurarme de que las imágenes se facturarán correctamente?

Puede agregar **licenseType: Windows_Server** en una plantilla de Azure Resource Manager. Esta configuración debe agregarse a cada bloque de recursos de la máquina virtual.

## <a name="activation"></a>Activación

Para activar una máquina virtual de Windows Server en Azure Stack Hub, deben cumplirse las condiciones siguientes:

- El OEM estableció el marcador de BIOS adecuado en todos los sistemas host de Azure Stack Hub.
- Windows Server 2012 R2 y Windows Server 2016 deben usar la [activación automática de máquinas virtuales](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). En Azure Stack Hub, no se admiten el Servicio de administración de claves (KMS) ni otros servicios de activación.

### <a name="how-can-i-verify-that-my-vm-is-activated"></a>¿Cómo puedo comprobar que mi máquina virtual está activada?

Ejecute el siguiente comando en un símbolo del sistema con privilegios elevados:

```shell
slmgr /dlv
```

Si se activa correctamente, verá esto indicado claramente y el nombre de host se mostrará en la salida de `slmgr`. No se base en las marcas de agua en la pantalla, ya que es posible que no estén actualizadas o se muestren desde una máquina virtual anterior a la suya.

### <a name="my-vm-isnt-set-up-to-use-avma-how-can-i-fix-it"></a>Mi máquina virtual no está configurada para usar AVMA, ¿cómo puedo arreglarlo?

Ejecute el siguiente comando en un símbolo del sistema con privilegios elevados:

```shell
slmgr /ipk <AVMA key>
```

Consulte el artículo [Activación automática de máquinas virtuales](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) para obtener las claves necesarias para usar su imagen.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Me encargo de crear mis propias imágenes de Windows Server, ¿cómo puedo asegurarme de que usan AVMA?

Se recomienda que ejecute la línea de comandos `slmgr /ipk` con la clave adecuada antes de ejecutar el comando `sysprep`. O bien, puede incluir la clave de AVMA en cualquier archivo de instalación Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-its-not-activating-or-using-kms-activation"></a>Estoy intentando usar mi imagen de Windows Server 2016, que creé en Azure, y no se activa ni usa la activación de KMS.

Ejecute el comando `slmgr /ipk`. Es posible que las imágenes de Azure no recurran correctamente a AVMA; pero si alcanzan el sistema KMS de Azure, se activarán. Es recomendable asegurarse de que estas máquinas virtuales están configuradas para usar AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-vms-are-still-not-activating"></a>He realizado todos estos pasos, pero sigo sin conseguir activar mis máquinas virtuales.

Póngase en contacto con su proveedor de hardware para comprobar que se han instalado los marcadores de BIOS correctos.

### <a name="what-about-earlier-versions-of-windows-server"></a>¿Qué sucede con las versiones anteriores de Windows Server?

[Activación automática de máquinas virtuales](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) no se admite en versiones anteriores de Windows Server. Tendrá que activar las VM manualmente.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos.

- [Introducción al Marketplace de Azure Stack Hub](azure-stack-marketplace.md)
- [Descarga de elementos de Marketplace en Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
