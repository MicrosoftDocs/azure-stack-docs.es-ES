---
title: Uso de plantillas de Resource Manager para discos administrados de Azure Stack Hub
description: Conozca las diferencias entre discos administrados y discos no administrados cuando se usan plantillas de Azure Resource Manager.
author: sethmanheim
ms.author: sethm
ms.date: 8/25/2020
ms.topic: conceptual
ms.reviewer: wellsluo
ms.lastreviewed: 8/25/2020
ms.openlocfilehash: 2d2f274fffd1a07857b79255587b659003a3a1ce
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2020
ms.locfileid: "88823275"
---
# <a name="use-vm-managed-disks-templates"></a>Uso de discos administrados de máquina virtual

En este artículo se describen las diferencias entre discos administrados y discos no administrados cuando se usan plantillas de Azure Resource Manager para aprovisionar máquinas virtuales en Azure Stack Hub. Los ejemplos le ayudarán a convertir las plantillas existentes que usan discos no administrados a discos administrados.

## <a name="unmanaged-disks-template-formatting"></a>Formato de plantilla de discos no administrados

Para comenzar, veamos cómo se implementan los discos no administrados. Cuando se crean discos no administrados, se necesita una cuenta de almacenamiento para contener los archivos VHD. Puede crear una cuenta de almacenamiento o usar una ya existente. Cree un recurso de cuenta de almacenamiento en el bloque de recursos de la plantilla de la siguiente manera:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2017-10-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Dentro del objeto de máquina virtual, agregue una dependencia de la cuenta de almacenamiento para garantizar que se cree antes que la máquina virtual. En la sección `storageProfile`, se especifica el URI completo de la ubicación de VHD, que hace referencia a la cuenta de almacenamiento y se necesita para el disco de OS y cualquier disco de datos. En el ejemplo siguiente se crea un disco de sistema operativo a partir de una imagen y un disco de datos vacío con un tamaño de 1023 GB:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formato de plantilla de discos administrados

Con los discos administrados de Azure, el disco se convierte en un recurso de nivel superior y ya no es necesario que el usuario cree una cuenta de almacenamiento. Los discos administrados se introdujeron por primera vez en la versión de API de `2017-03-30`. En las secciones siguientes se describe la configuración predeterminada y se indica cómo personalizar aún más los discos.

### <a name="default-managed-disk-settings"></a>Configuración predeterminada de discos administrados

Para crear una VM con discos administrados, ya no es necesario crear el recurso de cuenta de almacenamiento. En el ejemplo de plantilla siguiente hay algunas diferencias con los ejemplos de discos no administrados anteriores:

- `apiVersion` es una versión para un tipo de recurso "virtualMachines" que admite discos administrados.
- `osDisk` y `dataDisks` ya no hacen referencia a un URI específico para VHD.
- Cuando se realiza la implementación sin especificar propiedades adicionales, el disco usa un tipo de almacenamiento acorde con el tamaño de la máquina virtual. Por ejemplo, si usa un tamaño de máquina virtual compatible con Premium Storage (tamaños con "s" en su nombre, como Standard_DS2_v2), los discos Premium se configurarán de manera predeterminada. Para modificar esta opción, use la configuración de la SKU del disco para especificar un tipo de almacenamiento.
- Si no se especifica ningún nombre para el disco, toma el formato de `<VMName>_OsDisk_1_<randomstring>` para el disco de SO y `<VMName>_disk<#>_<randomstring>` para cada disco de datos.
  - Si se crea una máquina virtual a partir de una imagen personalizada, la configuración predeterminada para el tipo de cuenta de almacenamiento y el nombre del disco se recuperan de las propiedades del disco definidas en el recurso de imagen personalizada. Se pueden invalidar mediante la especificación de valores para estos en la plantilla.
- De manera predeterminada, el almacenamiento en caché del disco es **Lectura/escritura** en el caso del disco de sistema operativo y **Ninguno** para los discos de datos.
- En el ejemplo siguiente todavía existe una dependencia de cuenta de almacenamiento, pero es solo para el almacenamiento de diagnósticos y no es necesaria para almacenamiento en disco:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="use-a-top-level-managed-disk-resource"></a>Uso de un recurso de disco administrado de nivel superior

Como alternativa para especificar la configuración de disco en el objeto de máquina virtual, puede crear un recurso de disco de nivel superior y adjuntarlo como parte de la creación de la máquina virtual. Asegúrese de usar `2017-03-30` como la versión de API del recurso `disks`. Por ejemplo, puede crear un recurso de disco de la manera siguiente para usarlo como un disco de datos. En este ejemplo, se usa `vmName` como parte del nombre del disco:

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2017-03-30",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Dentro del objeto de máquina virtual, puede hacer referencia a este objeto de disco para adjuntarlo. Especificar el identificador de recurso del disco administrado que creamos en la propiedad `managedDisk` permite adjuntar el disco cuando se crea la máquina virtual. El valor de la propiedad `apiVersion` del recurso de máquina virtual está establecido en `2017-12-01`. Se agrega una dependencia del recurso de disco para garantizar que se crea correctamente antes de la creación de la máquina virtual:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Creación de conjuntos de disponibilidad administrados con máquinas virtuales con discos administrados

Para crear conjuntos de disponibilidad administrados con máquinas virtuales con discos administrados, agregue el objeto `sku` al recurso de conjunto de disponibilidad y establezca la propiedad `name` en `Aligned`. Esta propiedad garantiza que los discos de cada máquina virtual estén suficientemente aislados entre sí para evitar puntos únicos de error. Tenga en cuenta también que el valor de la propiedad `apiVersion` del recurso del conjunto de disponibilidad está establecido en `2017-12-01`:

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 1,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

<!--
* For full templates that use managed disks visit the following Azure Quickstart Repo links.
    * [Windows VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
-->

- Para más información sobre los discos administrados, consulte [Discos administrados de Azure Stack Hub](azure-stack-managed-disk-considerations.md).
- Revise la documentación de referencia de plantilla de los recursos de máquina virtual en el documento de [referencia de plantilla Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/2017-12-01/virtualmachines).
- Revise la documentación de referencia de plantilla de los recursos de disco en el documento de [referencia de plantilla Microsoft.Compute/disks](/azure/templates/microsoft.compute/2017-03-30/disks).
