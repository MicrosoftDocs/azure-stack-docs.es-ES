---
title: Comprobación del estado del disco del nodo de unidad de escalado
description: Obtenga información sobre cómo comprobar el estado del disco del nodo de unidad de escalado.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: bebff3ff8970a43d0893f057a94ec3dfbf0a7fc9
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392843"
---
# <a name="verifying-scale-unit-node-disk-health"></a>Comprobación del estado del disco del nodo de unidad de escalado

1.  Conéctese al punto de conexión con privilegios (PEP).

    1.  Consulte Acceso a la estación de trabajo de acceso con privilegios y al punto de conexión con privilegios para obtener instrucciones sobre cómo conectarse al PEP.

    1.  Una vez conectado, escriba la sesión PEP `Enter-PSSession -Session $pep`.

2.  Obtenga el estado del disco virtual.

    1.  Ejecute `Get-VirtualDisk -cimsession "S-Cluster"` para comprobar el estado del disco virtual.

        Si el sistema no devuelve para **OperationalStatus** el valor **OK** (Correcto) y para **HealthStatus** el valor **Healthy** (Correcto), espere unos minutos y vuelva a ejecutar el comando.
        
        ![](media/image-57.png)
        
    1.  Ejecute `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` para comprobar que todos los trabajos de almacenamiento en ejecución han finalizado.
    
    1.  Compruebe que no se devuelve ningún resultado. Si todavía hay trabajos en ejecución, tal como se muestra en **JobState**, o todos los trabajos están marcados como completados, espere otros 10 minutos y vuelva a ejecutar el mismo comando. El estado final debe ser que no se muestre ningún trabajo.
    
    1.  Si es necesario, puede encontrar más pasos de comprobación del estado de almacenamiento en [Comprobación del estado de reparación del disco virtual mediante PowerShell de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-replace-disk?view=azs-2002&check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
