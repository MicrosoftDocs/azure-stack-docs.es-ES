---
title: Integración de red de MDC
description: Obtenga información acerca de la integración de red de Azure Stack para el dispositivo MDC.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: bfc1851d57db31a929b92902792851a3cef58b39
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183457"
---
# <a name="network-integration"></a>Integración de red 

En este tema se trata la integración de red de Azure Stack.

## <a name="border-connectivity-uplink"></a>Conectividad de borde (vínculo superior)


El planeamiento de la integración de red es un requisito previo importante para la correcta implementación, operación y administración de sistemas integrados de Azure Stack. El planeamiento de la conectividad de borde comienza con la elección de si se debe usar el enrutamiento dinámico con el protocolo de puerta de enlace de borde (BGP). Esto requiere la asignación de un número de sistema autónomo de BGP de 16 bits (público o privado) o el uso del enrutamiento estático, en el cual se asigna una ruta estática predeterminada a los dispositivos de borde.

Los conmutadores perimetrales requieren vínculos superiores de capa 3 con direcciones IP de punto a punto (redes /30) configuradas en las interfaces físicas. No se admite el uso de vínculos superiores de capa 2 con conmutadores perimetrales que admiten operaciones de Azure Stack. 

### <a name="bgp-routing"></a>Enrutamiento BGP

El uso de un protocolo de enrutamiento dinámico, como BGP, garantiza que el sistema siempre sea consciente de los cambios de red y facilita su administración. Para una seguridad mejorada, se puede establecer una contraseña en el emparejamiento BGP entre los dispositivos perimetral y de borde.

Como se muestra en el siguiente diagrama, la publicidad del espacio IP privado en el conmutador de Tor se bloquea mediante una lista de prefijos. La lista de prefijos deniega la publicidad de la red privada y se aplica como un mapa de ruta en la conexión entre los dispositivos perimetral y de borde.

El equilibrador de carga de software (SLB) que se ejecuta dentro de los niveles de la solución de Azure Stack se empareja con los dispositivos Tor para que pueda anunciar dinámicamente las direcciones VIP.

Para asegurarse de que el tráfico de usuario se recupera del error de forma transparente e inmediata, el VPC o MLAG configurados entre los dispositivos de Tor permiten el uso de la agregación de vínculos de chasis múltiples a los hosts y HSRP o VRRP que proporciona redundancia de red para las redes IP.


### <a name="static-routing"></a>Enrutamiento estático

El enrutamiento estático requiere una configuración adicional para los dispositivos de borde. Requiere más intervención y administración manual, así como un análisis exhaustivo antes de realizar cualquier cambio. Los problemas causados por un error de configuración pueden tardar más tiempo en revertirse según los cambios realizados. No se recomienda este método de enrutamiento, pero sí se admite.

Para integrar Azure Stack en su entorno de red con enrutamiento estático, los cuatro vínculos físicos entre los dispositivos perimetral y de borde deben estar conectados. No se puede garantizar la alta disponibilidad debido a cómo funciona el enrutamiento estático.

El dispositivo de borde debe configurarse con rutas estáticas que señalen a cada una de las cuatro direcciones IP P2P establecidas entre los dispositivos perimetral y de borde para el tráfico destinado a cualquier red en Azure Stack, pero para que funcione, solo se necesitan la red de VIP pública o externa. Para la implementación inicial, se necesitan rutas estáticas a las redes BMC y externa. Los operadores pueden optar por dejar las rutas estáticas en el borde para acceder a los recursos de administración que residen en la red BMC y de infraestructura. Es opcional agregar rutas estáticas a la red de infraestructura de conmutadores y de administración de conmutadores.

Los dispositivos de Tor están configurados con una ruta estática predeterminada que envía todo el tráfico a los dispositivos de borde. La única excepción de tráfico a la regla predeterminada se da en el espacio privado, que se bloquea con una lista de control de acceso aplicada en la conexión de Tor a borde.

El enrutamiento estático solo se aplica a los vínculos superiores entre los conmutadores perimetral y de borde.
El enrutamiento dinámico BGP se usa en el bastidor porque es una herramienta esencial para SLB y otros componentes, y no se puede deshabilitar ni quitar.


