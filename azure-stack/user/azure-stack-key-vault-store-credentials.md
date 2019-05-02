---
title: Azure Stack almacena las credenciales de entidad de servicio en Key Vault | Microsoft Docs
description: Obtenga información sobre cómo Key Vault almacena las credenciales de entidad de servicio en Azure Stack
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/16/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: d4bc560a1c607d4a12cfb3d08ffeacf9b5d2196a
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310863"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Almacenamiento de credenciales de entidad de servicio en Key Vault

Desarrollar aplicaciones en Azure Stack habitualmente requiere crear una entidad de servicio y usar esas credenciales para realizar la autenticación antes de la implementación. Sin embargo, las credenciales almacenadas para la entidad de servicio suelen estar ubicadas incorrectamente. En este artículo se describe cómo crear una entidad de servicio y almacenar los valores en Azure Key Vault para su posterior recuperación.

Para más información sobre Key Vault, consulte [este artículo](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción a una oferta que incluye el servicio de Azure Key Vault.
- PowerShell está configurado para su uso con Azure Stack.

## <a name="key-vault-in-azure-stack"></a>Key Vault en Azure Stack

Key Vault en Azure Stack ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Con Key Vault, puede cifrar claves y secretos.

Para crear un almacén de claves, siga estos pasos:

1. Inicie sesión en el portal de Azure Stack.

2. En el panel, seleccione **+ Crear un recurso**, **Seguridad e identidad** y, luego, **Key Vault**.

   ![Creación del almacén de claves](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. En el panel **Crear Key Vault**, asigne un **Nombre** al almacén. Los nombres de almacén solo pueden contener caracteres alfanuméricos y el carácter de guion (-). No deben comenzar por un número.

4. Elija una suscripción de la lista de suscripciones disponibles.

5. Seleccione un grupo de recursos existente o cree uno nuevo.

6. Seleccione el plan de tarifa.

7. Elija una de las directivas de acceso existentes o cree una nueva. Una directiva de acceso le permite conceder permisos para que un usuario, una aplicación o un grupo de seguridad realicen operaciones con este almacén.

8. Si lo desea, elija una directiva de acceso avanzado para habilitar el acceso a las características.

9. Después de realizar la configuración, seleccione **Aceptar**y, a continuación, seleccione **Crear**. Empieza la implementación del almacén de claves.

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

1. Inicie sesión en su cuenta de Azure mediante Azure Portal.

2. Seleccione **Azure Active Directory**, **Registros de aplicaciones** y, luego, **Agregar**.

3. Proporcione un nombre y una dirección URL para la aplicación. Seleccione either **Web app / API** (Aplicación web/API) o **Nativa** para el tipo de aplicación que quiere crear. Después de configurar los valores, seleccione **Crear**.

4. Seleccione **Active Directory**, **Registros de aplicaciones** y seleccione la aplicación.

5. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. Las aplicaciones en el ejemplo de aplicaciones usan el **id. de cliente** al hacer referencia al **id. de la aplicación**.

6. Para generar una clave de autenticación, seleccione **Claves**.

7. Proporcione una descripción para la clave y una duración.

8. Seleccione **Guardar**.

9. Copie la **clave** que aparece disponible después de hacer clic en **Guardar**.

## <a name="store-the-service-principal-inside-key-vault"></a>Almacenamiento de la entidad de servicio dentro de Key Vault

1. Inicie sesión en el portal de usuario de Azure Stack, seleccione el almacén de claves que creó anteriormente y, luego, el icono **Secreto**.

2. En el panel **Secreto**, seleccione **Generar o importar**.

3. En el panel **Crear un secreto**, seleccione **Manual** en la lista de opciones. Si ha creado la entidad de servicio mediante certificados, seleccione los certificados en la lista desplegable y, a continuación, cargue el archivo.

4. Escriba el **id. de la aplicación** copiado de la entidad de servicio como el nombre de la clave. El nombre de la clave solo puede contener caracteres alfanuméricos y el carácter de guion (-).

5. Pegue el valor de la clave de la entidad de servicio en la pestaña **Valor**.

6. Seleccione **Entidad de servicio** para el **tipo de contenido**.

7. Establezca los valores de **fecha de activación** y **fecha de expiración** para la clave.

8. Seleccione **Crear** para iniciar la implementación.

Cuando el secreto se cree correctamente, la información de la entidad de servicio se almacenará ahí. Puede seleccionarla en cualquier momento en **Secretos** y vea o modifique sus propiedades. La sección de propiedades contiene el identificador del secreto, que es un identificador uniforme de recursos (URI) que las aplicaciones externas usan para acceder a este secreto.

## <a name="next-steps"></a>Pasos siguientes

- [Usar entidades de servicio](azure-stack-create-service-principals.md)
- [Administrar Key Vault en Azure Stack mediante el portal](azure-stack-key-vault-manage-portal.md)  
- [Administrar Key Vault en Azure Stack mediante PowerShell](azure-stack-key-vault-manage-powershell.md)