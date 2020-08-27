---
title: Solución de problemas de la validación como un servicio
titleSuffix: Azure Stack Hub
description: Solucione problemas de validación como servicio de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 06e9958d2311d456e977d72fe37eb88d18008092
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764807"
---
# <a name="troubleshoot-validation-as-a-service"></a>Solución de problemas de la validación como servicio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Los siguientes son problemas comunes no relacionados con las versiones de software, y sus soluciones.

## <a name="local-agent"></a>Agente local

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>El portal muestra el agente local en modo de depuración

Este problema se debe posiblemente a que el agente no puede enviar latidos al servicio debido a una conexión de red inestable. Se envía un latido cada cinco minutos. Si el servicio no recibe un latido durante 15 minutos, considera que el agente está inactivo y ya no se programarán pruebas en él. Compruebe el mensaje de error en el archivo *Agenthost.log* ubicado en el directorio donde se inició el agente.

> [!Note]
> Las pruebas que ya estén en ejecución en el agente seguirán ejecutándose, pero si el latido no se restaura antes de que finalice la prueba, el agente no podrá actualizar el estado de prueba ni cargar registros. La prueba siempre se mostrará como **en ejecución** y debe cancelarse.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>El proceso del agente en el equipo se cerró mientras se ejecutaba la prueba. Qué cabe esperar

Si el proceso del agente se cierra de forma incorrecta, la prueba que se estaba ejecutando en él seguirá apareciendo como **En ejecución**. En un ejemplo de apagado incorrecto, la máquina se reinicia y el proceso se termina (CTRL+C en la ventana del agente se considera un apagado correcto). Si se reinicia el agente, este actualizará el estado de la prueba a **cancelado**. Si no se reinicia el agente, la prueba aparece como **en ejecución** y debe cancelarla manualmente.

> [!Note]
> Las pruebas dentro de un flujo de trabajo están programadas para ejecutarse de forma secuencial. Las pruebas **pendientes** no se ejecutarán hasta que no se completen las pruebas en estado **en ejecución** en el mismo flujo de trabajo.

## <a name="vm-images"></a>Imágenes de VM

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Se produce un error al cargar la imagen de VM en el script `VaaSPreReq`
Consulte la sección siguiente en **Control de la conectividad de red lenta**. Proporciona pasos manuales para cargar las imágenes de máquina virtual en la marca de Azure Stack.

### <a name="handle-slow-network-connectivity"></a>Control de la conectividad de red lenta

#### <a name="1-verify-that-the-environment-is-healthy"></a>1. Compruebe que el entorno es correcto

1. Desde la DVM/jumpbox, compruebe que puede iniciar sesión correctamente en el portal de administración mediante las credenciales de administrador.

2. Confirme que no haya ninguna alerta ni advertencia.

3. Si el entorno es correcto, cargue manualmente las imágenes de máquina virtual necesarias para las series de pruebas de VaaS, como se indica en los pasos de la siguiente sección.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="2-download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>2. Descarga de la imagen PIR en el recurso compartido local en el caso de tráfico de red lento

1. Descargue AzCopy desde: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Extraiga AzCopy.zip y cambie al directorio que contiene `AzCopy.exe`.

3. Abra Windows PowerShell con un símbolo del sistema con privilegios elevados. Ejecute los comandos siguientes:

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'OpenLogic-CentOS-69-20180105.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Debian8_latest.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare es la ruta de acceso del recurso compartido o la ruta de acceso local.

#### <a name="3-verifying-pir-image-file-hash-value"></a>3. Comprobación del valor hash del archivo de imagen PIR

Puede usar el cmdlet **Get-HashFile** para obtener el valor hash para los archivos de imagen del repositorio de imágenes públicas descargadas para comprobar la integridad de las imágenes.

