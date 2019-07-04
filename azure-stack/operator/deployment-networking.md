---
title: Tráfico de red de implementación de Azure Stack | Microsoft Docs
description: En este artículo se describe lo que puede esperar acerca de los procesos de red de implementación de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 59858d5538552fb04ddf41de0ad59cf77dcd7783
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131066"
---
# <a name="about-deployment-network-traffic"></a>Acerca del tráfico de red de implementación
Comprender cómo fluye el tráfico de red durante la implementación de Azure Stack es fundamental para garantizar una correcta implementación. Este artículo le guía por el tráfico de red esperado durante el proceso de implementación para ayudarle a comprender lo que puede anticipar.

En esta ilustración se muestran todos los componentes y las conexiones que intervienen en el proceso de implementación:

![Topología de red de implementación de Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> En este artículo se describen los requisitos para una implementación conectada; para aprender sobre otros métodos de implementación, consulte [Modelos de conexión de implementación de Azure Stack](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>La máquina virtual de implementación
La solución de Azure Stack incluye un grupo de servidores que se usan para hospedar componentes de Azure Stack, y un servidor adicional llamado Host de ciclo de vida de hardware (HLH). Este servidor se usa para implementar y administrar el ciclo de vida de la solución y hospeda la máquina virtual de implementación (DVM) durante la implementación.

## <a name="deployment-requirements"></a>Requisitos de implementación
Antes de iniciar la implementación, hay algunos requisitos mínimos que puede validar su OEM para asegurarse de que la operación se realiza correctamente. Comprender estos requisitos le ayudará a preparar el entorno y a asegurarse de que la validación se realiza correctamente. Los requisitos son:

-   [Certificates](azure-stack-pki-certs.md)
-   [Suscripción de Azure](https://azure.microsoft.com/free/?b=17.06)
-   Acceso a Internet
-   DNS
-   NTP

> [!NOTE]
> Este artículo se centra en los tres últimos requisitos. Para más información sobre los dos primeros, consulte los vínculos anteriores.

## <a name="deployment-network-traffic"></a>Tráfico de red de implementación
La DVM está configurada con una dirección IP de la red BMC y requiere acceso de red a Internet. Aunque no todos los componentes de la red BMC requieren enrutamiento externo o acceso a Internet, algunos componentes específicos del OEM que usan direcciones IP de esta red podrían también necesitarlo.

Durante la implementación, la DVM se autentica en Azure Active Directory (Azure AD) mediante una cuenta de Azure desde su suscripción. Para ello, la DVM necesita acceso a través de Internet a una lista de direcciones URL y puertos específicos. Puede encontrar la lista completa en la documentación [Publicación de puntos de conexión](azure-stack-integrate-endpoints.md). La DVM usará un servidor DNS para reenviar las solicitudes de DNS que realizan los componentes internos a las direcciones URL externas. El DNS interno reenvía estas solicitudes a la dirección del reenviador DNS que se proporciona al OEM antes de la implementación. Lo mismo puede decirse del servidor NTP: se necesita un servidor horario confiable para mantener la coherencia y la sincronización de la hora en todos los componentes de Azure Stack.

El acceso a Internet que requiere la DVM durante la implementación es solo saliente; no se realizan llamadas entrantes durante este proceso. Tenga en cuenta que esta máquina usa su IP como origen y que Azure Stack no admite configuraciones de proxy. Por lo tanto, si es necesario, debe proporcionar un proxy o NAT transparentes para el acceso a Internet. Durante la implementación, algunos componentes internos iniciarán el acceso a internet a través de la red externa mediante VIP públicas. Una vez finalizada la implementación, toda la comunicación entre Azure y Azure Stack se realiza mediante la red externa usando direcciones IP virtuales públicas.

Las configuraciones de red en los modificadores de Azure Stack contienen listas de control de acceso (ACL) que restringen el tráfico entre determinados orígenes y destinos de red. La DVM es el único componente con acceso sin restricciones; incluso el HLH es muy restrictivo. Puede preguntar a los OEM sobre las opciones de personalización para facilitar la administración y el acceso desde sus redes. Debido a estas ACL, es importante evitar cambiar las direcciones de servidor DNS y NTP durante la implementación. Si lo hace, deberá volver a configurar todos los modificadores de la solución.

Después de finalizar la implementación, los componentes del sistema seguirán usando directamente las direcciones de servidor DNS y NTP proporcionadas. Por ejemplo, si comprueba las solicitudes DNS una vez finalizada la implementación, el origen cambiará de la dirección IP de DVM a una dirección del intervalo de la red externa.

Después de finalizar la implementación, los componentes del sistema seguirán usando las direcciones de servidor DNS y NTP proporcionadas a través de SDN mediante la red externa. Por ejemplo, si comprueba las solicitudes DNS una vez finalizada la implementación, el origen cambiará de la dirección IP de DVM a una VIP pública.

## <a name="next-steps"></a>Pasos siguientes
[Validación del registro de Azure](azure-stack-validate-registration.md)
