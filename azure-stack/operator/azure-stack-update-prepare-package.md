---
title: Preparación de un paquete de actualización en Azure Stack Hub
description: Aprenda a preparar un paquete de actualización en Azure Stack Hub.
author: sethmanheim
ms.topic: how-to
ms.date: 07/22/2020
ms.author: sethm
ms.lastreviewed: 09/10/2019
ms.reviewer: sranthar
ms.openlocfilehash: ef2813c34bd089ef1bb429ba704e02faa06005be
ms.sourcegitcommit: 16ff77f7157e5b04a8cd401b095f7b71f51d5a11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86949634"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Preparación de un paquete de actualización de Azure Stack Hub

En este artículo se ofrece información general sobre cómo preparar paquetes de actualizaciones en Azure Stack Hub para que se puedan usar para actualizar cualquier entorno de Azure Stack Hub. Este proceso consta de los pasos siguientes:

- [Descarga de la actualización](#download-the-update-package).
- [Importación de la actualización en un entorno de Azure Stack Hub mediante el portal de administración de Azure Stack Hub](#import-and-install-updates).

En los sistemas que pueden conectarse a los puntos de conexión de actualización automática, las actualizaciones de software y las revisiones de Azure Stack Hub se descargan y preparan automáticamente. En sistemas sin conectividad y para cualquier actualización del fabricante de equipo original (OEM), la actualización debe prepararse tal como se explica en este artículo.  

En la siguiente tabla se muestra en qué momento las actualizaciones requieren preparación manual y cuándo se preparan automáticamente.

| Tipo de actualización | Conectividad | Acción requerida |
| --- | --- | --- |
| Actualizaciones de software de Azure Stack Hub | Conectado | La actualización se descarga y prepara automáticamente cuando se aplica la actualización. |
| Revisiones de Azure Stack Hub | Conectado | La actualización se descarga y prepara automáticamente cuando se aplica la actualización. |
| Actualizaciones del paquete del OEM | Conectado | Se debe preparar la actualización. Siga los pasos de este artículo. |
| Actualizaciones de software de Azure Stack Hub | Conexión débil o desconectado | Se debe preparar la actualización. Siga los pasos de este artículo. |
| Revisiones de Azure Stack Hub | Conexión débil o desconectado | Se debe preparar la actualización. Siga los pasos de este artículo. |
| Actualizaciones del paquete del OEM | Conexión débil o desconectado | Se debe preparar la actualización. Siga los pasos de este artículo. |

## <a name="download-the-update-package"></a>Descarga del paquete de actualización

El paquete para las actualizaciones y revisiones de Azure Stack Hub está disponible para los sistemas conectados a través de la hoja de actualización en el portal. Tanto si va a actualizar un paquete de OEM como si va a permitir un sistema desconectado, descargue el paquete y muévalo a una ubicación a la que pueda acceder la instancia de Azure Stack Hub. Es posible que también necesite descargar, y posteriormente cargar, el paquete en una ubicación accesible si ejecuta un sistema con una conexión intermitente.

Revise el contenido del paquete. Normalmente, un paquete de actualización consta de los siguientes archivos:

- **Un archivo \<PackageName>.zip autoextraíble**. Este archivo contiene la carga útil de la actualización.
- **Un archivo Metadata.xml**. Este archivo contiene información esencial acerca de la actualización, como el editor, el nombre, el requisito previo, el tamaño y la dirección URL de ruta de acceso al soporte técnico.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Descarga y preparación automáticas de las actualizaciones

Tanto las actualizaciones de software como las revisiones de Azure Stack Hub se preparan automáticamente en los sistemas con conexión a los **puntos de conexión de actualización automática de Azure Stack Hub**: <https://*.azureedge.net> y <https://aka.ms/azurestackautomaticupdate>. Para obtener más información acerca de cómo configurar la conectividad con los **puntos de conexión de actualización automática de Azure Stack Hub**, consulte los puntos de conexión de **Revisión y actualización** que se describen en [Integración del firewall de Azure Stack Hub](./azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Ubicación de la descarga de las actualizaciones de Azure Stack Hub

Las actualizaciones de Azure Stack Hub para [actualizaciones completas y rápidas](./azure-stack-updates.md#update-package-types) se hospedan en un punto de conexión seguro de Azure. Cuando las actualizaciones estén disponibles, los operadores de Azure Stack Hub con instancias conectadas verán que las [actualizaciones de Azure Stack Hub aparecen automáticamente en el portal de administración](#automatic-download-and-preparation-for-update-packages). En el caso de los sistemas sin conexión o con una conectividad a Internet débil, se puede usar la [herramienta de descarga de actualizaciones de Azure Stack Hub](https://aka.ms/azurestackupdatedownload) para descargar la actualización. Estos paquetes pueden contener no solo actualizaciones de los servicios de Azure Stack Hub, sino también actualizaciones del sistema operativo de sus unidades de escalado.

>[!NOTE]
>La propia actualización y su contenido (como archivos binarios, scripts de PowerShell, etc.) están firmados con certificados propiedad de Microsoft. Si se manipula el paquete, la firma dejará de ser válida.

### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Ubicación de descarga de los paquetes de correcciones de Azure Stack Hub

Los paquetes de las [revisiones de Azure Stack Hub](./azure-stack-updates.md#update-package-types) se hospedan en el mismo punto de conexión seguro de Azure que las actualizaciones de Azure Stack Hub. Los operadores de Azure Stack Hub con instancias conectadas verán que las [actualizaciones de Azure Stack Hub aparecen automáticamente en el portal de administración](#automatic-download-and-preparation-for-update-packages) cuando estén disponibles. Puede descargarlas mediante los vínculos insertados en cada uno de los artículos de KB de revisión respectivos, como [Revisión de Azure Stack Hub 1.1906.11.52](https://support.microsoft.com/help/4515650). También podrá encontrar revisiones en las notas de la versión correspondientes a su versión de Azure Stack Hub.

### <a name="where-to-download-oem-update-packages"></a>Ubicación de la descarga de las actualizaciones de OEM

Es posible que su distribuidor de OEM también publique actualizaciones, tanto de controladores como de firmware. Aunque su proveedor de hardware ofrece estas actualizaciones como [actualizaciones de paquetes de OEM ](./azure-stack-updates.md#update-package-types) independientes, se importan, instalan y administran de la misma manera que los paquetes de actualización de Microsoft. Puede consultar una lista de vínculos de contacto del proveedor en el artículo [Aplicación de actualizaciones de OEM de Azure Stack Hub](./azure-stack-update-oem.md#oem-contact-information).

## <a name="import-and-install-updates"></a>Importación e instalación de actualizaciones

El siguiente procedimiento muestra cómo importar e instalar actualizaciones en el portal de administración.

> [!IMPORTANT]  
> Notifique a los usuarios las operaciones de mantenimiento y asegúrese de programar ventanas de mantenimiento normales durante el horario no laborable tanto como sea posible. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo del usuario como a las operaciones del portal.

1. En el portal de administración, haga clic en **Todos los servicios**. Luego, en la categoría **DATOS Y ALMACENAMIENTO**, haga clic en **Cuentas de almacenamiento**. O bien, en el cuadro de filtro, empiece a escribir **cuentas de almacenamiento** y selecciónelo.

    ![Actualización de Azure Stack Hub](./media/azure-stack-update-prepare-package/image1.png)

2. En el cuadro de filtro, escriba **update**y seleccione la cuenta de almacenamiento **updateadminaccount**.

3. En los detalles de la cuenta de almacenamiento, en **Servicios**, seleccione **Blobs**.

    ![Actualización de Azure Stack Hub: blob](./media/azure-stack-update-prepare-package/image2.png)

4. En **Blob service**, seleccione **+ Contenedor** para crear un contenedor. Escriba un nombre (por ejemplo, **actualización 1811**) y, luego, seleccione **Aceptar**.

    ![Actualización de Azure Stack Hub: contenedor](./media/azure-stack-update-prepare-package/image3.png)

5. Tras crear el contenedor, seleccione su nombre y, después, **Cargar**, con el fin de cargar los archivos de paquete en el contenedor.

    ![Actualización de Azure Stack Hub: carga](./media/azure-stack-update-prepare-package/image4.png)

6. En **Cargar blob**, seleccione el icono de la carpeta, vaya al archivo ZIP de la actualización y seleccione **Abrir** en la ventana del explorador de archivos.

7. En **Cargar blob**, seleccione **Cargar**.

    ![Actualización de Azure Stack Hub: blob de carga](./media/azure-stack-update-prepare-package/image5.png)

8. Repita los pasos 6 y 7 para el archivo **Metadata.xml** y cualquier archivo ZIP adicional de la actualización. No importe el archivo **Supplemental Notice.txt** si se incluye.

9. Cuando haya finalizado, puede revisar las notificaciones (seleccione el icono de campana de la esquina superior derecha del portal). Una notificación debe indicar que la carga ha finalizado.

10. Vuelva a la hoja **Actualización** del panel. La hoja debe indicar que hay una actualización disponible. Esto significa que la actualización se ha preparado correctamente. Seleccione la hoja para examinar el paquete de actualización recién agregado.

11. Para instalar la actualización, seleccione el paquete que está marcado como **Listo** y haga clic con el botón derecho en él y seleccione **Actualizar ahora**, o bien seleccione la acción **Actualizar ahora** cerca de la parte superior.

12. Al seleccionar el paquete de actualización de instalación, puede ver el estado en el área **Update run details** (Detalles de ejecución de actualización). Aquí también puede seleccionar **Descargar resumen** para descargar los archivos de registro. Los registros de las ejecuciones de actualización están disponibles durante seis meses después de la finalización del intento.

13. Cuando la actualización finaliza, en la hoja Update (Actualizar) aparece la versión actualizada de Azure Stack Hub.

Puede eliminar manualmente las actualizaciones de la cuenta de almacenamiento después de haberlas instalado en Azure Stack Hub. Azure Stack Hub busca periódicamente los paquetes de actualización más antiguos y los elimina del almacenamiento. Azure Stack Hub puede tardar hasta dos semanas en quitar los paquetes antiguos.

## <a name="next-steps"></a>Pasos siguientes

[Aplicar la actualización](azure-stack-apply-updates.md)
