---
title: Modificación de la configuración específica de la configuración del conmutador de Azure Stack Hub
description: Aprenda lo que puede personalizar en la configuración del conmutador de Azure Stack Hub. Después de que el fabricante de equipos originales (OEM) cree la configuración, no la modifique sin su consentimiento o el del equipo de ingeniería de Microsoft Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 19964b761478bb210d3377b177f05f708697167b
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368726"
---
#  <a name="modify-specific-settings-on-your-azure-stack-hub-switch-configuration"></a>Modificación de la configuración específica de la configuración del conmutador de Azure Stack Hub

Puede modificar algunas opciones de configuración del entorno en la configuración del conmutador Azure Stack Hub. Puede identificar qué opciones de configuración se pueden cambiar en la plantilla que creó el fabricante de equipos originales (OEM). En este artículo se explica cada una de esas opciones de configuración personalizables y de qué manera afectan los cambios a Azure Stack Hub. Estas opciones de configuración incluyen la actualización de contraseña, el servidor syslog, la supervisión de SNMP, la autenticación y la lista de control de acceso. 

Durante la implementación de la solución de Azure Stack Hub, el fabricante de equipos originales (OEM) crea y aplica la configuración del conmutador para TOR y BMC. A continuación, el OEM usa la herramienta de automatización de Azure Stack Hub para confirmar que las opciones necesarias estén configuradas correctamente en los dispositivos. La configuración se basa en la información de la [hoja de cálculo de implementación](azure-stack-deployment-worksheet.md) de Azure Stack Hub. Después de que el OEM cree la configuración, **no** la modifique sin el consentimiento del OEM o del equipo de ingeniería de Microsoft Azure Stack Hub. Recuerde que un cambio en la configuración del dispositivo de red puede afectar significativamente a la operación o a la resolución de problemas de red en su instancia de Azure Stack Hub.

Sin embargo, hay algunos valores que se pueden agregar, quitar o cambiar en la configuración de los conmutadores de red.

>[!Warning]  
> **No** modifique la configuración sin el consentimiento del OEM o del equipo de ingeniería de Microsoft Azure Stack Hub. Recuerde que un cambio en la configuración del dispositivo de red puede afectar significativamente a la operación o a la resolución de problemas de red en su instancia de Azure Stack Hub.
>
> Para obtener más información acerca de estas funciones en el dispositivo de red o cómo realizar estos cambios, póngase en contacto con el proveedor de hardware del OEM o con el Soporte técnico de Microsoft. El OEM tiene el archivo de configuración que se creó con la herramienta de automatización en función de la hoja de cálculo de implementación de Azure Stack Hub. 

## <a name="password-update"></a>Actualización de contraseñas

El operador puede actualizar en cualquier momento la contraseña de cualquier usuario en los conmutadores de red. No es necesario cambiar ninguna información en el sistema de Azure Stack Hub ni usar los pasos de [Cambio de secretos en Azure Stack](azure-stack-rotate-secrets.md).

## <a name="syslog-server"></a>Servidor de Syslog

Los operadores pueden redirigir los registros del conmutador a un servidor de syslog en su centro de datos. Use esta configuración para asegurarse de que los registros de un momento determinado se puedan usar para la resolución de problemas. De forma predeterminada, los registros se almacenan en los conmutadores; tenga en cuenta que su capacidad para almacenar registros es limitada. Consulte la sección que cuenta con las [actualizaciones de la lista de control de acceso](#access-control-list-updates) para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

## <a name="snmp-monitoring"></a>Supervisión de SNMP

El operador puede configurar las versiones 2 o 3 del Protocolo simple de administración de redes (SNMP) para supervisar los dispositivos de red y enviar capturas a una aplicación de supervisión de red en el centro de datos. Por motivos de seguridad, use SNMPv3, ya que es más seguro que la versión 2. Consulte a su proveedor de hardware OEM para conocer las MIB y la configuración necesaria. Consulte la sección que cuenta con las [actualizaciones de la lista de control de acceso](#access-control-list-updates) para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

## <a name="authentication"></a>Authentication

El operador puede configurar los elementos RADIUS o TACACS para administrar la autenticación en los dispositivos de red. Consulte a su proveedor de hardware OEM para conocer los métodos admitidos y la configuración necesaria.  Consulte la sección que cuenta con las [actualizaciones de la lista de control de acceso](#access-control-list-updates) para obtener información general sobre cómo configurar los permisos para obtener acceso de administración al conmutador.

## <a name="access-control-list-updates"></a>Actualizaciones de la lista de control de acceso

> [!NOTE]
> A partir de 1910, la hoja de cálculo de implementación tendrá un nuevo campo para **Redes permitidas** que reemplaza los pasos manuales requeridos para proporcionar acceso a las interfaces de administración de dispositivos de red y al host de ciclo de vida de hardware (HLH) desde un intervalo de red del centro de datos de confianza. Para más información acerca de esta nueva característica, consulte [Planeamiento de la capacidad de red de Azure Stack Hub](azure-stack-network.md#permitted-networks).

El operador puede cambiar la lista de control de acceso (ACL) para permitir el acceso a las interfaces de administración de dispositivos de red y al host de ciclo de vida de hardware (HLH) desde un intervalo de red del centro de datos de confianza. Con la lista de control de acceso, el operador puede permitir que sus máquinas virtuales de jumpbox de administración de un intervalo de red específico obtengan acceso a la interfaz de administración de conmutadores, al sistema operativo HLH y al BMC HLH.

## <a name="next-steps"></a>Pasos siguientes

[Integración de Azure Stack Hub en el centro de datos: DNS](azure-stack-integrate-dns.md)
