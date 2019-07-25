---
title: Solución de problemas de validación como servicio de Azure Stack | Microsoft Docs
description: Solucione problemas de validación como servicio para Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9c8807d6fb28a99c9de8464a0eaff7114bd6a162
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418275"
---
# <a name="troubleshoot-validation-as-a-service"></a>Solución de problemas de la validación como servicio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Los siguientes son problemas comunes no relacionados con las versiones de software, y sus soluciones.

## <a name="local-agent"></a>Agente local

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>El portal muestra el agente local en modo de depuración

Esto probablemente se deba a que el agente no puede enviar latidos al servicio debido a una conexión de red inestable. Se envía un latido cada cinco minutos. Si el servicio no recibe un latido durante 15 minutos, considera que el agente está inactivo y ya no se programarán pruebas en él. Compruebe el mensaje de error en el archivo *Agenthost.log* ubicado en el directorio donde se inició el agente.

> [!Note]
> Las pruebas que ya estén en ejecución en el agente seguirán ejecutándose, pero si el latido no se restaura antes de que finalice la prueba, el agente no podrá actualizar el estado de prueba ni cargar registros. La prueba siempre se mostrará como **en ejecución** y debe cancelarse.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>El proceso del agente en el equipo se cerró mientras se ejecutaba la prueba. Qué cabe esperar

Si el proceso del agente se cierre de manera brusca, por ejemplo, se reinicia el equipo, se finaliza el proceso (CTRL+C en la ventana del agente se considera un apagado estable), la prueba que se ejecutaba en él se seguirá mostrando como **en ejecución**. Si se reinicia el agente, este actualizará el estado de la prueba a **cancelado**. Si no se reinicia el agente, la prueba aparece como **en ejecución** y debe cancelarla manualmente.

> [!Note]
> Las pruebas dentro de un flujo de trabajo están programadas para ejecutarse de forma secuencial. Las pruebas **pendientes** no se ejecutarán hasta que no se completen las pruebas en estado **en ejecución** en el mismo flujo de trabajo.

## <a name="vm-images"></a>Imágenes de VM

### <a name="handle-slow-network-connectivity"></a>Control de la conectividad de red lenta

Puede descargar la imagen del PIR en un recurso compartido en el centro de datos local. Luego puede comprobar la imagen.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Descarga de la imagen PIR en el recurso compartido local en el caso de tráfico de red lento

1. Descargue AzCopy desde: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Extraiga AzCopy.zip y cambie al directorio que contiene AzCopy.exe.

3. Abra Windows PowerShell con un símbolo del sistema con privilegios elevados. Ejecute los comandos siguientes:

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare es la ruta de acceso del recurso compartido o la ruta de acceso local.

#### <a name="verifying-pir-image-file-hash-value"></a>Comprobación del valor hash del archivo de imagen PIR

Puede usar el cmdlet **Get-HashFile** para obtener el valor hash para los archivos de imagen del repositorio de imágenes públicas descargadas para comprobar la integridad de las imágenes.

| Nombre de archivo | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Se produce un error al cargar la imagen de VM en el script `VaaSPreReq`

En primer lugar, compruebe que el entorno está en buen estado:

1. Desde la DVM/jumpbox, compruebe que puede iniciar sesión correctamente en el portal de administración mediante las credenciales de administrador.
1. Confirme que no haya ninguna alerta ni advertencia.

Si el entorno está en buen estado, cargue manualmente las 5 imágenes de VM necesarias para las series de pruebas de VaaS:

1. Inicie sesión en el portal de administración como administrador de servicios. Puede encontrar la dirección URL del portal de administración en el almacén ECE o el archivo de información de marca. Para obtener instrucciones, consulte [Parámetros del entorno](azure-stack-vaas-parameters.md#environment-parameters).
1. Seleccione **Más servicios** > **Proveedores de recursos** > **Proceso** > **Imágenes de VM**.
1. Seleccione el botón **+ Agregar** situado en la parte superior de la hoja **Imágenes de VM**.
1. Modifique o compruebe los valores de los siguientes campos para la primera imagen de VM:
    > [!IMPORTANT]
    > No todos los valores predeterminados son correctos para el elemento de Marketplace existente.

    | Campo  | Valor  |
    |---------|---------|
    | Publicador | Microsoft Windows Server |
    | Oferta | Windows Server |
    | OS Type (Tipo de SO) | Windows |
    | SKU | Centro de datos de 2012-R2 |
    | Versión | 1.0.0 |
    | URI del blob de disco de sistema operativo | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Seleccione el botón **Crear**.
1. Repita para las imágenes de VM restantes.

Las propiedades de las 5 imágenes de VM son las siguientes:

| Publicador  | Oferta  | OS Type (Tipo de SO) | SKU | Versión | URI del blob de disco de sistema operativo |
|---------|---------|---------|---------|---------|---------|
| Microsoft Windows Server| Windows Server | Windows | Centro de datos de 2012-R2 | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| Microsoft Windows Server | Windows Server | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| Microsoft Windows Server | Windows Server | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Notas de la versión para la validación como servicio](azure-stack-vaas-release-notes.md) para conocer los cambios en las versiones más recientes.