---
title: Solución de problemas de CredSSP
description: Aprenda a solucionar problemas de CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 472f0cf6103ac1559a9f8a0f757d66bc545f9a5d
ms.sourcegitcommit: f4a1a7e9d0b64ca84105d48170a23e1f473e976c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743546"
---
# <a name="troubleshoot-credssp"></a>Solución de problemas de CredSSP

> Se aplica a Azure Stack HCl, versión v20H2

Algunas operaciones de Azure Stack HCI usan Administración remota de Windows (WinRM) la cual no permite la delegación de credenciales de forma predeterminada. Para permitir la delegación, el equipo debe tener el proveedor de servicios de seguridad de credenciales (CredSSP) habilitado temporalmente. CredSSP es un proveedor de servicios de seguridad de credenciales que permite a un cliente delegar credenciales en un servidor para la autenticación remota.

Habilitar CredSSP es una medida de seguridad en desuso y, en la mayoría de los casos, se debe deshabilitar una vez que se ha completado la tarea u operación.

Entre las tareas que requieren que se habilite CredSSP se incluyen:

- Flujo de trabajo del asistente para crear clúster
- Consultas o actualizaciones de Active Directory
- Consultas o actualizaciones de SQL Server
- Colocación de cuentas o equipos en un dominio diferente o en un entorno no unido a un dominio

## <a name="troubleshooting-tips"></a>Sugerencias de solución de problemas

Si tiene problemas con CredSSP, las siguientes sugerencias de solución de problemas pueden ayudarle:

- Para usar el Asistente para crear clústeres al ejecutar Windows Admin Center en un servidor en lugar de un equipo, debe ser miembro del grupo Administradores de puerta de enlace en el servidor de Windows Admin Center. Para más información, consulte [Opciones de acceso de usuario con Windows Admin Center](/windows-server/manage/windows-admin-center/plan/user-access-options).

- Cuando se ejecuta el asistente para crear clúster, CredSSP puede notificar un problema si una confianza de Active Directory no está establecida o es incorrecta. Esto pasa cuando se utilizan servidores basados en un grupo de trabajo para la creación del clúster. En este caso, intente reiniciar manualmente cada servidor del clúster.

- Cuando ejecute Windows Admin Center en un servidor, asegúrese de que la cuenta de usuario sea miembro del grupo Administradores de puerta de enlace.

- Se recomienda ejecutar Windows Admin Center en un equipo que sea miembro del mismo dominio que los servidores administrados.

- Para habilitar o deshabilitar CredSSP en un servidor, asegúrese de que pertenece al grupo Administradores de puerta de enlace en ese equipo. Para más información, consulte las dos primeras secciones de [Configuración de los permisos y el control de acceso de usuarios](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

- Al reiniciar el servicio WinRM en los servidores del clúster se le podría pedir que vuelva a establecer la conexión de WinRM entre cada servidor de clúster y Windows Admin Center.

    Una forma de hacerlo es ir a cada servidor de clúster y, en Windows Admin Center, en el menú **Herramientas**, seleccionar **Servicios**, seleccionar **WinRM**, seleccionar **Reiniciar** y, a continuación, en el símbolo del sistema **Reiniciar el servicio**, seleccionar **Sí**.

## <a name="manual-troubleshooting"></a>Solución manual de problemas

Si recibe el siguiente mensaje de error de WinRM, pruebe a usar los pasos de comprobación manual de esta sección para resolver el error. Ejemplo de mensaje de error:

`Connecting to remote <sever name> failed with the following error message: The WinRM client cannot process the request. A computer policy does not allow the delegation of the user credentials to the target computer because the computer is not trusted. The identity of the target computer can be verified if you configure the WSMAN service to use a valid certificate.`

Los pasos de comprobación manual de esta sección requieren la configuración de los siguientes equipos:
- El equipo en el que se ejecuta Windows Admin Center.
- El servidor en el que ha recibido el mensaje de error.

Para resolver el error, pruebe los siguientes pasos según sea necesario:

**Solución 1:**
1. Reinicie el equipo en el que se ejecuta Windows Admin Center y el servidor.
1. Intente volver a ejecutar el Asistente para crear clúster.

    Para más información sobre cómo ejecutar el asistente, consulte [Creación de un clúster de Azure Stack HCI mediante Windows Admin Center](../deploy/create-cluster.md).

**Solución 2:**
1. En el equipo que ejecuta Windows Admin Center, abra Windows PowerShell como administrador y ejecute los siguientes comandos:

    ```powershell
    Disable-WsmanCredSSP -Role Client  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Client -DelagateComputer <Server FQDN Name>
    ```

1. Use la característica RDP para conectarse al servidor y, a continuación, ejecute los siguientes comandos de PowerShell:

    ```powershell  
    Disable-WsmanCredSSP -Role Server  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Server  
    ```
    
1. Intente volver a ejecutar el Asistente para crear clúster.

    Para más información sobre cómo ejecutar el asistente, consulte [Creación de un clúster de Azure Stack HCI mediante Windows Admin Center](../deploy/create-cluster.md).

**Solución 3:**
1. En el equipo en el que se ejecuta Windows Admin Center, ejecute el siguiente comando de PowerShell para comprobar el nombre de entidad de seguridad de servicio (SPN):

    ```powershell
    setspn -Q WSMAN/<Windows Admin Center Computer Name>  
    ```
    
    El resultado debe mostrar la siguiente salida:

    `WSMAN/<Windows Admin Center Computer Name>`

    `WSMAN/<Windows Admin Center Computer FQDN Name>`

1. Si los resultados no se muestran, ejecute los siguientes comandos de PowerShell para registrar el SPN:

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer FQDN Name>  
    ```

1. Use la característica RDP para conectarse al servidor y, a continuación, ejecute los siguientes comandos de PowerShell para comprobar el SPN:

    ```powershell
    setspn -Q WSMAN/<Server Name>  
    ```

    El resultado debe mostrar la siguiente salida:

    `WSMAN/<Server Name>`

    `WSMAN/<Server FQDN Name>`

1. Si los resultados no se muestran, ejecute los siguientes comandos de PowerShell para registrar el SPN:

    ```powershell
    setspn -S WSMAN/<Server Name> <Server Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Server Name> <Server FQDN Name>  
    ```

1. Intente volver a ejecutar el Asistente para crear clúster.

    Para más información sobre cómo ejecutar el asistente, consulte [Creación de un clúster de Azure Stack HCI mediante Windows Admin Center](../deploy/create-cluster.md).


**Solución 4:**

Si alguno de los pasos anteriores de la solución no se ha realizado correctamente o no se ha completado, podría indicar que hay un conflicto de registros en Active Directory. Puede usar un nombre de equipo diferente para restablecer el registro como un nuevo registro en Active Directory.

Para restablecer el registro en Active Directory, vuelva a instalar el sistema operativo Azure Stack HCI con un nombre de equipo nuevo.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre CredSSP, consulte [Proveedor de servicios de seguridad de credenciales](/windows/win32/secauthn/credential-security-support-provider).