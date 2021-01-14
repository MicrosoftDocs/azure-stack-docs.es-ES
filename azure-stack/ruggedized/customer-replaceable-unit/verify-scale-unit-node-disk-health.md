---
title: Comprobación del estado del disco del nodo de unidad de escalado
description: Obtenga información sobre cómo comprobar el estado del disco del nodo de unidad de escalado.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 7928ab8ba09ed834f00c05e6c606e993fc2f3ebe
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974173"
---
# <a name="verifying-scale-unit-node-disk-health"></a>Comprobación del estado del disco del nodo de unidad de escalado

1.  Conéctese al punto de conexión con privilegios (PEP).

    1.  Consulte Acceso a la estación de trabajo de acceso con privilegios y al punto de conexión con privilegios para obtener instrucciones sobre cómo conectarse al PEP.

    1.  Una vez conectado, escriba la sesión PEP `Enter-PSSession -Session $pep`.

2.  Obtenga el estado del disco virtual.

    1.  Ejecute `Get-VirtualDisk -cimsession "S-Cluster"` para comprobar el estado del disco virtual.

        Si el sistema no devuelve para **OperationalStatus** el valor **OK** (Correcto) y para **HealthStatus** el valor **Healthy** (Correcto), espere unos minutos y vuelva a ejecutar el comando.
        
        ![Captura de pantalla en la que se muestra Windows PowerShell con las columnas "OperationsStatus" y "HealthStatus" resaltadas.](media/image-57.png)
        
    1.  Ejecute `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` para comprobar que todos los trabajos de almacenamiento en ejecución han finalizado.
    
    1.  Compruebe que no se devuelve ningún resultado. Si todavía hay trabajos en ejecución, tal como se muestra en **JobState**, o todos los trabajos están marcados como completados, espere otros 10 minutos y vuelva a ejecutar el mismo comando. El estado final debe ser que no se muestre ningún trabajo.
    
    1.  Si es necesario, puede encontrar más pasos de comprobación del estado de almacenamiento en [Comprobación del estado de reparación del disco virtual mediante PowerShell de Azure Stack Hub](../../operator/azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
