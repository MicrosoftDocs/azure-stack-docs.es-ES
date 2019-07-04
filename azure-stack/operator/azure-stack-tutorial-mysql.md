---
title: Oferta de bases de datos MySQL de alta disponibilidad en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo crear un equipo host de proveedor de recursos de MySQL Server y bases de datos de alta disponibilidad MySQL con Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: e62e78705d6ca1ec8fa7d839b70eda3f147f82ab
ms.sourcegitcommit: 3f52cf06fb5b3208057cfdc07616cd76f11cdb38
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316302"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>Tutorial: Oferta de bases de datos MySQL de alta disponibilidad

Como operador de Azure Stack, puede configurar máquinas virtuales de servidor para hospedar bases de datos de MySQL Server. Una vez que se haya creado correctamente un clúster de MySQL y se administre por parte de Azure Stack, los usuarios suscritos a servicios de SQL pueden crear fácilmente bases de datos MySQL de alta disponibilidad.

Este tutorial muestra cómo utilizar elementos de Marketplace de Azure Stack para crear un clúster de [MySQL con replicación](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Esta solución usa varias máquinas virtuales para replicar las bases de datos desde el nodo maestro a un número configurable de réplicas. Una vez creado, el clúster se puede agregar como un servidor de hospedaje de MySQL de Azure Stack y, a continuación, los usuarios podrán crear bases de datos MySQL de alta disponibilidad.

> [!IMPORTANT]
> El elemento de Markeplace de Azure Stack **MySQL con replicación** podría no estar disponible para todos los entornos de suscripción en la nube de Azure. Compruebe que el elemento de Marketplace está disponible en su suscripción antes de intentar seguir el resto de este tutorial.

Lo qué aprenderá:

> [!div class="checklist"]
> * Crear un clúster de MySQL Server desde elementos de Marketplace
> * Crear un servidor de hospedaje MySQL de Azure Stack
> * Crear una base de datos MySQL de alta disponibilidad

En este tutorial, creará un clúster de MySQL Server para tres máquinas virtuales, que se configurará con los elementos disponibles de Marketplace para Azure Stack. 

Antes de comenzar los pasos descritos en este tutorial, asegúrese de que el [proveedor de recursos de MySQL Server](azure-stack-mysql-resource-provider-deploy.md) ha instalado correctamente y ha puesto a disposición de los usuarios los siguientes elementos en el Marketplace para Azure Stack:

> [!IMPORTANT]
> Todos los elementos siguientes son necesarios para crear el clúster de MySQL.

- [MySQL con replicación](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Se trata de la plantilla de solución de Bitnami que se usará para la implementación del clúster de MySQL.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian). Debian 8 "Jessie" con kernel backports para Microsoft Azure proporcionado por credativ. Debian GNU/Linux es una de las distribuciones de Linux más populares.
- [Script personalizado para Linux 2.0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). La extensión de script personalizado es una herramienta para ejecutar sus tareas de personalización de VM tras el aprovisionamiento de la VM. Cuando esta extensión se agrega a una máquina virtual, puede descargar los scripts de Azure Storage y ejecutarlos en la máquina virtual. Las tareas de la extensión de script personalizado también se pueden automatizar mediante los cmdlets de PowerShell de Azure y la interfaz de línea de comandos multiplataforma de Azure (CLI xPlat).
- Extensión de acceso a máquina virtual para Linux 1.4.7. La extensión de acceso a máquina virtual le permite restablecer la contraseña, la clave SSH o las configuraciones de SSH, por lo que puede recuperar el acceso a la máquina virtual. También puede agregar un nuevo usuario con la contraseña o la clave SSH, o bien eliminar un usuario mediante esta extensión. Esta extensión está destinada a las máquinas virtuales Linux.

Para obtener más información acerca de cómo agregar elementos a Marketplace de Azure Stack, consulte [Información general de Azure Stack Marketplace](azure-stack-marketplace.md).

También, necesitará un cliente SSH como [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) para iniciar sesión en las máquinas virtuales Linux después de que se hayan implementado.

## <a name="create-a-mysql-server-cluster"></a>Creación de un clúster de MySQL Server 
Siga los pasos de esta sección para implementar el clúster de MySQL Server con el elemento de Marketplace [MySQL con replicación](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Esta plantilla implementa tres instancias de MySQL Server configuradas en un clúster de MySQL de alta disponibilidad. De manera predeterminada, crea estos recursos:

- Una red virtual
- Un grupo de seguridad de red
- Una cuenta de almacenamiento
- Un conjunto de disponibilidad
- Tres interfaces de red (una para cada VM predeterminada)
- Una dirección IP pública (para la VM del clúster de MySQL)
- Tres VM de Linux para hospedar el clúster de MySQL

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Seleccione **\+** **Crear un recurso** > **Proceso** y, a continuación, **MySQL con replicación**.

   ![Implementación de plantilla personalizada](media/azure-stack-tutorial-mysqlrp/1.png)

3. Proporcione información básica sobre la implementación en la página **Aspectos básicos**. Revise los valores predeterminados, haga los cambios pertinentes y haga clic en **Aceptar**.<br><br>Como mínimo, proporcione lo siguiente:
   - Nombre de la implementación (el valor predeterminado es mymysql)
   - Contraseña raíz de la aplicación. Proporcione una contraseña de 12 caracteres alfanuméricos **sin caracteres especiales**
   - Nombre de la base de datos de aplicación (el valor predeterminado es bitnami)
   - Número de máquinas virtuales de réplica de base de datos de MySQL para crear (el valor predeterminado es 2)
   - Seleccione la suscripción que se va a usar
   - Seleccione el grupo de recursos o cree uno nuevo
   - Seleccione la ubicación (el valor predeterminado es local para el Kit de desarrollo de Azure Stack)

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "Conceptos básicos de la implementación")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. En la página de **configuración del entorno**, proporcione la información siguiente y, después, haga clic en **Aceptar**: 
   - Contraseña o SSH de clave pública que se usará para la autenticación de Secure Shell (SSH). Si usa una contraseña, debe contener letras y números, y **puede** contener caracteres especiales
   - Tamaño de máquina virtual (el valor predeterminado es VM Standard D1 v2)
   - Tamaño del disco de datos en GB Haga clic en **Aceptar**

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "Configuración del entorno")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. Revise el **resumen** de la implementación. Si lo desea, puede descargar la plantilla personalizada y los parámetros; a continuación, haga clic en **Aceptar**.

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "Resumen")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. Haga clic en **Crear** en la página **Comprar** para iniciar la implementación.

   ![Comprar](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > La implementación tardará aproximadamente una hora. Asegúrese de que ha terminado la implementación y el clúster de MySQL se ha configurado completamente antes de continuar. 

7. Una vez que todas las implementaciones se hayan completado correctamente, revise los elementos del grupo de recursos y seleccione el elemento **mysqlip** de la dirección IP pública. Anote la dirección IP pública y el nombre de dominio completo de la dirección IP pública del clúster.<br><br>Deberá proporcionar esta información al operador de Azure Stack para que pueda crear un servidor de hospedaje SQL aprovechando este clúster de MySQL.


### <a name="create-a-network-security-group-rule"></a>Creación de una regla de grupo de seguridad de red
De forma predeterminada, no hay ningún acceso público configurado para MySQL en la máquina virtual del host. Para que el proveedor de recursos de MySQL en Azure Stack se conecte al clúster de MySQL y lo administre, debe crearse una regla de grupo de seguridad de red entrante.

1. En el portal de administrador, vaya al grupo de recursos que creó al implementar el clúster de MySQL y seleccione el grupo de seguridad de red (**default-subnet-sg**):

   ![abierto](media/azure-stack-tutorial-mysqlrp/6.png)

2. Seleccione **Reglas de seguridad de entrada** y luego haga clic en **Agregar**.<br><br>Escriba **3306** en el **Intervalo de puertos de destino** y, opcionalmente, proporcione una descripción en los campos **Nombre** y **Descripción**. Haga clic en Agregar para cerrar el cuadro de diálogo de regla de seguridad de entrada.

   ![abierto](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>Configuración del acceso externo al clúster de MySQL
Para que el clúster de MySQL se pueda agregar como un host de servidor de MySQL de Azure Stack, debe habilitarse el acceso externo.

1. Con un cliente de SSH (en este ejemplo se usa [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)), inicie sesión en la máquina principal de MySQL desde un equipo que pueda tener acceso a la dirección IP pública. El nombre de la VM principal de MySQL generalmente termina con **0** y tiene asignada una dirección IP pública.<br><br>Use la dirección IP pública e inicie sesión en la máquina virtual con el nombre de usuario de **bitnami** y la contraseña de aplicación que creó anteriormente sin caracteres especiales.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. En la ventana del cliente SSH, use el comando siguiente para asegurarse de que el servicio de bitnami está activo y en funcionamiento. Proporcione la contraseña de bitnami de nuevo cuando se le solicite:

   `sudo service bitnami status`

   ![Comprobación de servicio](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Cree una cuenta de usuario de acceso remoto que utilizará el servidor de hospedaje de MySQL de Azure Stack para conectarse a MySQL y, a continuación, cierre el cliente SSH.<br><br>Ejecute los siguientes comandos para iniciar sesión en MySQL como raíz, con la contraseña raíz que creó anteriormente, y cree un nuevo usuario administrador; reemplace el *\<nombre de usuario\>* y la  *\<contraseña\>* según sea necesario para su entorno. En este ejemplo, el usuario que se va a crear se llama **sqlsa** y se utiliza una contraseña segura:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Creación de usuario administrador](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Registre la nueva información de usuario de MySQL.<br><br>Deberá proporcionar este nombre de usuario y contraseña, junto con la dirección IP o nombre de dominio completo de la dirección IP pública para el clúster, a un operador de Azure Stack para que pueda crear un servidor de hospedaje de MySQL con este clúster de MySQL.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Crear un servidor de hospedaje MySQL de Azure Stack
Una vez que el clúster de MySQL se ha creado y configurado correctamente, un operador de Azure Stack debe crear un servidor de hospedaje de SQL de Azure Stack para facilitar una mayor capacidad que permita a los usuarios crear bases de datos. 

Asegúrese de usar la dirección IP pública o el nombre de dominio completo para la dirección IP pública de la máquina virtual principal del clúster de MySQL que anotó anteriormente, cuando creó el grupo de recursos del clúster de MySQL (**mysqlip**). Además, el operador deberá conocer las credenciales de autenticación del servidor MySQL que ha creado para tener acceso remoto a la base de datos del clúster de MySQL.

> [!NOTE]
> Este paso se debe ejecutar desde el portal de administración de Azure Stack por un operador de Azure Stack.

Con la información de inicio de sesión de autenticación de MySQL y la dirección IP pública del clúster de MySQL, el operador de Azure Stack ahora puede [crear un servidor de hospedaje de MySQL con el nuevo clúster de MySQL](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Asegúrese también de que se han creado planes y ofertas para que la creación de la base de datos de MySQL esté disponible para los usuarios. El operador deberá agregar el servicio **Microsoft.MySqlAdapter** a un plan y crear una nueva cuota específicamente para las bases de datos de alta disponibilidad. Para obtener más información acerca de la creación de planes, consulte [Introducción a los planes, ofertas, cuotas y suscripciones](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> El servicio **Microsoft.MySqlAdapter** no estará disponible para agregarlo a planes hasta que [se haya implementado el proveedor de recursos de MySQL Server](azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Creación de una base de datos MySQL de alta disponibilidad
Una vez que el clúster de MySQL se ha creado, configurado y agregado como servidor de hospedaje MySQL de Azure Stack por un operador de Azure Stack, un usuario inquilino con una suscripción que tenga capacidades de base de datos MySQL Server puede crear bases de datos MySQL de alta disponibilidad siguiendo los pasos de esta sección. 

> [!NOTE]
> Ejecute estos pasos desde el portal de usuario de Azure Stack como un usuario inquilino con una suscripción que proporcione funciones de MySQL Server (servicio Microsoft.MySQLAdapter).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Seleccione **\+** **Crear un recurso** > **Datos\+ y almacenamiento** y, a continuación, **Base de datos MySQL**.<br><br>Proporcione la información de propiedad de base de datos requerida, incluidos el nombre, la intercalación y la suscripción para utilizar y la ubicación que se emplearán en la implementación. 

   ![Creación de la base de datos MySQL](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Seleccione **SKU** y, a continuación, elija la SKU del servidor de hospedaje de MySQL apropiado que se usará. En este ejemplo, el operador de Azure Stack ha creado la SKU **MySQL-HA** para admitir la alta disponibilidad de bases de datos de clúster de MySQL.

   ![Selección de SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Seleccione **Inicio de sesión** > **Create a new login** (Crear un nuevo inicio de sesión) y, a continuación, proporcione las credenciales de autenticación de MySQL que se usarán para la nueva base de datos. Cuando termine, haga clic en **Aceptar** y, a continuación, en **Crear** para comenzar el proceso de implementación de la base de datos.

   ![Adición de inicio de sesión](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Cuando la implementación de la base de datos MySQL se complete correctamente, revise las propiedades de la base de datos para detectar la cadena de conexión que se usará para conectarse a la nueva base de datos de alta disponibilidad. 

   ![Visualización de la cadena de conexión](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear un clúster de MySQL Server desde elementos de Marketplace
> * Crear un servidor de hospedaje MySQL de Azure Stack
> * Creación de una base de datos MySQL de alta disponibilidad

Prosiga con el siguiente tutorial para aprender a:
> [!div class="nextstepaction"]
> [Oferta de aplicaciones web](azure-stack-tutorial-app-service.md)
