---
title: Estación de trabajo de acceso con privilegios y acceso al punto de conexión con privilegios
description: Obtenga información sobre la estación de trabajo de acceso con privilegios y el acceso al punto de conexión con privilegios.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 96a4e944b6b86c8b5db314141fd3473a8512d518
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256207"
---
# <a name="privileged-access-workstation-and-privileged-endpoint-access"></a>Estación de trabajo de acceso con privilegios y acceso al punto de conexión con privilegios

## <a name="overview"></a>Introducción

Para este procedimiento, debe conectarse a la estación de trabajo de acceso con privilegios. El cliente deberá proporcionarle la posibilidad de conectarse a la estación mediante Escritorio remoto.

## <a name="configuring-the-winrm"></a>Configuración de WinRM

Para permitir conexiones con el punto de conexión con privilegios desde la estación de trabajo con privilegios, asegúrese de que las direcciones IP del punto de conexión con privilegios, tal como están definidas en el portal de administración de Azure Stack Hub, se establecen como host de confianza en la estación de trabajo con privilegios. Las instrucciones para obtener estas direcciones IP desde el portal de administración se encuentran en Comprobación del acceso y estado del nodo de unidad de escalado.

Para ver o editar los hosts de confianza de WinRM, inicie una sesión de PowerShell con privilegios elevados:

-   Visualización de hosts de confianza.

Para ver los hosts de confianza actuales, en PowerShell, ejecute:

-   Edición de hosts de confianza.

Si las direcciones IP del servidor de la consola de recuperación de emergencia (ERCS) no están presentes, ejecute lo siguiente para establecer un nuevo valor para los hosts de confianza; sustituya *\<ERCS01_IP\*, *\<ERCS02_IP\* y *\<ERCS03_IP\* con las tres direcciones IP de punto de conexión con privilegios definidas en el portal de administración de Azure Stack Hub:

## <a name="connect-to-the-privileged-endpoint"></a>Conexión al punto de conexión con privilegios

En la estación de trabajo con privilegios, abra una sesión de PowerShell con privilegios elevados y ejecute los dos comandos siguientes. Reemplace *\<ERCS_IP\* por una dirección IP de una de las instancias de punto de conexión con privilegios, como se indicó anteriormente en este procedimiento. Cuando se le solicite, escriba las credenciales del punto de conexión con privilegios (PEP) suministradas por el cliente.

## <a name="close-the-privileged-endpoint"></a>Cierre del punto de conexión con privilegios

Para cerrar la sesión del punto de conexión con privilegios, ejecute lo siguiente:

## <a name="further-reading"></a>Información adicional

Para obtener más información sobre cómo conectarse y trabajar con el punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack](../../operator/azure-stack-privileged-endpoint.md)
[Hub](../../operator/azure-stack-privileged-endpoint.md).