---
title: Ampliación del centro de datos en Azure Stack Hub | Microsoft Docs
description: Aprenda a ampliar el centro de datos en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 92e82f549cddf51b1cbd6764cc122acc3ca8fdfc
ms.sourcegitcommit: ed44d477b9fd11573d1e0d1ed3a3c0ef4512df53
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73845973"
---
# <a name="how-to-extend-the-data-center-on--azure-stack-hub"></a>Ampliación del centro de datos en Azure Stack Hub

*Se aplica a: Sistemas integrados de Azure Stack Hub y al kit de desarrollo de Azure Stack Hub*

En este artículo se ofrece información sobre la infraestructura de almacenamiento de Azure Stack Hub que le ayudará a decidir cómo integrar Azure Stack en un entorno de red existente. Después de explicar de forma general la ampliación del centro de datos, el artículo presenta dos escenarios diferentes. Puede conectarse a un servidor de almacenamiento de archivos de Windows. También puede conectarse a un servidor iSCSI de Windows.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>Introducción a la ampliación del almacenamiento en Azure Stack Hub

Hay escenarios en los que colocar los datos en la nube pública no es suficiente. Puede que tenga una carga de trabajo de una base de datos virtualizada de proceso intensivo que es sensible a las latencias y el tiempo de ida y vuelta a la nube pública podría afectar al rendimiento de dicha carga de trabajo. Es posible que haya datos locales, que se mantienen en un servidor de archivos, un NAS o una matriz de almacenamiento de iSCSI, a los que las cargas de trabajo locales deban acceder y que tengan que residir en el entorno local para cumplir objetivos normativos o de cumplimiento.  Hay solo dos escenarios en los que el hecho de que los datos residan en el entorno local sigue siendo importante para muchas organizaciones.

Por lo tanto, ¿por qué no hospedar simplemente los datos en cuentas de almacenamiento en Azure Stack o en servidores de archivos virtualizados que se ejecutan en el sistema de Azure Stack? Bueno, a diferencia de Azure, el almacenamiento de Azure Stack es limitado. La capacidad que tiene disponible depende por completo de la capacidad por nodo que haya adquirido y del número de nodos del que disponga. Y como Azure Stack es una solución hiperconvergente, si desea aumentar la capacidad de almacenamiento para satisfacer las demandas de uso, deberá aumentar también la superficie de proceso mediante la adición de nodos.  El costo de esto podría ser exorbitante, especialmente si tenemos en cuenta que la necesidad de capacidad adicional sería para almacenamiento en frío y archivado, y dicha capacidad se podría lograr a un precio barato fuera del sistema de Azure Stack.

Lo cual le lleva al escenario del que se va a tratar a continuación. ¿Cómo se pueden conectar sistemas de Azure Stack y cargas de trabajo virtualizadas que se ejecutan en ese sistema de forma sencilla y eficaz con sistemas de almacenamiento fuera de Azure Stack, accesibles a través de la red?

## <a name="design-for-extending-storage"></a>Diseño para extender el almacenamiento

En el diagrama se muestra un escenario en el que una sola máquina virtual, en la que se ejecuta una carga de trabajo, se conecta a un espacio de almacenamiento externo (a la máquina virtual y al propio Azure Stack) y lo utiliza para fines de lectura y escritura de datos, etc. Este artículo se centrará en la recuperación simple de archivos, pero este ejemplo se puede ampliar a escenarios más complejos, como el almacenamiento remoto de archivos de base de datos.

![Ampliación del almacenamiento a Azure Stack](./media/azure-stack-network-howto-extend-datacenter/image1.png)

