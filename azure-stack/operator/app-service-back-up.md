---
title: Copia de seguridad de App Service en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo realizar una copia de seguridad de App Services en Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b49390434990ac2efb81692c1177c634aee4bab0
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959532"
---
# <a name="back-up-app-service-on-azure-stack"></a>Copia de seguridad de App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*  

Este documento proporciona instrucciones acerca de cómo realizar una copia de seguridad de App Service en Azure Stack.

> [!IMPORTANT]
> La copia de seguridad de App Service en Azure Stack no se realiza como parte de la [copia de seguridad de infraestructura de Azure Stack](azure-stack-backup-infrastructure-backup.md). Como operador de Azure Stack, primero debe realizar los pasos para asegurarse de que App Service se puede recuperar correctamente si es necesario.

Azure App Service en Azure Stack tiene cuatro componentes principales que hay que tener en cuenta al planear la recuperación ante desastres:
1. La infraestructura del proveedor de recursos; los roles de servidor, los niveles de trabajo, etc. 
2. Los secretos de App Service
3. La instancia de SQL Server de App Service que hospeda y mide bases de datos
4. El contenido de la carga de trabajo del usuario de App Service almacenado en el recurso compartido de archivos de App Service

## <a name="back-up-app-service-secrets"></a>Copia de seguridad de secretos de App Service
Al recuperar App Service a partir de una copia de seguridad, debe proporcionar las claves de App Service usadas en la implementación inicial. Esta información se debe guardar tan pronto como App Service se haya implementado correctamente y almacenado en una ubicación segura. La configuración de infraestructura del proveedor de recursos se vuelve a crear a partir de la copia de seguridad durante la recuperación mediante los cmdlets de PowerShell para la recuperación de App Service.

Use el portal de administración para realizar una copia de seguridad de los secretos de App Service siguiendo estos pasos: 

1. Inicie sesión en el portal de administración de Azure Stack como administrador de servicios.

2. Vaya a **App Service** -> **Secretos**. 

3. Seleccione **Descargar secretos**.

   ![Descarga de secretos en el portal de administración de Azure Stack](./media/app-service-back-up/download-secrets.png)

4. Cuando los secretos estén listos para descargar, haga clic en **Guardar** y almacene el archivo de los secretos de App Service (**SystemSecrets.JSON**) en una ubicación segura. 

   ![Almacenamiento de secretos en el portal de administración de Azure Stack](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Repita estos pasos cada vez que cambien los secretos de App Service.

## <a name="back-up-the-app-service-databases"></a>Copia de seguridad de las bases de datos de App Service
Para restaurar App Service, necesita las copias de seguridad de las bases de datos **Appservice_hosting** y **Appservice_metering**. Le recomendamos que use los planes de mantenimiento de SQL Server o Azure Backup Server para garantizar que estas bases de datos tengan una copia de seguridad y se guarden de forma segura y periódica. Sin embargo, se puede usar cualquier método que garantice la creación de copias de seguridad periódicas de SQL.

Para realizar manualmente una copia de seguridad de estas bases de datos con la sesión iniciada en SQL Server, use los siguientes comandos de PowerShell:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Si necesita realizar una copia de seguridad de las bases de datos Always On de SQL, siga [estas instrucciones](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Una vez la copia de seguridad de todas las bases de datos se haya realizado correctamente, copie los archivos .bak en una ubicación segura junto con la información de secretos de App Service.

## <a name="back-up-the-app-service-file-share"></a>Copia de seguridad del recurso compartido de archivos de App Service
App Service almacena información de aplicaciones de inquilino en el recurso compartido de archivos. Se debe realizar una copia de seguridad de este recurso compartido de archivos periódicamente junto con las bases de datos de App Service para que la pérdida de datos sea mínima si se requiere una restauración.

Para realizar una copia de seguridad del contenido del recurso compartido de archivos de App Service, use Azure Backup Server u otro método que copie periódicamente el contenido del recurso compartido de archivos en la ubicación donde guardó toda la información de recuperación anterior.

Por ejemplo, puede usar estos pasos para utilizar Robocopy desde una sesión de consola de Windows PowerShell (no PowerShell ISE):

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Pasos siguientes
[Restaurar App Service en Azure Stack](app-service-recover.md)