---
title: Adición de servidores de hospedaje de MySQL en Azure Stack Hub
description: Descubra cómo agregar servidores de hospedaje de MySQL para el aprovisionamiento mediante el proveedor de recursos de adaptador de MySQL.
author: mattbriggs
ms.topic: article
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 6cd5d09dcfc2467bd596b94597d001c4803e1655
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881810"
---
# <a name="add-mysql-hosting-servers-in-azure-stack-hub"></a>Adición de servidores de hospedaje de MySQL en Azure Stack Hub

Puede hospedar una instancia del servidor host de MySQL en una máquina virtual en [Azure Stack Hub](azure-stack-overview.md), o en una máquina virtual fuera de su entorno de Azure Stack Hub, siempre y cuando el proveedor de recursos de MySQL pueda conectarse a la instancia.

> [!NOTE]
> El proveedor de recursos de MySQL debe crearse en la suscripción de proveedor predeterminada, mientras que los servidores de hospedaje de MySQL deben crearse en una suscripción de usuario facturable. El servidor del proveedor de recursos no debe usarse para hospedar bases de datos de usuario.

Para los servidores de hospedaje, se pueden usar las versiones de MySQL 5.6, 5.7 y 8.0. RP de MySQL no admite la autenticación caching_sha2_password, que se agregará en la próxima versión. Los servidores MySQL 8.0 deben configurarse para usar mysql_native_password. También se admite MariaDB.

## <a name="connect-to-a-mysql-hosting-server"></a>Conexión a un servidor de hospedaje MySQL

Asegúrese de que tiene las credenciales de una cuenta con privilegios de administrador del sistema.

> [!NOTE]
> En el caso de MySQL 8.0 y versiones posteriores, el acceso remoto no está habilitado de forma predeterminada. Debe crear una nueva cuenta de usuario y conceder el privilegio de acceso remoto a esta cuenta de usuario antes de agregarla como servidor de hospedaje.

Para agregar un servidor de hospedaje, siga estos pasos:

1. Inicie sesión en el portal del administrador de Azure Stack Hub como administrador de servicios.
2. Seleccione **Todos los servicios**.
3. Bajo la categoría **RECURSOS ADMINISTRATIVOS**, seleccione **MySQL Hosting Servers** (Servidores de hospedaje de MySQL) >  **+ Add** (Agregar). Esta acción abre el cuadro de diálogo **Add a MySQL Hosting Server** (Agregar un servidor de hospedaje de MySQL), que se muestra en la siguiente captura de pantalla.

   ![Configuración de un servidor de hospedaje de MySQL](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Proporcione los detalles de conexión de la instancia del servidor MySQL.

   * En el caso de **MySQL Hosting Server Name** (Nombre del servidor de hospedaje MySQL), proporcione el nombre de dominio completo (FQDN) o una dirección IPv4 válida. No use el nombre corto de la máquina virtual.
   * El administrador predeterminado **Nombre de usuario** de las imágenes MySQL de Bitnami disponibles en Azure Stack Hub Marketplace es *raíz*.
   * Si no conoce la **Contraseña** raíz, consulte la [documentación de Bitnami](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) para averiguar cómo obtenerla.
   * No se ha proporcionado ninguna instancia de MySQL, por lo que debe especificar el valor de **Size of Hosting Server in GB** (Tamaño del servidor de hospedaje en GB). Debe ser similar a la capacidad del servidor de bases de datos.
   * Mantenga la configuración predeterminada para **Subscrición**.
   * En el caso de **Grupo de recursos**, cree uno o use un grupo ya existente.

   > [!NOTE]
   > Si Azure Resource Manager del inquilino y del administrador tiene acceso a la instancia de MySQL, puede ponerla bajo el control del proveedor de recursos. Sin embargo, la instancia de MySQL **debe** asignarse exclusivamente al proveedor de recursos.

5. Seleccione las **SKU** para abrir el cuadro de diálogo **Create SKU** (Crear SKU).

   ![Crear una SKU de MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   El valor de **Nombre** de la SKU debe reflejar sus propiedades para que los usuarios puedan implementar sus bases de datos en la SKU adecuada.

6. Seleccione **Aceptar** para crear la SKU.
   > [!NOTE]
   > Las SKU pueden tardar hasta una hora en estar visibles en el portal. No se puede crear una base de datos hasta que la SKU esté implementada y en ejecución.

7. En **Add a MySQL Hosting Server** (Agregar un servidor de hospedaje MySQL), seleccione **Crear**.

A medida que agregue servidores, asígnelos a una SKU nueva o existente para diferenciar las ofertas de servicio. Por ejemplo, puede tener una instancia empresarial de MySQL que proporcione copias de seguridad automáticas y bases de datos mayores. Puede reservar este servidor de alto rendimiento para diferentes departamentos de su organización.

## <a name="security-considerations-for-mysql"></a>Consideraciones de seguridad para MySQL

La siguiente información se aplica a los servidores de hospedaje de MySQL y RP:

* Asegúrese de que todos los servidores de hospedaje estén configurados para la comunicación con TLS 1.1. Consulte [Configuración de MySQL para usar conexiones cifradas](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Emplee [Cifrado de datos transparente](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* RP de MySQL no admite la autenticación caching_sha2_password.

## <a name="increase-backend-database-capacity"></a>Aumento de la capacidad de la base de datos back-end

Puede aumentar la capacidad de la base de datos back-end mediante la implementación de más servidores de MySQL en el portal de Azure Stack Hub. Agregue dichos servidores a una SKU nueva o existente. Si agrega un servidor a una SKU existente, asegúrese de que las características del servidor son las mismas que las de otros servidores de la SKU.

## <a name="sku-notes"></a>Notas de la SKU
Use un nombre de SKU que describa las capacidades de los servidores de la SKU, como la capacidad y el rendimiento. El nombre sirve de ayuda para que los usuarios implementen sus bases de datos en la SKU adecuada. Por ejemplo, puede usar nombres de SKU para diferenciar las ofertas de servicio mediante las características siguientes:
  
* alta capacidad
* alto rendimiento
* alta disponibilidad

Como procedimiento recomendado, todos los servidores de hospedaje de una SKU deben tener las mismas características de rendimiento y recursos.

Las SKU no se pueden asignar a usuarios o grupos específicos.

Para editar una SKU, diríjase a **All services (Todos los servicios)**  > **MySQL Adapter (Adaptador de MySQL)**  > **SKUs (SKU)** . Seleccione la SKU que quiere modificar, realice los cambios necesarios y haga clic en **Guardar** para guardar los cambios. 

Para eliminar una SKU que ya no es necesaria, vaya a **All services (Todos los servicios)**  > **MySQL Adapter (Adaptador de MySQL)**  > **SKUs (SKU)** . Haga clic en el nombre de la SKU y seleccione **Eliminar** para eliminarla.

> [!IMPORTANT]
> Las nuevas SKU pueden tardar hasta cuatro horas en estar disponibles en el portal del usuario.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Disposición de los servidores de base de datos MySQL para los usuarios

Cree planes y ofertas para poner los servidores de bases de datos MySQL a disposición de los usuarios. Agregue el servicio Microsoft.MySqlAdapter al plan y cree una cuota nueva. MySQL no permite limitar el tamaño de las bases de datos.

> [!IMPORTANT]
> Pueden pasar hasta dos horas hasta que las nuevas cuotas estén disponibles en el portal del usuario o antes de que se aplique una cuota modificada.

## <a name="next-steps"></a>Pasos siguientes

[Creación de una base de datos MySQL](azure-stack-mysql-resource-provider-databases.md)
