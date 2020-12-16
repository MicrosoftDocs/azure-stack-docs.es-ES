---
title: Solución de problemas de CredSSP
description: Aprenda a solucionar problemas de CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: f90e03c275c4ca7a28a9d8392351bf55d0adb2c0
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010845"
---
# <a name="troubleshoot-credssp"></a>Solución de problemas de CredSSP

> Se aplica a Azure Stack HCl, versión v20H2

Algunas operaciones de Azure Stack HCI usan Administración remota de Windows (WinRM) la cual no permite la delegación de credenciales de forma predeterminada. Para permitir la delegación, el equipo debe tener el proveedor de servicios de seguridad de credenciales (CredSSP) habilitado temporalmente. CredSSP es un proveedor de servicios de seguridad de credenciales que permite a un cliente delegar credenciales en un servidor de destino para la autenticación remota. 

Habilitar CredSSP es una medida de seguridad en desuso y, en la mayoría de los casos, se debe deshabilitar una vez que se ha completado la tarea u operación.

Entre las tareas que requieren que se habilite CredSSP se incluyen:

- Flujo de trabajo del asistente para crear clúster
- Consultas o actualizaciones de Active Directory
- Consultas o actualizaciones de SQL Server
- Colocación de cuentas o equipos en un dominio diferente o en un entorno no unido a un dominio

## <a name="troubleshooting-tips"></a>Sugerencias de solución de problemas

Si tiene problemas con CredSSP, las siguientes sugerencias de solución de problemas pueden ayudarle:

- Para usar el Asistente para crear clústeres al ejecutar Windows Admin Center en un servidor en lugar de un equipo, debe ser miembro del grupo administradores de puerta de enlace en el servidor de Windows Admin Center. Para más información, consulte [Opciones de acceso de usuario con Windows Admin Center](/windows-server/manage/windows-admin-center/plan/user-access-options).

- Cuando se ejecuta el asistente para crear clúster, CredSSP puede notificar un problema si una confianza de Active Directory no está establecida o es incorrecta. Esto pasa cuando se utilizan servidores basados en un grupo de trabajo para la creación del clúster. En este caso, intente reiniciar manualmente cada servidor del clúster.

- Cuando ejecute Windows Admin Center en un servidor (modo de servicio), asegúrese de que la cuenta de usuario es miembro del grupo Administradores de puerta de enlace.

- Se recomienda ejecutar Windows Admin Center en un equipo que sea miembro del mismo dominio que los servidores administrados.

- Para habilitar o deshabilitar CredSSP en un servidor, asegúrese de que pertenece al grupo Administradores de puerta de enlace en ese equipo. Para más información, consulte las dos primeras secciones de [Configuración de los permisos y el control de acceso de usuarios](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

- Al reiniciar el servicio Administración remota de Windows (WinRM) en los servidores del clúster se le podría pedir que vuelva a establecer la conexión de WinRM entre cada servidor de clúster y Windows Admin Center.

    Una forma de hacerlo es ir a cada servidor de clúster y, en Windows Admin Center, en el menú **Herramientas**, seleccionar **Servicios**, seleccionar **WinRM**, seleccionar **Reiniciar** y, a continuación, en el símbolo del sistema **Reiniciar el servicio**, seleccionar **Sí**.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre CredSSP, consulte [Proveedor de servicios de seguridad de credenciales](/windows/win32/secauthn/credential-security-support-provider).