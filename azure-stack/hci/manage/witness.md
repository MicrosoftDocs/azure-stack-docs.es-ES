---
title: Configuración de un testigo del clúster
description: Aprenda a configurar un testigo del clúster.
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 32d0f717e987d757f5315cfe048c75300ae9c776
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647956"
---
# <a name="set-up-a-cluster-witness"></a>Configuración de un testigo del clúster

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

Se recomienda la configuración de un recurso de testigo para todos los clústeres y se debe configurar justo después de crear un clúster. Los clústeres de dos nodos necesitan un testigo para que los servidores que se quede sin conexión no provoquen que el otro nodo deje de estar disponible. Los clústeres de tres y más nodos necesitan un testigo para poder soportar dos servidores con errores o sin conexión.  

Puede usar un recurso compartido de archivos de SMB como testigo o un testigo en la nube de Azure. Le recomendamos que use un testigo en la nube de Azure, siempre que todos los nodos de servidor del clúster tengan una conexión a Internet confiable. En este artículo se describe la creación de un testigo en la nube.

## <a name="before-you-begin"></a>Antes de comenzar

Antes de poder crear un testigo en la nube, debe tener una cuenta y una suscripción de Azure y registrar el clúster de Azure Stack HCI con Azure. Para obtener más información, consulte los siguientes artículos:

- [Creación de una cuenta de Azure](https://docs.microsoft.com/dotnet/azure/create-azure-account)
- Si procede, [cree una suscripción de Azure adicional](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).
- [Conexión de Azure Stack HCl a Azure](../deploy/register-with-azure.md)

Para los testigos de recurso compartido de archivos, hay requisitos para el servidor de archivos. Para más información, consulte [Requisitos del sistema](../concepts/system-requirements.md).

## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

En esta sección se describe cómo crear una cuenta de Azure Storage. Esta cuenta se usa para almacenar un archivo de blob de Azure que se usa para el arbitraje de un clúster específico. Puede usar la misma cuenta de Azure Storage para configurar un testigo en la nube para varios clústeres.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En el menú principal de Azure Portal, en **Servicios de Azure**, seleccione **Cuentas de almacenamiento**. Si falta este icono, seleccione **Crear un recurso** para crear primero un recurso de *Cuentas de almacenamiento*.

    :::image type="content" source="media/witness/cloud-witness-home.png" alt-text="Pantalla de inicio de Azure Portal" lightbox="media/witness/cloud-witness-home.png":::

1. En la página **Cuentas de almacenamiento**, seleccione **Nuevo**.

    :::image type="content" source="media/witness/cloud-witness-create.png" alt-text="Nueva cuenta de almacenamiento de Azure" lightbox="media/witness/cloud-witness-create.png":::

1. En la página **Crear cuenta de almacenamiento**, siga estos pasos:
    1. Seleccione la **Suscripción** de Azure a la que se va a aplicar la cuenta de almacenamiento.
    1. Seleccione el **Grupo de recursos** de Azure al que se va a aplicar la cuenta de almacenamiento.
    1. Escriba un **nombre de cuenta de almacenamiento**.
    <br>Los nombres de las cuentas de almacenamiento deben tener entre 3 y 24 caracteres y solo pueden incluir números y letras en minúscula. Este nombre también debe ser único dentro de Azure.
    1. Seleccione la **Ubicación** más cercana físicamente.
    1. En **Rendimiento**, seleccione **Estándar**.
    1. En **Tipo de cuenta**, seleccione **Storage (uso general)** .
    1. En **Replicación**, seleccione **Almacenamiento con redundancia local (LRS)** .
    1. Cuando haya terminado, haga clic en **Revisar y crear**.

    :::image type="content" source="media/witness/cloud-witness-create-storage-account.png" alt-text="Creación de una cuenta de almacenamiento de Azure" lightbox="media/witness/cloud-witness-create-storage-account.png":::

1. Asegúrese de que la cuenta de almacenamiento pasa la validación y revise la configuración de la cuenta. Cuando haya terminado, haga clic en **Crear**.

    :::image type="content" source="media/witness/cloud-witness-validation.png" alt-text="Validación de la cuenta de Azure Storage" lightbox="media/witness/cloud-witness-validation.png":::

1. La implementación de la cuenta puede tardar unos segundos en ejecutarse en Azure. Cuando la implementación finalice, haga clic en **Ir al recurso**.

    :::image type="content" source="media/witness/cloud-witness-deployment.png" alt-text="Implementación de la cuenta de Azure Storage" lightbox="media/witness/cloud-witness-deployment.png":::

## <a name="copy-the-access-key-and-endpoint-url"></a>Copia de la clave de acceso y la dirección URL del punto de conexión

Cuando se crea una cuenta de Azure Storage, el proceso genera automáticamente dos claves de acceso, una clave principal (Key1) y una clave secundaria (Key2). Para la primera vez que se crea un testigo en la nube, se usa **Key1**. La dirección URL del punto de conexión también se genera automáticamente.

Un testigo en la nube de Azure usa un archivo blob para el almacenamiento, con un punto de conexión generado con el formato *nombre_de_la_cuenta_de_almacenamiento.blob.core.windows.net* como punto de conexión. 

> [!NOTE]  
> El testigo en la nube de Azure usa HTTPS (puerto predeterminado 443) para establecer la comunicación con el servicio de blobs de Azure. Asegúrese de que el puerto HTTPS es accesible.

### <a name="copy-the-account-name-and-access-key"></a>Copia del nombre de cuenta y la clave de acceso

1. En Azure Portal, en **Configuración**, seleccione **Claves de acceso**.
1. Seleccione **Mostrar claves** para mostrar la información de las claves.
1. Haga clic en el icono de copiar y pegar situado a la derecha de los campos **Nombre de la cuenta de almacenamiento** y **Key1** y pegue cada cadena de texto en el Bloc de notas o en otro editor de texto.

    :::image type="content" source="media/witness/cloud-witness-access-keys.png" alt-text="Claves de acceso de la cuenta de Azure Storage" lightbox="media/witness/cloud-witness-access-keys.png":::

### <a name="copy-the-endpoint-url-optional"></a>Copia de la dirección URL del punto de conexión (opcional)

La dirección URL del punto de conexión es opcional y es posible que no sea necesaria para un testigo en la nube.

1. En Azure Portal, seleccione **Propiedades**.
1. Seleccione **Mostrar claves** para mostrar la información del punto de conexión.
1. En **Blob service**, haga clic en el icono de copiar y pegar situado a la derecha del campo **Blob service** y pegue la cadena de texto en el Bloc de notas o en otro editor de texto.

    :::image type="content" source="media/witness/cloud-witness-blob-service.png" alt-text="Punto de conexión del blob de Azure" lightbox="media/witness/cloud-witness-blob-service.png":::

## <a name="create-a-cloud-witness-using-windows-admin-center"></a>Creación de un testigo en la nube mediante Windows Admin Center

Ahora está listo para crear una instancia del testigo para el clúster mediante Windows Admin Center.

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Configuración**.
1. En el panel derecho, seleccione **Testigo**.
1. En **Witness type** (Tipo de testigo), seleccione una de las siguientes opciones:
      - **Testigo en la nube**: escriba el nombre, la clave de acceso y la dirección URL del punto de conexión de la cuenta de Azure Storage, tal como se describió anteriormente.
      - **Testigo de recurso compartido de archivos**: escriba la ruta de acceso del recurso compartido de archivos "(//server/share)".
1. En el caso de un testigo en la nube, para los siguientes campos, pegue las cadenas de texto que copió anteriormente para:
    1. **Nombre de la cuenta de almacenamiento de Azure**
    1. **Clave de acceso de almacenamiento de Azure**
    1. **Punto de conexión del servicio de Azure**

    :::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Claves de acceso del testigo en la nube" lightbox="media/witness/cloud-witness-1.png":::

1. Cuando termine, haga clic en **Guardar**. La información puede tardar un poco en propagarse a Azure.

> [!NOTE]
> La tercera opción, **Testigo de disco**, no es adecuada para usarse en clústeres extendidos.

## <a name="create-a-cloud-witness-using-windows-powershell"></a>Creación de un testigo en la nube mediante Windows PowerShell

Como alternativa, puede crear una instancia de testigo para el clúster mediante PowerShell.

Use el cmdlet siguiente para crear un testigo en la nube de Azure. Escriba la información de nombre y clave de acceso de la cuenta de Azure Storage como se describió anteriormente:

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

Use el cmdlet siguiente para crear un testigo de recurso compartido de archivos. Escriba la ruta de acceso al recurso compartido del servidor de archivos:

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el cuórum de clúster, consulte [Descripción del cuórum de clúster y de grupo en Azure Stack HCl](../concepts/quorum.md).

- Para más información sobre la creación y administración de cuentas de Azure Storage, consulte [Creación de una cuenta de Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create).