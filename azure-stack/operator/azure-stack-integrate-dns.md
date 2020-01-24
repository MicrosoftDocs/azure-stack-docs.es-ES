---
title: Integración del DNS de Azure Stack Hub en el centro de datos | Microsoft Docs
description: Aprenda a integrar el DNS de Azure Stack Hub con el DNS del centro de datos.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 08/21/2019
keywords: ''
ms.openlocfilehash: 3ef8bb7595711c5df991956d6cbde8d4e379ec47
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535287"
---
# <a name="azure-stack-hub-datacenter-dns-integration"></a>Integración del DNS de Azure Stack Hub en el centro de datos

Para tener acceso a los puntos de conexión de Azure Stack Hub (como **portal**, **adminportal**, **management** y **adminmanagement**) desde fuera de Azure Stack Hub, debe integrar los servicios de DNS de Azure Stack Hub con los servidores DNS que hospedan las zonas DNS que quiere usar en Azure Stack Hub.

## <a name="azure-stack-hub-dns-namespace"></a>Espacio de nombres de DNS de Azure Stack Hub

Se le solicitará que proporcione información importante relacionada con DNS cuando implemente Azure Stack Hub.


|Campo  |Descripción  |Ejemplo|
|---------|---------|---------|
|Region|Ubicación geográfica de la implementación de Azure Stack Hub.|`east`|
|Nombre de dominio externo|Nombre de la zona que quiere usar para la implementación de Azure Stack Hub.|`cloud.fabrikam.com`|
|Nombre de dominio interno|Nombre de la zona interna que se usa para los servicios de infraestructura en Azure Stack Hub. Está integrado en Directory Service y es privado (no es accesible desde fuera de la implementación de Azure Stack Hub).|`azurestack.local`|
|Reenviadores de DNS|Servidores DNS que se usan para reenviar consultas de DNS, registros y zonas DNS que se hospedan fuera de Azure Stack Hub, ya sea en la intranet corporativa o en la red pública de Internet. Puede editar el valor del reenviador DNS con el [cmdlet **Set-AzSDnsForwarder**](#editing-dns-forwarder-ips) después de la implementación. 
|Prefijo de nomenclatura (opcional)|Prefijo de nomenclatura que quiere que tengan los nombres de máquina de instancia de rol de infraestructura de Azure Stack Hub.  Si no se proporciona, el valor predeterminado es `azs`.|`azs`|

El nombre de dominio completo (FQDN) de su implementación y puntos de conexión de Azure Stack Hub es la combinación de los parámetros de región y de nombre de dominio externo. Con los valores de los ejemplos de la tabla anterior, el FQDN para esta implementación de Azure Stack Hub sería el siguiente:

`east.cloud.fabrikam.com`

De esta forma, los ejemplos de algunos de los puntos de conexión de esta implementación tendrían un aspecto parecido a las direcciones URL siguientes:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Para usar este espacio de nombres DNS de ejemplo para una implementación de Azure Stack Hub, se deben cumplir las condiciones siguientes:

- La zona `fabrikam.com` está registrada con un registrador de dominios, con un servidor DNS corporativo interno o con ambos, en función de los requisitos de resolución de nombres.
- El dominio secundario `cloud.fabrikam.com` existe en la zona `fabrikam.com`.
- Los servidores DNS que hospedan las zonas `fabrikam.com` y `cloud.fabrikam.com` son accesibles desde la implementación de Azure Stack Hub.

Para poder resolver nombres DNS para puntos de conexión e instancias de Azure Stack Hub desde fuera de Azure Stack Hub, debe integrar los servidores DNS que hospedan la zona DNS externa para Azure Stack Hub con los servidores DNS que hospedan la zona principal que quiere usar.

### <a name="dns-name-labels"></a>Etiquetas de nombre DNS

Azure Stack Hub admite la adición de una etiqueta de nombre DNS a una dirección IP pública para permitir la resolución de nombres de las direcciones IP públicas. Las etiquetas DNS constituyen una manera práctica de acceder a las aplicaciones y los servicios hospedados en Azure Stack Hub por el nombre. La etiqueta de nombre DNS usa un espacio de nombres ligeramente diferente al de los puntos de conexión de la infraestructura. Siguiendo el ejemplo de espacio de nombres anterior, el espacio de nombres para las etiquetas de nombre DNS aparece de la siguiente manera:

`*.east.cloudapp.cloud.fabrikam.com`

Por lo tanto, si un inquilino indica un valor **Myapp** en el campo de la etiqueta de nombre DNS de un recurso de dirección IP pública, crea un registro A para **myapp** en la zona **east.cloudapp.cloud.fabrikam.com** en el servidor DNS externo de Azure Stack Hub. El nombre de dominio completo resultante aparece de la siguiente manera:

`myapp.east.cloudapp.cloud.fabrikam.com`

Si quiere aprovechar esta funcionalidad y usar este espacio de nombres, debe integrar los servidores DNS que hospedan la zona DNS externa para Azure Stack Hub con los servidores DNS que hospedan la zona principal que también quiere utilizar. Se trata de un espacio de nombres distinto del espacio de nombres de los puntos de conexión de servicio de Azure Stack Hub, por lo que debe crear una delegación adicional o una regla de reenvío condicional.

Para más información acerca del funcionamiento de la etiqueta de nombre DNS, consulte [Uso de DNS en Azure Stack Hub](../user/azure-stack-dns.md).

## <a name="resolution-and-delegation"></a>Resolución y delegación

Existen dos tipos de servidores DNS:

- Un servidor DNS autoritativo hospeda zonas DNS. Solo responde a las consultas DNS de los registros de dichas zonas.
- Un servidor DNS recursivo no hospeda zonas DNS. Responde a todas las consultas DNS, para lo que realiza una llamada a los servidores DNS autoritativos a fin de recopilar los datos que necesita.

Azure Stack Hub incluye servidores DNS autoritativos y recursivos. Los servidores recursivos se usan para resolver todos los nombres, salvo para la zona privada interna y la zona DNS pública externa para esa implementación de Azure Stack Hub.

![Arquitectura de DNS de Azure Stack Hub](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack-hub"></a>Resolución de nombres DNS externos desde Azure Stack Hub

Para resolver nombres DNS de puntos de conexión fuera de Azure Stack Hub (por ejemplo: www\.bing.com), debe proporcionar servidores DNS que Azure Stack Hub pueda usar para reenviar las solicitudes DNS para las que Azure Stack Hub no es autoritativo. Para la implementación, se requieren los servidores DNS a los que Azure Stack Hub reenvía las solicitudes en la hoja de cálculo de implementación (en el campo Reenviador DNS). Proporcione al menos dos servidores en este campo por razones de tolerancia a errores. Sin estos valores, se produce un error de implementación de Azure Stack Hub. Puede editar los valores del reenviador DNS con el [cmdlet **Set-AzSDnsForwarder**](#editing-dns-forwarder-ips) después de la implementación. 



### <a name="configure-conditional-dns-forwarding"></a>Configure el reenvío condicional de DNS

> [!IMPORTANT]
> Esto solo se aplica a una implementación de AD FS.

Para habilitar la resolución de nombres con la infraestructura DNS existente, configure el reenvío condicional.

Para agregar un reenviador condicional, debe utilizar el punto de conexión con privilegios.

Para este procedimiento, use un equipo de la red del centro de datos que pueda comunicarse con el punto de conexión con privilegios de Azure Stack Hub.

1. Abra una sesión de Windows PowerShell con privilegios elevados (ejecútela como administrador) y conéctese a la dirección IP del punto de conexión con privilegios. Use las credenciales para la autenticación de CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Después de conectarse al punto de conexión con privilegios, ejecute el siguiente comando de PowerShell. Sustituya los valores de ejemplo proporcionados por su nombre de dominio y direcciones IP de los servidores DNS que quiere usar.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-hub-dns-names-from-outside-azure-stack-hub"></a>Resolución de nombres DNS de Azure Stack Hub desde fuera de Azure Stack Hub
Los servidores autoritativos son los que contienen la información de zona DNS externa y las zonas creadas por el usuario. Realice la integración con estos servidores para habilitar la delegación de zona o el reenvío condicional para resolver nombres DNS de Azure Stack Hub desde fuera de Azure Stack Hub.

## <a name="get-dns-server-external-endpoint-information"></a>Obtener información del punto de conexión externo del servidor DNS

Para integrar la implementación de Azure Stack Hub con la infraestructura de DNS, necesitará la información siguiente:

- FQDN del servidor DNS
- Direcciones IP del servidor DNS

Los FQDN de los servidores DNS de Azure Stack Hub tienen el formato siguiente:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Con los valores de ejemplo, los FQDN de los servidores DNS son los siguientes:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Esta información también se crea al final de todas las implementaciones de Azure Stack Hub en un archivo denominado `AzureStackStampInformation.json`. Este archivo se encuentra en la carpeta `C:\CloudDeployment\logs` de la máquina virtual de implementación. Si no está seguro de qué valores se han usado para la implementación de Azure Stack Hub, puede obtener los valores desde aquí.

Si la máquina virtual de implementación ya no está disponible o no es accesible, puede obtener los valores si se conecta al punto de conexión con privilegios y ejecuta el cmdlet `Get-AzureStackStampInformation` de PowerShell. Para más información, consulte [Punto de conexión con privilegios](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack-hub"></a>Configuración del reenvío condicional a Azure Stack Hub

La manera más sencilla y segura de integrar Azure Stack Hub con la infraestructura de DNS es hacer un reenvío condicional de la zona desde el servidor que hospeda la zona principal. Se recomienda este enfoque si tiene el control directo de los servidores DNS que hospedan la zona principal del espacio de nombres DNS externo de Azure Stack Hub.

Si no está familiarizado con los reenvíos condicionales mediante DNS, consulte el siguiente artículo de TechNet: [Assign a Conditional Forwarder for a Domain Name](https://technet.microsoft.com/library/cc794735) (Asignación de reenviadores condicionales a un nombre de dominio) o la documentación específica de la solución DNS.

Si ha especificado que la zona DNS externa de Azure Stack Hub debe parecer un dominio secundario de su nombre de dominio corporativo, no se puede usar el reenvío condicional. Se debe configurar la delegación DNS.

Ejemplo:

- Nombre de dominio DNS corporativo: `contoso.com`
- Nombre de dominio DNS externo de Azure Stack Hub: `azurestack.contoso.com`

## <a name="editing-dns-forwarder-ips"></a>Edición de direcciones IP de reenviador DNS

Las direcciones IP del reenviador DNS se establecen durante la implementación de Azure Stack Hub. Sin embargo, si las direcciones IP del reenviador deben actualizarse por algún motivo, puede conectarse al punto de conexión con privilegios y ejecutar los cmdlets de PowerShell `Get-AzSDnsForwarder` y `Set-AzSDnsForwarder [[-IPAddress] <IPAddress[]>]` para modificar los valores. Para más información, consulte [Punto de conexión con privilegios](azure-stack-privileged-endpoint.md).

## <a name="delegating-the-external-dns-zone-to-azure-stack-hub"></a>Delegación de la zona DNS externa en Azure Stack Hub

Para que los nombres DNS puedan resolverse desde fuera de una implementación de Azure Stack Hub, debe configurar la delegación de DNS.

Cada registrador dispone de sus propias herramientas de administración de DNS para cambiar los registros de servidores de nombres de un dominio. En la página de administración de DNS del registrador, edite los registros NS y reemplace los registros NS de la zona por los de Azure Stack Hub.

La mayoría de los registradores DNS requieren que se proporcione un mínimo de dos servidores DNS para completar la delegación.

## <a name="next-steps"></a>Pasos siguientes

[Integración del firewall](azure-stack-firewall.md)
