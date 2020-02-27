---
title: Tráfico de red de implementación
titleSuffix: Azure Stack Hub
description: Obtenga información sobre el flujo de tráfico de red durante la implementación de Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 12/05/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 12/05/2019
ms.openlocfilehash: 958cdb4aeaa7ab2244632b99d5eebdf7eb8368a8
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77508062"
---
# <a name="deployment-network-traffic"></a>Tráfico de red de implementación

Comprender cómo funciona el tráfico durante la implementación de Azure Stack Hub ayuda a que esta se realice correctamente. Este artículo le guiará a través del flujo de tráfico de red durante el proceso de implementación para que sepa qué esperar.

En esta ilustración se muestran todos los componentes y las conexiones que intervienen en el proceso de implementación:

![Topología de red de implementación de Azure Stack Hub](media/deployment-networking/figure1.png)

> [!NOTE]
> En este artículo se describen los requisitos para una implementación conectada. Para más información sobre otros métodos de implementación, consulte [Modelos de conexión de la implementación de Azure Stack Hub](azure-stack-connection-models.md).

## <a name="the-deployment-vm"></a>La máquina virtual de implementación

La solución de Azure Stack Hub incluye un grupo de servidores que se usa para hospedar componentes de Azure Stack Hub y un servidor adicional llamado Host de ciclo de vida de hardware (HLH). Este servidor se usa para implementar y administrar el ciclo de vida de la solución y hospeda la máquina virtual de implementación (DVM) durante la implementación.

Los proveedores de la solución Azure Stack Hub pueden aprovisionar máquinas virtuales de administración adicionales. Confirme con el proveedor de la solución antes de realizar cambios en las máquinas virtuales de administración de un proveedor de soluciones.

## <a name="deployment-requirements"></a>Requisitos de implementación

Antes de iniciar la implementación, hay algunos requisitos mínimos que puede validar su OEM para asegurarse de que la operación se realiza correctamente:

- [Certificados](azure-stack-pki-certs.md).
- [Suscripción de Azure](azure-stack-validate-registration.md). Puede que tenga que comprobar la suscripción.
- Acceso a Internet.
- DNS.
- NTP.

> [!NOTE]
> Este artículo se centra en los tres últimos requisitos. Para más información sobre los dos primeros, consulte los vínculos anteriores.

## <a name="about-deployment-network-traffic"></a>Acerca del tráfico de red de implementación

La DVM está configurada con una dirección IP de la red BMC y requiere acceso de red a Internet. Aunque no todos los componentes de la red BMC requieren enrutamiento externo o acceso a Internet, algunos componentes específicos del OEM que usan direcciones IP de esta red podrían también necesitarlo.

Durante la implementación, la DVM se autentica en Azure Active Directory (Azure AD) mediante una cuenta de Azure desde su suscripción. Para ello, la DVM necesita acceso a través de Internet a una lista de [direcciones URL y puertos específicos](azure-stack-integrate-endpoints.md). La DVM usará un servidor DNS para reenviar las solicitudes de DNS que realizan los componentes internos a las direcciones URL externas. El DNS interno reenvía estas solicitudes a la dirección del reenviador DNS que se proporciona al OEM antes de la implementación. Lo mismo puede decirse del servidor NTP: se necesita un servidor horario confiable para mantener la coherencia y la sincronización de la hora en todos los componentes de Azure Stack Hub.

El acceso a Internet que requiere la DVM durante la implementación es solo saliente; no se realizan llamadas entrantes durante este proceso. Tenga en cuenta que esta máquina usa su IP como origen y que Azure Stack Hub no admite configuraciones de proxy. Por lo tanto, si es necesario, debe proporcionar un proxy o NAT transparentes para el acceso a Internet. Durante la implementación, algunos componentes internos iniciarán el acceso a Internet a través de la red externa mediante VIP públicas. Una vez finalizada la implementación, toda la comunicación entre Azure y Azure Stack Hub se realiza mediante la red externa con direcciones IP virtuales públicas.

Las configuraciones de red en los modificadores de Azure Stack Hub contienen listas de control de acceso (ACL) que restringen el tráfico entre determinados orígenes y destinos de red. La DVM es el único componente con acceso sin restricciones; incluso el HLH está restringido. Puede preguntar a los OEM sobre las opciones de personalización para facilitar la administración y el acceso desde sus redes. Debido a estas ACL, es importante evitar cambiar las direcciones de servidor DNS y NTP durante la implementación. Si lo hace, deberá volver a configurar todos los modificadores de la solución.

Después de finalizar la implementación, los componentes del sistema seguirán usando las direcciones de servidor DNS y NTP proporcionadas a través de SDN mediante la red externa. Por ejemplo, si comprueba las solicitudes DNS una vez finalizada la implementación, el origen cambiará de la dirección IP de DVM a una VIP pública.

## <a name="next-steps"></a>Pasos siguientes

[Validación del registro de Azure](azure-stack-validate-registration.md)
