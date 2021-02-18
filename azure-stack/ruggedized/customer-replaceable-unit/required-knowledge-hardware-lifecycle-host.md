---
title: Conocimientos necesarios para trabajar con el host de ciclo de vida de hardware
description: Obtenga información sobre los conocimientos necesarios para trabajar con el host de ciclo de vida de hardware.
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3d3e650e9b6b1b6c37e2f265aa5c049246600c2f
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487789"
---
# <a name="required-knowledge-for-working-with-the-hardware-lifecycle-host"></a>Conocimientos necesarios para trabajar con el host de ciclo de vida de hardware

Para completar los procedimientos de FRU, debe estar familiarizado con los siguientes conceptos y guías, y disponer de acceso a ellos.

## <a name="hardware-lifecycle-host"></a>Host de ciclo de vida de hardware

El host del ciclo de vida del hardware (HLH) es un servidor de administración físico situado en la parte superior del bastidor de Azure Stack Hub. Para acceder al host, puede conectarse a él mediante uno de estos tres métodos:

* Directo (carro de bloqueos)
* iDRAC (puerto de servicio)
* iDRAC (acceso por IP)

Si está dentro del centro de datos, puede conectarse directamente al servidor HLH mediante los puertos VGA y USB. Por ejemplo, mediante la conexión de un carro de bloqueos.

Si está dentro del centro de datos, conecte el portátil al puerto de servicio de iDRAC 9 mediante un cable micro USB. Para obtener más información, consulte Acceso a la interfaz de iDRAC mediante conexión USB directa.

Colabore con el cliente para conectarse al servidor HLH desde su red de administración y su estación de trabajo de administración a la IP del controlador iDRAC.

> [!NOTE]
> Solo las redes previamente agregadas a las ACL de conmutador pueden conectarse directamente a la interfaz iDRAC del servidor HLH.

## <a name="credentials"></a>Credenciales

Colabore con el cliente para obtener las credenciales para lo siguiente:

* HLH
* administrator
* cuenta iDRAC (opcional)

Una cuenta de Windows con todos los derechos de administrador.

Si no se conecta directamente al servidor mediante un carro de bloqueos, necesitará las credenciales de la cuenta de iDRAC para obtener acceso al KVM virtual.


