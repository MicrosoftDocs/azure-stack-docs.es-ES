---
title: Introducción al almacén de claves de Azure Stack | Microsoft Docs
description: Aprenda cómo el almacén de claves de Azure Stack administra claves y secretos
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 87c93f77011082d3e43b1c7d238999441f1b90c1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310335"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introducción a Key Vault en Azure Stack

## <a name="prerequisites"></a>Requisitos previos

* Debe suscribirse a una oferta que incluya el servicio Azure Key Vault.  
* [PowerShell está configurado para su uso con Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Conceptos básicos de Key Vault

Key Vault en Azure Stack ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Con Key Vault, puede cifrar claves y secretos, como por ejemplo:

* Claves de autenticación
* Claves de cuenta de almacenamiento
* Claves de cifrado de datos
* Archivos .pfx
* Contraseñas

Key Vault agiliza el proceso de administración de claves y le permite mantener el control de claves que obtienen acceso a sus datos y los cifran. Los desarrolladores pueden crear claves para desarrollo y prueba en minutos y, a continuación, migrarlas sin problemas a claves de producción. Los administradores de seguridad pueden conceder (y revocar) permisos a las claves según sea necesario.

Cualquiera que tenga una suscripción a Azure Stack puede crear y usar los almacenes de claves. Aunque Key Vault beneficia a los desarrolladores y los administradores de seguridad, el operador que administra otros servicios de Azure Stack en una organización puede implementarlo y administrarlo. Por ejemplo, el operador de Azure Stack puede iniciar sesión con una suscripción de Azure Stack, crear un almacén para la organización en el que almacenaría las claves y asumir la responsabilidad de las tareas operativas:

* Crear o importar una clave o un secreto
* Revocar o eliminar una clave o un secreto
* Autorice a usuarios o aplicaciones para que puedan acceder al almacén de claves para que puedan administrar o usar sus claves y secretos.
* Configurar el uso de claves (por ejemplo, para firmar o cifrar)

El operador puede proporcionar luego a los desarrolladores identificadores uniformes de recursos (URI) para realizar llamadas desde sus aplicaciones. Los operadores también pueden proporcionar a los administradores de seguridad información del registro de uso de claves.

Los desarrolladores también pueden administrar las claves directamente mediante API. Para más información, consulte la guía para desarrolladores de Key Vault.

## <a name="scenarios"></a>Escenarios

En los escenarios siguientes se describe cómo Key Vault puede ayudar a satisfacer las necesidades de los desarrolladores y los administradores de seguridad.

### <a name="developer-for-an-azure-stack-application"></a>Desarrollador para una aplicación de Azure Stack

**Problema:** quiero escribir una aplicación para Azure Stack que use claves para el cifrado y la firma. Quiero que estas claves sean externas a mi aplicación, de forma que la solución sea adecuada para aplicaciones distribuidas geográficamente.

**Instrucción:** las claves se almacenan en un almacén y las invoca un identificador URI cuando se necesitan.

### <a name="developer-for-software-as-a-service-saas"></a>Desarrollador para software como servicio (SaaS)

**Problema:** no quiero asumir la responsabilidad, ni tampoco la posible responsabilidad legal, de las claves y los secretos de mis clientes. Quiero que los clientes posean y administren sus claves, de modo que pueda concentrarme en mi trabajo, que es proporcionar las características de software principales.

**Instrucción:** los clientes pueden importar sus propias claves en Azure Stack y administrarlas.

### <a name="chief-security-officer-cso"></a>Responsable principal de la seguridad (CSO)

**Problema:** Deseo asegurarme de que mi organización tiene el control del ciclo de vida de las claves y puedo supervisar el uso de estas.

**Instrucción:** Key Vault está diseñado para que Microsoft no vea ni extraiga sus claves. Cuando una aplicación necesita realizar operaciones criptográficas mediante las claves de los clientes, Key Vault las usa en nombre de la aplicación. La aplicación no ve las claves de los clientes. Aunque se usan varios servicios y recursos de Azure Stack, puede administrar las claves desde una sola ubicación en Azure Stack. El almacén proporciona una sola interfaz, independientemente del número de almacenes que tenga en Azure Stack, las regiones que admitan y las aplicaciones que los usen.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de Key Vault en Azure Stack mediante el portal](azure-stack-key-vault-manage-portal.md)  
* [Administrar Key Vault en Azure Stack mediante PowerShell](azure-stack-key-vault-manage-powershell.md)