En el diagrama, verá que la máquina virtual del sistema de Azure Stack se ha implementado con varias tarjetas de interfaz de red. Desde la redundancia, pero también como procedimiento recomendado de almacenamiento, es importante tener varias rutas entre el objetivo y el destino. En los entornos más complejos es donde las máquinas virtuales de Azure Stack tienen direcciones IP públicas y privadas, al igual que en Azure. Si el almacenamiento externo necesita acceder a la máquina virtual, solo puede hacerlo a través de la dirección IP pública, ya que las direcciones IP privadas se usan principalmente en los sistemas de Azure Stack, en las redes virtuales y en las subredes. El almacenamiento externo no podrá comunicarse con el espacio de direcciones IP privadas de la máquina virtual, a menos que atraviese una VPN de sitio a sitio para conectarse a la propia red virtual. Por lo tanto, en este ejemplo, nos centraremos en la comunicación a través del espacio de direcciones IP públicas. Un aspecto que se debe tener en cuenta con el espacio de direcciones IP públicas del gráfico es que hay dos subredes del grupo de direcciones IP públicas diferentes. De forma predeterminada, Azure Stack requiere solo un grupo para la dirección IP pública, pero hay algo que se debe tener en cuenta: para el enrutamiento redundante puede que haya que agregar un segundo grupo. Sin embargo, no es posible seleccionar una dirección IP de un grupo específico, por lo que puede acabar con máquinas virtuales con direcciones IP públicas del mismo grupo en varias tarjetas de red virtual.

En este ejemplo, puede dar por hecho que se trata el enrutamiento entre los dispositivos del borde y el almacenamiento externo, y el tráfico puede atravesar la red correctamente. En este ejemplo, no importa si la red troncal es de 1 GbE, 10 GbE, 25 GbE o incluso más rápida, pero sería importante tenerlo en cuenta a la hora de planear la integración, para satisfacer las necesidades de rendimiento de las aplicaciones que acceden a este almacenamiento externo.

## <a name="connect-to-a-windows-server-file-server-storage"></a>Conexión al almacenamiento de un servidor de archivos de Windows Server

En este escenario, implemente y configure una máquina virtual de Windows Server 2019 en Azure Stack y prepárela para conectarse a un servidor de archivos externo que también puede ejecutar Windows Server 2019. Cuando corresponda, establezca características clave, como SMB multicanal, para optimizar el rendimiento y la conectividad entre la máquina virtual y el almacenamiento externo.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>Implementación de la máquina virtual con Windows Server 2019 en Azure Stack

1.  En el **portal de administrador de Azure Stack**, suponiendo que el sistema se haya registrado correctamente y esté conectado a Marketplace, seleccione **Marketplace Management** (Administración de Marketplace) y, luego, suponiendo que todavía no tenga una imagen de Windows Server 2019, seleccione **Add from Azure** (Agregar desde Azure) y, luego, busque **Windows Server 2019** y agregue la imagen de **Windows Server 2019 Datacenter**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    La imagen de Windows Server 2019 puede tardar un tiempo en descargarse.

2.  Una vez que tenga una imagen de Windows Server 2019 en el entorno de Azure Stack, inicie sesión en el portal de usuarios de Azure Stack**.

3.  Una vez que haya iniciado sesión en el portal de usuarios de Azure Stack, asegúrese de que tiene una [suscripción a una oferta](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908), lo que le permite aprovisionar recursos de IaaS (Compute, Storage y Network).

4.  Una vez que tenga una suscripción disponible, vuelva al **panel** del portal de usuarios de Azure Stack, seleccione **Create a resource** (Crear un recurso), seleccione **Compute** y, luego, seleccione el **elemento de la galería Windows Server 2019 Datacenter**.

5.  En la hoja **Básico** :

    a.  **Nombre**: VM001

    b.  **Username** (Nombre de usuario): localadmin

    c.  **Password** (Contraseña) y **Confirm password** (Confirmar contraseña): \<la contraseña que prefiera>

    d.  **Subscription** (Suscripción): \<la suscripción que prefiera, con los recursos compute/storage/network>

    e. **Grupo de recursos**: crear nuevo: storagetesting

    f.  Luego, seleccione **OK** (Aceptar)

6.  En la hoja **Choosing a size** (Elegir un tamaño), seleccione **Standard_F8s_v2**.

7.  En la hoja **Settings** (Configurar), seleccione la **red virtual** y, en la hoja **Create virtual network** (Crear red virtual), ajuste el espacio de direcciones para que sea **red 10.10.10.0/23** y actualice el intervalo de direcciones de subred para que sea **10.10.10.0/24** y seleccione **OK** (Aceptar).

