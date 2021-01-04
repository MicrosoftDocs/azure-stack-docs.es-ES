---
title: Estación de trabajo de acceso con privilegios y acceso al punto de conexión con privilegios
description: Obtenga información sobre la estación de trabajo de acceso con privilegios y el acceso al punto de conexión con privilegios.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: a186ef96c0ce35457fd9871a97ef4b5533813933
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392855"
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

Para obtener más información sobre cómo conectarse y trabajar con el punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)
[Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).
