---
title: Preparación de un paquete de actualización de Azure Stack Hub
description: Aprenda a preparar un paquete de actualización de Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 5532345fe383b19bb96ea2f240634e15004a22b4
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509031"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Preparación de un paquete de actualización de Azure Stack Hub

Este artículo proporciona una introducción sobre cómo preparar paquetes de actualizaciones de Azure Stack Hub para que se puedan usar para actualizar el entorno de Azure Stack Hub. Este proceso consta de las siguientes secciones:

- [Descargar el paquete de actualización](#download-the-update-package)
- [Importación del paquete de actualización en el entorno de Azure Stack Hub utilizando el portal del administrador de Azure Stack Hub](#import-and-install-updates)

En los sistemas que pueden conectarse a los puntos de conexión de actualización automática, las actualizaciones de software y las revisiones de Azure Stack Hub se descargan y preparan automáticamente. En sistemas sin conectividad y para cualquier actualización del OEM, la actualización debe prepararse tal y como se explica en este tema.  

En la siguiente tabla se muestra cuándo las actualizaciones requieren preparación manual y cuándo se preparan automáticamente.

| Tipo de actualización | Conectividad | Acción requerida |
| --- | --- | --- |
| Actualizaciones de software de Azure Stack Hub | Conectado | La actualización se descarga y prepara automáticamente cuando se aplica la actualización. |
| Revisiones de Azure Stack Hub | Conectado | La actualización se descarga y prepara automáticamente cuando se aplica la actualización. |
| Actualizaciones de paquetes de OEM | Conectado | Se debe preparar la actualización. Siga los pasos de este artículo. |
| Actualizaciones de software de Azure Stack Hub | Conexión débil o desconectado | Se debe preparar la actualización. Siga los pasos de este artículo. |
| Revisiones de Azure Stack Hub | Conexión débil o desconectado | Se debe preparar la actualización. Siga los pasos de este artículo. |
| Actualizaciones de paquetes de OEM | Conexión débil o desconectado | Se debe preparar la actualización. Siga los pasos de este artículo. |

## <a name="download-the-update-package"></a>Descarga del paquete de actualización
La actualización para las actualizaciones y revisiones de Azure Stack Hub está disponible a través de la hoja de actualización para los sistemas conectados. Tanto si va a actualizar un paquete de OEM como si va a permitir un sistema desconectado, tendrá que descargar el paquete y moverlo a una ubicación a la que tenga acceso la instancia de Azure Stack Hub. También puede que necesite descargar y después cargar el paquete en una ubicación accesible si ejecuta un sistema con una conexión intermitente.

Revise el contenido del paquete. Normalmente, un paquete de actualización consta de los siguientes archivos:

-   **Un archivo \<PackageName>.zip autoextraíble**. Este archivo contiene la carga útil de la actualización.
- **Un archivo Metadata.xml**. Este archivo contiene información esencial acerca de la actualización, como el editor, el nombre, el requisito previo, el tamaño y la dirección URL de ruta de acceso al soporte técnico.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Descarga y preparación automáticas de las actualizaciones
Las actualizaciones y revisiones de software de Azure Stack Hub se preparan automáticamente para los sistemas con conexión a los **puntos de conexión de actualización automática de Azure Stack Hub**: https://*.azureedge.net y https://aka.ms/azurestackautomaticupdate. Para más información acerca de cómo configurar la conectividad con los **puntos de conexión de actualización automática de Azure Stack Hub**, consulte los puntos de conexión **Revisar y actualizar** que se describen en [Integración del firewall de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound).

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Ubicación de la descarga de las actualizaciones de Azure Stack Hub

Las actualizaciones de Azure Stack Hub para [actualizaciones completas y rápidas](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) se hospedan en un punto de conexión seguro de Azure. Los operadores de Azure Stack Hub con instancias conectadas verán que las [actualizaciones de Azure Stack Hub aparecen automáticamente en el portal de administración](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). En el caso de los sistemas desconectados de Internet o sistemas con conectividad de Internet débil, las actualizaciones se pueden descargar con la [aplicación de descarga de actualizaciones de Azure Stack Hub](https://aka.ms/azurestackupdatedownload). Las actualizaciones de software de Azure Stack Hub pueden contener actualizaciones de los servicios de esta solución, así como actualizaciones del sistema operativo de sus unidades de escalado.

>[!NOTE]
>La propia actualización y su contenido (como archivos binarios, scripts de PowerShell, etc.) están firmados con certificados propiedad de Microsoft. Si se manipula el paquete, la firma dejará de ser válida.


### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Ubicación de descarga de los paquetes de correcciones de Azure Stack Hub

El paquete para [revisiones de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) se hospeda en el mismo punto de conexión seguro de Azure que las actualizaciones de Azure Stack Hub. Los operadores de Azure Stack Hub con instancias conectadas verán que las [actualizaciones de Azure Stack Hub aparecen automáticamente en el portal de administración](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Puede descargarlas mediante los vínculos insertados en cada uno de los artículos de KB de revisión respectivos, como [Revisión de Azure Stack Hub 1.1906.11.52](https://support.microsoft.com/help/4515650). Encontrará correcciones en las notas de la versión correspondientes a su versión de Azure Stack Hub.

### <a name="where-to-download-oem-update-packages"></a>Ubicación de la descarga de las actualizaciones de OEM
Su distribuidor de OEM también liberará actualizaciones, tanto de controladores como de firmware. Aunque su proveedor de hardware entrega estas actualizaciones como [actualizaciones de paquetes de OEM ](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) independientes, todavía se importan, instalan y administran de la misma manera que los paquetes de actualización de Microsoft. Puede consultar una lista de vínculos de contacto del proveedor en [Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information).

## <a name="import-and-install-updates"></a>Importación e instalación de actualizaciones

En el siguiente procedimiento se muestra cómo importar e instalar actualizaciones en el portal de administración.

> [!Important]  
> Notifique a los usuarios cualquier operación de mantenimiento, y programe ventanas de mantenimiento normales durante el horario no laborable tanto como sea posible. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo del usuario como a las operaciones del portal.

1.  En el portal de administración, haga clic en **Todos los servicios**. Luego, en la categoría **DATOS Y ALMACENAMIENTO**, haga clic en **Cuentas de almacenamiento**. (o bien, en el cuadro de filtro, empiece a escribir **storage accounts** y selecciónelo).

    ![Actualización de Azure Stack Hub](./media/azure-stack-update-prepare-package/image1.png) 

1.  En el cuadro de filtro, escriba **update**y seleccione la cuenta de almacenamiento **updateadminaccount**.

2.  En los detalles de la cuenta de almacenamiento, en **Servicios**, seleccione **Blobs**.

    ![Actualización de Azure Stack Hub: blob](./media/azure-stack-update-prepare-package/image2.png)

1.  En **Blob service**, seleccione **+ Contenedor** para crear un contenedor. Escriba un nombre (por ejemplo *actualización-1811*) y, luego, seleccione **Aceptar**.

    ![Actualización de Azure Stack Hub: contenedor](./media/azure-stack-update-prepare-package/image3.png)

1.  Tras crear el contenedor, haga clic en su nombre y, después, haga clic en **Cargar** para cargar los archivos de paquete en el contenedor.

    ![Actualización de Azure Stack Hub: carga](./media/azure-stack-update-prepare-package/image4.png)

1.  En **Cargar blob**, haga clic en el icono de la carpeta, vaya al archivo ZIP de la actualización y haga clic en **Abrir** en la ventana del explorador de archivos.

2.  En **Cargar blob**, haga clic en **Cargar**.

    ![Actualización de Azure Stack Hub: blob de carga](./media/azure-stack-update-prepare-package/image5.png)

1.  Repita los pasos 6 y 7 para el archivo Metadata.xml y cualquier archivo ZIP adicional de la actualización. No importe el archivo Supplemental Notice.txt si está incluido.

2.  Cuando haya finalizado, puede revisar las notificaciones (el icono de campana de la esquina superior derecha del portal). Las notificaciones deben indicar que la carga se ha completado.

3.  Vuelva a la hoja Actualización del panel. La hoja debe indicar que hay una actualización disponible. Esto significa que la actualización se ha preparado correctamente. Haga clic en la hoja para revisar la actualización recién agregada.

4.  Para instalar la actualización, seleccione el paquete que está marcado como **Listo** y haga clic con el botón derecho en él y seleccione **Actualizar ahora**, o bien haga clic en la acción **Actualizar ahora** cerca de la parte superior.

5.  Al hacer clic en el paquete de actualización de instalación, puede ver el estado en el área **Update run details** (Detalles de ejecución de actualización). Aquí también puede hacer clic en **Descargar resumen** para descargar los archivos de registro. Los registros de las ejecuciones de actualización están disponibles durante 6 meses después de la finalización del intento.

6.  Cuando la actualización se completa, en la hoja Update (Actualizar) aparece la versión actualizada de Azure Stack Hub.

Puede eliminar manualmente las actualizaciones de la cuenta de almacenamiento después de haberlas instalado en Azure Stack Hub. Azure Stack Hub busca periódicamente los paquetes de actualización más antiguos y los elimina del almacenamiento. Azure Stack Hub puede tardar dos semanas en quitar los paquetes antiguos.

## <a name="next-steps"></a>Pasos siguientes

[Aplicar la actualización](azure-stack-apply-updates.md)