\* La red BMC es opcional después de la implementación.

\*\* La red de la infraestructura de conmutadores es opcional, ya que se puede incluir toda la red en la red de administración de conmutadores.

\*\*\* La red de administración de conmutadores es necesaria y se puede agregar por separado desde la red de la infraestructura de conmutadores.

### <a name="transparent-proxy"></a>Proxy transparente

Si el centro de datos requiere que todo el tráfico utilice un proxy, debe configurar un proxy transparente para procesar todo el tráfico del bastidor y tratarlo de acuerdo con la directiva, separando el tráfico entre las zonas de la red.

La solución Azure Stack no es compatible con servidores proxy web normales. 


Un proxy transparente (también conocido como proxy interceptor, alineado o forzado) intercepta la comunicación normal en la capa de red sin requerir ninguna configuración especial del cliente. Los clientes no necesitan estar enterados de la existencia del proxy.



No se admite la interceptación de tráfico SSL y puede provocar errores de servicio cuando se accede a los puntos de conexión. El tiempo de expiración máximo admitido para comunicarse con los puntos de conexión necesarios para la identidad es de 60 s con 3 reintentos.

## <a name="dns"></a>DNS

En esta sección se trata la configuración del Sistema de nombres de dominio (DNS).


### <a name="configure-conditional-dns-forwarding"></a>Configure el reenvío condicional de DNS

Esto solo se aplica a una implementación de AD FS. 

Para habilitar la resolución de nombres con la infraestructura DNS existente, configure el reenvío condicional.

Para agregar un reenviador condicional, debe utilizar el punto de conexión con privilegios.

Para este procedimiento, use un equipo de la red del centro de datos que pueda comunicarse con el punto de conexión con privilegios de Azure Stack.

1.  Abra una sesión de Windows PowerShell con privilegios elevados (ejecútela como administrador) y conéctese a la dirección IP del punto de conexión con privilegios. Use las credenciales para la autenticación de CloudAdmin.

    ```powershell
    \$cred=Get-Credential Enter-PSSession -ComputerName \<IP Address of ERCS\> -ConfigurationName PrivilegedEndpoint -Credential \$cred 
    ```
    

2.  Después de conectarse al punto de conexión con privilegios, ejecute el siguiente comando de PowerShell. Sustituya los valores de ejemplo proporcionados por su nombre de dominio y direcciones IP de los servidores DNS que quiere usar.

    ```powershell
    Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2" 
    ```

### <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Resolver nombres DNS de Azure Stack desde fuera de Azure Stack

Los servidores autoritativos son los que contienen la información de zona DNS externa y las zonas creadas por el usuario. Realice la integración con estos servidores para habilitar la delegación de zona o el reenvío condicional para resolver nombres DNS de Azure Stack desde fuera de Azure Stack.

### <a name="get-dns-server-external-endpoint-information"></a>Obtener información del punto de conexión externo del servidor DNS

Para integrar la implementación de Azure Stack con la infraestructura DNS, necesitará la información siguiente:

-   FQDN del servidor DNS

-   Direcciones IP del servidor DNS

Los FQDN de los servidores DNS de Azure Stack tienen el formato siguiente:

*\<NAMINGPREFIX\>-ns01.\<REGION\>.\<EXTERNALDOMAINNAME\>*

*\<NAMINGPREFIX\>-ns02.\<REGION\>.\<EXTERNALDOMAINNAME\>*

Con los valores de ejemplo, los FQDN de los servidores DNS son los siguientes:

*azs-ns01.east.cloud.fabrikam.com*

*azs-ns02.east.cloud.fabrikam.com*

Esta información está disponible en el portal de administración, pero también se crea al finalizar todas las implementaciones de Azure Stack en un archivo denominado  *AzureStackStampInformation.json* .
Este archivo se encuentra en la carpeta *C:\\CloudDeployment\\logs* de la máquina virtual de implementación. Si no está seguro de qué valores se han usado para la implementación de Azure Stack, puede obtener los valores desde aquí.

