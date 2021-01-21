---
title: Solución de problemas comunes con los certificados PKI
titleSuffix: Azure Stack Hub
description: Corrija problemas comunes con los certificados PKI de Azure Stack Hub mediante Azure Stack Hub Readiness Checker.
author: BryanLa
ms.topic: how-to
ms.date: 11/10/2020
ms.author: bryanla
ms.reviewer: unknown
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: f9a7a42fafe55e7b598e6fcb67e5353c2453222e
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255833"
---
# <a name="fix-common-issues-with-azure-stack-hub-pki-certificates"></a>Solución de problemas comunes con los certificados PKI de Azure Stack Hub

La información de este artículo le ayudará a reconocer y resolver problemas comunes con los certificados PKI de Azure Stack Hub. Puede detectar problemas cuando usa la herramienta Azure Stack Hub Readiness Checker para [validar certificados de PKI en Azure Stack Hub](azure-stack-validate-pki-certs.md). La herramienta comprueba si los certificados cumplen los requisitos de PKI de una implementación de Azure Stack Hub y una rotación de secretos de Azure Stack Hub, y después registra los resultados en un [archivo report.json](azure-stack-validation-report.md).  

## <a name="http-crl---warning"></a>CRL HTTP: advertencia

**Problema** : el certificado no contiene la CRL HTTP en la extensión CDP.

**Corrección** : no se trata de un problema de bloqueo. Azure Stack requiere una CRL HTTP para la comprobación de revocación según los [requisitos del certificado de infraestructura de clave pública (PKI) de Azure Stack Hub](./azure-stack-pki-certs.md).  No se detectó ninguna CRL HTTP en el certificado.  Para asegurarse de que la comprobación de la revocación de certificados funciona, la entidad de certificación debe emitir un certificado con una CRL HTTP en la extensión CDP.

## <a name="http-crl---fail"></a>CRL HTTP: error

**Problema** : no se puede establecer conexión con la CRL HTTP en la extensión CDP.

**Corrección** : se trata de un problema de bloqueo. Azure Stack requiere conectividad con una CRL de HTTP para la comprobación de revocación según el artículo [Publicación de puertos y direcciones URL de Azure Stack Hub (salientes)](./azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

## <a name="pfx-encryption"></a>Cifrado de PFX

**Problema**: el cifrado PFX no es TripleDES-SHA1.

**Corrección**: exporte los archivos PFX con cifrado **TripleDES-SHA1**. Este es el cifrado predeterminado para todos los clientes Windows 10 cuando se exporta desde un complemento de certificado o se usa `Export-PFXCertificate`.

## <a name="read-pfx"></a>Lectura de PFX

**Advertencia**: la contraseña sólo protege la información privada del certificado.  

**Corrección**: exporte los archivos PFX con la configuración opcional de **Habilitar privacidad de certificado**.  

**Problema**: el archivo PFX no es válido.  

**Corrección**: vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack Hub para la implementación](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmo de firma

**Problema**: el algoritmo de firma es SHA1.

**Corrección**: siga los pasos de Generación de solicitudes de firma de certificado para Azure Stack Hub para volver a generar la solicitud de firma de certificado (CSR) con el algoritmo de firma SHA256. Luego, reenvíe la CSR a la entidad de certificación para que vuelva a emitir el certificado.

## <a name="private-key"></a>Clave privada

**Problema**: falta la clave privada o no contiene el atributo de máquina local.  

**Corrección**: desde el equipo que generó la solicitud de firma de certificado, vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack Hub para la implementación](azure-stack-prepare-pki-certs.md). Estos pasos incluyen la exportación desde el almacén de certificados de la máquina local.

## <a name="certificate-chain"></a>Cadena de certificados

**Problema**: la cadena de certificados no está completa.  

**Corrección**: los certificados deben contener una cadena de certificados completa. Vuelva a exportar el certificado siguiendo los pasos descritos en [Preparación de certificados PKI de Azure Stack Hub para la implementación](azure-stack-prepare-pki-certs.md), y seleccione la opción **Incluir todos los certificados en la ruta de certificación si es posible**.

## <a name="dns-names"></a>Nombres DNS

**Problema**: el elemento **DNSNameList** del certificado no contiene el nombre del punto de conexión de servicio de Azure Stack Hub ni una coincidencia válida de caracteres comodín. Las coincidencias de caracteres comodín solo son válidas para el espacio de nombres del extremo izquierdo del nombre DNS. Por ejemplo, `*.region.domain.com` es válida para `portal.region.domain.com`, pero no para `*.table.region.domain.com`.

