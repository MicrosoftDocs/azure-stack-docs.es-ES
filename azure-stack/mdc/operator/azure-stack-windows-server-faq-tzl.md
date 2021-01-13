---
title: 'Preguntas frecuentes relacionadas con Windows Server de Azure Stack: MDC | Microsoft Docs'
description: Consulte una lista con las preguntas más frecuentes del marketplace de Azure Stack para Windows Server cuando lo opere un Centro de datos modular (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 64ccefb0a6644d2cd613a39d8beaaf1f82ee9fc0
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910795"
---
# <a name="windows-server-in-azure-stack-marketplace-faq---modular-data-center-mdc"></a>Preguntas frecuentes sobre Windows Server en el marketplace de Azure Stack: Centro de datos modular (MDC)

En este artículo se responden algunas preguntas frecuentes sobre las imágenes de Windows Server en el [Marketplace de Azure Stack Hub](../../operator/azure-stack-marketplace.md).

## <a name="faqs"></a>Preguntas más frecuentes

**¿Qué opciones hay para obtener licencias de imágenes de Windows Server en el Marketplace de Azure Stack?**

Los usuarios de Azure Stack Hub resistente y Centro de datos modular tienen derecho a usar gratis Windows Server como sistema operativo invitado.

Microsoft ofrece dos versiones de imágenes de Windows Server a través de Marketplace de Azure Stack. Solo una versión de esta imagen puede usarse en los entornos de Azure Stack al mismo tiempo.

- **Pago por uso**: Estas imágenes no se deben usar en Azure Stack Hub resistente ni en el Centro de datos modular.

- **Traiga su propia licencia (BYOL)** : Estas imágenes se pueden usar en Azure Stack Hub resistente y en el Centro de datos modular.

**¿Qué ocurre con otras máquinas virtuales que usan Windows Server, como SQL Server?**

La licencia de software de Windows Server solo se aplica al sistema operativo Windows, y no a productos en capas, como SQL, que tienen que aportar su propia licencia.

**¿Cómo actualizo a una imagen de Windows más reciente?**

En primer lugar, determine si las plantillas de Azure Resource Manager hacen referencia a alguna versión específica. Si es así, actualice las plantillas o mantenga versiones de imágenes antiguas. Es mejor usar **version: latest**.

A continuación, si los conjuntos de escalado de máquinas virtuales hacen referencia a una versión específica, debería pensar si será necesario escalarlas más tarde y decidir si conservar las versiones anteriores. Si ninguna de estas condiciones es aplicable, elimine las imágenes antiguas de Marketplace antes de descargar las más recientes. Puede usar Administración de Marketplace para eliminarlas si es así cómo se descargó el original. A continuación, descargue la versión más reciente.

**¿Qué ocurre si descargué la versión incorrecta para los usuarios?**

Primero, debe eliminar la versión incorrecta a través de Administración de Marketplace. Espere a que la eliminación finalice (consulte las notificaciones que indican la finalización, no la hoja **Administración de Marketplace**). A continuación, descargue la versión correcta.

Si descarga ambas versiones de la imagen, solo la versión más reciente es visible para los clientes finales en la galería de Marketplace.

**¿Cómo puedo activar la máquina virtual de Windows Server?**

Para activar una máquina virtual de Windows Server en Azure Stack, deben cumplirse las condiciones siguientes:

- Windows Server 2012 R2 y Windows Server 2016 deben usar la [activación automática de máquina virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). En Azure Stack, no se admiten el Servicio de administración de claves (KMS) ni otros servicios de activación.

**¿Cómo puedo comprobar que mi máquina virtual está activada?**

Ejecute el siguiente comando en un símbolo del sistema con privilegios elevados:

```shell
slmgr /dlv
```

Si se activa correctamente, verá esto indicado claramente y el nombre de host se mostrará en la salida de slmgr. No se base en las marcas de agua en la pantalla, ya que es posible que no estén actualizadas o se muestren desde una máquina virtual anterior a la suya.

**Mi máquina virtual no está configurada para usar AVMA, ¿cómo puedo arreglarlo?**

Ejecute el siguiente comando en un símbolo del sistema con privilegios elevados:

```shell
slmgr /ipk <AVMA key>
```

Consulte el artículo [Activación de la máquina virtual automática](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) para obtener las claves necesarias para usar su imagen.

**Me encargo de crear mis propias imágenes de Windows Server, ¿cómo puedo asegurarme de que usan AVMA?**

Se recomienda que ejecute el comando `slmgr /ipk` con la clave adecuada antes de ejecutar el comando `sysprep`. O bien, puede incluir la clave de AVMA en cualquier archivo de instalación Unattend.exe.

**Estoy intentando usar mi imagen de Windows Server 2016, que cree en Azure, y no se activa o no usa la activación de KMS.**

Ejecute el comando `slmgr /ipk`. Es posible que las imágenes de Azure no recurran correctamente a AVMA; pero si alcanzan el sistema KMS de Azure, se activarán. Es recomendable asegurarse de que estas máquinas virtuales están configuradas para usar AVMA.

**He realizado todos estos pasos, pero sigo sin conseguir activar mis máquinas virtuales.**

Póngase en contacto con el equipo de soporte técnico de Microsoft para comprobar que se han instalado los marcadores BIOS correctos.

**¿Qué sucede con las versiones anteriores de Windows Server?**

[Activación automática de máquina virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) no se admite en versiones de Windows Server anteriores a 2012 R2. Tendrá que activar las máquinas virtuales manualmente mediante claves MAK.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos.

- [Información general de Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
- [Descarga de elementos de Marketplace desde Azure a Azure Stack](azure-stack-download-azure-marketplace-item-tca.md)