8.  Seleccione la **dirección IP pública** y en la hoja **Create public IP address** (Crear dirección IP pública ), seleccione **Static** (Estática).

9.  En **Select public inbound ports** (Seleccionar puertos de entrada públicos), seleccione **RDP (3389)** .

10. Deje los restantes valores predeterminados y seleccione **OK** (Aceptar).
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Lea el resumen, espere la validación y seleccione **OK** (Aceptar) para comenzar la implementación. La implementación debería finalizar en unos 10 minutos.

12. Una vez finalizada la implementación, en **Resource** (Recurso) seleccione el nombre de la máquina virtual, **VM001** para ir a la hoja *Overview* (Información general).
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. En DNS name (Nombre de DNS), seleccione **Configure** (Configurar) y especifique la etiqueta de un nombre de DNS, **vm001, y seleccione **Save** (Guardar) y, después, seleccione **VM001** en la ruta de navegación para volver a la hoja *Overview* (Información general).

14. En el lado derecho de la hoja de información general, seleccione **storagetesting-vnet/default** en el texto de Virtual network/subnet (Red virtual/subred).

15. En la hoja storagetesting-vnet, seleccione **Subnets** (Subredes), luego **+Subnet** (+Subred) y, después, en la hoja nueva Add Subnet (Agregar subred), escriba la siguiente información: 

    a.  **Name** (Nombre): subnet2

    b.  **Intervalo de direcciones (bloque CIDR)** : 10.10.11.0/24

    c. **Network Security Group** (Grupo de seguridad de red): None

    d.  **Tabla de rutas**: None

    e. Seleccione **OK** (Aceptar):

16. Una vez guardada, seleccione **VM001** en la ruta de navegación para volver a la hoja de información general.

17. Seleccionar **Redes**.

18. Seleccione **Attach network interface** (Asociar interfaz de red) y, después, seleccione **Create network interface** (Crear interfaz de red).

19. En la hoja **Create network interface** (Crear interfaz de red):

    a.  **Name** (Nombre): vm001nic2

    b.  **Subred**: Asegúrese de que la subred es 10.10.11.0/24

    c. **Grupo de seguridad de red**: VM001-nsg

    d.  **Resource group** (Grupo de recursos): storagetesting

20. Una vez creada correctamente, seleccione **VM001** en la ruta de navegación y seleccione **Stop** (Detener) para apagar la máquina virtual.

21. Una vez que la máquina virtual se detenga (desasignada), seleccione **Networking** (Redes), **Attach network interface** (Asociar interfaz de red) y, después, seleccione **vm001nic2** y **OK** (Aceptar). En unos momentos, agregará una tarjeta de interfaz de red adicional a la máquina virtual.

22. En la hoja **Networking** (Redes), seleccione la pestaña **vm001nic2** y, después, seleccione **Network Interface (Interfaz de red):vm001nic2**.

23. En la hoja de la interfaz de vm001nic, seleccione **IP configurations** (Configuraciones de IP) y, en el centro de la hoja, seleccione **ipconfig1**.

24. En la hoja configuración de ipconfig1, seleccione **Enabled** (Habilitada) en Public IP address (Dirección IP pública) y seleccione **Configure required settings** (Configurar los valores obligatorios), **Create new (Crear nuevo)** y escriba vm001nic2pip como nombre, seleccione **Static** (Estático), seleccione **OK** (Aceptar) y, después, **Save** (Guardar).

25. Una vez que se haya guardado correctamente, vuelva a la hoja de información general de VM001 y seleccione **Start** (Iniciar) para iniciar la máquina virtual de Windows Server 2019 configurada.

### <a name="configure-the-windows-server-2019-file-server-storage"></a>Configuración del almacenamiento del servidor de archivos de Windows Server 2019

En este primer ejemplo, va a validar una configuración en la que el servidor de archivos de Windows Server 2019 es una máquina virtual que se ejecuta en Hyper-V. Esta máquina virtual se configurará con ocho procesadores virtuales, un solo archivo VHDX y, lo que es más importante, dos adaptadores de red virtuales. En un escenario ideal, estos adaptadores de red tendrán diferentes subredes enrutables, pero en esta prueba tendrán adaptadores de red en la misma subred.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

