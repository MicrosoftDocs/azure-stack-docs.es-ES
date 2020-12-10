---
title: Habilitación de la copia de seguridad de Azure Stack desde el portal de administración | Microsoft Docs
description: Aprenda a habilitar el servicio Copia de seguridad de infraestructura desde el portal de administración para que Azure Stack se pueda restaurar si se produce un error.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 3864183ecda856500db1fcbfe38df84d70f3bae9
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941449"
---
# <a name="configure-backup-for-azure-stack-hub-from-the-administrator-portal"></a>Configuración de la copia de seguridad de Azure Stack Hub desde el portal del administrador

*Se aplica a: Centro de datos modular, Azure Stack Hub resistente*

Puede configurar el servicio Copia de seguridad de infraestructura desde el portal del administrador para exportar las copias de seguridad de la infraestructura a una ubicación de almacenamiento externo. El equipo de soporte técnico puede usar las copias de seguridad de infraestructura para corregir los servicios degradados.

## <a name="configure-backup"></a>Configuración de la copia de seguridad

1. Abra el [portal de administración de Azure Stack](../../operator/azure-stack-manage-portals.md).

2. Seleccione **Todos los servicios** y, a continuación, en la categoría **Administración**, seleccione **Copia de seguridad de infraestructura**. Elija **Configuración** en la hoja **Copia de seguridad de infraestructura**.

3. Escriba la ruta de acceso a la **ubicación de almacenamiento de la copia de seguridad**. Utilice una cadena de convención de nomenclatura universal (UNC) para la ruta de acceso de un recurso compartido de archivos hospedado en un dispositivo independiente. Una cadena UNC especifica la ubicación de recursos como archivos compartidos o dispositivos. Para el servicio, puede usar una dirección IP. Para garantizar la disponibilidad de los datos de copia de seguridad después de un desastre, el dispositivo debe estar en una ubicación independiente.

    > [!NOTE]  
    > Si el entorno admite la resolución de nombres de la red de infraestructura de Azure Stack para su entorno empresarial, puede usar un nombre de dominio completo (FQDN) en lugar de la dirección IP.

4. Escriba el **nombre de usuario** con el dominio y el nombre de usuario con acceso suficiente para leer y escribir archivos; por ejemplo, **Contoso\backupshareuser**.

5. Escriba la **Contraseña** del usuario.

6. Escriba la contraseña de nuevo para **Confirmar la contraseña**.

7. La **frecuencia en horas** determina con qué frecuencia se crean las copias de seguridad. El valor predeterminado es 12. Scheduler admite un mínimo de 4 y un máximo de 12.

8. El **período de retención en días** determina cuántos días de copias de seguridad se conservan en la ubicación externa. El valor predeterminado es 7. Scheduler admite un mínimo de 2 y un máximo de 14. Las copias de seguridad anteriores al período de retención se eliminan automáticamente de la ubicación externa.

   > [!NOTE]
   > Si desea archivar las copias de seguridad anteriores al período de retención, asegúrese de hacer una copia de seguridad de los archivos antes de que Scheduler las elimine. Si reduce el período de retención de copia de seguridad (por ejemplo, de 7 a 5 días), Scheduler eliminará todas las copias de seguridad anteriores al nuevo período de retención. Asegúrese de que desea que las copias de seguridad se eliminen antes de actualizar este valor.

9. En **Configuración de cifrado**, la huella digital del certificado público es para el certificado proporcionado durante la implementación. No es necesario actualizar el certificado existente.

10. Seleccione **Aceptar** para guardar la configuración del controlador de copia de seguridad.

    ![Azure Stack: configuración del controlador de copia de seguridad](media/azure-stack-backup-enable-backup-console-tzl/backup-controller-settings-certificate.png)

## <a name="enable-or-disable-automatic-backups"></a>Habilitación o deshabilitación de las copias de seguridad automáticas

Las copias de seguridad se habilitan automáticamente durante la implementación. Después de configurar la ubicación de almacenamiento, las copias de seguridad automáticas exportan las copias de seguridad a una ubicación de almacenamiento externo según una programación, en función de la configuración de la frecuencia. Puede comprobar el momento en que se realizará la próxima copia de seguridad en la hoja **Información esencial**.

![Azure Stack: copia de seguridad a petición](media/azure-stack-backup-enable-backup-console-tzl/on-demand-backup.png)

Si necesita deshabilitar futuras copias de seguridad programadas, seleccione **Deshabilitar copias de seguridad automáticas**. Al deshabilitar las copias de seguridad automáticas, se mantendrá la configuración y la programación de las copias de seguridad. Esta acción indica al programador que omita las futuras copias de seguridad.

![Azure Stack: deshabilitar las copias de seguridad programadas](media/azure-stack-backup-enable-backup-console-tzl/disable-auto-backup.png)

Confirme en **Información esencial** que se han deshabilitado las copias de seguridad programadas futuras:

![Azure Stack: confirmar que se han deshabilitado las copias de seguridad](media/azure-stack-backup-enable-backup-console-tzl/confirm-disable.png)

Seleccione **Habilitar copias de seguridad automáticas** para informar a Scheduler de que deben iniciarse las futuras copias de seguridad en el momento programado.

![Azure Stack: habilitar las copias de seguridad programadas](media/azure-stack-backup-enable-backup-console-tzl/enable-auto-backup.png)

## <a name="update-backup-settings"></a>Actualización de la configuración de copia de seguridad

Para actualizar el certificado usado para cifrar los datos de copia de seguridad, puede cargar un nuevo archivo .CER con la parte de la clave pública y seleccionar Aceptar para guardar la configuración.

Las nuevas copias de seguridad empezarán a usar la clave pública en el nuevo certificado. No hay ningún efecto sobre todas las copias de seguridad existentes creadas con el certificado anterior. Asegúrese de conservar el certificado antiguo en una ubicación segura por si lo necesitara para la recuperación en la nube.

![Azure Stack: visualización de la huella digital de certificado](media/azure-stack-backup-enable-backup-console-tzl/encryption-settings-thumbprint.png)

## <a name="next-steps"></a>Pasos siguientes

Para comprobar que la copia de seguridad se ejecutó, consulte [Confirmación de que la copia de seguridad se ha completado en el portal del administrador](../../operator/azure-stack-backup-back-up-azure-stack.md).
