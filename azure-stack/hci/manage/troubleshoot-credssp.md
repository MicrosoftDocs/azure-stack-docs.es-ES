---
title: Solución de problemas de CredSSP
description: Aprenda a solucionar problemas de CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 08/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 24e9483aa9658809adc9be7fbfa4a2cb13daab84
ms.sourcegitcommit: 952d26ad08fcc28ad3ad83e27644e61497623a44
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2020
ms.locfileid: "87899931"
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

- Cuando se ejecuta el asistente para crear clúster, CredSSP puede notificar un problema si una confianza de Active Directory no está establecida o es incorrecta. Esto pasa cuando se utilizan servidores basados en un grupo de trabajo para la creación del clúster. En este caso, intente reiniciar manualmente cada servidor del clúster.

- Cuando ejecute Windows Admin Center en un servidor (modo de servicio), asegúrese de que la cuenta de usuario es miembro del grupo Administradores de puerta de enlace.

- Para habilitar o deshabilitar CredSSP en un servidor, asegúrese de que pertenece al grupo Administradores de puerta de enlace en ese equipo. Para más información, consulte las dos primeras secciones de [Configuración de los permisos y el control de acceso de usuarios](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre CredSSP, consulte [Proveedor de servicios de seguridad de credenciales](/windows/win32/secauthn/credential-security-support-provider).