En el caso de su servidor de archivos, podría ser Windows Server 2016 o 2019, físico o virtual, que se ejecuta en Hyper-V, VMware o cualquier otra plataforma que elija. Aquí hay que centrarse en la conectividad del sistema de Azure Stack; sin embargo, es preferible tener varias rutas de acceso entre el origen y el destino, ya que proporciona redundancia adicional y permite usar funcionalidades más avanzadas para mejorar el rendimiento, como el SMB multicanal.

Actualice el servidor de archivos con las actualizaciones acumulativas y correcciones más recientes, y reinicie el equipo antes de continuar con la configuración de los recursos compartidos de archivos.

Tras la actualización y reinicialización puede configurar este servidor como servidor de archivos.

1) En la máquina del servidor de archivos, ejecute **ipconfig/all** desde **CMD** y anote el **servidor DNS** que utiliza el servidor de archivos.

2)  Abra el **Administrador del servidor**, seleccione **Manage** (Administrar) y luego **Add Roles and Features** (Agregar roles y características).

3)  Seleccione **Next** (Siguiente), seleccione **Role-based or feature-based installation** (Instalación basada en características o en roles) y recorra las selecciones hasta que llegue a la página **Select server roles** (Seleccionar roles de servidor).

4)  Expanda **File and Storage Services** (Servicios de archivos y de almacenamiento), expanda **File & iSCSI Services** (Servicios de archivos y de iSCSI) y seleccione **File Server** (Servidor de archivos). Al finalizar, cierre el **Administrador del servidor**.

5)  Abra el **Administrador del servidor** y seleccione **File and Storage Services** (Servicios de archivos y almacenamiento).

6)  Select **Shares** (Recursos compartidos).

7)  En el **cuadro Shares** (Recursos compartidos), seleccione el vínculo **To create a file share, start the New Share Wizard** (Para crear un recurso compartido de archivos, inicie el Asistente para nuevo recurso compartido).

8)  En el cuadro **New Share Wizard** (Asistente para nuevo recurso compartido), seleccione **SMB Share – Quick** (Recurso compartido SMB - Rápido), luego **Next** (Siguiente), recorra el asistente y seleccione el **volumen C:\\\\** .

9)  Asigne un nombre al recurso compartido, **TestStorage**, y seleccione **Next** (Siguiente).

10) Vuelva al **New Share Wizard** (Asistente para nuevo recurso compartido), seleccione **Next** (Siguiente), luego **Create** (Crear) y, después, **Close** (Cerrar).

Ya ha creado un recurso compartido de archivos en el servidor de archivos.

### <a name="testing-file-storage-performance-and-connectivity"></a>Prueba del rendimiento y la conectividad del almacenamiento de archivos

Para comprobar la comunicación y ejecutar varias pruebas rudimentarias, inicie sesión en el portal de usuarios del sistema de **Azure Stack** y vaya a la hoja de **información general** de **VM001**.

1)  Seleccione **Connect** (Conectar) para establecer una conexión RDP en VM001.

2)  Para comunicarse mediante el nombre de host, va a editar el archivo **Hosts**; sin embargo, en un entorno de producción, DNS se configuraría de forma óptima, con el fin de que los nombres se resuelvan de forma automática. También puede usar direcciones IP. Sin embargo, en nuestro ejemplo, va a editar el archivo **Hosts**.

3)  Abra el **Bloc de notas** y asegúrese de que lo **ejecuta como administrador**.

4)  Una vez que el Bloc de notas esté abierto, seleccione **Archivo**, **Abrir**, vaya a C:\\Windows\System32\Drivers\etc\, y seleccione **Todos los archivos** en la esquina inferior derecha del cuadro de diálogo Abrir. Seleccione el archivo **hosts** y, después, **Abrir**.

5)  Edite el archivo hosts agregando una dirección IP y un nombre DNS para el servidor de archivos.

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

6)  Guarde el archivo y cierre el Bloc de notas.

7)  Abra **CMD** y haga ping al nombre del servidor de archivos que especificó en el archivo hosts. Debería devolver la dirección IP de uno de los adaptadores de red del servidor de archivos, así que pruebe manualmente la comunicación con el otro adaptador, para lo que debe hacer ping a la dirección IP.

