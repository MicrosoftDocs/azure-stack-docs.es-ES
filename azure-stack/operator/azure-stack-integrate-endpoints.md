---
title: Publicación de servicios de Azure Stack Hub en el centro de datos
description: Aprenda a publicar servicios de Azure Stack Hub en el centro de datos.
author: myoung
ms.topic: article
ms.date: 09/24/2020
ms.author: patricka
ms.reviewer: wamota
ms.lastreviewed: 09/24/2020
ms.openlocfilehash: 989a20216fd5613fa1269009e5c6dc7b918b8300
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870809"
---
# <a name="publish-azure-stack-hub-services-in-your-datacenter"></a>Publicación de servicios de Azure Stack Hub en el centro de datos

Azure Stack Hub configura direcciones IP virtuales (VIP) para sus roles de infraestructura. Estas VIP se asignan desde el grupo de direcciones IP públicas. Cada VIP está protegida con una lista de control de acceso (ACL) en el nivel de red definido por software. Las ACL también se usan en los conmutadores físicos (Tor y BMC) para proteger aún más la solución. Se crea una entrada DNS para cada punto de conexión de la zona DNS externa que se haya especificado durante la implementación. Por ejemplo, el portal de usuarios se asigna a la entrada de host de DNS de portal. *&lt;region>.&lt;fqdn>* .

En el siguiente diagrama de arquitectura se muestran los diferentes niveles de red y ACL:

![Diagrama que muestra diferentes capas de red y ACL](media/azure-stack-integrate-endpoints/integrate-endpoints-01.svg)

## <a name="ports-and-urls"></a>Puertos y direcciones URL

Para que los servicios de Azure Stack Hub (como los portales, Azure Resource Manager, DNS, etc.) estén disponible para las redes externas, debe permitir el tráfico entrante en estos puntos de conexión procedente de direcciones URL, puertos y protocolos específicos.

