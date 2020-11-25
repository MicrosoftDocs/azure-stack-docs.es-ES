---
title: Introducción a las redes de Azure Stack Hub
description: Más información sobre las redes de Azure Stack Hub
author: mattbriggs
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 2cb8ede31a91ba05102c55591a4942f512bce0c8
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674479"
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