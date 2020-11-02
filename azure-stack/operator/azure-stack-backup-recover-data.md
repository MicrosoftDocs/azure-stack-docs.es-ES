---
title: Recuperación ante una pérdida de datos grave en Azure Stack Hub
description: Aprenda cómo recuperar y restaurar los datos de infraestructura en Azure Stack Hub después de una pérdida de datos grave.
author: justinha
ms.topic: article
ms.date: 10/23/2020
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 35d55eec6c1311c39014f94f94fb04d39c7acb3b
ms.sourcegitcommit: 25f6211aa16308d50315872f647d840f402fa62e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2020
ms.locfileid: "92496417"
---
# <a name="recover-from-catastrophic-data-loss"></a>Recuperación después de una pérdida de datos grave

Azure Stack Hub ejecuta servicios de Azure en su centro de datos y puede ejecutarse incluso en entornos de cuatro nodos instalados en un único bastidor. En cambio, Azure se ejecuta en más de 40 regiones en varios centros de datos y distintas zonas en cada región. Los recursos de usuario pueden abarcar varios servidores, bastidores, centros de datos y regiones. Con Azure Stack Hub, actualmente solo tiene la opción de implementar toda la nube en un único bastidor. Esta limitación hace que la nube quede expuesta al riesgo de que se produzcan errores graves en el centro de datos, o bien fallos debido a errores importantes en productos. Cuando se produce un desastre, la instancia de Azure Stack Hub queda sin conexión. Todos los datos son potencialmente irrecuperables.

En función de la causa principal de la pérdida de datos, puede que tenga que reparar un solo servicio de infraestructura o que restaurar toda la instancia de Azure Stack Hub. Incluso puede que tenga que restauran en un hardware distinto en la misma ubicación o en una ubicación diferente.

En este escenario se aborda la recuperación de toda la instalación si se produce un error, y la reimplementación de la nube privada.

| Escenario                                                           | Pérdida de datos                            | Consideraciones                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Recuperación después de una pérdida de datos grave debido a un desastre o a un error en un producto. | Todos los datos de infraestructura, usuarios y aplicaciones. | Se puede restaurar en un fabricante de equipos originales diferente.<br/> Se puede restaurar en una generación diferente de hardware.<br/> Se puede restaurar en un recuento diferente de nodos de unidad de escalado.<br/> Los datos y las aplicaciones de usuario se protegen por separado de los datos de la infraestructura. |

## <a name="workflows"></a>Workflows

El proceso para proteger Azure Stack Hub comienza con la creación de una copia de seguridad de la infraestructura y los datos de aplicación o inquilino por separado. Este documento explica cómo proteger la infraestructura. 

![Flujo de trabajo de recuperación de datos de Azure Stack Hub: implementación](media/azure-stack-backup/azure-stack-backup-workflow1.png)

En los peores escenarios, en los que se pierden todos los datos, la recuperación de Azure Stack Hub es el proceso de restaurar los datos de infraestructura que son únicos para esa implementación de Azure Stack Hub y todos los datos de usuario. 

![Flujo de trabajo de recuperación de datos de Azure Stack Hub: reimplementación](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restauración

Si hay una pérdida de datos grave, pero todavía es posible usar el hardware, es necesario reimplementar Azure Stack Hub. Durante la implementación, puede especificar la ubicación de almacenamiento y las credenciales que se requieren para acceder a las copias de seguridad. En este modo, no es necesario especificar los servicios que se deben restaurar. Infrastructure Backup Controller inserta el estado del plano de control como parte del flujo de trabajo de la implementación.

Si se produce un desastre que hace que no se pueda usar el hardware, solo es posible realizar la reimplementación en hardware nuevo. La reimplementación puede tardar varias semanas desde que se pide el hardware de reemplazo y este llega al centro de datos. Restaurar los datos del plano de control se puede hacer en cualquier momento. Sin embargo, no se admite la restauración si la versión de la instancia reimplementada es más de una versión mayor que la versión que se usó en la última copia de seguridad.

| Modo de implementación | Punto de partida | Punto de conexión                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Instalación limpia   | Compilación de línea de base | OEM implementa Azure Stack Hub y se actualiza a la versión compatible más reciente.                                                                                                                                          |
| Modo Recuperación   | Compilación de línea de base | OEM implementa Azure Stack Hub en el modo de recuperación y controla que la versión coincida con los requisitos en función de la copia de seguridad más reciente disponible. OEM completa la implementación al actualizar a la versión compatible más reciente. |

## <a name="data-in-backups"></a>Datos en las copias de seguridad

Azure Stack Hub admite un tipo de implementación llamado modo de recuperación en la nube. Este modo se usa solo si elige recuperar Azure Stack Hub después de que un desastre o error en un producto hayan hecho que la solución no se pueda recuperar. Este modo de implementación no recupera ninguno de los datos de usuario almacenados en la solución. El ámbito de este modo de implementación está limitado a la restauración de los datos siguientes:

 - Entradas de implementación
 - Datos internos del servicio de identidad
 - Configuración de identificación federada (implementaciones de ADFS).
 - Certificados raíz usados por la entidad de certificación interna.
 - Datos de usuario de la configuración de Azure Resource Manager, como suscripciones, planes, ofertas, grupos de recursos, etiquetas, cuotas de almacenamiento, cuotas de red y recursos de proceso.
 - Almacenes y secretos de KeyVault.
 - Asignaciones de directivas de RBAC y asignaciones de rol.

Ninguno de los recursos de usuario de Infraestructura como servicio (IaaS) o Plataforma como servicio (PaaS) se recupera durante la implementación. Se pierden las máquinas virtuales de IaaS, las cuentas de almacenamiento, los blobs, las tablas, la configuración de red, etc. El propósito de la recuperación en la nube es garantizar que los operadores y usuarios puedan volver a iniciar sesión en el portal una vez que se complete la implementación. Los usuarios que vuelven a iniciar sesión no verán ninguno de sus recursos. Se restauran las suscripciones de los usuarios y, junto con ellas, las directivas de ofertas y planes originales que definió el administrador. Los usuarios que vuelven a iniciar sesión en el sistema funcionan bajo las mismas restricciones que imponía la solución original antes del desastre. Una vez que se completa la recuperación en la nube, el operador puede restaurar manualmente los puntos de recuperación de terceros y con valor agregado, además de los datos asociados.

## <a name="validate-backups"></a>Validación de copias de seguridad 

Puede usar el ASDK para probar una copia de seguridad para confirmar que los datos son válidos y se pueden usar. Para obtener más información, consulte [Uso del ASDK para validar una copia de seguridad de Azure Stack](../asdk/asdk-validate-backup.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre los procedimientos recomendados para [usar el servicio Infrastructure Backup](azure-stack-backup-best-practices.md).