## <a name="connect-to-a-windows-server-iscsi-target-server"></a>Conexión a un servidor de destino iSCSI de Windows Server

En este escenario, implementará y configurará una máquina virtual con Windows Server 2019 en Azure Stack y la preparará para conectarse a un servidor de destino iSCSI que también ejecutará Windows Server 2019.

> [!Note]  
> Si ya ha completado el escenario uno, personalice sus máquinas.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>Implementación de la máquina virtual con Windows Server 2019 en Azure Stack

Si aún no ha implementado la máquina virtual con Windows Server 2019 en Azure Stack, siga los pasos de [Implementación de la máquina virtual con Windows Server 2019 en Azure Stack](#deploy-the-windows-server-2019-vm-on-azure-stack). Luego puede configurar el destino iSCSI de Windows Server 2019.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Configuración del destino iSCSI de Windows Server 2019

Para este segundo escenario, valide una configuración en la que el destino iSCSI de Windows Server 2019 es una máquina virtual que se ejecuta en Hyper-V. Esta máquina virtual se configurará con ocho procesadores virtuales, un solo archivo VHDX y, lo que es más importante, dos adaptadores de red virtuales. En un escenario ideal, estos adaptadores de red tendrán diferentes subredes enrutables, pero en esta prueba tendrá adaptadores de red en la misma subred.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

En el caso de su destino iSCSI, podría ser Windows Server 2016 o 2019, físico o virtual, que se ejecuta en Hyper-V, VMware o cualquier otra plataforma que elija. Aquí hay que centrarse en la conectividad del sistema de Azure Stack; sin embargo, es preferible tener varias rutas de acceso entre el origen y el destino, ya que proporciona redundancia y rendimiento adicionales.

Actualice el servidor de archivos con las actualizaciones acumulativas y correcciones más recientes, y reinicie el equipo antes de continuar con la configuración del servidor de destino iSCSI.

Tras la actualización y reinicialización puede configurar este servidor como servidor de destino iSCSI.

1. Abra **Administrador del servidor** > **Administrar** > **Agregar roles y características**.

2. Una vez abierto, seleccione **Siguiente**, seleccione **Instalación basada en características o en roles** y recorra las selecciones hasta que llegue a la página **Seleccionar roles de servidor**.

3. Expanda **Servicios de archivos y almacenamiento**, expanda **File & iSCSI Services** (Servicios de archivos y de iSCSI) y seleccione el **Servidor del destino iSCSI**. Después, acepte las solicitudes para agregar nuevas características y continúe hasta terminar.

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)
    
    Al finalizar, cierre el **Administrador del servidor**.

4. Abra **Explorador de archivos**, vaya hasta `C:\\` y **cree una carpeta** denominada `iSCSI`.

5. Vuelva a abrir **Administrador del servidor** > **Servicios de archivos y almacenamiento** en el menú de la izquierda.

6. Seleccione **iSCSI** y seleccione la opción **Para crear un disco virtual iSCSI, inicie el Asistente para nuevo disco virtual**.

7. En la página **Seleccionar ubicación de disco virtual iSCSI**, seleccione **Type a custom path** (Escribir una ruta personalizada) y vaya a `C:\\iSCSI`. Seleccione **Next** (Siguiente).

8. Asigne al disco virtual de iSCSI el nombre de `iSCSIdisk1`, una descripción (opcional) y, a continuación, seleccione **Siguiente**.

9. Establezca el tamaño del disco virtual en `10GB`, seleccione **Tamaño fijo** y **Siguiente**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

10. Dado que se trata de un nuevo destino, seleccione **New iSCSI target** (Nuevo destino iSCSI) y seleccione **Siguiente**.

11. En la página **Especificar nombre de destino**, escriba **TARGET1** y seleccione **Siguiente**.

12. En la página **Especificar servidores de acceso**, seleccione **Agregar**. Se abre un cuadro de diálogo para especificar los **iniciadores** específicos que estarán autorizados a conectarse al destino iSCSI.

