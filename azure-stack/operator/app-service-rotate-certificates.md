---
title: Rotación de secretos y certificados de App Service en Azure Stack Hub | Microsoft Docs
description: Aprenda a rotar secretos y certificados usados por Azure App Service en Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 079be062ee45e012abf3ec1bc869409ec4ee03a5
ms.sourcegitcommit: c4368652f0dd68c432aa1dabddbabf161a4a6399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914567"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>Rotación de secretos y certificados de App Service en Azure Stack Hub

Estas instrucciones solo se aplican a Azure App Service en Azure Stack Hub.  La rotación de los secretos de Azure App Service en Azure Stack Hub no se incluye en el procedimiento centralizado de rotación de secretos de Azure Stack Hub.  Los operadores pueden supervisar la validez de los secretos dentro del sistema, la fecha en la que se actualizaron por última vez y el tiempo restante hasta que expiren.

> [!Important]
> Los operadores no recibirán alertas en el panel de Azure Stack Hub cuando expire un secreto, ya que Azure App Service en Azure Stack Hub no está integrado con el servicio de alertas de Azure Stack Hub.  Los operadores deben supervisar regularmente sus secretos con la experiencia de administración de Azure App Service en Azure Stack Hub en el portal de administradores de Azure Stack Hub.

Este documento contiene el procedimiento para rotar los siguientes secretos:

* Claves de cifrado usadas en Azure App Service en Azure Stack Hub.
* Credenciales de conexión de base de datos que se usan en Azure App Service en Azure Stack Hub para interactuar con las bases de datos de hospedaje y medición.
* Certificados que se usan en Azure App Service en Azure Stack Hub para proteger los puntos de conexión.
* Credenciales del sistema de los roles de infraestructura de Azure App Service en Azure Stack Hub.

## <a name="rotate-encryption-keys"></a>Rotación de las claves de cifrado

Para rotar las claves de cifrado usadas en Azure App Service en Azure Stack Hub, realice los pasos siguientes:

1. Vaya a la experiencia de administración de App Service en el portal de administradores de Azure Stack Hub.

1. Vaya a la opción de menú **Secrets** (Secretos).

1. Haga clic en el botón **Rotate** (Rotar) de la sección de claves de cifrado.

1. Haga clic en **OK** (Aceptar) para iniciar el procedimiento de rotación.

1. Las claves de cifrado se rotan y se actualizan todas las instancias de rol. Los operadores pueden supervisar el estado del procedimiento mediante el botón **Status** (Estado).

## <a name="rotate-connection-strings"></a>Rotación de las cadenas de conexión

Para actualizar las credenciales de la cadena de conexión de las bases de datos de hospedaje y medición de App Service, realice los pasos siguientes:

1. Vaya a la experiencia de administración de App Service en el portal de administradores de Azure Stack Hub.

1. Vaya a la opción de menú **Secrets** (Secretos).

1. Haga clic en el botón **Rotate** (Rotar) de la sección de cadenas de conexión.

1. Proporcione el **nombre de usuario de SA de SQL** y la **contraseña** y haga clic en **OK** (Aceptar) para iniciar el procedimiento de rotación. 

1. Las credenciales se rotarán en todas las instancias de rol de Azure App Service. Los operadores pueden supervisar el estado del procedimiento mediante el botón **Status** (Estado).

## <a name="rotate-certificates"></a>Rotación de certificados

Para rotar los certificados usados en Azure App Service en Azure Stack Hub, realice los pasos siguientes:

1. Vaya a la experiencia de administración de App Service en el portal de administradores de Azure Stack Hub.

1. Vaya a la opción de menú **Secrets** (Secretos).

1. Haga clic en el botón **Rotate** (Rotar) de la sección de certificados.

1. Proporcione el **archivo de certificado** y la **contraseña** asociada de los certificados que quiere rotar y haga clic en **OK** (Aceptar).

1. Los certificados se rotarán según sea necesario en las instancias de rol de Azure App Service en Azure Stack Hub.  Los operadores pueden supervisar el estado del procedimiento mediante el botón **Status** (Estado).

## <a name="rotate-system-credentials"></a>Rotación de las credenciales del sistema

Para rotar las credenciales del sistema usadas en Azure App Service en Azure Stack Hub, realice los pasos siguientes:

1. Vaya a la experiencia de administración de App Service en el portal de administradores de Azure Stack Hub.

1. Vaya a la opción de menú **Secrets** (Secretos).

1. Haga clic en el botón **Rotate** (Rotar) de la sección de credenciales del sistema.

1. Seleccione el **ámbito** de la credencial del sistema que va a rotar.  Los operadores pueden optar por rotar las credenciales del sistema de todos los roles o solo de algunos.

1. Especifique el **nombre de usuario del administrador local**, una nueva **contraseña**, confirme la **contraseña** y haga clic en **OK** (Aceptar).

1. Las credenciales se rotarán según sea necesario en la instancia de rol correspondiente de Azure App Service en Azure Stack Hub.  Los operadores pueden supervisar el estado del procedimiento mediante el botón **Status** (Estado).



