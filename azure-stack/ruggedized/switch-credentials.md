---
title: Credenciales del conmutador
description: Explica cómo actualizar las credenciales del conmutador para un dispositivo Azure Stack Hub Ruggedized.
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/14/2020
ms.reviewer: justinha
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: a761544c8d514fd69364d917890d284ab06e9962
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941333"
---
# <a name="switch-credentials"></a>Credenciales del conmutador

En este tema se muestra cómo cambiar las credenciales de administrador (admin) y del Protocolo simple de administración de redes (SNMP) en todos los conmutadores. 

## <a name="prerequisites"></a>Prerrequisitos

Antes de ejecutar los procedimientos:

- Use Escritorio remoto para conectarse al HLH.
- Busque PuTTy en el HLH, normalmente en E:\Tools\Putty\putty.exe. Si PuTTy no está disponible, descárguelo y cópielo en el HLH.
- Asegúrese de que tiene las credenciales actuales y nuevas de los conmutadores.

## <a name="update-credentials-for-the-admin-and-enable-accounts"></a>Actualización de las credenciales del administrador y habilitación de cuentas 

Para cada conmutador de la unidad de escalado (BMC, TOR1 y TOR2):

1. En el HLH, con PuTTy, inicie sesión en el conmutador con las credenciales actuales. 
1. Ejecute el comando siguiente, reemplazando \<new password\> por las nuevas credenciales del administrador y de habilitación de cuentas. 
   ```ini
   enable
   configuration terminal
   username admin pass
   word <new_password> privilege 15
   enable password <new_password>
   ```
1. Deje abierta la sesión actual en el conmutador.
1. En el HLH, con PuTTy, inicie sesión en el conmutador con las nuevas credenciales.
1. Ejecute el comando siguiente. El comando enable no debe solicitar una contraseña.

   ```ini
   enable
   write
   dir flash:
   ```

1. Compruebe que startup-config tiene la fecha de hoy.
1. Después de la confirmación, cierre esta sesión y la sesión original.

## <a name="update-snmp-accounts"></a>Actualización de cuentas SNMP

Para cada conmutador de la unidad de escalado (BMC, TOR1 y TOR2):

1. En el HLH, con PuTTy, inicie sesión en el conmutador.
1. Ejecute el siguiente comando para obtener los grupos y usuarios de lectura y escritura de SNMP actuales:

   ```ini
   enable
   show run snmp | grep user
   ```

   En el ejemplo siguiente se muestra el tipo de contenido que se devuelve, incluidos el usuario, el nombre de grupo y los hash de contraseña:

   ```ini
   snmp-server user <user> <group> 3 encrypted auth sha <password_hash>
   ```

1. Ejecute el comando siguiente, reemplazando \<user\> y \<group\> por los detalles que se determinaron en el paso anterior y reemplazando \<password\> por la nueva contraseña:

   ```ini
   configuration terminal
   snmp-server user <user> <group> 3 auth sha <password_new>
   end
   write
   exit
   ```

## <a name="next-steps"></a>Pasos siguientes

[Cambio de secretos en Azure Stack Hub](../operator/azure-stack-rotate-secrets.md)