**Corrección**: siga los pasos de generación de solicitudes de firma de certificado para Azure Stack Hub para volver a generar la solicitud de firma de certificado con los nombres DNS correctos que admitan los puntos de conexión de Azure Stack Hub. Vuelva a enviar la solicitud de firma de certificado a una entidad de certificación. Después, siga los pasos de [Preparación de certificados PKI de Azure Stack Hub para la implementación](azure-stack-prepare-pki-certs.md) para exportar el certificado desde la máquina que generó la solicitud.  

## <a name="key-usage"></a>Uso de las claves

**Problema**: en el uso de las claves falta la firma digital o el cifrado de clave, o en el uso mejorado de las claves falta la autenticación de servidor o la autenticación de cliente.  

**Corrección**: siga los pasos de [Generación de solicitudes de firma de certificado para Azure Stack Hub](azure-stack-get-pki-certs.md) para volver a generar la solicitud de firma de certificado con los atributos de uso de clave correctos. Vuelva a enviar la solicitud de firma de certificado a la entidad de certificación y confirme que ninguna plantilla de certificado sobrescribe el uso de clave en la solicitud.

## <a name="key-size"></a>Tamaño de clave

**Problema**: el tamaño de la clave es inferior a 2048.

**Corrección**: siga los pasos de [Generación de solicitudes de firma de certificado para Azure Stack Hub](azure-stack-get-pki-certs.md) para volver a generar la solicitud de firma de certificado con la longitud de clave correcta (2048) y, después, reenvíe la solicitud a la autoridad de certificación.

## <a name="chain-order"></a>Orden de la cadena

**Problema**: el orden de la cadena de certificados es incorrecto.  

**Corrección**: vuelva a exportar el certificado siguiendo los pasos descritos en [Preparación de certificados PKI de Azure Stack Hub para la implementación](azure-stack-prepare-pki-certs.md), y seleccione la opción **Incluir todos los certificados en la ruta de certificación si es posible**. Asegúrese de que solo se selecciona el certificado de hoja para la exportación.

## <a name="other-certificates"></a>Otros certificados

**Problema**: el paquete PFX contiene certificados que no son el certificado de hoja ni parte de la cadena de certificados.  

**Corrección**: vuelva a exportar el certificado siguiendo los pasos descritos en [Preparación de certificados PKI de Azure Stack Hub para la implementación](azure-stack-prepare-pki-certs.md), y seleccione la opción **Incluir todos los certificados en la ruta de certificación si es posible**. Asegúrese de que solo se selecciona el certificado de hoja para la exportación.

## <a name="fix-common-packaging-issues"></a>Solución de problemas comunes de empaquetado

La herramienta **AzsReadinessChecker** contiene un cmdlet asistente denominado **Repair-AzsPfxCertificate** que puede importar y luego exportar un archivo PFX para solucionar problemas comunes de empaquetado, por ejemplo:

- El **cifrado PFX** no es TripleDES-SHA1.
- Falta la **clave privada** en el atributo de la máquina Local.
- La **cadena de certificados** es incorrecta o está incompleta. La máquina local debe contener la cadena de certificados si no lo hace el paquete PFX.
- **Otros certificados**

**Repair-AzsPfxCertificate** no sirve de ayuda si hay que generar una nueva solicitud de firma de certificado y volver a emitir un certificado.

### <a name="prerequisites"></a>Prerrequisitos

Deben cumplirse los siguientes requisitos previos en el equipo donde se ejecuta la herramienta:

- Windows 10 o Windows Server 2016, con conectividad a internet.
- Azure PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente cmdlet de PowerShell y luego revise la versión **principal** y las versiones **secundarias**:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Configure [PowerShell para Azure Stack Hub](powershell-install-az-module.md).
- Descargue la versión más reciente de la herramienta [Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker).

### <a name="import-and-export-an-existing-pfx-file"></a>Importación y exportación de una archivo PFX existente

1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute el siguiente comando para instalar Azure Stack Hub Readiness Checker:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
   ```

2. Desde el símbolo del sistema de PowerShell, ejecute el siguiente cmdlet para establecer la contraseña PFX. Escriba la contraseña cuando se le solicite:

   ```powershell
   $password = Read-Host -Prompt "Enter password" -AsSecureString
   ```

3. Desde el símbolo del sistema de PowerShell, ejecute el siguiente comando para exportar un nuevo archivo PFX:

   - En `-PfxPath`, especifique la ruta de acceso al archivo PFX con el que trabaja. En el ejemplo siguiente, la ruta de acceso es `.\certificates\ssl.pfx`.
   - En `-ExportPFXPath`, especifique la ubicación y el nombre del archivo PFX que se va a exportar. En el ejemplo siguiente, la ruta de acceso es `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Cuando se haya completado la herramienta, compruebe el éxito en el resultado:

   ```shell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Hub Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Más información acerca de la seguridad de Azure Stack](azure-stack-rotate-secrets.md)