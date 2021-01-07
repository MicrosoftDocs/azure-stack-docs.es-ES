---
title: Aplicación de una actualización de un fabricante de equipos originales en Azure Stack Hub
description: Aprenda a aplicar la actualización de un fabricante de equipos originales (OEM) a Azure Stack Hub.
author: PatAltimore
ms.topic: how-to
ms.date: 10/15/2019
ms.author: patricka
ms.lastreviewed: 03/04/2020
ms.reviewer: ppacent
ms.openlocfilehash: 8b8c3a39f61693cad9904b99bd1f82b175d53a7b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868870"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Aplicación de actualizaciones del fabricante de equipos originales de Azure Stack Hub

Puede aplicar las actualizaciones del fabricante de equipos originales a los componentes de hardware de Azure Stack Hub para obtener actualizaciones de controladores y de firmware, y parches de seguridad. Estas actualizaciones se realizan con un impacto mínimo en los usuarios. En este artículo, puede obtener información sobre las actualizaciones de OEM, la información de contacto de OEM y cómo aplicar una actualización de OEM.

## <a name="overview-of-oem-updates"></a>Información general de las actualizaciones de OEM

Además de las actualizaciones de Microsoft Azure Stack Hub, muchos OEM también publican actualizaciones periódicas del hardware de Azure Stack Hub, como las actualizaciones de firmware y de controladores. Estas actualizaciones se conocen como **actualizaciones de paquetes de un fabricante de equipos originales**. Para saber si su fabricante de equipos originales publica actualizaciones de paquetes, consulte la [documentación de Azure Stack Hub del fabricante de equipos originales](#oem-contact-information).

Estas actualizaciones de paquetes de OEM se cargan en la cuenta de almacenamiento **updateadminaccount** y se aplican a través del portal de administración de Azure Stack Hub. Para más información, consulte [Aplicación de actualizaciones de OEM](#apply-oem-updates).

Consulte a su fabricante de equipos originales sobre su proceso de notificación específico para garantizar que su organización recibe notificaciones sobre la actualización de paquetes.

Algunos proveedores de hardware pueden necesitar *una VM del proveedor de hardware* que controle el proceso de actualización de firmware interno. Para más información, consulte [Configuración de la VM del proveedor de hardware](#configure-hardware-vendor-vm).

## <a name="oem-contact-information"></a>Información de contacto del OEM

En esta sección encontrará la información de contacto del OEM y vínculos a material de referencia de Azure Stack Hub del OEM.

| Asociado de hardware | Region | URL |
|-----|----|-----|
| Cisco | All | [Guía de operaciones de Cisco Integrated System para Microsoft Azure Stack Hub](https://aka.ms/aa708e2)<br><br>[UCS C-Series Rack-Mount UCS-Managed Server Software](https://aka.ms/aa700rq) |
| Dell EMC | All | [Nube para Microsoft Azure Stack Hub 14G (cuenta e inicio de sesión necesarios)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Nube para Microsoft Azure Stack Hub 13G (cuenta e inicio de sesión necesarios)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPÓN | [Departamento de soporte técnico de servicio administrado de Fujitsu (cuenta e inicio de sesión necesarios)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA y Estados Unidos | [Soporte técnico de TI de Fujitsu para productos y sistemas](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | All | [HPE ProLiant para Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM Best Recipes](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Paquete de firmware/OEM](https://aka.ms/AA6z600)<br>[Documentación de terra de Azure Stack Hub (incluye FRU)](https://aka.ms/aa6zktc)

## <a name="apply-oem-updates"></a>Aplicación de actualizaciones de OEM

Aplicar paquetes de OEM siguiendo estos pasos:

> [!IMPORTANT]
> Antes de aplicar las actualizaciones de Azure Stack Hub, asegúrese de que ha completado **TODOS** los pasos de la [lista de comprobación previa a la actualización](release-notes-checklist.md) y ha programado una ventana de mantenimiento apropiada para el tipo de actualización que está aplicando.

1. Póngase en contacto con su fabricante de equipos originales para:
      - Determinar la versión actual del paquete de OEM.  
      - Buscar el mejor método para descargar el paquete de OEM.  
2. Antes de aplicar una actualización de paquete del fabricante de equipos originales, siempre debe aplicar la revisión de Azure Stack Hub más reciente disponible en la versión de Azure Stack Hub actual del sistema. Para más información, consulte [Revisiones de Azure Stack Hub](azure-stack-servicing-policy.md).
3. Prepare el paquete de OEM con los pasos que se describen en [Descarga de paquetes de actualización para sistemas integrados](azure-stack-servicing-policy.md).
4. Aplique las actualizaciones con los pasos descritos en [Aplicar actualizaciones en Azure Stack Hub](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Configuración de la VM del proveedor de hardware

Algunos proveedores de hardware pueden necesitar una máquina virtual para proporcionarle ayuda con el proceso de actualización de OEM. El fabricante del hardware será responsable de crear estas máquinas virtuales y documentarlas si necesita `ProxyVM` o `HardwareManager` para **-VMType** cuando se ejecute el cmdlet **Set-OEMExternalVM**, así como de las credenciales que se deben usar para **-Credential**. Una vez creadas las VM, configúrelas con **Set-OEMExternalVM** desde el punto de conexión con privilegios.

Para más información sobre el punto de conexión con privilegios en Azure Stack Hub, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](azure-stack-privileged-endpoint.md).

1. Acceda al punto de conexión con privilegios.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Configure la VM del proveedor de hardware mediante el cmdlet **Set-OEMExternalVM**. El cmdlet valida la dirección IP y las credenciales de **-VMType** `ProxyVM`. Para **-VMType** `HardwareManager`, el cmdlet no validará la entrada. El parámetro **-Credential** proporcionado a **Set-OEMExternalVM** es uno que se documentará claramente con la documentación del proveedor de hardware.  *NO* es la credencial de CloudAdmin que se usa con el punto de conexión con privilegios o cualquier otra credencial de Azure Stack Hub existente.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        {
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Pasos siguientes

- [Actualizaciones de Azure Stack Hub](azure-stack-updates.md)
