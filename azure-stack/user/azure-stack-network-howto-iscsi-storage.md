---
title: Conexión al almacenamiento de iSCSI con Azure Stack Hub | Microsoft Docs
description: Aprenda a conectarse al almacenamiento de iSCSI con Azure Stack Hub.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 5616284e7632f89ba31c89febb5a26158ad81bd7
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75879044"
---
# <a name="how-to-connect-to-iscsi-storage-with-azure-stack-hub"></a>Conexión al almacenamiento de iSCSI con Azure Stack Hub

Puede usar la plantilla de este artículo para conectar una máquina virtual de Azure Stack Hub a un destino iSCSI local. Configure la máquina virtual para que use el almacenamiento hospedado fuera de nuestra instancia de Azure Stack Hub y en otra parte de su centro de datos. En este artículo se examina el uso de una máquina Windows como destino iSCSI.

La plantilla se puede encontrar en la bifurcación **lucidqdreams** del repositorio de GitHub [Azure Intelligent Edge Patterns](https://github.com/lucidqdreams/azure-intelligent-edge-patterns). La plantilla se encuentra en la carpeta **storage-iSCSI**. La plantilla se ha diseñado para configurar la infraestructura necesaria en Azure Stack Hub para conectarse a un destino iSCSI. Esto incluye una máquina virtual que actuará como el iniciador iSCSI junto con la red virtual, el grupo de seguridad de red, el PIP y el almacenamiento que lo acompañan. Una vez implementada la plantilla, es preciso ejecutar dos scripts de PowerShell para completar la configuración. Un script se ejecutará en la máquina virtual local (destino) y otro se ejecutará en la máquina virtual de Azure Stack Hub (iniciador). Una vez que finalicen ambas ejecuciones, el almacenamiento local se habrá agregado a la máquina virtual de Azure Stack Hub. 

## <a name="overview"></a>Información general

En el diagrama se muestra una máquina virtual hospedada en Azure Stack Hub con un disco montado en iSCSI desde una máquina Windows local (física o virtual) que permite que el almacenamiento externo en Azure Stack Hub pueda montarse dentro de la máquina virtual hospedada en Azure Stack Hub a través del protocolo iSCSI.

![texto alternativo](./media/azure-stack-network-howto-iscsi-storage/overview.png)

### <a name="requirements"></a>Requisitos

- Una máquina local (física o virtual) con Windows Server 2016 Datacenter o Windows Server 2019 Datacenter.
- Elementos de Marketplace requeridos para Azure Stack Hub:
    -  Windows Server 2016 Datacenter o Windows Server 2019 Datacenter (se recomienda la última compilación).
    -  Extensión DSC de PowerShell.
    -  Extensión de script personalizado.
    -  Una máquina virtual o física existente. Lo ideal sería que la máquina tuviera dos adaptadores de red. También podría ser otro destino iSCSI como una SAN, por ejemplo.

### <a name="things-to-consider"></a>Aspectos que se deben tener en cuenta:

- Se aplica un grupo de seguridad de red a la subred de la plantilla. Revise este aspecto y realice las provisiones adicionales que sean necesarias.
- Se aplica una regla de denegación de RDP al grupo de seguridad de red del túnel y debe establecerse en permitir si tiene previsto acceder a las máquinas virtuales mediante la dirección IP pública.
- Esta solución no tiene en cuenta la resolución de DNS.
- Debe cambiar Chapusername y Chappassword. Chappassword debe tener entre 12 y 16 caracteres.
- Esta plantilla usa una dirección IP estática para la máquina virtual, ya que la conexión iSCSI usa la dirección local en la configuración.
- Esta plantilla usa licencia BYOL de Windows.
- También puede conectar sistemas Linux a los destinos iSCSI. Puede encontrar instrucciones al respecto en el artículo acerca del [iniciador de iSCSI](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html) de la documentación de Ubuntu.

### <a name="options"></a>Opciones

- Puede usar su propia cuenta de almacenamiento de blobs y el token de SAS con los parámetros **_artifactsLocation** y **_artifactsLocationSasToken** y la capacidad para usar su propio blob de almacenamiento con un token de SAS.
- Esta plantilla proporciona valores predeterminados para la nomenclatura de redes virtuales y el direccionamiento IP.
- Esta configuración solo tiene una tarjeta de interfaz de red de iSCSI procedente del cliente iSCSI. Se han probado varias configuraciones para usar subredes y tarjetas de interfaz de red independientes, pero se han producido problemas con varias puertas de enlace y se ha intentado crear una subred de almacenamiento independiente para aislar el tráfico y que, realmente, haya redundancia. 
- Procure que estos valores estén en todo momento dentro de los rangos de direcciones y subredes legales, ya que la implementación puede no realizarse. 
- El objetivo principal de los paquetes de DSC de PowerShell es comprobar los reinicios pendientes. Este DSC se puede personalizar más, en caso de que sea necesario. Para más información, consulte [ComputerManagementDsc](https://github.com/PowerShell/ComputerManagementDsc/).

### <a name="resource-group-template-iscsi-client"></a>Plantilla de grupo de recursos (cliente iSCSI)

En el diagrama se muestran los recursos implementados desde la plantilla para crear el cliente iSCSI que puede usar para conectarse al destino iSCSI. Esta plantilla implementará la máquina virtual y otros recursos; además, ejecutará prepare-iSCSIClient.ps1 y reiniciará la máquina virtual.

![texto alternativo](./media/azure-stack-network-howto-iscsi-storage/iscsi-file-server.png)

### <a name="the-deployment-process"></a>El proceso de implementación

La plantilla del grupo de recursos genera una salida pensada para que sea la entrada del siguiente paso. Se centra principalmente en el nombre del servidor y en la dirección IP pública de Azure Stack Hub de la que parte el tráfico iSCSI. En este ejemplo:

1. Implemente la plantilla de la infraestructura.
2. Implemente una máquina virtual de Azure Stack Hub en una máquina virtual hospedada en otra parte del centro de datos. 
3. Ejecute `Create-iSCSITarget.ps1` con la dirección IP y el nombre del servidor que aparecen en la plantilla como parámetros de entrada-salida para el script en el destino iSCSI, que puede ser una máquina virtual o un servidor físico.
4. Use la dirección IP externa o las direcciones del servidor de destino iSCSI como entradas para ejecutar el script de `Connect-toiSCSITarget.ps1`. 

![texto alternativo](./media/azure-stack-network-howto-iscsi-storage/process.png)

### <a name="inputs-for-azuredeployjson"></a>Entradas de azuredeploy.json

|**Parámetros**|**valor predeterminado**|**description**|
|------------------|---------------|------------------------------|
|WindowsImageSKU         |2019-Datacenter   |Seleccione la imagen de máquina virtual Windows base
|VMSize                  |Standard_D2_v2    |Especifique el tamaño de la máquina virtual
|VMName                  |FileServer        |Nombre de la máquina virtual
|adminUsername           |storageadmin      |El nombre del administrador de la nueva máquina virtual
|adminPassword           |                  |La contraseña de la cuenta Administrador de las máquinas virtuales nuevas. El valor predeterminado es el identificador de la suscripción
|VNetName                |Storage           |El nombre de la red virtual. Se usará para etiquetar los recursos
|VNetAddressSpace        |10.10.0.0/23      |Espacio de direcciones para la red virtual
|VNetInternalSubnetName  |Interno          |Nombre de la subred interna de la red virtual
|VNetInternalSubnetRange |10.10.1.0/24      |Intervalo de direcciones para la subred interna de la red virtual
|InternalVNetIP          |10.10.1.4         |Dirección estática para la dirección IP interna del servidor de archivos.
|_artifactsLocation      ||
|_artifactsLocationSasToken||

### <a name="deployment-steps"></a>Pasos de implementación

1. Implemente la infraestructura de cliente iSCSI mediante `azuredeploy.json`
2. Ejecute `Create-iSCSITarget.ps1` en el destino iSCSI del servidor local. Una vez que se complete la plantilla, tendrá que ejecutar el archivo Create-iSCSITarget.ps1 en el destino iSCSI del servidor local con las salidas del primer paso
3. Ejecute `Connect-toiSCSITarget.ps1` en el cliente iSCSI. Ejecute el archivo Connect-toiSCSITarget.ps1 en el cliente iSCSI con los detalles del destino iSCSI

## <a name="adding-iscsi-storage-to-existing-vms"></a>Adición del almacenamiento iSCSI a las máquinas virtuales existentes

También puede ejecutar los scripts en una máquina virtual existente para conectarse desde el cliente iSCSI a un destino iSCSI. Este flujo se da si va a crear el destino iSCSI. En este diagrama se muestra el flujo de ejecución de los scripts de PowerShell. Estos scripts se pueden encontrar en el directorio Script:

![texto alternativo](./media/azure-stack-network-howto-iscsi-storage/script-flow.png)

### <a name="prepare-iscsiclientps1"></a>Prepare-iSCSIClient.ps1

El script `Prepare-iSCSIClient.ps1` instala los requisitos previos en el cliente iSCSI, lo que incluye:
- la instalación de los servicios de E/S de múltiples rutas
- la configuración del inicio del servicio del iniciador iSCSI como automático
- la habilitación de la compatibilidad con E/S de múltiples rutas en iSCSI
- la habilitación de notificaciones automáticas de todos los volúmenes iSCSI
- establecimiento del tiempo de espera del disco en 60 segundos

Es importante reiniciar el sistema después de instalar estos requisitos previos. Para poder establecer la directiva de equilibrio de carga de E/S de múltiples rutas es preciso reiniciar el sistema.

### <a name="create-iscsitargetps1"></a>Create-iSCSITarget.ps1

El script `Create-iSCSITarget.ps1 `se debe ejecutar en el sistema que presta servicio al almacenamiento. Puede crear varios discos y destinos, y la única restricción la establecen los iniciadores. Este script se puede ejecutar varias veces para crear muchos discos virtuales que se pueden conectar a diferentes destinos. Puede conectar varios discos a un solo destino. 

|**Entrada**|**valor predeterminado**|**description**|
|------------------|---------------|------------------------------|
|RemoteServer         |FileServer               |El nombre del servidor que se conecta al destino iSCSI
|RemoteServerIPs      |1.1.1.1                  |La dirección IP desde la que provendrá el tráfico iSCSI
|DiskFolder           |C:\iSCSIVirtualDisks     |La carpeta y unidad donde se almacenarán los discos virtuales
|DiskName             |DiskName                 |El nombre del archivo VHDX del disco
|DiskSize             |5 GB                      |El tamaño del disco VHDX
|TargetName           |RemoteTarget01           |El nombre de destino que se usa para definir la configuración de destino del cliente iSCSI. 
|ChapUsername         |username                 |El nombre de usuario de la autenticación CHAP
|ChapPassword         |userP@ssw0rd!            |El nombre de la contraseña de la autenticación CHAP. Debe tener entre 12 y 16 caracteres

### <a name="connect-toiscsitargetps1"></a>Connect-toiSCSITarget.ps1

`Connect-toiSCSITarget.ps1` es el script final, que se ejecuta en el cliente iSCSI y monta el disco presentado por el destino iSCSI en el cliente iSCSI.

|**Entrada**|**valor predeterminado**|**description**|
|------------------|---------------|------------------------------|
|TargetiSCSIAddresses   |"2.2.2.2","2.2.2.3"    |Las direcciones IP del destino iSCSI
|LocalIPAddresses       |"10.10.1.4"            |Esta es la dirección IP interna desde la que provendrá el tráfico iSCSI
|LoadBalancePolicy      |C:\iSCSIVirtualDisks   |La dirección IP desde la que provendrá el tráfico iSCSI
|ChapUsername           |username               |El nombre de usuario de la autenticación CHAP
|ChapPassword           |userP@ssw0rd!          |El nombre de la contraseña de la autenticación CHAP. Debe tener entre 12 y 16 caracteres

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md)  