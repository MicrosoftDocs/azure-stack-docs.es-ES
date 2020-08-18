---
title: Configuración de un testigo del clúster
description: Aprenda a configurar un testigo del clúster.
author: v-dasis
ms.topic: how-to
ms.date: 08/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 43d63e4e565a36c30837e2a8b460e0d73a35c0a5
ms.sourcegitcommit: 7d518629bd55f24e7459404bb19b7db8a54f4b94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88145557"
---
# <a name="set-up-a-cluster-witness"></a>Configuración de un testigo del clúster

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

La configuración de un recurso de testigo es obligatoria para todos los clústeres y debe configurarse justo después de crear un clúster. Los clústeres de dos nodos necesitan un testigo para que los servidores que se quede sin conexión no provoquen que el otro nodo deje de estar disponible. Los clústeres de tres y más nodos necesitan un testigo para poder soportar dos servidores con errores o sin conexión.  

Puede usar un recurso compartido de archivos de SMB como testigo o utilizar un testigo en la nube de Azure. Le recomendamos que use un testigo en la nube de Azure, siempre que todos los nodos de servidor del clúster tengan una conexión a Internet confiable. Para obtener más información, consulte [Implementación de un testigo en la nube para un clúster de conmutación por error](/windows-server/failover-clustering/deploy-cloud-witness).

En el caso de los testigos de recurso compartido de archivos, existen requisitos para el servidor de archivos. Para obtener más información, consulte el artículo [Antes de implementar Azure Stack HCI](before-you-start.md).

## <a name="set-up-a-witness-using-windows-admin-center"></a>Configuración de un testigo mediante Windows Admin Center

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Configuración**.
1. En el panel derecho, seleccione **Testigo**.
1. En **Witness type** (Tipo de testigo), seleccione una de las siguientes opciones:
      - **Testigo en la nube**: escriba el nombre, la clave de acceso y la dirección URL del punto de conexión de la cuenta de Azure Storage como se indica a continuación.
      - **Testigo de recurso compartido de archivos**: escriba la ruta de acceso del recurso compartido de archivos "(//server/share)".

> [!NOTE]
> La tercera opción, **Testigo de disco**, no es adecuada para usarse en clústeres extendidos.

## <a name="create-an-azure-storage-account-to-use-as-a-cloud-witness"></a>Creación de una cuenta de Azure Storage para su uso como testigo en la nube

En esta sección se describe cómo crear una cuenta de almacenamiento y cómo ver y copiar las direcciones URL del punto de conexión y las claves de acceso de esa cuenta.

Para configurar el testigo en la nube, debe tener una cuenta de Azure Storage válida que se pueda usar para almacenar el archivo de blob (usado para el arbitraje). El testigo en la nube crea un contenedor conocido **msft-cloud-witness** en la cuenta de almacenamiento de Microsoft. El testigo en la nube escribe un único archivo de blob con el identificador único del clúster correspondiente que se usa como nombre de archivo del archivo de blob en este contenedor **msft-cloud-witness**. Esto significa que puede usar la misma cuenta de Microsoft Azure Storage para configurar un testigo en la nube para varios clústeres diferentes.

Cuando se usa la misma cuenta de Azure Storage para configurar el testigo en la nube para varios clústeres diferentes, se crea automáticamente un solo contenedor **msft-cloud-witness**. Este contenedor contendrá un archivo de un blob por clúster.

> [!NOTE]  
> El testigo en la nube usa HTTPS (puerto predeterminado 443) para establecer la comunicación con Azure Blob service. Asegúrese de que se puede acceder al puerto HTTPS a través del proxy.