Si la máquina virtual de implementación ya no está disponible o no es accesible, puede obtener los valores si se conecta al punto de conexión con privilegios y ejecuta el cmdlet *Get-AzureStackStampInformation* de PowerShell. Para más información, consulte Punto de conexión con privilegios.

### <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Configurar el reenvío condicional a Azure Stack

La manera más sencilla y segura de integrar Azure Stack con la infraestructura de DNS es hacer un reenvío condicional de la zona desde el servidor que hospeda la zona principal. Se recomienda este enfoque si tiene el control directo de los servidores DNS que hospedan la zona principal del espacio de nombres DNS externo de Azure Stack.

Si no está familiarizado con los reenvíos condicionales mediante DNS, consulte el siguiente artículo de TechNet: Assign a Conditional Forwarder for a Domain Name (Asignación de reenviadores condicionales a un nombre de dominio) o la documentación específica de la solución DNS.

Si ha especificado que la zona DNS externa de Azure Stack debe parecer un dominio secundario de su nombre de dominio corporativo, no se puede usar el reenvío condicional. Se debe configurar la delegación DNS.

Ejemplo:

-   Nombre de dominio DNS corporativo:  *contoso.com*

-   Nombre de dominio DNS externo de Azure Stack:  *azurestack.contoso.com*

### <a name="editing-dns-forwarder-ips"></a>Edición de direcciones IP de reenviador DNS

Las direcciones IP del reenviador DNS se establecen durante la implementación de Azure Stack. Sin embargo, si las direcciones IP del reenviador deben actualizarse por algún motivo, puede conectarse al punto de conexión con privilegios y ejecutar los cmdlets *Get-AzSDnsForwarder* y *Set-AzSDnsForwarder [[-IPAddress] \<IPAddress[]\>]*  de PowerShell para modificar los valores. Para más información, consulte Punto de conexión con privilegios.

### <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegar la zona DNS externa en Azure Stack

Para que los nombres DNS puedan resolverse desde fuera de una implementación de Azure Stack, debe configurar la delegación de DNS.

Cada registrador dispone de sus propias herramientas de administración de DNS para cambiar los registros de servidores de nombres de un dominio. En la página de administración de DNS del registrador, edite los registros NS y reemplace los registros NS de la zona por los de Azure Stack.

La mayoría de los registradores DNS requieren que se proporcione un mínimo de dos servidores DNS para completar la delegación.

### <a name="firewall"></a>Firewall

Azure Stack configura direcciones IP virtuales (VIP) para sus roles de infraestructura.
Estas VIP se asignan desde el grupo de direcciones IP públicas. Cada VIP está protegida con una lista de control de acceso (ACL) en el nivel de red definido por software. Las ACL también se usan en los conmutadores físicos (Tor y BMC) para proteger aún más la solución. Se crea una entrada DNS para cada punto de conexión de la zona DNS externa que se haya especificado durante la implementación. Por ejemplo, el portal de usuarios se asigna a la entrada de host de DNS del portal. *\<region\>.\<fqdn\>* .

En el siguiente diagrama de arquitectura se muestran los diferentes niveles de red y ACL:

![En el diagrama de arquitectura se muestran los diferentes niveles de red y ACL](media/network-deployment/network-architecture.png) 

### <a name="ports-and-urls"></a>Puertos y direcciones URL

Para que los servicios de Azure Stack (como los portales, Azure Resource Manager, DNS, entre otros) estén disponible para las redes externas, debe permitir tráfico entrante en estos puntos de conexión para las direcciones URL, puertos y protocolos específicos.

En una implementación en la que un proxy transparente establece un vínculo superior a un servidor proxy tradicional o un firewall protege la solución, debe permitir puertos y direcciones URL concretos para la comunicación entrante y saliente. Aquí se incluyen puertos y direcciones URL de identidad, productos de Marketplace, revisiones y actualizaciones y datos de uso.

### <a name="outbound-communication"></a>Comunicación saliente

