---
title: Creación de un paquete de extensión de OEM
titleSuffix: Azure Stack Hub
description: Aprenda a crear un paquete de extensión de OEM en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dcc1bb0ce68b0c18363bc1caa196d084a54a6c0b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874700"
---
# <a name="create-an-oem-package"></a>Creación de un paquete de OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

El paquete de extensión de OEM de Azure Stack Hub es el mecanismo por el que se agrega contenido específico del OEM a la infraestructura de Azure Stack Hub. El contenido se usa en los procesos operativos y de implementación como la actualización, la expansión y el reemplazo de campos.

## <a name="creating-the-package"></a>Creación del paquete

Una vez creado y validado, el paquete de extensión de OEM puede usarse en VaaS. Antes de continuar, asegúrese de que ha completado los pasos para [crear un paquete de OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Después, el paquete se envía a Microsoft junto con los resultados de la prueba de VaaS para iniciar sesión en el flujo de trabajo de validación del paquete. En los siguientes pasos se explica cómo agrupar los archivos generados en un solo archivo ZIP que pueda consumir VaaS.

1. Identifique el siguiente contenido para el paquete:
    - Un archivo ZIP que incluye el contenido del paquete.
    - Un archivo de manifiesto denominado `oemMetadata.xml`, que debe tener un contenido idéntico al archivo `metadata.xml` en la raíz del contenido del paquete.

2. Seleccione los archivos de contenido y cree un archivo ZIP:

    ![Comprima el contenido del archivo cuando cree un paquete de extensión OEM](media/vaas-create-oem-package-1.png). ![Comprima el contenido de los elementos al crear un paquete de extensión de OEM](media/vaas-create-oem-package-2.png).

3. Cambie el nombre del archivo resultante de modo que sea lo suficientemente descriptivo para que pueda identificarlo.

## <a name="verifying-the-contents"></a>Comprobación del contenido

Para validar la estructura del archivo ZIP, inspecciónelo y compruebe que no haya ninguna subcarpeta. El TLD tiene el contenido comprimido. A continuación se muestra una estructura de paquete válida:

> [!IMPORTANT]
> Comprimir la carpeta principal en lugar del contenido provocará un error de firma del paquete.

![Comprima adecuadamente el contenido del paquete al crear un paquete de extensión de OEM.](media/vaas-create-oem-package-3.png)

El archivo ZIP ahora puede cargarse en VaaS y Microsoft puede firmarlo en el flujo de trabajo de validación del paquete.

## <a name="next-steps"></a>Pasos siguientes

- [Validación de paquetes de OEM](azure-stack-vaas-validate-oem-package.md)