13. Seleccione **Enter a value for the selected type** (Escribir un valor para el tipo seleccionado) en la **ventana Agregar id. de iniciador** y, en **Tipo** asegúrese de que se ha seleccionado IQN en el menú desplegable. Escriba `iqn.1991-05.com.microsoft:<computername>` donde `<computername>` es el **nombre del equipo** de **VM001**. Seleccione **Next** (Siguiente).

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

14. En la página **Habilitar autenticación**, deje los cuadros en blanco y, a continuación, seleccione **Siguiente**.

15. Confirme las selecciones, elija **Crear** y, después, cierre.

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

El disco virtual iSCSI se debería haber creado en Administrador del servidor.

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>Configuración del iniciador iSCSI y E/S de múltiples rutas de Windows Server 2019

Para configurar el iniciador iSCSI, inicie sesión en el **portal de usuario** del sistema de **Azure Stack**, vaya a la hoja de **información general** y busque **VM001**.

1.  Establezca una conexión RDP a VM001. Una vez conectado, abra **Administrador del servidor**.

2.  Seleccione **Agregar roles y características** y acepte los valores predeterminados hasta llegar a la página **Características**.

3.  En la página **Características**, agregue **E/S de múltiples rutas** y seleccione **Siguiente**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

4.  Marque la casilla **Restart the destination server automatically if necessary** (Reiniciar el servidor de destino automáticamente si es necesario), seleccione **Instalar** y, a continuación, seleccione **Cerrar**. Lo más probable es que se necesite reiniciar, por lo que una vez que se haya completado, vuelva a conectarse a VM001.

5.  De nuevo en **Administrador del servidor**, espere a que se **complete la instalación de E/S de múltiples rutas**, seleccione **Cerrar**, **Herramientas** y seleccione **MPIO**.

6.  Seleccione la pestaña **Detectar múltiples rutas**, seleccione la casilla **Agregar compatibilidad con dispositivos iSCSI** > **Agregar** y, finalmente, **Sí** para **reiniciar** VM001. Seleccione **Aceptar** y, a continuación, reinicie manualmente.

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

1.  Una vez que se haya reiniciado, establezca una **nueva conexión RDP a VM001**.

2.  Una vez conectado, abra **Administrador del servidor**, seleccione **Herramientas** > **Iniciador iSCSI**.

3.  Seleccione **Sí** cuando se abra el iniciador iSCSI de Microsoft para permitir que el servicio iSCSI se ejecute de forma predeterminada.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

4.  Seleccione la pestaña **Detección**.

5.  Seleccione el botón **Discover Portal** (Detectar portal). Ahora agregará dos destinos.

6.  Escriba la primera dirección IP del servidor de destino iSCSI y seleccione **Opciones avanzadas**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7. Seleccione las siguientes **opciones avanzadas**:

    - Adaptador local: Iniciador iSCSI de Microsoft

    - Dirección IP del iniciador: 10.10.10.4

    - Una vez establecida, seleccione **Aceptar**.

8.  Seleccione **Aceptar** en **Detectar portal de destino**.

9.  Repita el proceso con la siguiente información:

    - Dirección IP: La segunda dirección IP de destino iSCSI

    - Adaptador local: Iniciador iSCSI de Microsoft

    - Dirección IP del iniciador: 10.10.11.4

10. Los portales de destino deben tener el siguiente aspecto, con sus propias direcciones IP de destinos iSCSI en la columna **Dirección**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

11. Seleccione la pestaña **Destinos** y, a continuación, seleccione el destino iSCSI. Seleccione **Conectar**.

12. Seleccione **Habilitar múltiples rutas** en **Conectarse al destino** y **Opciones avanzadas**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. En **Conectarse al destino**, escriba la siguiente información:

    - Adaptador local: Iniciador iSCSI de Microsoft

    - Dirección IP del iniciador: 10.10.10.4

    - Dirección IP del portal de destino: \<la primera dirección IP de destino iSCSI / 3260>

    - Seleccione **Aceptar**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

1.  Repita el proceso con la segunda combinación de iniciador y destino.

    - Adaptador local: Iniciador iSCSI de Microsoft

    - Dirección IP del iniciador: 10.10.11.4

    - Dirección IP del portal de destino: \<la segunda dirección IP de destino iSCSI / 3260>

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

