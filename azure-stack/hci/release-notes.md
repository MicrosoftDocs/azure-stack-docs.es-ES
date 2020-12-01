---
title: Notas de la versión de Azure Stack HCI
description: Notas de la versión de Azure Stack HCl, versión 20H2.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/24/2020
ms.openlocfilehash: 56cc4c35ecbb92c30883bd1f2018422cdcac0894
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812715"
---
# <a name="release-notes-for-azure-stack-hci-public-preview"></a>Notas de la versión de Azure Stack HCl, versión preliminar pública

> Se aplica a: Azure Stack HCI, versión 20H2

En este artículo se describe el contenido de los paquetes de actualización de la versión preliminar pública de Azure Stack Hub.

## <a name="november-23-2020-preview-update-kb4586852"></a>Actualización de la versión preliminar del 23 de noviembre de 2020 (KB4586852)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI. 

   > [!IMPORTANT]
   > Si tiene clústeres de Azure Stack HCI configurados y registrados mediante la imagen de versión preliminar pública, debe reparar el registro de Azure después de instalar la actualización KB4586852 a fin de poder usar las nuevas características que ofrece la actualización. Después de instalar la actualización, siga estos pasos para cada clúster:
   >
   > 1. Asegúrese de que todos los servidores del clúster se han actualizado a KB4586852. Si no es así, se producirá un error en la reparación y se indicarán los nodos que deben actualizarse.
   >
   > 2. Conéctese a uno de los nodos de clúster, ya sea localmente o mediante `Enter-PSSession <server-name>`.
   >
   > 3. Descargue la versión 0.4.1 del módulo de registro AzStackHCI desde la Galería de PowerShell. Use `Install-Module -Name Az.StackHCI` para obtener el módulo más reciente.
   >
   > 4. Ejecute el siguiente comando para reparar el registro. Use el id. de la suscripción que se usó para registrar el clúster originalmente. `Get-AzureStackHCI` muestra el URI de ARM actual, que incluye la información de la suscripción.
   >
   >   ```PowerShell
   >   Register-AzStackHCI -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -RepairRegistration
   >   ```
   > 

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización no relacionada con la seguridad incluye mejoras de calidad. Los cambios principales son: 

- Con esta actualización, los clientes de Azure Stack HCI que tienen licencias válidas de Windows Server 2019 Datacenter Edition pueden usarlas para activar cómodamente las máquinas virtuales (VM) hospedadas en Azure Stack HCI sin tener que administrar las claves de producto de cada VM. En concreto, puede usar Windows Admin Center o PowerShell para especificar una clave de activación de Windows Server 2019 Datacenter Edition sin usar directamente en el host de Azure Stack HCI a fin de habilitar la activación automática de la máquina virtual (AVMA). Las máquinas virtuales que ejecutan Windows Server 2019 o una versión anterior pueden heredar la activación del host. La clave introducida puede ser una clave de activación múltiple (MAK) obtenida del centro de licencias por volumen, la clave impresa en el adhesivo del certificado de autenticidad (COA) aplicada a un servidor OEM o la clave de una copia de venta la por menor de Windows Server 2019 Datacenter Edition. En esta versión, no se admiten las claves de licencia por volumen genéricas (GVLK).

