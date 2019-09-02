---
title: Agregar usuarios de Azure Stack a AD FS | Microsoft Docs
description: Obtenga información sobre cómo agregar usuarios de Azure Stack para implementaciones de Servicios de federación de Active Directory (AD FS).
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 4411290b075e105a827de8fb2c8295dfd84e3b50
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118644"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Incorporación de usuarios de Azure Stack a AD FS
Puede usar el complemento **Usuarios y equipos de Active Directory**  para agregar usuarios adicionales a un entorno de Azure Stack con los Servicios de federación de Active Directory (AD FS) como proveedor de identidad.

## <a name="add-windows-server-active-directory-users"></a>Incorporación de usuarios de Windows Server Active Directory
> [!TIP]
> En este ejemplo se utiliza el ASDK Active Directory azurestack.local predeterminado. 

1. Inicie sesión en un equipo con una cuenta que proporcione acceso a las herramientas administrativas de Windows y abra una nueva consola de Microsoft Management Console (MMC).
2. Seleccione **File > Add or remove snap-in** (Archivo > Agregar o quitar complemento).
3. Seleccione **Active Directory Users and Computers** > **AzureStack.local** > **Users** (Usuarios y equipos de Active Directory > AzureStack.local > Usuarios).
4. Seleccione **Action** > **New** > **User** (Acción > Nuevo > Usuario).
5. En Objeto nuevo - Usuario, proporcione detalles del usuario. Seleccione **Next** (Siguiente).
6. Proporcione y confirme una contraseña.
7. Seleccione **Siguiente** para completar los valores. Seleccione **Finish** (Finalizar) para crear el usuario.


## <a name="next-steps"></a>Pasos siguientes
[Creación de entidades de servicio](azure-stack-create-service-principals.md)