Azure Stack solo admite servidores proxy transparentes. En una implementación en la que un proxy transparente establece un vínculo superior a un servidor proxy tradicional, debe permitir los puertos y las direcciones URL de la tabla siguiente para la comunicación saliente al realizar la implementación en el modo conectado.

No se admite la interceptación de tráfico SSL y puede provocar errores de servicio cuando se accede a los puntos de conexión. El tiempo de expiración máximo admitido para comunicarse con los puntos de conexión necesarios para la identidad es de 60 s.

>[!NOTE] 
>Azure Stack no admite el uso de ExpressRoute para acceder a los servicios de Azure que se enumeran en la tabla siguiente porque ExpressRoute no puede enrutar el tráfico a todos los puntos de conexión.      


|Propósito|Dirección URL de destino|Protocolo|Puertos|Red de origen|
|---------|---------|---------|---------|---------|
|Identidad|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https:\//management.core.windows.net<br>ARMUri = https:\//management.azure.com<br>https:\//\*.msftauth.net<br>https:\//\*.msauth.net<br>https:\//\*.msocdn.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure China 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure Alemania**<br>https:\//login.microsoftonline.de/<br>https:\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|VIP pública - /27<br>Red de la infraestructura pública|
|Redifusión de Marketplace|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;.blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|VIP pública - /27|
|Revisión y actualización|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|VIP pública - /27|
|Registro|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn|HTTPS|443|VIP pública - /27|
|Uso|**Azure**<br>https://&#42;.trafficmanager.net<br>**Azure Government**<br>https://&#42;.usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;.trafficmanager.cn|HTTPS|443|VIP pública - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|VIP pública - /27<br>Red de la infraestructura pública|
|NTP|(Se proporciona la dirección IP del servidor NTP para la implementación)|UDP|123|VIP pública - /27|
|DNS|(Se proporciona la dirección IP del servidor DNS para la implementación)|TCP<br>UDP|53|VIP pública - /27|
|CRL|(La dirección URL en los puntos de distribución de CRL en el certificado)|HTTP|80|VIP pública - /27|
|LDAP|Bosque de Active Directory proporcionado para la integración con Graph|TCP<br>UDP|389|VIP pública - /27|
|SSL de LDAP|Bosque de Active Directory proporcionado para la integración con Graph|TCP|636|VIP pública - /27|
|GC DE LDAP|Bosque de Active Directory proporcionado para la integración con Graph|TCP|3268|VIP pública - /27|
|SSL de GC de LDAP|Bosque de Active Directory proporcionado para la integración con Graph|TCP|3269|VIP pública - /27|
|AD FS|Punto de conexión de metadatos de AD FS proporcionado para la integración con AD FS|TCP|443|VIP pública - /27|
|Servicio de recopilación de registros de diagnóstico|Dirección URL de SAS de blob proporcionada por Azure Storage|HTTPS|443|VIP pública - /27|
|     |     |     |     |     |
                                                                                                                                                                
### <a name="inbound-communication"></a>Comunicación entrante

Se requiere un conjunto de direcciones IP virtuales de infraestructura para publicar los puntos de conexión de Azure Stack en redes externas. En la tabla  *Punto de conexión (VIP)*  se muestra cada punto de conexión, el puerto necesario y el protocolo. Consulte la documentación de implementación de proveedor de recursos específica para los puntos de conexión que requieren proveedores de recursos adicionales, como el proveedor de recursos de SQL.

Las VIP de infraestructura interna no se indican porque no son necesarias para la publicación de Azure Stack. Las VIP de usuario son dinámicas y están definidas por los propios usuarios, sin ningún control por parte del operador de Azure Stack.

>[!NOTE] 
>VPN IKEv2 es una solución de VPN con IPsec basada en estándares que utiliza los puertos UDP 500 y 4500 y el puerto TCP 50. Los firewalls no siempre abren estos puertos, por lo que es posible que VPN IKEv2 no pueda atravesar servidores proxy y firewalls. 


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
|Puertas de enlace de VPN|     |     |[Consulte las preguntas más frecuentes sobre las puertas de enlace de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

