---
title: Configuración de un testigo del clúster
description: Aprenda a configurar un testigo del clúster.
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e660d1cc10e5d4b4e3d1ffbebeee99e421cd6dbb
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947476"
---
# <a name="set-up-a-cluster-witness"></a>Configuración de un testigo del clúster

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

La configuración de un recurso de testigo es obligatoria para todos los clústeres y debe configurarse justo después de crear un clúster. Los clústeres de dos nodos necesitan un testigo para que los servidores que se quede sin conexión no provoquen que el otro nodo deje de estar disponible. Los clústeres de tres y más nodos necesitan un testigo para poder soportar dos servidores con errores o sin conexión.  

Puede usar un recurso compartido de archivos como testigo o utilizar un testigo en la nube de Azure. Le recomendamos que use un testigo en la nube de Azure, siempre que todos los nodos de servidor del clúster tengan una conexión a Internet confiable. Para obtener más información, consulte [Implementación de un testigo en la nube para un clúster de conmutación por error](/windows-server/failover-clustering/deploy-cloud-witness).

En el caso de los testigos de recurso compartido de archivos, existen requisitos para el servidor de archivos. Para obtener más información, consulte el artículo [Antes de implementar Azure Stack HCI](before-you-start.md).

## <a name="set-up-a-witness-using-windows-admin-center"></a>Configuración de un testigo mediante Windows Admin Center

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Configuración**.
1. En el panel derecho, seleccione **Testigo**.
1. En **Witness type** (Tipo de testigo), seleccione una de las siguientes opciones:
      - **Testigo en la nube**: escriba el nombre, la clave y el punto de conexión de la cuenta de Azure Storage.
      - **Testigo de recurso compartido de archivos**: escriba la ruta de acceso del recurso compartido de archivos (//server/share).

> [!NOTE]
> La tercera opción, **Testigo de disco**, no es adecuada para usarse en clústeres extendidos.

## <a name="set-up-a-witness-using-windows-powershell"></a>Configuración de un testigo mediante Windows PowerShell

Para configurar un testigo de clúster con PowerShell, ejecute uno de los siguientes cmdlets.

Use el cmdlet siguiente para crear un testigo en la nube de Azure.

```powershell
Set-ClusterQuorum –Cluster Cluster1 -CloudWitness -AccountName <AzureStorageAccountName> -AccessKey <AzureStorageAccountAccessKey>
```

Use el cmdlet siguiente para crear un testigo de recurso compartido de archivos:

```powershell
Set-ClusterQuorum –Cluster Cluster1 -FileShareWitness \\fileserver\fsw
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el cuórum de clúster, consulte [Descripción del cuórum de clúster y de grupo en Azure Stack HCl](../concepts/quorum.md).