En una implementación en la que un proxy transparente establece un vínculo superior a un servidor proxy tradicional o un firewall protege la solución, debe permitir puertos y direcciones URL concretos para la comunicación [entrante](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) y [saliente](azure-stack-integrate-endpoints.md#ports-and-urls-outbound). Aquí se incluyen puertos y direcciones URL de identidad, productos de Marketplace, revisiones y actualizaciones y datos de uso.

[No se admite](azure-stack-firewall.md#ssl-interception) la interceptación de tráfico SSL y puede provocar errores de servicio cuando se accede a los puntos de conexión. 

## <a name="ports-and-protocols-inbound"></a>Puertos y protocolos (de entrada)

Se requiere un conjunto de direcciones IP virtuales de infraestructura para publicar puntos de conexión de Azure Stack Hub en redes externas. La tabla *Punto de conexión (VIP)* se muestra cada punto de conexión, el puerto requerido y el protocolo. Consulte la documentación de implementación de proveedor de recursos específica para los puntos de conexión que requieren proveedores de recursos adicionales, como el proveedor de recursos de SQL.

Las direcciones IP virtuales de infraestructura interna no se indican porque no son necesarias para la publicación de Azure Stack Hub. Las direcciones IP virtuales de usuario son dinámicas y están definidas por los propios usuarios, sin ningún control por parte del operador de Azure Stack Hub.

> [!Note]  
> VPN IKEv2 es una solución de VPN con IPsec basada en estándares que utiliza los puertos UDP 500 y 4500 y el puerto TCP 50. Los firewalls no siempre abren estos puertos, por lo que es posible que VPN IKEv2 no pueda atravesar servidores proxy y firewalls.

Con la adición del [host de extensiones](azure-stack-extension-host-prepare.md), no se requieren puertos en el intervalo 12495-30015.

|Punto de conexión (VIP)|Registro de host DNS A|Protocolo|Puertos|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrador)|Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (administrador)|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (usuario)|Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (usuario)|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Grafo|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista de revocación de certificados|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP y UDP|53|
|Hospedaje | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (usuario)|&#42;.vault. *&lt;región>.&lt;fqdn>*|HTTPS|443|
|Key Vault (administrador)|&#42;.adminvault. *&lt;región>.&lt;fqdn>*|HTTPS|443|
|Cola de almacenamiento|&#42;.queue. *&lt;región>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabla de almacenamiento|&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Proveedor de recursos SQL|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Proveedor de recursos MySQL|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice. *&lt;región>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;región>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;región>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice. *&lt;región>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Puertas de enlace de VPN|     |     |[Consulte las preguntas más frecuentes sobre las puertas de enlace de VPN](/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Puertos y direcciones URL (de salida)

Azure Stack Hub solo admite servidores proxy transparentes. En una implementación en la que un proxy transparente establece un vínculo superior a un servidor proxy tradicional, debe permitir los siguientes puertos y direcciones URL para la comunicación saliente.

[No se admite](azure-stack-firewall.md#ssl-interception) la interceptación de tráfico SSL y puede provocar errores de servicio cuando se accede a los puntos de conexión. El tiempo de expiración máximo admitido para comunicarse con los puntos de conexión necesarios para la identidad es de 60 s.

> [!Note]  
> Azure Stack Hub no admite el uso de ExpressRoute para acceder a los servicios de Azure que se enumeran en la tabla siguiente porque ExpressRoute no puede enrutar el tráfico a todos los puntos de conexión.

|Propósito|Dirección URL de destino|Protocolo|Puertos|Red de origen|
|---------|---------|---------|---------|---------|
|Identidad|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https:\//management.core.windows.net<br>ARMUri = https:\//management.azure.com<br>https:\//\*.msftauth.net<br>https:\//\*.msauth.net<br>https:\//\*.msocdn.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure China 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure Alemania**<br>https:\//login.microsoftonline.de/<br>https:\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|VIP pública - /27<br>Red de la infraestructura pública|
|Redifusión de Marketplace|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;.blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|VIP pública - /27|
|Revisión y actualización|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|VIP pública - /27|
|Registro|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn|HTTPS|443|VIP pública - /27|
|Uso|**Azure**<br>https://&#42;.trafficmanager.net<br>**Azure Government**<br>https://&#42;.usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;.trafficmanager.cn|HTTPS|443|VIP pública - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br><br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|VIP pública - /27<br>Red de la infraestructura pública|
|NTP|(Se proporciona la dirección IP del servidor NTP para la implementación)|UDP|123|VIP pública - /27|
|DNS|(Se proporciona la dirección IP del servidor DNS para la implementación)|TCP<br>UDP|53|VIP pública - /27|
|SYSLOG|(Se proporciona la dirección IP del servidor de SYSLOG para la implementación)|TCP<br>UDP|6514<br>514|VIP pública - /27|
|CRL|(La dirección URL en los puntos de distribución de CRL en el certificado)<br>http://crl.microsoft.com/pki/crl/products<br>http://mscrl.microsoft.com/pki/mscorp<br>http://www.microsoft.com/pki/certs<br>http://www.microsoft.com/pki/mscorp<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>|HTTP|80|VIP pública - /27|
|LDAP|Bosque de Active Directory proporcionado para la integración con Graph|TCP<br>UDP|389|VIP pública - /27|
|SSL de LDAP|Bosque de Active Directory proporcionado para la integración con Graph|TCP|636|VIP pública - /27|
|GC DE LDAP|Bosque de Active Directory proporcionado para la integración con Graph|TCP|3268|VIP pública - /27|
|SSL de GC de LDAP|Bosque de Active Directory proporcionado para la integración con Graph|TCP|3269|VIP pública - /27|
|AD FS|Punto de conexión de metadatos de AD FS proporcionado para la integración con AD FS|TCP|443|VIP pública - /27|
| Recopilación de registros de diagnóstico |https://*.blob.core.windows.net<br>https://azsdiagprdlocalwestus02.blob.core.windows.net<br>https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com<br>https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com | HTTPS | 443 | VIP pública - /27 |
|     |     |     |     |     |

Las direcciones URL de salida tienen equilibrio de carga mediante Azure Traffic Manager para proporcionar la mejor conectividad posible basada en la ubicación geográfica. Con las direcciones URL con equilibrio de carga, Microsoft puede actualizar y cambiar los puntos de conexión de back-end sin que ello afecte a los usuarios. Microsoft no comparte la lista de direcciones IP para las direcciones URL con equilibrio de carga. Debe usar un dispositivo que admita el filtrado por dirección URL, en lugar de por dirección IP.

El DNS de salida se necesita en todo momento, lo que varía es el origen que consulta el DNS externo y el tipo de integración de identidad que se ha elegido. Durante la implementación de un escenario conectado, el DVM que se encuentra en la red de BMC necesita acceso de salida. Pero después de la implementación, el servicio DNS se traslada a un componente interno que enviará las consultas a través de una dirección IP virtual pública. En ese momento, se puede quitar el acceso DNS de salida a través de la red BMC, pero el acceso de la IP virtual pública a ese servidor DNS debe permanecer o, de lo contrario, la autenticación producirá un error.

## <a name="next-steps"></a>Pasos siguientes

[Requisitos de PKI de Azure Stack Hub](azure-stack-pki-certs.md)
