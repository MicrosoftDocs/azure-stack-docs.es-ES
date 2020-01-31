---
title: Adición de usuarios de Azure Stack Hub a AD FS
description: Obtenga información sobre cómo agregar usuarios de Azure Stack Hub para implementaciones de Servicios de federación de Active Directory (AD FS).
author: ihenkel
ms.topic: article
ms.date: 06/03/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 7d11ae849a5ecc51c1506e8d978510884b752a9a
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890381"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-active-directory-federation-services-ad-fs"></a>Adición de una nueva cuenta de usuario de Azure Stack Hub en los Servicios de federación de Active Directory (AD FS)

Puede usar el complemento **Active Directory Users and Computers** (Usuarios y equipos de Active Directory) para incorporar usuarios adicionales a un entorno de Azure Stack Hub, usando AD FS como proveedor de identidades.

## <a name="add-windows-server-active-directory-users"></a>Incorporación de usuarios de Windows Server Active Directory

1. Inicie sesión en un equipo con una cuenta que proporcione acceso a las herramientas administrativas de Windows y abra una nueva consola de Microsoft Management Console (MMC).
2. Seleccione **File > Add or remove snap-in** (Archivo > Agregar o quitar complemento).

   > [!TIP]
   > Reemplace *directory-domain* por el dominio que coincida con el directorio. 

3. Seleccione **Usuarios y equipos de Active Directory** > *directory-domain* > **Usuarios**.
4. Seleccione **Action** > **New** > **User** (Acción > Nuevo > Usuario).
5. En Objeto nuevo - Usuario, proporcione detalles del usuario. Seleccione **Next** (Siguiente).
6. Proporcione y confirme una contraseña.
7. Seleccione **Siguiente** para completar los valores. Seleccione **Finish** (Finalizar) para crear el usuario.


## <a name="next-steps"></a>Pasos siguientes

[Creación de una identidad de aplicación para acceder a recursos de Azure Stack Hub](azure-stack-create-service-principals.md)