| Nombre de archivo | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |
| OpenLogic-CentOS-69-20180105.vhd | C8B874FE042E33B488110D9311AF1A5C7DC3B08E6796610BF18FDD6728C7913C |
| Debian8_latest.vhd | 06F8C11531E195D0C90FC01DFF5DC396BB1DD73A54F8252291ED366CACD996C1 |

#### <a name="4-upload-vm-images-to-a-storage-account"></a>4. Carga de imágenes de máquina virtual en una cuenta de almacenamiento

1. Use una cuenta de almacenamiento existente o cree una nueva cuenta en Azure.

2. Cree un contenedor en el que cargar las imágenes.

3. Use la herramienta Azcopy para cargar las imágenes de máquina virtual del [*LocalFileShare*] anterior (en el que descargó las imágenes de máquina virtual) en el contenedor que acaba de crear.
    > [!IMPORTANT]
    > Cambie el "nivel de acceso público" del contenedor a "Blob (acceso de lectura anónimo solo para blobs)".

#### <a name="5-upload-vm-images-to-azure-stack-environment"></a>5. Carga de imágenes de máquina virtual en el entorno de Azure Stack

1. Inicie sesión en el portal de administración como administrador de servicios. Puede encontrar la dirección URL del portal de administración en el almacén ECE o el archivo de información de marca. Para obtener instrucciones, consulte [Parámetros del entorno](azure-stack-vaas-parameters.md#environment-parameters).

2. Seleccione **Más servicios** > **Proveedores de recursos** > **Proceso** > **Imágenes de VM**.

3. Seleccione el botón **+ Agregar** situado en la parte superior de la hoja **Imágenes de VM**.

4. Modifique o compruebe los valores de los siguientes campos para la primera imagen de VM:

    > [!IMPORTANT]
    > No todos los valores predeterminados son correctos para el elemento de Marketplace existente.

    | Campo  | Value  |
    |---------|---------|
    | Publicador | Microsoft Windows Server |
    | Oferta | Windows Server |
    | Tipo de SO | Windows |
    | SKU | Centro de datos de 2012-R2 |
    | Versión | 1.0.0 |
    | URI del blob de disco de sistema operativo | https://<*cuentaDeAlmacenamiento*>/<*nombreDelContenedor*>/WindowsServer2012R2DatacenterBYOL.vhd |


5. Seleccione el botón **Crear**.

6. Repita para las imágenes de VM restantes.

Las propiedades de las imágenes de máquina virtual necesarias son las siguientes:

| Publicador  | Oferta  | Tipo de SO | SKU | Versión | URI del blob de disco de sistema operativo |
|---------|---------|---------|---------|---------|---------|
| Microsoft Windows Server| Windows Server | Windows | Centro de datos de 2012-R2 | 1.0.0 | https://[*cuentaDeAlmacenamiento*]/[*nombreDelContenedor*]/WindowsServer2012R2DatacenterBYOL.vhd |
| Microsoft Windows Server | Windows Server | Windows | 2016-Datacenter | 1.0.0 | https://[*cuentaDeAlmacenamiento*]/[*nombreDelContenedor*]/Server2016DatacenterFullBYOL.vhd |
| Microsoft Windows Server | Windows Server | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://[*cuentaDeAlmacenamiento*]/[*nombreDelContenedor*]/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://[*cuentaDeAlmacenamiento*]/[*nombreDelContenedor*]/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://[*cuentaDeAlmacenamiento*]/[*nombreDelContenedor*]/Ubuntu1604-20170619.1.vhd |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://[*cuentaDeAlmacenamiento*]/[*nombreDelContenedor*]/OpenLogic-CentOS-69-20180105.vhd |
| Credativ | Debian | Linux | 8 | 1.0.0 | https://[*cuentaDeAlmacenamiento*]/[*nombreDelContenedor*]/Debian8_latest.vhd |

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Notas de la versión para la validación como servicio](azure-stack-vaas-release-notes.md) para conocer los cambios en las versiones más recientes.