- Azure Stack HCI ahora recopila los datos de diagnóstico necesarios, que son los mínimos necesarios para mantener el dispositivo protegido, actualizado y en funcionamiento según lo previsto. Los datos de diagnóstico necesarios recopilan un conjunto limitado de datos que resultan fundamentales para comprender el dispositivo y su configuración. Estos datos permiten identificar problemas que pueden producirse en una configuración de hardware o software específica.  

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización 
La actualización de seguridad del 23 de noviembre de 2020 (KB4586852) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (compilación 17784.1381 del sistema operativo), descargue la  [información de archivo para la actualización acumulativa 4586852](https://download.microsoft.com/download/5/c/6/5c6f8c37-3e0b-4239-a6d9-9c709e18e869/4586852.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="november-10-2020-security-update-kb4586811"></a>Actualización de seguridad del 10 de noviembre de 2020 (KB4586811)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización contiene varias mejoras de seguridad relacionadas con la funcionalidad interna del sistema operativo. Soluciona una vulnerabilidad de seguridad, ya que impide que las aplicaciones que se ejecutan como una cuenta del sistema se impriman en puertos locales que apuntan a un archivo. Los trabajos de impresión con errores registran el error 50 "No se admite la solicitud" en el id. del evento 372 del registro de eventos PrintService\Admin. Para solucionar este problema en el futuro, asegúrese de que las aplicaciones o los servicios se ejecutan como un usuario o una cuenta de servicio específicos.

No se han documentado problemas adicionales para esta versión.

Para más información sobre las vulnerabilidades resueltas de seguridad, consulte la [guía de actualizaciones de seguridad](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 10 de noviembre de 2020 (KB4586811) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1345), descargue la [información de archivo de la actualización acumulativa 4586811](https://download.microsoft.com/download/8/f/2/8f2ce4bb-e113-4abc-b3ff-f0f4c4c71403/4586811.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="november-10-2020-servicing-stack-update-kb4590242"></a>Actualización de la pila de servicio del 10 de noviembre de 2020 (KB4590242)

Esta actualización incluye mejoras de calidad para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización realiza mejoras de calidad en la pila de servicio, que es el componente que instala las actualizaciones. Las actualizaciones de la pila de servicio (SSU) garantizan que tiene una pila de servicio potente y confiable para que los dispositivos puedan recibir e instalar actualizaciones de Microsoft.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de la pila de servicio de noviembre de 2020 (KB4590242) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Información de reinicio
No es necesario reiniciar el equipo después de aplicar esta actualización.

### <a name="removal-information"></a>Información de eliminación
Las actualizaciones de la pila de servicio (SSU) realizan cambios en cómo se instalan las actualizaciones y no se pueden desinstalar del dispositivo.

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1342), descargue la información de [archivo para la actualización acumulativa 4590242](https://download.microsoft.com/download/b/b/4/bb4fb4f5-c0ba-4e55-bada-d72310857982/4590242.csv).

### <a name="references"></a>Referencias

Para más información sobre las actualizaciones de la pila de servicio, consulte los artículos siguientes:

- [Actualizaciones de la pila de servicio](/windows/deployment/update/servicing-stack-updates)
- [Actualizaciones de la pila de servicio: Preguntas más frecuentes](https://support.microsoft.com/help/4535697)

Obtenga información sobre la [terminología](https://support.microsoft.com/help/824684) que Microsoft usa para describir las actualizaciones de software.

## <a name="october-20-2020-preview-update-kb4580388"></a>Actualización de la versión preliminar del 20 de octubre de 2020 (KB4580388)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización no relacionada con la seguridad incluye mejoras de calidad. Los cambios principales son:

- Con esta actualización, los clientes de Azure Stack HCI que tienen licencias válidas de Windows Server 2019 Datacenter Edition pueden usarlas para activar cómodamente las máquinas virtuales (VM) hospedadas en Azure Stack HCI sin tener que administrar las claves de producto de cada VM.

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización

Microsoft tiene constancia de un problema con esta actualización.

#### <a name="symptom"></a>Síntoma
Al usar la opción Migración en vivo para trasladar una VM entre los sistemas operativos Windows Server y Azure Stack HCI, es posible que se muestre un error similar al siguiente: "Se bloqueó una operación de migración para la máquina virtual <vmname> porque no se admite la migración de VM entre diferentes ediciones de Windows (identificador de la máquina virtual)".

Esto también puede provocar un error en la operación Actualización compatible con clústeres (CAU) si se espera que alguna de las VM ejecute Migración en vivo durante dicha operación.

#### <a name="workaround"></a>Solución alternativa

Use la opción Migración rápida en lugar de Migración en vivo. Si usa la operación CAU, cambie temporalmente el comportamiento predeterminado para que esta use la opción Migración rápida.

Ejemplo:

```powershell
Get-ClusterResourceType "Virtual Machine" | Set-ClusterParameter MoveTypeThreshold 3001
```

Se recomienda volver al valor `MoveTypeThreshold` anterior una vez que la operación CAU se complete correctamente.

Para obtener más información, consulte [Configuring How VMs Are Moved when a Node is Drained](https://techcommunity.microsoft.com/t5/failover-clustering/configuring-how-vms-are-moved-when-a-node-is-drained/ba-p/371848) (Configuración del movimiento de las VM al purgar un nodo).

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 20 de octubre de 2020 (KB4580388) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1321), descargue la [información de archivo de la actualización acumulativa 4580388](https://download.microsoft.com/download/2/f/b/2fb766d3-c4c8-4279-8718-8efbd0b6f211/4580388.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="october-13-2020-security-update-kb4580363"></a>13 de octubre de 2020, actualización de seguridad (KB4580363)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización contiene varias mejoras de seguridad relacionadas con la funcionalidad interna del sistema operativo. No se han documentado problemas adicionales para esta versión.

Para más información sobre las vulnerabilidades resueltas de seguridad, consulte la [guía de actualizaciones de seguridad](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 13 de octubre de 2020 (KB4580363) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1288), descargue la [información de archivo de la actualización acumulativa 4580363](https://download.microsoft.com/download/f/7/8/f78801f0-e7e5-4a3d-a971-f642ccd24ee4/4580363.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="october-13-2020-servicing-stack-update-kb4583287"></a>Actualización de la pila de servicio del 13 de octubre de 2020 (KB4583287)

Esta actualización incluye mejoras de calidad para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización realiza mejoras de calidad en la pila de servicio, que es el componente que instala las actualizaciones. Las actualizaciones de la pila de servicio (SSU) garantizan que tiene una pila de servicio potente y confiable para que los dispositivos puedan recibir e instalar actualizaciones de Microsoft.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de la pila de servicio del 13 de octubre de 2020 (KB4583287) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Información de reinicio
No es necesario reiniciar el equipo después de aplicar esta actualización.

### <a name="removal-information"></a>Información de eliminación
Las actualizaciones de la pila de servicio (SSU) realizan cambios en cómo se instalan las actualizaciones y no se pueden desinstalar del dispositivo.

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1287), descargue la [información de archivo de la actualización acumulativa 4583287](https://download.microsoft.com/download/b/8/5/b85160fb-85d9-49f9-b9d5-7dbc0158a944/4583287.csv).

### <a name="references"></a>Referencias

Para más información sobre las actualizaciones de la pila de servicio, consulte los artículos siguientes:

- [Actualizaciones de la pila de servicio](/windows/deployment/update/servicing-stack-updates)
- [Actualizaciones de la pila de servicio: Preguntas más frecuentes](https://support.microsoft.com/help/4535697)

Obtenga información sobre la [terminología](https://support.microsoft.com/help/824684) que Microsoft usa para describir las actualizaciones de software.

## <a name="september-17-2020-preview-update-kb4577629"></a>Actualización de la versión preliminar del 17 de septiembre de 2020 (KB4577629)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización no relacionada con la seguridad incluye mejoras de calidad. Los cambios principales son:
- Se ha solucionado un problema en el que el tráfico del equilibrador de carga del software que pasaba por el multiplexor se redirigía a un host diferente lo cual producía un problema de conexión de la aplicación.

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 17 de septiembre de 2020 (KB4577629) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1259), descargue la [información de archivo de la actualización acumulativa 4577629](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="september-8-2020-security-update-kb4577470"></a>Actualización de seguridad del 8 de septiembre de 2020 (KB4577470)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización contiene varias mejoras de seguridad relacionadas con la funcionalidad interna del sistema operativo. No se han documentado problemas adicionales para esta versión.

Para más información sobre las vulnerabilidades resueltas de seguridad, consulte la [guía de actualizaciones de seguridad](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 8 de septiembre de 2020 (KB4577470) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1226), descargue la [información de archivo de la actualización acumulativa 4577470](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>Actualización de la pila de servicio del 8 de septiembre de 2020 (KB4577558)

Esta actualización incluye mejoras de calidad para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización realiza mejoras de calidad en la pila de servicio, que es el componente que instala las actualizaciones. Las actualizaciones de la pila de servicio (SSU) garantizan que tiene una pila de servicio potente y confiable para que los dispositivos puedan recibir e instalar actualizaciones de Microsoft.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de la pila de servicio del 8 de septiembre de 2020 (KB4577558) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Información de reinicio 
No es necesario reiniciar el equipo después de aplicar esta actualización.

### <a name="removal-information"></a>Información de eliminación
Las actualizaciones de la pila de servicio (SSU) realizan cambios en cómo se instalan las actualizaciones y no se pueden desinstalar del dispositivo.

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1220), descargue la [información de archivo de la actualización acumulativa 4577558](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv).

### <a name="references"></a>Referencias

Para más información sobre las actualizaciones de la pila de servicio, consulte los artículos siguientes:

- [Actualizaciones de la pila de servicio](/windows/deployment/update/servicing-stack-updates)
- [Actualizaciones de la pila de servicio: Preguntas más frecuentes](https://support.microsoft.com/help/4535697)

Obtenga información sobre la [terminología](https://support.microsoft.com/help/824684) que Microsoft usa para describir las actualizaciones de software.

## <a name="august-11-2020-security-update-kb4574585"></a>Actualización de seguridad del 11 de agosto de 2020 (KB4574585)

Esta actualización incluye mejoras y correcciones para la versión más reciente de Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Mejoras y correcciones
Esta actualización contiene varias mejoras de seguridad relacionadas con la funcionalidad interna del sistema operativo. No se han documentado problemas adicionales para esta versión.

Para más información sobre las vulnerabilidades resueltas de seguridad, consulte la [guía de actualizaciones de seguridad](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problemas conocidos de esta actualización
A fecha de hoy, Microsoft no conoce ningún problema con esta actualización.

### <a name="how-to-get-this-update"></a>Obtención de esta actualización
La actualización de seguridad del 11 de agosto de 2020 (KB4574585) para la [versión preliminar de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se proporciona a través de Windows Update. Para instalarla en el clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](manage/update-cluster.md).

### <a name="file-information"></a>información de archivo
Para obtener una lista de los archivos que se proporcionan en esta actualización (Sistema operativo versión 17784.1162), descargue la [información de archivo de la actualización acumulativa 4574585](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv).

   > [!NOTE]
   > En algunos archivos se incluye por error el estado "No aplicable" en la columna "Versión del archivo" del archivo CSV. Esto podría provocar falsos positivos o falsos negativos al usar herramientas de detección de análisis de terceros para validar la compilación.