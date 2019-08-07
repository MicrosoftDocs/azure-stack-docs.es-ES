---
title: Uso de iDNS en Azure Stack | Microsoft Docs
description: Obtenga información para usar las características y capacidades de iDNS en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 6dda8562e47f17c97da5e0597a2ed88865bc6425
ms.sourcegitcommit: 82d09bbae3e5398d2fce7e2f998dfebff018716c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497855"
---
# <a name="use-idns-in-azure-stack"></a>Uso de iDNS en Azure Stack 

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

iDNS es una característica de redes de Azure Stack que permite resolver nombres DNS externos (por ejemplo, https:\//www.bing.com). También permite registrar los nombres de redes virtuales internas. Así, puede resolver las máquinas virtuales (VM) de la misma red virtual por nombre en lugar de por dirección IP. Este enfoque elimina la necesidad de proporcionar las entradas de servidor DNS personalizadas. Para más información acerca de DNS, consulte la [Introducción a Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>¿Para qué sirve iDNS?

Con iDNS en Azure Stack, dispone de las siguientes funcionalidades sin tener que especificar entradas del servidor DNS personalizadas:

- Servicios de resolución de nombres Compartir los servicios de resolución de nombres DNS compartidos para las cargas de trabajo del inquilino.
- Servicio DNS autoritativo para la resolución de nombres y el registro DNS en la red virtual del inquilino.
- Servicio DNS recursivo para la resolución de los nombres de Internet de las máquinas virtuales de los inquilinos. Los inquilinos ya no necesitan especificar entradas de DNS personalizadas para resolver nombres de Internet (por ejemplo, www\.bing.com).

Si lo desea, puede traer su propio sistema DNS y utilizar servidores DNS personalizados. Pero, al usar iDNS, puede resolver nombres DNS de Internet y conectarse a otras máquinas virtuales de la misma red virtual sin necesidad de crear entradas DNS personalizadas.

## <a name="what-doesnt-idns-do"></a>¿Qué es lo que no hace iDNS?

iDNS no permite crear un registro DNS para un nombre que se puede resolver desde fuera de la red virtual.

En Azure, tiene la opción de especificar una etiqueta de nombre DNS que se puede asociar con una dirección IP pública. Puede elegir la etiqueta (prefijo), pero Azure elige el sufijo, que depende de la región en la que crea la dirección IP pública.

![Ejemplo de una etiqueta de nombre DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Como muestra la imagen anterior, Azure creará un registro "A" en DNS para la etiqueta de nombre DNS especificada en la zona **westus.cloudapp.azure.com**. El prefijo y el sufijo se combinan para crear un [nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) que se puede resolver desde cualquier lugar de Internet público.

Azure Stack solo admite iDNS para el registro de nombres interno, por lo que no puede hacer lo siguiente:

- Crear un registro DNS en una zona DNS hospedada existente (por ejemplo, local.azurestack.external).
- Crear una zona DNS (como Contoso.com).
- Crear un registro en su propia zona DNS personalizada.
- Admitir la compra de nombres de dominio.

## <a name="next-steps"></a>Pasos siguientes

[Uso de DNS en Azure Stack](azure-stack-dns.md)
