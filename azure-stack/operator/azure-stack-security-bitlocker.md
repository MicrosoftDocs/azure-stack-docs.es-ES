---
title: Cifrado de datos en reposo en Azure Stack
description: Obtenga información sobre cómo Azure Stack protege sus datos con cifrado en reposo.
services: azure-stack
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.topic: article
origin.date: 12/06/2018
ms.date: 01/14/2019
ms.author: v-jay
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 018b8f6cf4fc5d3cd380535fca71a038b7fd4208
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307065"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Cifrado de datos en reposo en Azure Stack

Azure Stack protege los datos de infraestructura y de usuario en el nivel del subsistema de almacenamiento mediante cifrado en reposo. El subsistema de almacenamiento de Azure Stack se cifra mediante BitLocker con un cifrado AES de 128 bits. Las claves de BitLocker se conservan de manera persistente en un almacén secreto interno.

El cifrado de datos en reposo es un requisito común para las principales normas de cumplimiento (por ejemplo, PCI-DSS, FedRAMP e HIPAA). Azure Stack le permite cumplir esos requisitos sin configuraciones ni tareas adicionales. Para obtener más información sobre cómo Azure Stack le ayuda a cumplir con las normas de cumplimiento, vea el [Portal de confianza de servicios de Microsoft](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> El cifrado de datos en reposo protege sus datos frente al acceso por usuarios que roben físicamente uno o más discos duros. El cifrado de datos en reposo no protege contra la interceptación de datos por la red (datos en tránsito), los datos usados actualmente (datos en memoria) o, de forma más general, datos filtrados mientras el sistema está en funcionamiento.

## <a name="retrieving-bitlocker-recovery-keys"></a>Recuperar las claves de recuperación de BitLocker

Las claves de BitLocker de Azure Stack para los datos en reposo se administran de forma interna. No necesita proporcionarlas para operaciones normales o durante el inicio del sistema. Pero puede que, en algunos escenarios de soporte técnico, se necesiten las claves de recuperación de BitLocker para reactivar el sistema.  

> [!WARNING]
> Obtenga las claves de recuperación de BitLocker y guárdelas en una ubicación segura fuera de Azure Stack. Si no dispone de las claves de recuperación durante algunos escenarios de soporte técnico, puede producirse una pérdida de datos y es posible que sea necesario realizar una restauración del sistema a partir de una imagen de copia de seguridad.

Para obtener las claves de recuperación de BitLocker, es necesario acceder al [punto de conexión con privilegios](azure-stack-privileged-endpoint.md) (PEP). Desde una sesión de PEP, ejecute el cmdlet Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Parámetros opcionales para el cmdlet *Get-AzsRecoveryKeys*:

| Parámetro | DESCRIPCIÓN | Type | Obligatorio |
|---------|---------|---------|---------|
|*raw* (sin formato) | devuelve datos sin procesar de la asignación entre la clave de recuperación, el nombre de equipo y los identificadores de contraseña de cada volumen cifrado  | Modificador | no (diseñado para escenarios de soporte técnico)|


## <a name="troubleshoot-issues"></a>Solución de problemas

En circunstancias extremas, una solicitud de desbloqueo de BitLocker podría producir errores y causar que un volumen específico no arranque. Según la disponibilidad de algunos de los componentes de la arquitectura, este error podría producir un tiempo de inactividad y la posible pérdida de datos en caso de que no disponga de las claves de recuperación de BitLocker.

> [!WARNING]
> Obtenga las claves de recuperación de BitLocker y guárdelas en una ubicación segura fuera de Azure Stack. Si no dispone de las claves de recuperación durante algunos escenarios de soporte técnico, puede producirse una pérdida de datos y es posible que sea necesario realizar una restauración del sistema a partir de una imagen de copia de seguridad.

Si cree que el sistema tiene problemas con BitLocker (por ejemplo, si Azure Stack no puede iniciarse), póngase en contacto con el soporte técnico. El servicio de soporte técnico necesita sus claves de recuperación de BitLocker. La mayoría de los problemas relacionados con BitLocker pueden solucionarse con una operación de FRU para ese volumen, host o VM específico. En el resto de los casos, puede realizarse un procedimiento de desbloqueo manual con las claves de recuperación de BitLocker. Si las claves de recuperación de BitLocker no están disponibles, la única opción es realizar una restauración a partir de una imagen de copia de seguridad. Dependiendo de cuándo se haya realizado la última copia de seguridad, puede que se produzca la pérdida de datos.

## <a name="next-steps"></a>Pasos siguientes

- [Más información acerca de la seguridad de Azure Stack](azure-stack-security-foundations.md)
- Para obtener más información sobre cómo BitLocker protege CSV, vea [Proteger volúmenes compartidos de clúster y redes de área de almacenamiento con BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).