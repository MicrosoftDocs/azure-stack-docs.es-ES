---
title: Eliminación de volúmenes en Azure Stack HCI
description: Eliminación de volúmenes en Azure Stack HCI mediante Windows Admin Center y PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 7a90948cccb75bb8bb1578101d530c46852e99d6
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866529"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Eliminación de volúmenes en Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se proporcionan instrucciones para eliminar volúmenes en un clúster de Azure Stack HCI mediante Windows Admin Center.

Vea un vídeo rápido sobre cómo eliminar un volumen con Windows Admin Center.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Uso de Windows Admin Center para eliminar un volumen

1. En Windows Admin Center, conéctese a un clúster de Espacios de almacenamiento directo y seleccione **Volumes** (Volúmenes) en el panel **Tools** (Herramientas).
2. En la página **Volumes** (Volúmenes), seleccione la pestaña **Inventory** (Inventario) y, a continuación, seleccione el volumen que quiere eliminar.
3. En la parte superior de la página de detalles del volumen, seleccione **Delete** (Eliminar).
4. En el cuadro de diálogo de confirmación, active la casilla para confirmar que desea eliminar el volumen y seleccione **Delete** (Eliminar).

## <a name="delete-volumes-using-powershell"></a>Eliminación de volúmenes con PowerShell

Use el cmdlet **Remove-VirtualDisk** para eliminar volúmenes en Espacios de almacenamiento directo. Este cmdlet se utiliza para eliminar el objeto **VirtualDisk** y devolver el espacio que usaba al grupo de almacenamiento que expone el objeto **VirtualDisk**.

En primer lugar, inicie PowerShell en el equipo de administración y ejecute el cmdlet **Get-VirtualDisk** con el parámetro **CimSession**, que es el nombre de un clúster de Espacios de almacenamiento directo o un nodo de servidor, por ejemplo *nombreDelClúster.microsoft.com*:

```PowerShell
Get-VirtualDisk -CimSession clustername.microsoft.com
```

Esto devolverá una lista de valores posibles para el parámetro **-FriendlyName**, que se corresponde con los nombres de los volúmenes del clúster.

### <a name="example"></a>Ejemplo

Para eliminar un volumen reflejado llamado *Volume1*, ejecute el siguiente comando en PowerShell:

```PowerShell
Remove-VirtualDisk -FriendlyName "Volume1"
```

Se le pedirá que confirme que desea realizar la acción y borrar todos los datos que contiene el volumen. Elija Sí o No.

   > [!WARNING]
   > Esta no es una acción recuperable. En este ejemplo se elimina de forma permanente un objeto de volumen **VirtualDisk**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener instrucciones detalladas sobre otras tareas de administración del almacenamiento esenciales, consulte también:

- [Planeamiento de volúmenes](../concepts/plan-volumes.md)
- [Creación de volúmenes](create-volumes.md)
- [Extensión de volúmenes](extend-volumes.md)