---
title: Actualización de Antivirus de Windows Defender
titleSuffix: Azure Stack Hub
description: Obtenga información sobre cómo actualizar el antivirus de Windows Defender en Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 12/04/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 31ef55554646065a5d61cc21abf4e72b7fcb4eda
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488100"
---
# <a name="update-windows-defender-antivirus-on-azure-stack-hub"></a>Actualización de Antivirus de Windows Defender en Azure Stack Hub

[Antivirus de Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) es una solución antimalware que proporciona seguridad y protección contra virus. Todos los componentes de la infraestructura de Azure Stack Hub (los hosts de Hyper-V y las máquinas virtuales) están protegidos con el antivirus de Windows Defender. Para mantener al día la protección, es necesario actualizar periódicamente las definiciones, el motor y la plataforma de Antivirus de Windows Defender. La forma de aplicar las actualizaciones depende de la configuración.

## <a name="connected-scenario"></a>Escenario conectado

El [proveedor de recursos de actualización](azure-stack-updates.md#the-update-resource-provider) de Azure Stack Hub descarga las definiciones de antimalware y las actualizaciones del motor varias veces al día. Cada componente de la infraestructura de Azure Stack Hub obtiene la actualización del proveedor de recursos de actualización y la aplica de forma automática.

En el caso de las implementaciones de Azure Stack Hub que están conectadas a la red pública de Internet, aplique la [actualización mensual de Azure Stack Hub](azure-stack-apply-updates.md). La actualización mensual de Azure Stack Hub incluye las actualizaciones de la plataforma Antivirus de Windows Defender para el mes en curso.

## <a name="disconnected-scenario"></a>Escenario sin conexión

En el caso de las implementaciones de Azure Stack Hub que no están conectadas a la red pública de Internet (por ejemplo, centros de datos con separación aérea), a partir de la versión 1910, los clientes pueden aplicar las definiciones de antimalware y las actualizaciones del motor a medida que se publican. 

Para aplicar las actualizaciones a la solución de Azure Stack Hub, primero debe descargarlas del sitio de Microsoft (los vínculos están más adelante) y, después, importarlas en un contenedor de blobs de almacenamiento en su *updateadminaccount*. Una tarea programada examina el contenedor de blobs cada 30 minutos y, si se encuentran nuevas definiciones y actualizaciones del motor de Defender, los aplica a la infraestructura de Azure Stack Hub. 

En el caso de las implementaciones desconectadas que aún no tienen la versión 1910 o posteriores, o que no tienen la capacidad de descargar las definiciones y las actualizaciones del motor de Defender a diario, la actualización mensual de Azure Stack Hub incluye las actualizaciones de las definiciones, el motor y la plataforma de Antivirus de Windows Defender para el mes. 


### <a name="set-up-windows-defender-for-manual-updates"></a>Configuración de la actualización manual de Windows Defender 

En la versión 1910 se han agregado dos nuevos cmdlets al punto de conexión con privilegios para configurar la actualización manual de Windows Defender en Azure Stack Hub. 

```powershell 
### cmdlet to configure the storage blob container for the Defender updates 
Set-AzsDefenderManualUpdate [-Container <string>] [-Remove]  
### cmdlet to retrieve the configuration of the Defender manual update settings 
Get-AzsDefenderManualUpdate  
``` 

En el procedimiento siguiente se muestra cómo configurar la actualización manual de Windows Defender. 

1. Conéctese al punto de conexión con privilegios y ejecute el siguiente cmdlet para especificar el nombre del contenedor de blobs de almacenamiento donde se cargarán las actualizaciones de Defender. 

   > [!NOTE] 
   > El proceso de actualización manual que se describe a continuación solo funciona en entornos desconectados en los que no se permite el acceso a "go.microsoft.com". Si intenta ejecutar el cmdlet Set-AzsDefenderManualUpdate en entornos conectados, se producirá un error. 

   ```powershell 
   ### Configure the storage blob container for the Defender updates 
   Set-AzsDefenderManualUpdate -Container <yourContainerName>
   ``` 

2. Descargue los dos paquetes de actualización de Windows Defender y guárdelos en una ubicación a la que se pueda acceder desde el portal de administración de Azure Stack Hub.  

   * mpam-fe.exe desde [https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64](https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64) 
   * nis_full.exe desde [https://go.microsoft.com/fwlink/?LinkId=197094](https://go.microsoft.com/fwlink/?LinkId=197094) 

   > [!NOTE] 
   > Tendrá que descargar estos dos archivos **cada vez** que quiera actualizar las signaturas de Defender. 

3. En el portal de administración, haga clic en **Todos los servicios**. Luego, en la categoría **DATOS Y ALMACENAMIENTO**, haga clic en **Cuentas de almacenamiento**. (o bien, en el cuadro de filtro, empiece a escribir **storage accounts** y selecciónelo). 

   ![Defender en Azure Stack Hub: todos los servicios](./media/azure-stack-security-av/image1.png)  

4. En el cuadro de filtro, escriba **update**y seleccione la cuenta de almacenamiento **updateadminaccount**. 

5. En los detalles de la cuenta de almacenamiento, en **Servicios**, seleccione **Blobs**. 

   ![Defender en Azure Stack Hub: blob](./media/azure-stack-security-av/image2.png) 

6. En **Blob service**, seleccione **+ Contenedor** para crear un contenedor. Escriba el nombre que se especificó con Set-AzsDefenderManualUpdate (en este ejemplo, *defenderupdates*) y, después, seleccione **Aceptar**. 

   ![Defender en Azure Stack Hub: contenedor](./media/azure-stack-security-av/image3.png) 

7. Tras crear el contenedor, haga clic en su nombre y, después, haga clic en **Cargar** para cargar los archivos de paquete en el contenedor. 

   ![Defender en Azure Stack Hub: carga](./media/azure-stack-security-av/image4.png) 

8. En **Cargar blob**, haga clic en el icono de la carpeta, vaya a los archivos *mpam-fe.exe* de la actualización de Windows Defender, y haga clic en **Abrir** en la ventana del explorador de archivos. 

9. En **Cargar blob**, haga clic en **Cargar**. 

   ![Defender en Azure Stack Hub: carga del blob1](./media/azure-stack-security-av/image5.png) 

1. Repita los pasos 8 y 9 para el archivo *nis_full.exe*. 

   ![Defender en Azure Stack Hub: carga del blob2](./media/azure-stack-security-av/image6.png)

Una tarea programada examina el contenedor de blobs cada 30 minutos y aplica los nuevos paquetes de Windows Defender.  

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca de la seguridad de Azure Stack](azure-stack-security-foundations.md)
