---
title: Introducción a las máquinas virtuales de Azure Stack Hub | Microsoft Docs
description: Más información acerca de las máquinas virtuales de Azure Stack Hub.
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 7a7da17dd3b9be99c797b2241ae38bd2d4e6c0d2
ms.sourcegitcommit: ce01b2cd114ca8ab5b70c6311b66c58ceb054469
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2020
ms.locfileid: "75923981"
---
# <a name="introduction-to-azure-stack-hub-vms"></a>Introducción a las máquinas virtuales de Azure Stack Hub

Azure Stack Hub le ofrece máquinas virtuales como un tipo de recurso informático escalable y a petición. Puede elegir una máquina virtual cuando necesite más control sobre el entorno informático. En este artículo se proporcionan detalles antes de crear la primera máquina virtual.

Una máquina virtual de Azure Stack Hub le da la flexibilidad de la virtualización sin necesidad de administrar clústeres o máquinas individuales. Sin embargo, la máquina virtual sigue necesitando mantenimiento con tareas como configurar, aplicar revisiones o actualizaciones e instalar el software que se ejecuta en ella.

Las máquinas virtuales de Azure Stack Hub pueden usarse de diversas maneras. Por ejemplo:

- **Desarrollo y pruebas**: Las máquinas virtuales de Azure Stack Hub permiten crear un equipo con las configuraciones específicas necesarias para codificar y probar una aplicación.

- **Aplicaciones en la nube**: Como la demanda de la aplicación puede fluctuar, tendría sentido desde el punto de vista económico ejecutarla en una máquina virtual en Azure Stack Hub. Pagará por las máquinas virtuales adicionales cuando las necesite y las desactivará cuando ya no sean necesarias.

- **Centro de datos ampliado**: Las máquinas virtuales de una red virtual de Azure Stack Hub se pueden conectar a la red de una organización o a Azure.

Las máquinas virtuales usadas por su aplicación se pueden escalar vertical u horizontalmente a la cifra necesaria para satisfacer sus necesidades.

## <a name="before-creating-a-vm"></a>Antes de crear una máquina virtual

Siempre es necesario tener en cuenta los aspectos relativos al diseño cuando se crea una infraestructura de aplicaciones en Azure Stack Hub. Es necesario reflexionar sobre estos aspectos antes de crear la infraestructura:

- Los nombres de los recursos de la aplicación
- El tamaño de la máquina virtual
- El número máximo de máquinas virtuales que se pueden crear
- El sistema operativo que ejecuta la máquina virtual
- La configuración de la máquina virtual después de iniciarse
- Los recursos relacionados que necesita la máquina virtual

### <a name="naming"></a>Nomenclatura

Una máquina virtual tiene un nombre asignado y un nombre de equipo configurado como parte del sistema operativo. El nombre de una máquina virtual puede tener hasta 15 caracteres.

Si usa Azure Stack Hub para crear el disco de sistema operativo, el nombre del equipo y el nombre de la máquina virtual serán iguales. Si carga y usa su propia imagen que contiene un sistema operativo ya configurado y la usa para crear una máquina virtual, puede que los nombres sean diferentes. Como procedimiento recomendado, cuando cargue su propio archivo de imagen, asegúrese de que el nombre del equipo en el sistema operativo y el nombre de la máquina virtual sean iguales.

### <a name="vm-size"></a>Tamaño de VM

El tamaño de la máquina virtual que use depende de la carga de trabajo que vaya a ejecutar. El tamaño que elija determina factores tales como la capacidad de almacenamiento, la memoria y la capacidad de procesamiento. Azure Stack Hub ofrece diferentes tipos de tamaños que admiten muchos tipos de usos.

### <a name="vm-limits"></a>Límites de máquina virtual

Su suscripción tiene límites de cuota predeterminados que pueden afectar a la implementación de las máquinas virtuales en un proyecto. El límite actual por suscripción es 20 máquinas virtuales por región.

### <a name="operating-system-disks-and-images"></a>Imágenes y discos del sistema operativo

Las máquinas virtuales usan discos duros virtuales (VHD) para almacenar el sistema operativo y los datos. Estos discos también se usan para las imágenes entre las que se puede elegir para instalar un sistema operativo. Azure Stack Hub proporciona un Marketplace para usar con diversas versiones y tipos de sistemas operativos. Las imágenes de Marketplace se identifican mediante el publicador de la imagen, la oferta, la SKU y la versión (normalmente, la última versión se especifica como **la más reciente**).

En la tabla siguiente se muestra como encontrar la información sobre una imagen:

|Método|Descripción|
|---------|---------|
|Portal de Azure Stack Hub|Los valores se especifican automáticamente cuando se selecciona una imagen para usarla.|
|PowerShell de Azure Stack Hub|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|API de REST     |[List image publishers](/rest/api/compute/platformimages/platformimages-list-publishers) (Lista de publicadores de imágenes)<br>[List image offers](/rest/api/compute/platformimages/platformimages-list-publisher-offers) (Lista de ofertas de imágenes)<br>[List image SKUs](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) (Lista de SKU de imágenes)|

