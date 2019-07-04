---
title: Preparación de un host de extensiones de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo preparar un host de extensiones, que se habilite automáticamente con una actualización futura de Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 06/13/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: ab508956ddcc57baa04c74710ea485c07cc20416
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131158"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Preparación de un host de extensiones de Azure Stack

El host de extensiones protege a Azure Stack reduciendo el número de puertos TCP/IP necesarios. En este artículo, se explica cómo preparar Azure Stack para el host de extensiones, que se habilita automáticamente mediante una actualización de Azure Stack posterior a la actualización 1808. Este artículo se aplica a las actualizaciones 1808, 1809 y 1811 de Azure Stack.

## <a name="certificate-requirements"></a>Requisitos de certificados

El host de extensiones implementa dos nuevos espacios de nombres de dominio que permiten garantizar entradas de host únicas para cada extensión del portal. Los nuevos espacios de nombres de dominio requieren dos certificados comodín adicionales para garantizar una comunicación segura.

En la tabla, se muestran los nuevos espacios de nombres y los certificados asociados:

| Carpeta de implementación | Nombres alternativos del firmante (SAN) y firmante del certificado requeridos | Ámbito (por región) | Nombre del subdominio |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Administración del host de extensiones | *.adminhosting.\<region>.\<fqdn> (certificados SSL comodín) | Administración del host de extensiones | adminhosting.\<region>.\<fqdn> |
| Host de extensiones público | *.hosting.\<region>.\<fqdn> (certificados SSL comodín) | Host de extensiones público | hosting.\<region>.\<fqdn> |

Los requisitos detallados de los certificados pueden consultarse en el artículo [Requisitos de certificados de infraestructura de clave pública de Azure Stack](azure-stack-pki-certs.md).

## <a name="create-certificate-signing-request"></a>Creación de una solicitud de firma de certificados

La herramienta Readiness Checker de Azure Stack permite crear una solicitud de firma de certificados para los dos nuevos certificados SSL obligatorios. Siga los pasos del artículo [Generación de solicitudes de firma de certificados de Azure Stack](azure-stack-get-pki-certs.md).

> [!Note]  
> En función de cómo se hayan solicitado los certificados SSL, es posible que pueda omitir este paso.

## <a name="validate-new-certificates"></a>Validación de nuevos certificados

1. Abra PowerShell con permisos elevados en el host de ciclo de vida de hardware o en la estación de trabajo de administración de Azure Stack.
2. Ejecute el siguiente cmdlet para instalar la herramienta Readiness Checker de Azure Stack.

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Ejecute el script siguiente para crear la estructura de carpetas necesaria:

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Si realiza la implementación con Servicios de federación de Azure Active Directory (AD FS), deben agregarse los directorios siguientes a **$directories** en el script: `ADFS`, `Graph`.

4. Coloque los certificados existentes, que se utilizan actualmente en Azure Stack, en los directorios adecuados. Por ejemplo, coloque el certificado de **ARM de administrador** en la carpeta `Arm Admin`. A continuación, coloque los certificados de hospedaje recién creados en los directorios `Admin extension host` y `Public extension host`.
5. Ejecute el cmdlet siguiente para iniciar la comprobación de certificados:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Compruebe que la salida y todos los certificados pasen todas las pruebas.


## <a name="import-extension-host-certificates"></a>Importación de certificados del host de extensiones

Para seguir los pasos que se indican a continuación, utilice un equipo que pueda conectarse al punto de conexión con privilegios de Azure Stack. Asegúrese de que tiene acceso a los nuevos archivos de certificado de ese equipo.

1. Para seguir los pasos que se indican a continuación, utilice un equipo que pueda conectarse al punto de conexión con privilegios de Azure Stack. Asegúrese de que tiene acceso a los nuevos archivos de certificado de ese equipo.
2. Abra PowerShell ISE para ejecutar los siguientes bloques de script.
3. Importe el certificado del punto de conexión de hospedaje de administración.

    ```powershell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Importe el certificado del punto de conexión de hospedaje.
    ```powershell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>Actualización de la configuración de DNS

> [!Note]  
> Este paso no es necesario si usa la delegación de zonas DNS para la integración de DNS.
Si se han configurado registros A de un determinado host para publicar puntos de conexión de Azure Stack, será necesario crear dos registros A más:

| IP | Nombre de host. | type |
|----|------------------------------|------|
| \<IP> | *.Adminhosting.\<Region>.\<FQDN> | Una |
| \<IP> | *.Hosting.\<Region>.\<FQDN> | Una |

Las direcciones IP asignadas pueden recuperarse utilizando un punto de conexión con privilegios y ejecutando el cmdlet **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Puertos y protocolos

En el artículo [Integración del centro de datos de Azure Stack: publicar puntos de conexión](azure-stack-integrate-endpoints.md), se describen los puertos y protocolos que requieren comunicaciones entrantes para publicar puntos de conexión de Azure Stack antes del lanzamiento del host de extensiones.

### <a name="publish-new-endpoints"></a>Publicación de nuevos puntos de conexión

Hay dos nuevos puntos de conexión que deben publicarse a través del firewall. Las direcciones IP asignadas del grupo VIP público se pueden recuperar mediante el siguiente código que debe ejecutarse desde el [punto de conexión con privilegios del entorno](azure-stack-privileged-endpoint.md) de Azure Stack.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Salida de ejemplo

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Realice este cambio antes de habilitar el host de extensiones. De este modo, modo los portales de Azure Stack estarán disponibles en todo momento.

| Punto de conexión (VIP) | Protocolo | Puertos |
|----------------|----------|-------|
| Hospedaje de administración | HTTPS | 443 |
| Hospedaje | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Actualización de las reglas de publicación existentes (tras las habilitación de del host de extensiones)

> [!Note]  
> El paquete de actualización de Azure Stack 1808 **no** habilita todavía el host de extensiones. No obstante, permite prepararse para el host de extensiones importando los certificados necesarios. No cierre ningún puerto antes de que el host de extensiones se habilite automáticamente con un paquete de actualización de Azure Stack Update posterior al 1808.

Los siguientes puertos de punto de conexión existentes deben cerrarse en las reglas del firewall existentes.

> [!Note]  
> Es recomendable que estos puertos se cierren una vez que la validación se ha realizado correctamente.

| Punto de conexión (VIP) | Protocolo | Puertos |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portal (administrador) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portal (usuario) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (administrador) | HTTPS | 30024 |
| Azure Resource Manager (usuario) | HTTPS | 30024 |

## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de la [integración del firewall](azure-stack-firewall.md).
- Más información acerca de la [generación de solicitudes de firma de certificados de Azure Stack](azure-stack-get-pki-certs.md)
