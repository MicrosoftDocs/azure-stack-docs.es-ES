---
title: Recuperación después de una pérdida de datos grave en Azure Stack mediante el servicio Infrastructure Backup | Microsoft Docs
description: Cuando se produce un error grave en Azure Stack, puede restaurar los datos de la infraestructura en el momento de restablecer la implementación de Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: b6b6796f5d47189499e01c94b9c988dbf03091bb
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493994"
---
# <a name="recover-from-catastrophic-data-loss"></a>Recuperación después de una pérdida de datos grave

*Se aplica a: Sistemas integrados de Azure Stack.*

Azure Stack ejecuta servicios de Azure en su centro de datos y puede ejecutarse incluso en entornos de cuatro nodos instalados en un único bastidor. En cambio, Azure se ejecuta en más de 40 regiones en varios centros de datos y distintas zonas en cada región. Los recursos de usuario pueden abarcar varios servidores, bastidores, centros de datos y regiones. Con Azure Stack, actualmente solo tiene la opción de implementar toda la nube en un único bastidor. Esto hace que la nube quede expuesta al riesgo de que se produzcan errores graves en el centro de datos, o bien fallos debido a errores importantes en productos. Cuando se produce un desastre, la instancia de Azure Stack queda sin conexión. Todos los datos son potencialmente irrecuperables.

En función de la causa principal de la pérdida de datos, puede que tenga que reparar un solo servicio de infraestructura o que restaurar toda la instancia de Azure Stack. Incluso puede que tenga que restauran en un hardware distinto en la misma ubicación o en una ubicación diferente.

En este escenario se aborda la recuperación de toda la instalación si se produce un error del equipo y la reimplentación de la nube privada.

| Escenario                                                           | Pérdida de datos                            | Consideraciones                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Recuperación después de una pérdida de datos grave debido a un desastre o a un error en un producto | Todos los datos de infraestructura, usuario y aplicación | Los datos y la aplicación de usuario están protegidos por separado de los datos de infraestructura |

## <a name="workflows"></a>Workflows

El proceso de proteger Azure Stack comienza con la creación de la copia de seguridad de la infraestructura y los datos de aplicación o inquilino por separado. Este documento explica cómo proteger la infraestructura. 

![Implementación inicial de Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow1.png)

En los peores escenarios, en los que se pierden todos los datos, la recuperación de Azure Stack es el proceso de restaura los datos de infraestructura que son únicos para esa implementación de Azure Stack y todos los datos de usuario. 

![Nueva implementación de Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restauración

Si hay una pérdida de datos grave, pero todavía es posible usar el hardware, se requiere reimplementar Azure Stack. Durante la implementación, puede especificar la ubicación de almacenamiento y las credenciales que se requieren para acceder a las copias de seguridad. En este modo, no es necesario especificar los servicios que se deben restaurar. Infrastructure Backup Controller inserta el estado del plano de control como parte del flujo de trabajo de la implementación.

Si se produce un desastre que hace que no se pueda usar el hardware, solo es posible realizar la reimplementación en hardware nuevo. La reimplementación puede tardar varias semanas desde que se pide el hardware de reemplazo y este llega al centro de datos. Restaurar los datos del plano de control se puede hacer en cualquier momento. Sin embargo, no se admite la restauración si la versión de la instancia reimplementada es más de una versión mayor que la versión que se usó en la última copia de seguridad. 

| Modo de implementación | Punto de partida | Punto de conexión                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Instalación limpia   | Compilación de línea de base | OEM implementa Azure Stack y se actualiza a la versión compatible más reciente.                                                                                                                                          |
| Modo Recuperación   | Compilación de línea de base | OEM implementa Azure Stack en el modo de recuperación y controla que la versión coincida con los requisitos en función de la copia de seguridad más reciente disponible. OEM completa la implementación al actualizar a la versión compatible más reciente. |

## <a name="data-in-backups"></a>Datos en las copias de seguridad

Azure Stack admite un tipo de implementación llamado modo de recuperación en la nube. Este modo se usa solo si elige recuperar Azure Stack después de que un desastre o el error en un producto haya hecho que la solución no se pueda recuperar. Este modo de implementación no recupera ninguno de los datos de usuario almacenados en la solución. El ámbito de este modo de implementación está limitado a la restauración de los datos siguientes:

 - Entradas de implementación
 - Datos internos del servicio de identidad (implementaciones de ADFS)
 - Configuración de identificación federada (implementaciones de ADFS)
 - Certificados raíz usados por la entidad de certificación interna
 - Datos de usuario de la configuración de Azure Resource Manager, como suscripciones, planes, ofertas y cuotas de almacenamiento, red y recursos de proceso
 - Almacenes y secretos de KeyVault
 - Asignaciones de directivas de RBAC y asignaciones de rol 

Ninguno de los recursos de usuario de Infraestructura como servicio (IaaS) o Plataforma como servicio (PaaS) se recupera durante la implementación. Es decir, se pierden las máquinas virtuales de IaaS, las cuentas de almacenamiento, los blobs, las tablas, la configuración de red, etc. El propósito de la recuperación en la nube es garantizar que los operadores y usuarios puedan volver a iniciar sesión en el portal una vez que se complete la implementación. Los usuarios que vuelven a iniciar sesión no verán ninguno de sus recursos. Se restauran las suscripciones de los usuarios y, junto con ellas, las directivas de ofertas y planes originales que definió el administrador. Los usuarios que vuelven a iniciar sesión en el sistema funcionan bajo las mismas restricciones que imponía la solución original antes del desastre. Una vez que se completa la recuperación en la nube, el operador puede restaurar manualmente los puntos de recuperación de terceros y con valor agregado, además de los datos asociados.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre los procedimientos recomendados para [usar el servicio Infrastructure Backup](azure-stack-backup-best-practices.md).