Puede elegir cargar y usar su propia imagen. Si lo hace, el nombre del anunciante, la oferta y la SKU no se utilizan.

### <a name="extensions"></a>Extensiones

Las extensiones de máquina virtual ofrecen funcionalidades adicionales de máquina virtual por medio de la configuración posterior a la implementación y tareas automatizadas.
Pueden llevarse a cabo estas tareas comunes mediante las extensiones:

- **Ejecutar scripts personalizados**: La extensión de script personalizado ayuda a configurar cargas de trabajo en la máquina virtual al ejecutar su script cuando se aprovisiona la máquina virtual.

- **Implementar y administrar configuraciones**: La extensión de configuración de estado deseado (DSC) de PowerShell ayuda a configurar DSC en una máquina virtual para administrar entornos y configuraciones.

- **Recopilar datos de diagnóstico**: La extensión Azure Diagnostics ayuda a configurar la máquina virtual para que recopile datos de diagnóstico que sirvan para supervisar el estado de la aplicación.

### <a name="related-resources"></a>Recursos relacionados

Los recursos de la tabla siguiente se usan en la máquina virtual y deben ya existir o crearse al tiempo que esta:

|Resource|Obligatorio|Descripción|
|---------|---------|---------|
|Resource group|Sí|La máquina virtual debe encontrarse en un grupo de recursos.|
|Cuenta de almacenamiento|No|La máquina virtual no necesita la cuenta de Storage para almacenar sus discos duros virtuales si usa discos administrados. <br>La máquina virtual necesita la cuenta de Storage almacene sus discos duros virtuales si usa discos sin administrar.|
|Virtual network|Sí|La máquina virtual debe ser miembro de una red virtual.|
|Dirección IP pública|No|La máquina virtual puede tener una dirección IP pública asignada para acceder remotamente a ella.|
|interfaz de red|Sí|La máquina virtual necesita la interfaz de red para comunicarse en la red.|
|Discos de datos.|No|La máquina virtual puede incluir discos de datos para ampliar las funcionalidades de almacenamiento.|

## <a name="create-your-first-vm"></a>Creación de la primera máquina virtual

Hay varias opciones para crear una máquina virtual. La elección depende del entorno. En la tabla siguiente se proporciona información que le ayudará a comenzar a crear la máquina virtual:

|Método|Artículo|
|---------|---------|
|Portal de Azure Stack Hub|Creación de una máquina virtual Windows con el portal de Azure Stack Hub<br>[Creación de una máquina virtual Linux con el portal de Azure Stack Hub](azure-stack-quick-linux-portal.md)|
|Plantillas|Las plantillas de inicio rápido de Azure Stack Hub se encuentran en:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://aka.ms/aa6z60s)|
|PowerShell|[Creación de una máquina virtual Windows mediante PowerShell en Azure Stack Hub](azure-stack-quick-create-vm-windows-powershell.md)<br>[Creación de una máquina virtual Linux mediante PowerShell en Azure Stack Hub](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Creación de una máquina virtual Windows mediante la CLI en Azure Stack Hub](azure-stack-quick-create-vm-windows-cli.md)<br>[Creación de una máquina virtual Linux mediante la CLI en Azure Stack Hub](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Administración de la máquina virtual

Las máquinas virtuales pueden administrarse mediante un portal basado en el explorador, herramientas de línea de comandos con compatibilidad con scripts o directamente mediante API. Algunas tareas de administración habituales que puede realizar son:

- Obtener información sobre una máquina virtual
- Conectarse a una máquina virtual
- Administrar la disponibilidad
- Realizar copias de seguridad

### <a name="get-information-about-your-vm"></a>Obtención de información acerca de la máquina virtual

En la tabla siguiente se muestran algunas maneras en que puede obtener información sobre una máquina virtual.

|Método|Descripción|
|---------|---------|
|Portal de Azure Stack Hub|En el menú central, haga clic en **Máquinas virtuales** y seleccione la máquina virtual en la lista. En la página de la máquina virtual, puede ver información general, establecer valores y supervisar métricas.|
|Azure PowerShell|La administración de las máquinas virtuales es similar en Azure y Azure Stack Hub. Para más información sobre el uso de PowerShell, consulte el siguiente tema de Azure:<br>[Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|SDK de cliente|El uso de C# para administrar máquinas virtuales es similar en Azure y Azure Stack Hub. Para obtener más información, consulte el siguiente tema de Azure:<br>[Creación y administración de máquinas virtuales Windows en Azure mediante C#](/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Conexión a la máquina virtual

Puede usar el botón **Conectar** en el portal de Azure Stack Hub para conectarse a la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

- [Consideraciones sobre máquinas virtuales en Azure Stack Hub](azure-stack-vm-considerations.md)
