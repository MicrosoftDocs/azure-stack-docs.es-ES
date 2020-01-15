---
title: Aplicar una actualización del fabricante de equipos originales (OEM) a Azure Stack | Microsoft Docs
description: Aprenda a aplicar una actualización del fabricante de equipos originales (OEM) a Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 7c2ddd8c692efa423083b95eb7fb107dd763bab6
ms.sourcegitcommit: 1cb84d8417367b553194614d53a477a94a8e9be9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75755160"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Aplicación de actualizaciones del fabricante de equipos originales de Azure Stack Hub

*Se aplica a: Sistemas integrados de Azure Stack Hub*

Puede aplicar las actualizaciones del fabricante de equipos originales a los componentes de hardware de Azure Stack Hub para recibir mejoras de firmware y de controladores, así como revisiones de seguridad, a la vez que se reducen los inconvenientes para los usuarios. En este artículo, puede obtener información sobre las actualizaciones de OEM, la información de contacto de OEM y cómo aplicar una actualización de OEM.

## <a name="overview-of-oem-updates"></a>Información general de las actualizaciones de OEM

Además de las actualizaciones de Microsoft Azure Stack, muchos OEM también publican actualizaciones periódicas del hardware de Azure Stack, como las actualizaciones de firmware y de controladores. Estas se conocen como **actualizaciones de paquetes de OEM**. Para saber si su OEM publica actualizaciones de paquetes de OEM, consulte [la documentación de Azure Stack del OEM](#oem-contact-information).

Estas actualizaciones de paquetes de OEM se cargan en la cuenta de almacenamiento **updateadminaccount** y se aplican a través del portal de administración de Azure Stack Hub. Para obtener más información, consulte [Aplicación de actualizaciones de OEM](#apply-oem-updates).

Pregunte a su fabricante de equipos originales (OEM) cuál es el procedimiento de notificación específico para asegurarse de que su organización reciba las notificaciones de actualización de paquetes de OEM.

Algunos proveedores de hardware pueden necesitar *una VM del proveedor de hardware* que controle el proceso de actualización de firmware interno. Para obtener más información, consulte [Configuración de VM del proveedor de hardware](#configure-hardware-vendor-vm).

## <a name="oem-contact-information"></a>Información de contacto del OEM 

En esta sección encontrará la información de contacto del OEM y vínculos a material de referencia de Azure Stack del OEM.

| Asociado de hardware | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [Guía de operaciones de Cisco Integrated System for Microsoft Azure Stack](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_01000.html)<br><br>[UCS C-Series Rack-Mount UCS-Managed Server Software](https://software.cisco.com/download/home/283862063/type/286320368/release/2.0(0)) |
| Dell EMC | All | [Cloud for Microsoft Azure Stack 14G (cuenta e inicio de sesión necesarios)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud for Microsoft Azure Stack 13G (cuenta e inicio de sesión necesarios)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPÓN | [Departamento de soporte técnico de servicio administrado de Fujitsu (cuenta e inicio de sesión necesarios)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA y Estados Unidos | [Soporte técnico de TI de Fujitsu para productos y sistemas](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | All | [HPE ProLiant for Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM Best Recipes](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Paquete de firmware/OEM](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[Documentación de terra Azure Stack (incluye FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>Aplicación de actualizaciones de OEM

Aplicar paquetes de OEM siguiendo estos pasos:

1. Deberá ponerse en contacto con el OEM para:
      - Determinar la versión actual del paquete de OEM.  
      - Buscar el mejor método para descargar el paquete de OEM.  
2. Antes de aplicar una actualización de paquete OEM, siempre debe aplicar la revisión de Azure Stack más reciente disponible en la versión de Azure Stack Hub actual del sistema. Para más información, consulte [Revisiones de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy).
3. Prepare el paquete de OEM con los pasos que se describen en [Descarga de paquetes de actualización para sistemas integrados](azure-stack-servicing-policy.md).
4. Aplique las actualizaciones con los pasos descritos en [Aplicar actualizaciones en Azure Stack Hub](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Configuración de la VM del proveedor de hardware

Algunos proveedores de hardware pueden necesitar una VM para proporcionarle ayuda con el proceso de actualización de OEM. El fabricante del hardware será responsable de crear estas VM y documentarlas si necesita `ProxyVM` o `HardwareManager` para **-VMType** cuando se ejecute el cmdlet **Set-OEMExternalVM**, así como de las credenciales que se deben usar para **-Credential**. Una vez creadas las VM, configúrelas con **Set-OEMExternalVM** desde el punto de conexión con privilegios.

Para obtener más información sobre el punto de conexión con privilegios en Azure Stack, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md).

1.  Acceda al punto de conexión con privilegios.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Configure la VM del proveedor de hardware mediante el cmdlet **Set-OEMExternalVM**. El cmdlet valida la dirección IP y las credenciales de **-VMType** `ProxyVM`. Para **-VMType** `HardwareManager`, el cmdlet no validará la entrada. El parámetro **-Credential** proporcionado a **Set-OEMExternalVM** es uno que se documentará claramente con la documentación del proveedor de hardware.  NO es la credencial de CloudAdmin que se usa con el punto de conexión con privilegios o cualquier otra credencial de Azure Stack existente.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Pasos siguientes

[Actualizaciones de Azure Stack Hub](azure-stack-updates.md)
