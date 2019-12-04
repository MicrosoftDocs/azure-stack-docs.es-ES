---
title: Modificar la configuración específica de la configuración del conmutador de Azure Stack | Microsoft Docs
description: Obtenga información sobre lo que puede personalizar en la configuración del conmutador de Azure Stack. Después de que el fabricante de equipos originales (OEM) cree la configuración, no la modifique sin el consentimiento de ese OEM o del equipo de ingeniería de Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: Femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 99d52dc7165fdd37a614c1396d7e5c1f0165e235
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2019
ms.locfileid: "74299121"
---
#  <a name="modify-specific-settings-on-your-azure-stack-switch-configuration"></a>Modificar la configuración específica de la configuración del conmutador de Azure Stack

Puede modificar algunas opciones de configuración del entorno en la configuración del conmutador Azure Stack. Puede identificar qué opciones de configuración se pueden cambiar en la plantilla que creó el fabricante de equipos originales (OEM). En este artículo se explica cada una de esas opciones de configuración personalizables y de qué manera afectan los cambios a Azure Stack. Estas opciones de configuración incluyen la actualización de contraseña, el servidor syslog, la supervisión de SNMP, la autenticación y la lista de control de acceso. 

Durante la implementación de la solución de Azure Stack, el fabricante de equipos originales (OEM) crea y aplica la configuración del conmutador para TOR y BMC. A continuación, el OEM usa la herramienta de automatización de Azure Stack para confirmar que las opciones necesarias estén configuradas correctamente en los dispositivos. La configuración se basa en la información de la [hoja de cálculo de implementación](azure-stack-deployment-worksheet.md) de Azure Stack. Después de que el OEM cree la configuración, **no** la modifique sin el consentimiento del OEM o del equipo de ingeniería de Microsoft Azure Stack. Recuerde que un cambio en la configuración del dispositivo de red puede afectar significativamente a la operación o a la resolución de problemas de red en su instancia de Azure Stack.

Sin embargo, hay algunos valores que se pueden agregar, quitar o cambiar en la configuración de los conmutadores de red.

>[!Warning]  
> **No** modifique la configuración sin el consentimiento del OEM o del equipo de ingeniería de Microsoft Azure Stack. Recuerde que un cambio en la configuración del dispositivo de red puede afectar significativamente a la operación o a la resolución de problemas de red en su instancia de Azure Stack.
>
> Para obtener más información acerca de estas funciones en el dispositivo de red o cómo realizar estos cambios, póngase en contacto con el proveedor de hardware del OEM o con el Soporte técnico de Microsoft. El OEM tiene el archivo de configuración que se creó con la herramienta de automatización en función de la hoja de cálculo de implementación de Azure Stack. 

## <a name="password-update"></a>Actualización de contraseñas

El operador puede actualizar en cualquier momento la contraseña de cualquier usuario en los conmutadores de red. No es necesario cambiar ninguna información en el sistema de Azure Stack ni usar los pasos para el [Cambio de secretos en Azure Stack](azure-stack-rotate-secrets.md).

## <a name="syslog-server"></a>Servidor de Syslog

Los operadores pueden redirigir los registros del conmutador a un servidor de syslog en su centro de datos. Use esta configuración para asegurarse de que los registros de un momento determinado se puedan usar para la resolución de problemas. De forma predeterminada, los registros se almacenan en los conmutadores; tenga en cuenta que su capacidad para almacenar registros es limitada. Consulte la sección que cuenta con las [actualizaciones de la lista de control de acceso](#access-control-list-updates) para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

## <a name="snmp-monitoring"></a>Supervisión de SNMP

El operador puede configurar las versiones 2 o 3 del Protocolo simple de administración de redes (SNMP) para supervisar los dispositivos de red y enviar capturas a una aplicación de supervisión de red en el centro de datos. Por motivos de seguridad, use SNMPv3, ya que es más seguro que la versión 2. Consulte a su proveedor de hardware OEM para conocer las MIB y la configuración necesaria. Consulte la sección que cuenta con las [actualizaciones de la lista de control de acceso](#access-control-list-updates) para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

## <a name="authentication"></a>Authentication

El operador puede configurar los elementos RADIUS o TACACS para administrar la autenticación en los dispositivos de red. Consulte a su proveedor de hardware OEM para conocer los métodos admitidos y la configuración necesaria.  Consulte la sección que cuenta con las [actualizaciones de la lista de control de acceso](#access-control-list-updates) para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

## <a name="access-control-list-updates"></a>Actualizaciones de la lista de control de acceso

> [!NOTE]
> A partir de 1910, la hoja de cálculo de implementación tendrá un nuevo campo para **Redes permitidas** que reemplaza los pasos manuales requeridos para proporcionar acceso a las interfaces de administración de dispositivos de red y al host de ciclo de vida de hardware (HLH) desde un intervalo de red del centro de datos de confianza. Para más información acerca de esta nueva característica, consulte [Planeamiento de la capacidad de red de Azure Stack](azure-stack-network.md#permitted-networks).

El operador puede cambiar la lista de control de acceso (ACL) para permitir el acceso a las interfaces de administración de dispositivos de red y al host de ciclo de vida de hardware (HLH) desde un intervalo de red del centro de datos de confianza. Con la lista de control de acceso, el operador puede permitir que sus máquinas virtuales de jumpbox de administración de un intervalo de red específico obtengan acceso a la interfaz de administración de conmutadores, al sistema operativo HLH y al BMC HLH.

## <a name="next-steps"></a>Pasos siguientes

[Integración del centro de datos de Azure Stack: DNS](azure-stack-integrate-dns.md)