### <a name="to-create-an-azure-storage-account"></a>Para crear una cuenta de Azure Storage

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En el menú del concentrador, seleccione Nuevo -> Datos y almacenamiento -> Cuenta de almacenamiento.
1. En la página Crear una cuenta de almacenamiento, haga lo siguiente:
    1. Escriba un nombre para la cuenta de almacenamiento.
    <br>Los nombres de las cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden incluir números y letras en minúscula. El nombre de la cuenta de almacenamiento también debe ser único dentro de Azure.
    1. En **Tipo de cuenta** seleccione **Uso general**.
    <br>No se puede usar una cuenta de almacenamiento de blobs para un testigo en la nube.
    1. En **Rendimiento**, seleccione **Estándar**.
    <br>No se puede usar Azure Premium Storage para un testigo en la nube.
    1. En **Replicación**, seleccione **Almacenamiento con redundancia local (LRS)** .
    <br>Los clústeres de conmutación por error usan el archivo de blob como punto de arbitraje, lo cual requiere algunas garantías de coherencia al leer los datos. Por tanto, debe seleccionar **Almacenamiento con redundancia local (LRS)** como tipo de **Replicación**.

### <a name="view-and-copy-storage-access-keys-for-your-azure-storage-account"></a>Visualización y copia de las claves de acceso de almacenamiento de la cuenta de Azure Storage

Cuando se crea una cuenta de Microsoft Azure Storage, esta se asocia a dos claves de acceso que se generan automáticamente: la clave de acceso primaria y la secundaria. Para crear por primera vez un testigo en la nube, use la **clave de acceso primaria**. No hay ninguna restricción respecto a la clave que se va a usar para el testigo en la nube.  

#### <a name="to-view-and-copy-storage-access-keys"></a>Visualización y copia de las claves de acceso de almacenamiento

En Azure Portal, vaya a su cuenta de almacenamiento, haga clic en **Toda la configuración** y luego en **Claves de acceso** para ver, copiar y volver a generar las claves de acceso de la cuenta. La hoja de claves de acceso también incluye cadenas de conexión configuradas previamente que usan claves principales y secundarias que puede copiar para usarlas en las aplicaciones.

:::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Claves de acceso del testigo en la nube" lightbox="media/witness/cloud-witness-1.png":::

### <a name="view-and-copy-endpoint-url-links"></a>Visualización y copia de los vínculos de las direcciones URL del punto de conexión

Al crear una cuenta de almacenamiento, se generan las siguientes direcciones URL con el formato: `https://<Storage Account Name>.<Storage Type>.<Endpoint>`  

El testigo de la nube siempre usa **Blob** como tipo de almacenamiento. Azure usa **.core.windows.net** como punto de conexión. Al configurar el testigo de la nube, es posible que se configure con un punto de conexión diferente según el escenario (por ejemplo, el centro de datos de Microsoft Azure en China tiene un punto de conexión diferente).  

> [!NOTE]  
> El recurso del testigo en la nube genera automáticamente la dirección URL del punto de conexión y no hay ningún paso adicional de configuración que sea necesario para ello.  

#### <a name="to-view-and-copy-endpoint-url-links"></a>Visualización y copia de los vínculos de las direcciones URL del punto de conexión

En Azure Portal, vaya a su cuenta de almacenamiento, haga clic en **Toda la configuración** y después en **Propiedades** para ver y copiar las direcciones URL del punto de conexión.  

:::image type="content" source="media/witness/cloud-witness-2.png" alt-text="Dirección URL del punto de conexión del testigo en la nube" lightbox="media/witness/cloud-witness-2.png":::  

## <a name="set-up-a-witness-using-windows-powershell"></a>Configuración de un testigo mediante Windows PowerShell

Para configurar un testigo de clúster con PowerShell, ejecute uno de los siguientes cmdlets.

Use el cmdlet siguiente para crear un testigo en la nube de Azure.

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

Use el cmdlet siguiente para crear un testigo de recurso compartido de archivos:

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el cuórum de clúster, consulte [Descripción del cuórum de clúster y de grupo en Azure Stack HCl](../concepts/quorum.md).

- Para más información sobre creación y administración de cuentas de Azure Storage, consulte [Acerca de las cuentas de Azure Storage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/).
