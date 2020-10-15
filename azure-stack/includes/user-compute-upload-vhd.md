---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 28fe06ca182b2df8104c2d8b23ce9f5c27448064
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91936175"
---
Puede cargar el disco duro virtual con el portal, o con el contenedor que creó en el portal, mediante AzCopy.

### <a name="portal-to-generate-sas-url-and-upload-vhd"></a>Uso de Azure Portal para generar la dirección URL de SAS y cargar el disco duro virtual

1. Inicie sesión en el portal de usuarios de Azure Stack Hub.

2. Seleccione **Cuentas de almacenamiento** y seleccione una cuenta de almacenamiento existente o cree una nueva.

3. Seleccione **Blobs** en la hoja Cuenta de almacenamiento de la cuenta de almacenamiento. Seleccione Contenedor para crear un nuevo contenedor.

4. Escriba el nombre del contenedor y, a continuación, seleccione **Blob (acceso de lectura anónimo solo para blobs)** .

5. Si va a usar AzCopy para cargar la imagen en lugar del portal, cree un token de SAS. Seleccione **Firma de acceso compartido** en la cuenta de almacenamiento y, a continuación, seleccione **Generar la cadena de conexión y SAS**. Copie y anote la **URL de SAS de Blob service**. Usará esta dirección URL cuando utilice AzCopy para cargar el disco duro virtual.

6. Seleccione el contenedor y, luego, seleccione **Cargar**. Cargue el disco duro virtual.

### <a name="azcopy-vhd"></a>Disco duro virtual de AzCopy

Use Explorador de Azure Storage o AzCopy para reducir la posibilidad de que el disco duro virtual se dañe en el proceso de carga y esta sea más rápida. En los pasos siguientes se usa AzCopy en una máquina con Windows 10. AzCopy es una utilidad de línea de comandos que puede usar para copiar blobs o archivos a una cuenta de almacenamiento o desde una cuenta de almacenamiento.

1. Si no tiene instalado AzCopy, instálelo. Encontrará instrucciones para descargar y empezar a trabajar con AzCopy en el artículo [Introducción a AzCopy](/azure/storage/common/storage-use-azcopy-v10). Tome nota de dónde almacena el archivo binario. Puede [agregar AzCopy a la ruta de acceso](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/) para usarlo desde la línea de comandos de PowerShell.

2. Abra PowerShell para usar AzCopy desde el shell.

3. Use AzCopy para cargar el disco duro virtual en el contenedor de la cuenta de almacenamiento.

    ```powershell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    azcopy cp "/path/to/file.vhd" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS] --blob-type=PageBlob
    ```

> [!NOTE]  
> Cargue el disco duro virtual con una sintaxis similar a la carga de un archivo único en el directorio virtual. Agregue `--blob-type=PageBlob` para asegurarse de que el disco duro virtual se carga como un **blob en páginas**, en lugar de como **bloque** de forma predeterminada.

Para más información sobre el uso de AzCopy y otras herramientas de almacenamiento, consulte [Uso de herramientas de transferencia de datos en Azure Stack Hub Storage](../user/azure-stack-storage-transfer.md).