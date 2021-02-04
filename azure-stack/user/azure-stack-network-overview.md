---
title: Introducción a las redes de Azure Stack Hub
description: Más información sobre las redes de Azure Stack Hub
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: ca8c93e983290e925d3af38bef0799d11c4a745a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247615"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Introducción a las redes de Azure Stack Hub

Azure Stack Hub proporciona diferentes tipos de funcionalidades de red que se pueden usarse conjuntamente o por separado:

- **Conectividad entre recursos de Azure Stack Hub**  
    Conecte juntos los recursos de Azure en una red virtual privada y segura en la nube.
- **Conectividad de Internet**  
    Comunique a y desde los recursos de Azure Stack Hub a través de Internet.
- **Conectividad local**  
    Conecte una red local a los recursos de Azure Stack Hub a través de una red privada virtual (VPN), a través de Internet o a través de una conexión dedicada a Azure Stack Hub. 
    > [!IMPORTANT]
    > Debe crear una conexión de IP pública o VPN para tener acceso a los recursos locales.
- **Equilibrio de carga y dirección del tráfico**  
    Equilibre la carga del tráfico en los servidores de la misma ubicación y dirija el tráfico a los servidores de ubicaciones diferentes.
- **Seguridad**  
    Filtre el tráfico de red entre subredes de la red o máquinas virtuales individuales.
- **Enrutamiento**  
    Utilice el enrutamiento predeterminado o controle completamente el enrutamiento entre los recursos locales y su instancia de Azure Stack Hub.
- **Facilidad de uso**  
    Supervise y administre los recursos de red de Azure Stack Hub.
- **Herramientas de implementación y configuración**  
    Utilice un portal basado en web o herramientas de línea de comandos multiplataforma para implementar y configurar recursos de red.


## <a name="next-steps"></a>Pasos siguientes

* [Consideraciones sobre las redes de Azure Stack Hub](azure-stack-network-differences.md)