1.  Seleccione la pestaña **Volúmenes y dispositivos** y después **Autoconfigurar**. Ahora, debería aparecer la presentación de un volumen de E/S de múltiples rutas:

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

2.  De nuevo en la pestaña **Destinos**, seleccione **Dispositivos** y deberían aparecer las dos conexiones al disco duro virtual sencillo de iSCSI que creó anteriormente.

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

3.  Seleccione el **botón MPIO** para ver más información sobre la directiva de equilibrio de carga y las rutas de acceso.

    ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

4.  Seleccione **Aceptar** tres veces para salir de las ventanas y del iniciador iSCSI.

5.  Abra Administración de discos (diskmgmt.msc) y aparecerá un mensaje con un menú emergente **Inicializar disco**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

6.  Seleccione **Aceptar** para aceptar los valores predeterminados, desplácese hacia abajo hasta el nuevo disco, haga clic con el botón derecho y seleccione **New Simple Volume** (Nuevo volumen sencillo).

7.  Siga el asistente aceptando los valores predeterminados. Cambie la etiqueta de volumen a **iSCSIdisk1** y, a continuación, seleccione **Finalizar**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

8.  A continuación, se da formato a la unidad y se presenta con una letra de unidad.

9.  Abra **Explorador de archivos** > **Este equipo** para ver la nueva unidad conectada a VM001.

### <a name="test-external-storage-connectivity"></a>Pruebas de conectividad con el almacenamiento externo

Para comprobar la comunicación y ejecutar una prueba de copia de archivos rudimentaria, inicie sesión en el **portal de usuarios** del sistema de **Azure Stack** y vaya a la hoja de **información general** de **VM001**.

1. Seleccione **Conectar** para establecer una conexión RDP con **VM001**.

2. Abra **Administrador de tareas** y, a continuación, seleccione **Rendimiento**. Ajuste la ventana en el lado derecho de la sesión de RDP.

3. Abra **Windows PowerShell ISE** como administrador y ajústelo en el lado izquierdo de la sesión de RDP. En el lado derecho del ISE, cierre el panel **Comandos** y seleccione el botón **Script** para expandir el panel de scripts en blanco en la parte superior de la ventana de ISE.

4. En esta máquina virtual no hay módulos nativos de PowerShell para crear un archivo VHD que se pueda usar como archivo grande para probar la transferencia de archivos al destino iSCSI. En este caso, ejecutará DiskPart para crear un archivo VHD. En el ISE, ejecute lo siguiente:

    1. `Start-Process Diskpart`

    2. Se abre un nuevo símbolo del sistema. Escriba el siguiente comando:<br>`Create vdisk file="c:\\test.vhd" type=fixed maximum=5120`

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

    Tardará unos minutos en crearse. Una vez creado, para validar la creación, abra **Explorador de archivos** y vaya a C:\\. Ahora, aparecerá el nuevo archivo test.vhd que tiene un tamaño de 5 GB.

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

    Cierre el símbolo del sistema. Vuelva al ISE y escriba el siguiente comando. Sustituya F:\\ por la letra de la unidad de destino iSCSI que se aplicó anteriormente.

    1. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    2. Seleccione la línea en el ISE. Presione **F8** en el teclado.

    3. Mientras se ejecuta el comando, vea los dos adaptadores de red y consulte la transferencia de datos que se está llevando a cabo en ambos adaptadores de red en VM001. También debe tener en cuenta que cada adaptador de red debe compartir la carga uniformemente.

        ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

Este escenario se diseñó para resaltar la conectividad entre una carga de trabajo en ejecución en Azure Stack y una matriz de almacenamiento externo, en este caso, un destino iSCSI basado en Windows Server. No se ha diseñado para ser una prueba de rendimiento, ni para reflejar los pasos que tendría que realizar si usara un dispositivo basado en iSCSI alternativo, pero resalta algunos de los aspectos principales que tendría que realizar al implementar cargas de trabajo en Azure Stack y conectarlas a sistemas de almacenamiento fuera del entorno de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack](azure-stack-network-differences.md)  