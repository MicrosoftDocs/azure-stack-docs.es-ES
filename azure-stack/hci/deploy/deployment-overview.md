---
title: Introducción a la implementación de Azure Stack HCI
description: Introducción sobre el proceso de implementación de Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/28/2020
ms.openlocfilehash: 61cd03f7c4b381a434b5f99175b57b99169cb058
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064470"
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

Antes de implementar el sistema operativo, determine si el hardware cumple los [requisitos del sistema](../concepts/system-requirements.md) para Azure Stack HCI. A continuación, [instale Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) para administrar el clúster de Azure Stack HCI.

### <a name="1-deploy-azure-stack-hci"></a>1. Implementación de Azure Stack HCI

[Descargue Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) e implemente este [sistema operativo](operating-system.md) en cada servidor que desee incluir en el clúster. Si ha adquirido el hardware de una solución de sistema integrado de Azure Stack HCI en el [catálogo de Azure Stack HCI](https://azure.microsoft.com/en-us/products/azure-stack/hci/catalog/) de su asociado de hardware de Microsoft preferido, este sistema operativo vendrá preinstalado. En ese caso, puede omitir este paso y continuar en el paso 2.

### <a name="2-create-the-cluster"></a>2. Creación del clúster

Cree un clúster de conmutación por error mediante [Windows Admin Center](create-cluster.md) o [PowerShell](create-cluster-powershell.md). En el caso de la recuperación ante desastres y la continuidad empresarial nativa, puede implementar un [clúster extendido](../concepts/stretched-clusters.md) que abarque dos sitios geográficamente independientes.

### <a name="3-set-up-a-cluster-witness"></a>3. Configuración de un testigo del clúster

La [configuración de un recurso de testigo](witness.md) es obligatoria para todos los clústeres. Los clústeres de dos nodos necesitan un testigo para que los servidores que se queden sin conexión no provoquen que el otro nodo deje de estar disponible. Los clústeres de tres y más nodos necesitan un testigo para poder soportar dos servidores con errores o sin conexión. 

### <a name="4-register-with-azure"></a>4. Registrarse en Azure

Azure Stack HCI requiere una conexión a Azure. Para conectar el clúster a Azure, consulte [Registro de Azure Stack HCI en Azure](register-with-azure.md). Una vez registrado, el clúster se conecta automáticamente en segundo plano.

### <a name="5-validate-the-cluster"></a>5. Validación del clúster

Después de crear y registrar el clúster, [ejecute pruebas de validación de clúster](validate.md) para detectar problemas de hardware o configuración antes de que el clúster pase a producción.

### <a name="6-deploy-storage"></a>6. Implementación del almacenamiento

[Cree volúmenes](../manage/create-volumes.md) en un clúster de un solo sitio, o [cree volúmenes y configure la replicación en un clúster extendido](../manage/create-stretched-volumes.md).

### <a name="7-deploy-workloads"></a>7. Implementación de cargas de trabajo

Ahora está listo para [crear máquinas virtuales](../manage/vm.md) e implementar cargas de trabajo en Azure Stack HCI mediante Windows Admin Center.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a implementar el sistema operativo Azure Stack HCI.

> [!div class="nextstepaction"]
> [Implementación del sistema operativo Azure Stack HCI](operating-system.md)
