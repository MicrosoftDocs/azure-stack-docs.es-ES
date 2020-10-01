---
title: Introducción a la implementación de Azure Stack HCI
description: Introducción sobre el proceso de implementación de Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/09/2020
ms.openlocfilehash: 34a93a65d45861c7c7ff1727347cc95465968151
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572524"
---
# <a name="what-is-the-deployment-process-for-azure-stack-hci"></a>¿Cuál es el proceso de implementación de Azure Stack HCI?

> Se aplica a: Azure Stack HCI, versión 20H2

En este tema se proporciona una introducción paso a paso de alto nivel del proceso de implementación de Azure Stack HCI, con vínculos a información más detallada.

## <a name="plan"></a>Plan

Antes de implementar, planee cuidadosamente el almacenamiento, las redes y los requisitos para la agrupación en clústeres multisitio (si existen).

### <a name="plan-storage"></a>Planear el almacenamiento

Azure Stack HCI usa los servidores estándar del sector con unidades conectadas localmente para crear almacenamiento definido por software con una alta disponibilidad y escalabilidad. Para satisfacer los requisitos de rendimiento y capacidad, [elija las unidades](../concepts/choose-drives.md) y [planee los volúmenes](../concepts/plan-volumes.md) de manera cuidadosa.

### <a name="plan-networking"></a>Planeamiento de redes

Tome nota de los nombres de servidor, los nombres de dominio, los protocolos y versiones de RDMA y el identificador de VLAN de la implementación.

### <a name="plan-stretched-clusters"></a>Planeamiento de clústeres extendidos

Si la implementación de Azure Stack HCI se va a extender en varios sitios, determine cuántos servidores necesitará en cada sitio y si la configuración del clúster será activa/pasiva o activa/activa. Para más información, consulte [Acerca de los clústeres extendidos](../concepts/stretched-clusters.md).

## <a name="deploy"></a>Implementación

### <a name="1-before-you-begin"></a>1. Antes de empezar

Antes de empezar, [determine si el hardware cumple los requisitos básicos y recopile la información necesaria](before-you-start.md) para implementar Azure Stack HCI. A continuación, [instale Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) para administrar el clúster de Azure Stack HCI.

### <a name="2-deploy-azure-stack-hci"></a>2. Implementación de Azure Stack HCI

[Descargue Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) e implemente este [sistema operativo](operating-system.md) en cada servidor que desee incluir en el clúster.

### <a name="3-create-the-cluster"></a>3. Creación del clúster

Cree un clúster de conmutación por error mediante [Windows Admin Center](create-cluster.md) o [PowerShell](create-cluster-powershell.md). En el caso de la recuperación ante desastres y la continuidad empresarial nativa, puede implementar un [clúster extendido](../concepts/stretched-clusters.md) que abarque dos sitios geográficamente independientes.

### <a name="4-set-up-a-cluster-witness"></a>4. Configuración de un testigo del clúster

La [configuración de un recurso de testigo](witness.md) es obligatoria para todos los clústeres. Los clústeres de dos nodos necesitan un testigo para que los servidores que se queden sin conexión no provoquen que el otro nodo deje de estar disponible. Los clústeres de tres y más nodos necesitan un testigo para poder soportar dos servidores con errores o sin conexión. 

### <a name="5-register-with-azure"></a>5. Registrarse en Azure

Azure Stack HCI requiere una conexión a Azure. Para conectar el clúster a Azure, consulte [Registro de Azure Stack HCI en Azure](register-with-azure.md). Una vez registrado, el clúster se conecta automáticamente en segundo plano.

### <a name="6-validate-the-cluster"></a>6. Validación del clúster

Después de crear y registrar el clúster, [ejecute pruebas de validación de clúster](validate.md) para detectar problemas de hardware o configuración antes de que el clúster pase a producción.

### <a name="7-deploy-storage"></a>7. Implementación del almacenamiento

[Cree volúmenes](../manage/create-volumes.md) en un clúster de un solo sitio, o [cree volúmenes y configure la replicación en un clúster extendido](../manage/create-stretched-volumes.md).

### <a name="8-deploy-workloads"></a>8. Implementación de cargas de trabajo

Ahora está listo para [crear máquinas virtuales](../manage/vm.md) e implementar cargas de trabajo en Azure Stack HCI mediante Windows Admin Center.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre lo que necesita hacer antes de implementar Azure Stack HCI.

> [!div class="nextstepaction"]
> [Antes de empezar](before-you-start.md)
