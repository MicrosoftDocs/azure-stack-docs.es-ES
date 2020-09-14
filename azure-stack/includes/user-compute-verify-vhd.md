---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: c8f336f48fd5ef584d826c27fef52b2560fe17fe
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609928"
---
Antes de cargar el disco duro virtual, debe comprobar que este reúne los requisitos. Los discos duros virtuales que no cumplan los requisitos no se cargarán en Azure Stack Hub.

1. Usará los módulos de PowerShell que se encuentran con Hyper-V. Al activar Hyper-V, se instalan los módulos de PowerShell. Para comprobar que tiene el módulo, abra PowerShell con un símbolo del sistema con privilegios elevados y ejecute el siguiente cmdlet:

    ```powershell  
    Get-Command -Module hyper-v
    ```

    Si no tiene los comandos de Hyper-V, consulte [Trabajar con Hyper-V y Windows PowerShell](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/try-hyper-v-powershell). 

2. Obtenga la ruta de acceso al disco duro virtual de su máquina. Ejecute el siguiente cmdlet:

    ```powershell  
    get-vhd <path-to-your-VHD>
    ```

    El cmdlet devolverá el objeto VHD y mostrará los atributos, como:
    
    ```powershell  
    ComputerName            : YOURMACHINENAME
    Path                    : <path-to-your-VHD>
    VhdFormat               : VHD
    VhdType                 : Fixed
    FileSize                : 68719477248
    Size                    : 68719476736
    MinimumSize             : 32212254720
    LogicalSectorSize       : 512
    PhysicalSectorSize      : 512
    BlockSize               : 0
    ParentPath              :
    DiskIdentifier          : 3C084D21-652A-4C0E-B2D1-63A8E8E64C0C
    FragmentationPercentage : 0
    Alignment               : 1
    Attached                : False
    DiskNumber              :
    IsPMEMCompatible        : False
    AddressAbstractionType  : None
    Number                  :
    ```

3. Con el objeto VHD, compruebe que cumple los requisitos de Azure Stack Hub.
    - [El disco duro virtual es de tipo fijo.](#vhd-is-of-fixed-type)
    - [El disco duro virtual tiene un tamaño mínimo de 20 MB.](#vhd-has-minimum-virtual-size-of-at-least-20-mb)
    - [El VHD está alineado.](#vhd-is-aligned)
    - [La longitud del blob del disco duro virtual = tamaño virtual + longitud de pie de página del disco duro virtual (512).](#vhd-blob-length) 
    
    Ademas, Azure Stack Hub solo admite imágenes de [máquinas virtuales de la generación uno (1).](#generation-one-vms)

4. Si el disco duro virtual no es compatible con Azure Stack Hub, tendrá que volver a la imagen de origen y a Hyper-V, crear un disco duro virtual que cumpla los requisitos y cargarlo. Para minimizar los posibles daños en el proceso de carga, use AzCopy.

### <a name="how-to-fix-your-vhd"></a>Corrección del disco duro virtual

Se deben cumplir los siguientes requisitos para la compatibilidad del disco duro virtual con Azure Stack Hub.

#### <a name="vhd-is-of-fixed-type"></a>El disco duro virtual es de tipo fijo.
**Identifique**: Use el cmdlet `get-vhd` para obtener el objeto VHD.  
**Corrección**: Puede convertir un archivo VHDX en VHD y también convertir un disco de expansión dinámica en un disco de tamaño fijo, pero no puede cambiar la generación de una máquina virtual.
Use el [administrador de Hyper-V o PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-convert-the-disk) para convertir el disco.

### <a name="vhd-has-minimum-virtual-size-of-at-least-20-mb"></a>El disco duro virtual tiene un tamaño mínimo de 20 MB.
**Identifique**: Use el cmdlet `get-vhd` para obtener el objeto VHD.  
**Corrección**: Use el [administrador de Hyper-V o PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) para cambiar el tamaño del disco. 

### <a name="vhd-is-aligned"></a>El VHD está alineado.
**Identifique**: Use el cmdlet `get-vhd` para obtener el objeto VHD.  
**Corrección**: El tamaño virtual debe ser un múltiplo de uno (1) MB. 

Los discos deben tener un tamaño virtual alineado a 1 MiB. Si el disco duro virtual es una fracción de 1 MiB, deberá cambiar el tamaño del disco a un múltiplo de 1 MiB. Los discos que son fracciones de 1 MiB provocan errores al crear imágenes a partir del disco duro virtual cargado. Para comprobar el tamaño, puede usar el cmdlet Get-VHD de PowerShell para mostrar el valor de "Size", que debe ser un múltiplo de 1 MiB en Azure y "FileSize", que será igual al valor de "Size" más 512 bytes para el pie de página del VHD.

Use el [administrador de Hyper-V o PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) para cambiar el tamaño del disco. 


### <a name="vhd-blob-length"></a>Longitud de blob del disco duro virtual
**Identifique**: Use el cmdlet `get-vhd` para mostrar `Size`   
**Corrección**: La longitud del blob del disco duro virtual = tamaño virtual + longitud de pie de página del disco duro virtual (512). Un breve pie de página al final del blob describe las propiedades del disco duro virtual. `Size` debe ser un múltiplo de 1 MiB en Azure y `FileSize`, será igual a `Size` + 512 bytes para el pie de página del disco duro virtual.

Use el [administrador de Hyper-V o PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) para cambiar el tamaño del disco. 

### <a name="generation-one-vms"></a>Máquinas virtuales de generación 1
**Identifique**: Para confirmar si la máquina virtual es de la generación 1, use el cmdlet `Get-VM | Format-Table Name, Generation`.  
**Corrección**: Tendrá que volver a crear la máquina virtual en el hipervisor (Hyper-V).