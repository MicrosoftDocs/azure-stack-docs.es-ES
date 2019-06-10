---
title: Incorporación de usuarios a los AD FS de Azure Stack | Microsoft Docs
description: Aprenda a agregar usuarios a las implementaciones de Azure Stack
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
ms.openlocfilehash: d50eb52de39c789498928a7b5e2227998872b937
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/03/2019
ms.locfileid: "66458973"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Incorporación de usuarios de Azure Stack a AD FS
Puede usar el complemento **Active Directory Users and Computers** (Usuarios y equipos de Active Directory) para incorporar usuarios adicionales a un entorno de Azure Stack, usando AD FS como proveedor de identidades.

## <a name="add-windows-server-active-directory-users"></a>Incorporación de usuarios de Windows Server Active Directory
> [!TIP]
> En este ejemplo se utiliza el ASDK Active Directory azurestack.local predeterminado. 

1. Inicie sesión en un equipo con una cuenta que proporcione acceso a las herramientas administrativas de Windows y abra una nueva consola de Microsoft Management Console (MMC).
2. Seleccione **File > Add or remove snap-in** (Archivo > Agregar o quitar complemento).
3. Seleccione **Active Directory Users and Computers** > **AzureStack.local** > **Users** (Usuarios y equipos de Active Directory > AzureStack.local > Usuarios).
4. Seleccione **Action** > **New** > **User** (Acción > Nuevo > Usuario).
5. En New Object – User (Objeto nuevo – Usuario), proporcione la información del usuario. Seleccione **Next** (Siguiente).
6. Proporcione y confirme una contraseña.
7. Seleccione **Next** (Siguiente) para finalizar los valores. Seleccione **Finish** (Finalizar) para crear el usuario.


## <a name="next-steps"></a>Pasos siguientes
[Creación de entidades de servicio](azure-stack-create-service-principals.md)