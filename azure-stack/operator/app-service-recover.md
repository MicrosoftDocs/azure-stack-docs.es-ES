---
title: Recuperación de App Service en Azure Stack Hub
description: Aprenda sobre la recuperación ante desastres de App Service en Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 03/21/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: e04fa2f3e9d03f1982ef67d4d19549b7b73a1f1a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701690"
---
# <a name="app-service-recovery-on-azure-stack-hub"></a>Recuperación de App Service en Azure Stack Hub

En este tema se proporcionan instrucciones sobre las acciones que se deben llevar a cabo para realizar la recuperación ante desastres de App Service.

Las siguientes acciones deben realizarse para recuperar App Service de una copia de seguridad en Azure Stack Hub:
1. Restaurar las bases de datos de App Service.
2. Restaurar el contenido de los recursos compartidos del servidor de archivos.
3. Restaurar los servicios y roles de App Service.

Si se usó el almacenamiento de Azure Stack Hub para el almacenamiento de aplicaciones de función, también debe realizar los pasos para restaurar estas aplicaciones.

## <a name="restore-the-app-service-databases"></a>Restaurar las bases de datos de App Service
Las bases de datos de SQL Server de App Service deben restaurarse en una instancia de SQL Server lista para producción. 

Después de [preparar la instancia de SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) para hospedar las bases de datos de App Service, use estos pasos para restaurar las bases de datos de la copia de seguridad:

1. Inicie sesión en la instancia de SQL Server que hospedará las bases de datos de App Service recuperadas con permisos de administrador.
2. Use los comandos siguientes para restaurar las bases de datos de App Service desde un símbolo del sistema con permisos de administrador:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Compruebe que ambas bases de datos de App Service se han restaurado correctamente y salga de SQL Server Management Studio.

> [!NOTE]
> Para recuperarse de un error de instancia del clúster de conmutación por error, consulte [Recuperarse de un error en una instancia de clúster de conmutación por error](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Restaurar el contenido del recurso compartido de archivo de App Service
Después de [preparar el servidor de archivos](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) para hospedar el recurso compartido de archivos de App Service, debe restaurar el contenido del recurso compartido de archivos del inquilino de la copia de seguridad. Puede usar cualquier método que tenga disponible para copiar los archivos en la ubicación del recurso compartido de archivos de App Service recién creada. Al ejecutar este ejemplo en el servidor de archivos, usará PowerShell y Robocopy para conectarse a un recurso compartido remoto y copiar en él los archivos:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Además de copiar el contenido del recurso compartido de archivos, también debe restablecer los permisos en el propio recurso compartido de archivos. Para restablecer los permisos, abra un símbolo del sistema con privilegios de administrador en el equipo del servidor de archivos y ejecute el archivo **ReACL.cmd**. El archivo **ReACL.cmd** se encuentra en los archivos de instalación de App Service en el directorio **BCDR**.

## <a name="restore-app-service-roles-and-services"></a>Restaurar los servicios y roles de App Service
Después de restaurar las bases de datos de App Service y el contenido del recurso compartido de archivos, el próximo paso es usar PowerShell para restaurar los roles y servicios de App Service. Estos pasos restaurarán los secretos y las configuraciones de servicio de App Service.  

1. Inicie sesión en la máquina virtual **CN0-VM** del controlador de App Service como **roleadmin** con la contraseña que proporcionó durante la instalación de App Service. 
    > [!TIP]
    > Deberá modificar el grupo de seguridad de red de la VM para permitir conexiones RDP. 
2. Copie el archivo **SystemSecrets.JSON** localmente en la máquina virtual del controlador. En el siguiente paso, deberá proporcionar la ruta de acceso a este archivo como parámetro `$pathToExportedSecretFile`.
3. Use los siguientes comandos en una ventana de consola de PowerShell con privilegios elevados para restaurar los roles y servicios de App Service:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Al finalizar el comando, es recomendable cerrar esta sesión de PowerShell.

## <a name="restore-function-apps"></a>Restauración de las aplicaciones de función 
App Service para Azure Stack Hub no admite la restauración de aplicaciones o datos de usuario del inquilino que no sean el contenido del recurso compartido de archivos. Por lo tanto, el resto de datos se deben copiar y recuperar al margen de las operaciones de copia de seguridad y restauración de App Service. Si se usó almacenamiento de Azure Stack Hub para el almacenamiento de aplicaciones de función, se deben realizar los siguientes pasos para recuperar los datos perdidos:

1. Cree una cuenta de almacenamiento para su uso por la aplicación de función. Puede ser almacenamiento de Azure Stack Hub, almacenamiento de Azure o cualquier almacenamiento compatible.
2. Recupere la cadena de conexión del almacenamiento.
3. Abra el portal de funciones y vaya a la aplicación de funciones.
4. Vaya a la pestaña **Características de la plataforma** y haga clic en **Configuración de la aplicación**.
5. Cambie **AzureWebJobsDashboard** y **AzureWebJobsStorage** por la nueva cadena de conexión y haga clic en **Guardar**.
6. Cambie a **Información general**.
7. Reinicie la aplicación. Puede que sean necesarios varios intentos para borrar todos los errores.

## <a name="next-steps"></a>Pasos siguientes
[Introducción a App Service en Azure Stack Hub](azure-stack-app-service-overview.md)