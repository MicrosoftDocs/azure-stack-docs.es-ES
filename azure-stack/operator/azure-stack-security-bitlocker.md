---
title: Cifrado de datos en reposo
titleSuffix: Azure Stack Hub
description: Obtenga información sobre cómo Azure Stack Hub protege sus datos con el cifrado en reposo.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 60ad202ef08d7afd72591640fa1d4d8beef0c476
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488015"
---
# <a name="data-at-rest-encryption-in-azure-stack-hub"></a>Cifrado de datos en reposo en Azure Stack Hub

Azure Stack Hub protege los datos de infraestructura y de usuario en el nivel del subsistema de almacenamiento mediante el cifrado en reposo. El subsistema de almacenamiento de Azure Stack Hub se cifra mediante BitLocker con un cifrado AES de 128 bits. Las claves de BitLocker se conservan de manera persistente en un almacén secreto interno.

El cifrado de datos en reposo es un requisito común para las principales normas de cumplimiento (por ejemplo, PCI-DSS, FedRAMP e HIPAA). Azure Stack Hub le permite cumplir esos requisitos sin configuraciones ni tareas adicionales. Para más información sobre cómo Azure Stack Hub le ayuda a respetar las normas de cumplimiento, consulte el [Portal de confianza de servicios de Microsoft](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> El cifrado de datos en reposo protege sus datos frente al acceso por usuarios que roben físicamente uno o más discos duros. El cifrado de datos en reposo no protege contra la interceptación de datos en la red (datos en tránsito), datos que se están usando (datos en memoria) o, de forma más general, datos filtrados mientras el sistema está en funcionamiento.

## <a name="retrieving-bitlocker-recovery-keys"></a>Recuperar las claves de recuperación de BitLocker

Las claves de BitLocker de Azure Stack Hub para los datos en reposo se administran de forma interna. No necesita proporcionarlas para operaciones normales o durante el inicio del sistema. Pero puede que, en algunos escenarios de soporte técnico, se necesiten las claves de recuperación de BitLocker para reactivar el sistema.  

> [!WARNING]
> Obtenga las claves de recuperación de BitLocker y guárdelas en una ubicación segura fuera de Azure Stack Hub. Si no dispone de las claves de recuperación durante algunos escenarios de soporte técnico, puede producirse una pérdida de datos y es posible que sea necesario realizar una restauración del sistema a partir de una imagen de copia de seguridad.

Para obtener las claves de recuperación de BitLocker, es necesario acceder al [punto de conexión con privilegios](azure-stack-privileged-endpoint.md) (PEP). Desde una sesión de PEP, ejecute el cmdlet Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Parámetros opcionales para el cmdlet *Get-AzsRecoveryKeys*:

| Parámetro | Descripción | Tipo | Obligatorio |
|---------|---------|---------|---------|
|*raw* (sin formato) | Devuelve datos sin procesar de la asignación entre la clave de recuperación, el nombre de equipo y los identificadores de contraseña de cada volumen cifrado.  | Switch | No (diseñado para escenarios de soporte técnico).|

## <a name="troubleshoot-issues"></a>Solución de problemas

En circunstancias extremas, una solicitud de desbloqueo de BitLocker podría producir errores y causar que un volumen específico no arranque. Según la disponibilidad de algunos de los componentes de la arquitectura, este error podría producir un tiempo de inactividad y la posible pérdida de datos en caso de que no disponga de las claves de recuperación de BitLocker.

> [!WARNING]
> Obtenga las claves de recuperación de BitLocker y guárdelas en una ubicación segura fuera de Azure Stack Hub. Si no dispone de las claves de recuperación durante algunos escenarios de soporte técnico, puede producirse una pérdida de datos y es posible que sea necesario realizar una restauración del sistema a partir de una imagen de copia de seguridad.

Si cree que el sistema tiene problemas con BitLocker (por ejemplo, si Azure Stack Hub no puede iniciarse), póngase en contacto con el soporte técnico. El servicio de soporte técnico necesita sus claves de recuperación de BitLocker. La mayoría de los problemas relacionados con BitLocker pueden solucionarse con una operación de FRU para ese volumen, host o VM específico. En el resto de los casos, puede realizarse un procedimiento de desbloqueo manual con las claves de recuperación de BitLocker. Si las claves de recuperación de BitLocker no están disponibles, la única opción es realizar una restauración a partir de una imagen de copia de seguridad. Dependiendo de cuándo se haya realizado la última copia de seguridad, puede que se produzca una pérdida de datos.

## <a name="next-steps"></a>Pasos siguientes

- [Más información acerca de la seguridad de Azure Stack Hub](azure-stack-security-foundations.md).
- Para obtener más información sobre cómo BitLocker protege CSV, vea [Proteger volúmenes compartidos de clúster y redes de área de almacenamiento con BitLocker](/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).
