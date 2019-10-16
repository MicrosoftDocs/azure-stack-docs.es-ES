---
title: DNS en Azure Stack | Microsoft Docs
description: Obtenga información sobre DNS en Azure Stack y cómo crear y administrar zonas DNS.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 5600dd6537df35e703e0ac7a08ad4a61f976e489
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961479"
---
# <a name="use-dns-in-azure-stack"></a>Uso de DNS en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Azure Stack admite las siguientes características de Azure DNS:

* Resolución de nombres de host DNS.
* Cree y administre registros y zonas DNS con la API.

## <a name="support-for-dns-hostname-resolution"></a>Compatibilidad con la resolución de nombres de host DNS

Puede especificar una etiqueta de nombre de dominio DNS para recursos de IP pública. Azure Stack usa **domainnamelabel.location.cloudapp.azurestack.external** como nombre de etiqueta y lo asigna a la dirección IP pública en los servidores DNS administrados por Azure Stack.

Por ejemplo, si crea un recurso de IP pública con **Contoso** como etiqueta de nombre de dominio en la ubicación local de Azure Stack, el [nombre de dominio completo (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) **contoso.local.cloudapp.azurestack.external** se resolverá en la dirección IP pública del recurso. Puede usar este FQDN para crear un registro CNAME de dominio personalizado que apunte a la dirección IP pública en Azure Stack.

Para obtener más información sobre la resolución de nombres, consulte el artículo sobre la [resolución de DNS](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Cada etiqueta de nombre de dominio que cree debe ser única dentro de su ubicación de Azure Stack.

En la siguiente captura de pantalla se muestra el cuadro de diálogo **Crear dirección IP pública** para crear una dirección IP pública mediante el portal:

![Creación de una dirección IP pública](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Escenario de ejemplo

Tiene solicitudes de procesamiento de un equilibrador de carga desde una aplicación web. Detrás del equilibrador de carga, se encuentra un sitio web que se ejecuta en una o más máquinas virtuales. Puede tener acceso al sitio de web de carga equilibrada mediante un nombre DNS, en lugar de con una dirección IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-apis"></a>Creación y administración de registros y zonas DNS con las API

Puede crear y administrar registros y zonas DNS en Azure Stack.

Azure Stack proporciona un servicio DNS similar al de Azure mediante el uso de API que son coherentes con las API de Azure DNS.  Al hospedar dominios en DNS de Azure Stack, puede administrar los registros DNS con las mismas credenciales, API y herramientas. También puede usar la misma facturación y soporte técnico que los otros servicios de Azure.

La infraestructura de DNS de Azure Stack es más compacta que la de Azure. El tamaño y la ubicación de una implementación de Azure Stack afectará el ámbito, la escalabilidad y el rendimiento de DNS. Es decir, el rendimiento, la disponibilidad, la distribución global y la alta disponibilidad pueden variar de una implementación a otra.

## <a name="comparison-with-azure-dns"></a>Comparación con el DNS de Azure

El DNS en Azure Stack es similar al DNS en Azure, con algunas excepciones importantes:

* **No admite registros AAAA**: Azure Stack no admite registros AAAA porque no es compatible con las direcciones IPv6. Se trata de una diferencia clave entre el DNS de Azure y de Azure Stack.

* **No es multiinquilino**: el servicio DNS en Azure Stack no es multiinquilino. Los inquilinos no pueden crear la misma zona DNS. Solo la primera suscripción que intenta crear la zona lo consigue, mientras que se produce un error en el resto de solicitudes posteriores. Se trata de otra importante diferencia entre los DNS de Azure Stack y Azure.

* **Etiquetas, metadatos y etiquetas Etag**: Hay pequeñas diferencias en cómo Azure Stack manipula las etiquetas, los metadatos, las etiquetas ETag y los límites.

Para obtener más información sobre Azure DNS, consulte el artículo sobre [zonas y registros de DNS](/azure/dns/dns-zones-records).

### <a name="tags"></a>Etiquetas

El DNS de Azure Stack admite el uso de etiquetas de Azure Resource Manager en recursos de zona DNS. No admite etiquetas en conjuntos de registros DNS. Como alternativa, se admiten **metadatos** en conjuntos de registros de DNS, como se explica en la siguiente sección.

### <a name="metadata"></a>Metadatos

Como alternativa a las etiquetas de conjuntos de registros, el DNS de Azure Stack admite la anotación de conjuntos de registros mediante *metadatos*. De forma similar a las etiquetas, los metadatos permiten asociar pares nombre-valor a cada conjunto de registros. Los metadatos pueden ser útiles, por ejemplo, para registrar el propósito de cada conjunto de registros. A diferencia de las etiquetas, los metadatos no se pueden usar para proporcionar una vista filtrada de la factura de Azure ni se pueden especificar en una directiva de Azure Resource Manager.

### <a name="etags"></a>Etag

Supongamos que dos personas o dos procesos tratan de modificar un registro DNS al mismo tiempo. ¿Cuál gana? ¿Y el ganador sabe que ha sobrescrito cambios creados por otra persona?

El DNS de Azure Stack usa *etiquetas Etag* para administrar los cambios simultáneos realizados al mismo recurso de forma segura. Las etiquetas ETag son diferentes de las *etiquetas* de Azure Resource Manager. Cada recurso DNS (zona o conjunto de registros) tiene un valor de Etag asociado a él. Cuando se recupera un recurso, también se recupera su etiqueta Etag. Al actualizar un recurso, puede elegir devolver el valor de ETag para que el DNS de Azure Stack pueda comprobar que dicho valor del servidor coincide. Puesto que cada actualización a un recurso conlleva la regeneración de Etag, una incoherencia de Etag indica que se ha producido un cambio simultáneo. Los valores de ETag también se pueden usar al crear un nuevo recurso para asegurarse de que este no existe aún.

De forma predeterminada, los cmdlets de PowerShell para DNS de Azure Stack utilizan valores de etiquetas ETag para bloquear los cambios simultáneos a zonas y conjuntos de registros. Puede usar el modificador `-Overwrite` opcional para suprimir las comprobaciones de ETag. Sin estas, se sobrescribirá cualquier cambio simultáneo que se haya producido.

En el nivel de la API de REST de DNS de Azure Stack, los valores de ETag se especifican mediante los encabezados HTTP. Su comportamiento se describe en la siguiente tabla:

| Encabezado | Comportamiento|
|--------|---------|
| None   | PUT siempre se realiza correctamente (sin comprobaciones de ETag).|
| If-match| PUT solo se realiza correctamente si el recurso existe y ETag coincide.|
| If-match *| PUT solo se realiza correctamente si el recurso existe.|
| If-none-match *| PUT solo se realiza correctamente si el recurso no existe.|

### <a name="limits"></a>límites

Se aplican los límites predeterminados siguientes cuando se usa el DNS de Azure Stack:

| Resource| Límite predeterminado|
|---------|--------------|
| Zonas por suscripción| 100|
| Conjuntos de registros por zona| 5000|
| Registros por conjunto de registros| 20|

## <a name="next-steps"></a>Pasos siguientes

* [Presentación de iDNS para Azure Stack](azure-stack-understanding-dns.md)
