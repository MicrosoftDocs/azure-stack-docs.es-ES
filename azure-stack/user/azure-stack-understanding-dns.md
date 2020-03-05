---
title: Uso de iDNS en Azure Stack Hub
description: Aprenda a usar las características y funcionalidades de iDNS en Azure Stack Hub.
author: Justinha
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: Justinha
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3209c3a51b491bcdf2779e95c58f41cb124cd3f3
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704869"
---
# <a name="use-idns-in-azure-stack-hub"></a>Uso de iDNS en Azure Stack Hub 

iDNS es una característica de redes de Azure Stack Hub que permite resolver nombres DNS externos (por ejemplo, https:\//www.bing.com). También permite registrar los nombres de redes virtuales internas. Así, puede resolver las máquinas virtuales (VM) de la misma red virtual por nombre en lugar de por dirección IP. Este enfoque elimina la necesidad de proporcionar las entradas de servidor DNS personalizadas. Para más información acerca de DNS, consulte la [Introducción a Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>¿Para qué sirve iDNS?

Con iDNS en Azure Stack Hub, dispone de las siguientes funcionalidades sin tener que especificar entradas del servidor DNS personalizadas:

- Servicios de resolución de nombres Compartir los servicios de resolución de nombres DNS compartidos para las cargas de trabajo del inquilino.
- Servicio DNS autoritativo para la resolución de nombres y el registro DNS en la red virtual del inquilino.
- Servicio DNS recursivo para la resolución de los nombres de Internet de las máquinas virtuales de los inquilinos. Los inquilinos ya no necesitan especificar entradas de DNS personalizadas para resolver nombres de Internet (por ejemplo, www\.bing.com).

Si lo desea, puede traer su propio sistema DNS y utilizar servidores DNS personalizados. Pero, al usar iDNS, puede resolver nombres DNS de Internet y conectarse a otras máquinas virtuales de la misma red virtual sin necesidad de crear entradas DNS personalizadas.

## <a name="what-doesnt-idns-do"></a>¿Qué es lo que no hace iDNS?

iDNS no permite crear un registro DNS para un nombre que se puede resolver desde fuera de la red virtual.

En Azure, tiene la opción de especificar una etiqueta de nombre DNS que se puede asociar con una dirección IP pública. Puede elegir la etiqueta (prefijo), pero Azure elige el sufijo, que depende de la región en la que crea la dirección IP pública.

![Ejemplo de una etiqueta de nombre DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Como muestra la imagen anterior, Azure creará un registro "A" en DNS para la etiqueta de nombre DNS especificada en la zona **westus.cloudapp.azure.com**. El prefijo y el sufijo se combinan para crear un [nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) que se puede resolver desde cualquier lugar de Internet público.

Azure Stack Hub solo admite iDNS para el registro de nombres interno, por lo que no puede realizar las siguientes operaciones:

- Crear un registro DNS en una zona DNS hospedada existente (por ejemplo, local.azurestack.external).
- Crear una zona DNS (como Contoso.com).
- Crear un registro en su propia zona DNS personalizada.
- Admitir la compra de nombres de dominio.

## <a name="demo-of-how-idns-works"></a>Demostración del funcionamiento de iDNS

Todos los nombres de host de las máquinas virtuales de las redes virtuales se almacenan como registros de recursos DNS en la misma zona, pero en su propio compartimiento único, que se define como un GUID que se correlaciona con el identificador de la red virtual en la infraestructura de SDN en la que se implementó la máquina virtual. Los nombres de dominio completos (FQDN) de las máquinas virtuales de los inquilinos se componen del nombre del equipo y de la cadena del sufijo DNS de la red virtual, en formato GUID.

<!--- what does compartment mean? Add a screenshot? can we clarify what we mean by host name and computer name. the description doesn't match the example in the table.--->
 
A continuación, se muestra de forma práctica y sencilla cómo funciona esto. Hemos creado tres máquinas virtuales en una red virtual y una cuarta en una red virtual independiente:

<!--- Is DNS Label the right term? If so, we should define it. The column lists FQDNs, afaik. Where does the domain suffix come from? --->
 
|máquina virtual    |Red virtual    |Dirección IP privada   |Dirección IP pública    | Etiqueta DNS                                |
|------|--------|-------------|-------------|------------------------------------------|
|VM-A1 |VNetA   | 10.0.0.5    |172.31.12.68 |VM-A1-Label.lnv1.cloudapp.azscss.external |
|VM-A2 |VNetA   | 10.0.0.6    |172.31.12.76 |VM-A2-Label.lnv1.cloudapp.azscss.external |
|VM-A3 |VNetA   | 10.0.0.7    |172.31.12.49 |VM-A3-Label.lnv1.cloudapp.azscss.external |
|VM-B1 |VNetB   | 10.0.0.4    |172.31.12.57 |VM-B1-Label.lnv1.cloudapp.azscss.external |
 
 
|VNet  |GUID                                 |Cadena de sufijo DNS                                                  |
|------|-------------------------------------|-------------------------------------------------------------------|
|VNetA |e71e1db5-0a38-460d-8539-705457a4cf75 |e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local|
|VNetB |e8a6e386-bc7a-43e1-a640-61591b5c76dd |e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local|
 
 
Puede realizar algunas pruebas de resolución de nombres para comprender mejor el funcionamiento de iDNS:

<!--- why Linux?--->

Desde VM-A1 (VM Linux): Buscando VM-A2. Puede ver que se ha agregado el sufijo DNS de VNetA y que el nombre se resuelve en la dirección IP privada:
 
```console
carlos@VM-A1:~$ nslookup VM-A2
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2.e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local
Address: 10.0.0.6
```
 
La búsqueda de VM-A2-Label sin proporcionar el FQDN produce un error, tal como cabría esperar:

```console 
carlos@VM-A1:~$ nslookup VM-A2-Label
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-A2-Label: SERVFAIL
```

Si especifica el nombre de dominio completo de la etiqueta DNS, el nombre se resuelve en la dirección IP pública:

```console
carlos@VM-A1:~$ nslookup VM-A2-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.76
```
 
Se produce un error al intentar resolver VM-B1 (que es de otra red virtual), ya que este registro no existe en esta zona.

```console
carlos@caalcobi-vm4:~$ nslookup VM-B1
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1: SERVFAIL
```

El uso del FQDN para VM-B1 no sirve de ayuda, ya que este registro proviene de otra zona.

```console 
carlos@VM-A1:~$ nslookup VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local: SERVFAIL
```
 
Si utiliza el FQDN para la etiqueta DNS, se resuelve correctamente:

``` 
carlos@VM-A1:~$ nslookup VM-B1-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-B1-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.57
```
 
Desde VM-A3 (máquina virtual Windows). Observe la diferencia entre respuestas autoritativas y no autoritativas.

Registros internos:

```console
C:\Users\carlos>nslookup
Default Server:  UnKnown
Address:  168.63.129.16
 
> VM-A2
Server:  UnKnown
Address:  168.63.129.16
 
Name:    VM-A2.e71e1db5-0a38-460d-8539-705457ª4cf75.internal.lnv1.azurestack.local
Address:  10.0.0.6
```

Registros externos:

```console
> VM-A2-Label.lnv1.cloudapp.azscss.external
Server:  UnKnown
Address:  168.63.129.16
 
Non-authoritative answer:
Name:    VM-A2-Label.lnv1.cloudapp.azscss.external
Address:  172.31.12.76
``` 
 
En resumen, de lo anterior se puede observar que:
 
*   Cada red virtual tiene su propia zona, que contiene los registros D de todas las direcciones IP privadas, que constan del nombre de la máquina virtual y del sufijo DNS de la red virtual (que es su GUID).
    *   \<nombreVM>.\<vnetGUID\>.internal.\<region>.\<stackinternalFQDN>
    *   Esto se realiza de forma automática
*   Si usa direcciones IP públicas, también puede crear etiquetas DNS para ellas. Se resuelven como cualquier otra dirección externa.
 
 
- Los servidores de iDNS son los servidores autoritativos de sus zonas DNS internas y también actúan como una resolución de nombres públicos cuando las máquinas virtuales de los inquilinos intentan conectarse a recursos externos. Si hay una consulta para un recurso externo, los servidores de iDNS reenvían la solicitud a los servidores DNS autoritativos para que la resuelvan.
 
Como puede ver en los resultados del laboratorio, tiene control sobre la dirección IP que se usa. Si usa el nombre de la máquina virtual, obtendrá la dirección IP privada, mientras que si usa la etiqueta DNS, obtendrá la dirección IP pública.

## <a name="next-steps"></a>Pasos siguientes

[Uso de DNS en Azure Stack Hub](azure-stack-dns.md)
