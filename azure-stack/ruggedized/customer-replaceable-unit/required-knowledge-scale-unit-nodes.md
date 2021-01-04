---
title: Conocimientos necesarios para trabajar con nodos de unidad de escalado en un dispositivo en la nube táctico
description: Obtenga información sobre los conocimientos necesarios para trabajar con nodos de unidad de escalado en un dispositivo en la nube táctico.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f108559f191abd7a4d936f9f187384d150df8d62
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97393142"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-a-tactical-cloud-appliance"></a>Conocimientos necesarios para trabajar con nodos de unidad de escalado en un dispositivo en la nube táctico

Para completar los procedimientos de FRU, debe estar familiarizado con los siguientes conceptos, guías y sitios web, y disponer de acceso a ellos.

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>Estación de trabajo de acceso con privilegios y punto de conexión con privilegios

La estación de trabajo de acceso con privilegios (PAW) es una estación de trabajo dedicada protegida de Internet y vectores de amenaza externos. Puede residir en el host de ciclo de vida de hardware (HLH) o externamente en la red del cliente con acceso enrutable a los nodos de unidad de escalado de Azure Stack Hub.

Para acceder a la PAW, debe iniciar sesión con Escritorio remoto. Obtenga del cliente las credenciales y la dirección IP.

Desde esta máquina, también puede acceder al punto de conexión con privilegios (PEP).
Para más información acerca de la estación de trabajo de acceso con privilegios, así como sobre el PEP, consulte Acceso a la estación de trabajo de acceso con privilegios y al punto de conexión con privilegios.

## <a name="azure-stack-hub-administrator-portal"></a>Portal del administrador de Azure Stack Hub

Obtenga del cliente las credenciales y la dirección URL del portal del administrador.
Para más información, consulte [Uso del portal de administración](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals)
[de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals).

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Manual de instalación y servicio de EMC PowerEdge R640 de Dell

Para obtener información detallada sobre cómo reemplazar físicamente el hardware pertinente, consulte los procedimientos de instalación y retirada de los componentes del sistema PowerEdge R640 en el [Manual de instalación y servicio de EMC PowerEdge R640 de Dell](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Vaya a la sección [Instalación y retirada de](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us)
[componentes del sistema](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us).

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Manual de servicio del dispositivo en la nube táctico de Microsoft Azure Stack Hub

El manual de servicio del dispositivo en la nube táctico de Microsoft Azure Stack Hub contiene instrucciones para retirar servidores de nodo de unidad de escalado de los pods tácticos Tracewell.

## <a name="dell-emc-poweredge-idrac"></a>iDRAC de EMC PowerEdge de Dell

Debe entender cómo usar y navegar por la interfaz web de iDRAC del servidor EMC PowerEdge de Dell. Para más información sobre el uso de iDRAC, consulte la [Guía del usuario de Integrated Dell Remote Access Controller 9](https://www.dell.com/support/manuals/us/en/04/poweredge-r840/idrac9_4.00.00.00_ug_new/overview-of-idrac?guid=guid-a03c2558-4f39-40c8-88b8-38835d0e9003).

