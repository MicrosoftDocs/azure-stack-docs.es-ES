---
title: Introducción a Key Vault en Azure Stack Hub
description: Aprenda la forma en que Key Vault administra claves y secretos en Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 09db1c2e8daa34b566512ca6daee2480a86527df
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704053"
---
# <a name="introduction-to-key-vault-in-azure-stack-hub"></a>Introducción a Key Vault en Azure Stack Hub

## <a name="prerequisites"></a>Prerrequisitos

* Debe suscribirse a una oferta que incluya el servicio Azure Key Vault.  
* PowerShell está instalado y [configurado para su uso con Azure Stack Hub](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Conceptos básicos de Key Vault

Key Vault en Azure Stack Hub ayuda a proteger tanto las claves criptográficas como los secretos usados por los servicios y las aplicaciones en la nube. Con Key Vault, puede cifrar claves y secretos, como por ejemplo:

* Claves de autenticación
* Claves de cuenta de almacenamiento
* Claves de cifrado de datos
* Archivos .pfx
* Contraseñas

Key Vault agiliza el proceso de administración de claves y le permite mantener el control de claves que obtienen acceso a sus datos y los cifran. Los desarrolladores pueden crear claves para desarrollo y prueba en minutos y, a continuación, migrarlas sin problemas a claves de producción. Los administradores de seguridad pueden conceder (y revocar) permisos a las claves según sea necesario.

Cualquiera que tenga una suscripción a Azure Stack Hub puede crear y usar almacenes de claves. Aunque Key Vault beneficia a los desarrolladores y los administradores de seguridad, el operador que administra otros servicios de Azure Stack Hub en una organización puede implementarlo y administrarlo. Por ejemplo, el operador de Azure Stack Hub puede iniciar sesión con una suscripción de Azure Stack Hub y crear un almacén para la organización en el que almacenar las claves. Una vez hecho esto, puede:

* Crear o importar una clave o un secreto
* Revocar o eliminar una clave o un secreto
* Autorizar a usuarios o aplicaciones para que puedan acceder al almacén de claves para que puedan administrar o usar sus claves y secretos
* Configurar el uso de claves (por ejemplo, para firmar o cifrar)

El operador puede proporcionar luego a los desarrolladores identificadores uniformes de recursos (URI) para realizar llamadas desde sus aplicaciones. Los operadores también pueden proporcionar a los administradores de seguridad información del registro de uso de claves.

Los desarrolladores también pueden administrar las claves directamente mediante API. Para más información, consulte la [guía para desarrolladores de Key Vault](/azure/key-vault/key-vault-developers-guide).

## <a name="scenarios"></a>Escenarios

En los escenarios siguientes se describe cómo Key Vault puede ayudar a satisfacer las necesidades de los desarrolladores y los administradores de seguridad.

### <a name="developer-for-an-azure-stack-hub-app"></a>Desarrollador de una aplicación de Azure Stack Hub

**Problema:** Quiero escribir una aplicación para Azure Stack Hub que use claves para el cifrado y la firma. Quiero que estas claves sean externas a mi aplicación, de forma que la solución sea adecuada para aplicaciones distribuidas geográficamente.

**Instrucción:** Las claves se almacenan en un almacén y las invoca un identificador URI cuando se necesitan.

### <a name="developer-for-software-as-a-service-saas"></a>Desarrollador para software como servicio (SaaS)

**Problema:** no quiero asumir la responsabilidad, ni tampoco la posible responsabilidad legal, de las claves y los secretos de mis clientes. Quiero que los clientes sean propietarios y administren sus claves de modo que pueda concentrarme en mi trabajo, que es proporcionar las características de software principales.

**Instrucción:** Los clientes pueden importar sus propias claves en Azure Stack Hub y administrarlas.

### <a name="chief-security-officer-cso"></a>Responsable principal de la seguridad (CSO)

**Problema:** Deseo asegurarme de que mi organización tiene el control del ciclo de vida de las claves y puedo supervisar el uso de estas.

**Instrucción:** Key Vault está diseñado para que Microsoft no vea ni extraiga sus claves. Cuando una aplicación necesita realizar operaciones criptográficas mediante las claves de los clientes, Key Vault las usa en nombre de la aplicación. La aplicación no ve las claves de los clientes. Aunque usamos varios servicios y recursos de Azure Stack Hub, las claves se pueden desde una sola ubicación en Azure Stack Hub. El almacén proporciona una sola interfaz, independientemente del número de almacenes que tenga en Azure Stack Hub, las regiones que admitan y las aplicaciones que los usen.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de Key Vault en Azure Stack Hub mediante el portal](azure-stack-key-vault-manage-portal.md)  
* [Administración de Key Vault en Azure Stack Hub mediante PowerShell](azure-stack-key-vault-manage-powershell.md)
