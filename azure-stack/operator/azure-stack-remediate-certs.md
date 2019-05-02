---
title: Corrección de problemas de certificados en Azure Stack | Microsoft Docs
description: Use Azure Stack Readiness Checker para revisar y corregir problemas de certificados.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
origin.date: 02/21/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 009eb56621f7cd395c3d2eefb29b9fa624af888b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308091"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Corrección de problemas comunes con certificados de PKI en Azure Stack

La información de este artículo puede ayudarle a reconocer y resolver problemas comunes con los certificados de PKI en Azure Stack. Puede detectar problemas cuando usa la herramienta Azure Stack Readiness Checker para [validar certificados de PKI en Azure Stack](azure-stack-validate-pki-certs.md). La herramienta comprueba que los certificados cumplen los requisitos de PKI de una implementación de Azure Stack y una rotación de secretos de Azure Stack, y registra los resultados en un archivo [report.json file](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Cifrado de PFX

**Error**: el cifrado PFX no es TripleDES-SHA1.

**Corrección**: exportar archivos PFX con cifrado **TripleDES SHA1**. Esta es la opción predeterminada para todos los clientes Windows 10 al exportar desde un complemento de certificado o usar `Export-PFXCertificate`.

## <a name="read-pfx"></a>Lectura de PFX

**Advertencia**: la contraseña sólo protege la información privada del certificado.  

**Corrección**: exporte archivos PFX con la configuración opcional de **Habilitar privacidad de certificado**.  

**Error**archivo PFX no válido.  

**Corrección**: vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmo de firma

**Error**: el algoritmo de firma es SHA1.

**Corrección**: siga los pasos de generación de solicitudes de firma de certificados de Azure Stack para volver a generar la solicitud de firma de certificados (CSR) con el algoritmo de firma SHA256. Después, reenvíe la CSR a la entidad de certificación para que vuelva a emitir el certificado.

## <a name="private-key"></a>Clave privada

**Error**: falta la clave privada o no contiene el atributo de máquina Local.  

**Corrección**: desde el equipo que generó el CSR, vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment). Estos pasos incluyen la exportación desde el almacén de certificados de la máquina local.

## <a name="certificate-chain"></a>Cadena de certificados

**Error**: la cadena de certificados no está completa.  

**Corrección**: los -certificados deben contener una cadena de certificados completa. Vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) y seleccione la opción **Incluir todos los certificados en la ruta de certificación (si es posible)**.

## <a name="dns-names"></a>Nombres DNS

**Error**: el objeto DNSNameList del certificado no contiene el nombre del punto de conexión de servicio de Azure Stack ni una coincidencia válida de caracteres comodín. Las coincidencias de caracteres comodín solo son válidas para el espacio de nombres del extremo izquierdo del nombre DNS. Por ejemplo, _*.region.domain.com_ solo es válida para *portal.region.domain.com*, no para _*.table.region.domain.com_.

**Corrección**: siga los pasos de Generación de solicitudes de firma de certificados de Azure Stack para volver a generar la CSR con los nombres DNS correctos para admitir puntos de conexión de Azure Stack. Reenvíe la CSR a una entidad de certificación y luego siga los pasos de [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) para exportar el certificado de la máquina que genera la CSR.  

## <a name="key-usage"></a>Uso de las claves

**Error**: falta el uso de claves en la firma digital o el cifrado de clave, o falta el uso mejorado de clave en la autenticación de servidor o la autenticación de cliente.  

**Corrección**: siga los pasos de [Generación de solicitudes de firma de certificados de Azure Stack](azure-stack-get-pki-certs.md) para volver a generar la CSR con los atributos de uso de clave correctos. Vuelva a enviar la CSR a la entidad de certificación y confirme que ninguna plantilla de certificado sobrescribe el uso de clave en la solicitud.

## <a name="key-size"></a>Tamaño de clave

**Error**: el tamaño de clave es inferior a 2048.

**Corrección**: siga los pasos de [Generación de solicitudes de firma de certificados de Azure Stack](azure-stack-get-pki-certs.md) para volver a generar la CSR con la longitud de clave correcta (2048) y reenvíe la CSR a la entidad de certificación.

## <a name="chain-order"></a>Orden de la cadena

**Error**: el orden de la cadena de certificados es incorrecto.  

**Corrección**: vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) y seleccione la opción **Incluir todos los certificados en la ruta de certificación (si es posible)**. Asegúrese de que solo se selecciona el certificado de hoja para la exportación.

## <a name="other-certificates"></a>Otros certificados

**Error**: el paquete PFX contiene certificados que no son el certificado de hoja ni parte de la cadena de certificados.  

**Corrección**: vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) y seleccione la opción **Incluir todos los certificados en la ruta de certificación (si es posible)**. Asegúrese de que solo se selecciona el certificado de hoja para la exportación.

## <a name="fix-common-packaging-issues"></a>Solución de problemas comunes de empaquetado

**AzsReadinessChecker** contiene un cmdlet asistente llamado `Repair-AzsPfxCertificate`, que puede importar y luego exportar un archivo PFX para solucionar problemas comunes de empaquetado, por ejemplo:

- El *cifrado PFX*  no es TripleDES-SHA1.
- Falta la *clave privada* en el atributo de máquina Local.
- La *cadena de certificados* es incorrecta o está incompleta. La máquina local debe contener la cadena de certificados si no lo hace el paquete PFX.
- *Otros certificados*

`Repair-AzsPfxCertificate` no sirve de ayuda si hay que generar una nueva CSR y volver a emitir un certificado.

### <a name="prerequisites"></a>Requisitos previos

Deben cumplirse los siguientes requisitos previos en el equipo donde se ejecuta la herramienta:

- Windows 10 o Windows Server 2016, con conectividad a internet.
- Azure PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente cmdlet de PowerShell y luego revise la versión *principal* y las versiones *secundarias*:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Configure [PowerShell para Azure Stack](azure-stack-powershell-install.md).
- Descargue la versión más reciente de la herramienta [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

### <a name="import-and-export-an-existing-pfx-file"></a>Importación y exportación de una archivo PFX existente

1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema administrativo de PowerShell y ejecute el siguiente comando para instalar AzsReadinessChecker:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Desde el símbolo del sistema de PowerShell, ejecute el siguiente cmdlet para establecer la contraseña PFX. Reemplace `PFXpassword` por la contraseña real:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para exportar un nuevo archivo PFX:

   - En `-PfxPath`, especifique la ruta de acceso al archivo PFX con el que trabaja. En el ejemplo siguiente, la ruta de acceso es `.\certificates\ssl.pfx`.
   - En `-ExportPFXPath`, especifique la ubicación y el nombre del archivo PFX que se va a exportar. En el ejemplo siguiente, la ruta de acceso es `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Cuando se haya completado la herramienta, compruebe el éxito en el resultado:

   ```powershell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Consulte aquí más información acerca de la seguridad de Azure Stack](azure-stack-rotate-secrets.md).
