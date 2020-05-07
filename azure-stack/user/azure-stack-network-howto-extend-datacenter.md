---
title: Ampliación del centro de datos en Azure Stack Hub
description: Aprenda a ampliar el centro de datos en Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: 99a8425901213d50c17175ab946aeff78a5aa81d
ms.sourcegitcommit: 278aaeca069213a98b90751253f6b15423634849
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742595"
---
# <a name="extending-storage-to-azure-stack-hub"></a>Ampliación del almacenamiento a Azure Stack Hub

En este artículo se ofrece información sobre la infraestructura de almacenamiento de Azure Stack Hub que le ayudará a decidir cómo integrar Azure Stack Hub en un entorno de red existente. Después de explicar de forma general la ampliación del centro de datos, el artículo presenta dos escenarios diferentes. Puede conectarse a un servidor de almacenamiento de archivos de Windows. También puede conectarse a un servidor iSCSI de Windows.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>Introducción a la ampliación del almacenamiento en Azure Stack Hub

Hay escenarios en los que colocar los datos en la nube pública no es suficiente. Puede que tenga una carga de trabajo de una base de datos virtualizada de proceso intensivo que es sensible a las latencias y el tiempo de ida y vuelta a la nube pública podría afectar al rendimiento de dicha carga de trabajo. Es posible que haya datos locales, que se mantienen en un servidor de archivos, un NAS o una matriz de almacenamiento de iSCSI, a los que las cargas de trabajo locales deban acceder y que tengan que residir en el entorno local para cumplir objetivos normativos o de cumplimiento. Hay solo dos escenarios en los que el hecho de que los datos residan en el entorno local sigue siendo importante para muchas organizaciones.

Por tanto, ¿por qué no hospedar simplemente los datos en cuentas de almacenamiento de Azure Stack Hub o en servidores de archivos virtualizados que se ejecutan en el sistema de Azure Stack Hub? Bueno, a diferencia de Azure, el almacenamiento de Azure Stack Hub es finito. La capacidad que tiene disponible depende por completo de la capacidad por nodo que haya adquirido y del número de nodos del que disponga. Y como Azure Stack Hub es una solución hiperconvergente, si desea aumentar la capacidad de almacenamiento para satisfacer las demandas de uso, deberá aumentar también la superficie de proceso mediante la adición de nodos. El costo de esto podría ser prohibitivo, sobre todo si la necesidad de capacidad adicional es para almacenamiento en frío y archivado que se pudiera agregar a un precio barato fuera del sistema de Azure Stack Hub.

Lo cual le lleva al escenario del que se va a tratar a continuación. Cómo se pueden conectar sistemas de Azure Stack Hub y cargas de trabajo virtualizadas que se ejecutan en Azure Stack Hub, de forma sencilla y eficaz, con sistemas de almacenamiento fuera de Azure Stack Hub, accesibles a través de la red.

### <a name="design-for-extending-storage"></a>Diseño para extender el almacenamiento

En el diagrama se muestra un escenario en el que una sola máquina virtual, en la que se ejecuta una carga de trabajo, se conecta a un espacio de almacenamiento externo (a la máquina virtual y al propio Azure Stack Hub) y lo utiliza con fines de lectura y escritura de datos, etc. Este artículo se centrará en la recuperación simple de archivos, pero este ejemplo se puede ampliar a escenarios más complejos, como el almacenamiento remoto de archivos de base de datos.

![](./media/azure-stack-network-howto-extend-datacenter/azure-stack-network-howto-extend-datacenter-image1.svg)

En el diagrama, puede ver que la máquina virtual del sistema de Azure Stack Hub se ha implementado con varias tarjetas de interfaz de red. Para mejorar la redundancia es importante tener varias rutas entre el objetivo y el destino, pero también lo es como procedimiento recomendado de almacenamiento. En los entornos más complejos es donde las máquinas virtuales de Azure Stack Hub tienen direcciones IP públicas y privadas, al igual que en Azure. Si el almacenamiento externo necesitara acceder a la máquina virtual, solo puede hacerlo a través de la dirección IP pública, ya que las direcciones IP privadas se usan principalmente en los sistemas de Azure Stack Hub, en las redes virtuales y en las subredes. El almacenamiento externo no podrá comunicarse con el espacio de IP privadas de la máquina virtual, salvo que atraviese una VPN de sitio a sitio, para acceder a la propia red virtual. Por lo tanto, en este ejemplo, nos centraremos en la comunicación a través del espacio de direcciones IP públicas. Algo en lo que hay que fijarse en el espacio de direcciones IP públicas del diagrama es que hay dos subredes del grupo de direcciones IP públicas diferentes. De forma predeterminada, Azure Stack Hub requiere solo un grupo para la dirección IP pública, pero hay algo que se debe tener en cuenta para el enrutamiento redundante, que puede que haya que agregar un segundo grupo. Sin embargo, actualmente no se puede seleccionar una dirección IP de un grupo específico, por lo que puede acabar con máquinas virtuales con direcciones IP públicas del mismo grupo en varias tarjetas de red virtual.

En este artículo daremos por hecho que no se pasa por alto el enrutamiento entre los dispositivos del borde y el almacenamiento externo, y que el tráfico puede atravesar la red correctamente. En este ejemplo, no importa si la red troncal es de 1 GbE, 10 GbE, 25 GbE, o incluso más rápida, pero sería importante tenerlo en cuenta al planear la integración, con el fin de satisfacer las necesidades de rendimiento de las aplicaciones que acceden a este almacenamiento externo.

## <a name="connect-to-a-windows-server-iscsi-target"></a>Conexión a un destino iSCSI de Windows Server

En este escenario, implementaremos y configuraremos una máquina virtual con Windows Server 2019 en Azure Stack Hub y la prepararemos para conectarse a un destino iSCSI externo, en el que también se ejecutará Windows Server 2019. Cuando sea pertinente, habilitaremos algunas características clave, como MPIO, para optimizar el rendimiento y la conectividad entre la máquina virtual y el almacenamiento externo.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack-hub"></a>Implementación de la máquina virtual con Windows Server 2019 en Azure Stack Hub

1.  En el **portal del administrador de Azure Stack Hub**, suponiendo que el sistema se haya registrado correctamente y esté conectado a Marketplace, seleccione **Marketplace Management** (Administración de Marketplace) y, si aún no tiene una imagen de Windows Server 2019, seleccione **Add from Azure** (Agregar desde Azure), busque **Windows Server 2019** y agregue la imagen de **Windows Server 2019 Datacenter**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    La imagen de Windows Server 2019 puede tardar un tiempo en descargarse.

2.  Una vez que tenga una imagen de Windows Server 2019 en el entorno de Azure Stack Hub, **inicie sesión en el portal de usuarios de Azure Stack Hub**.

3.  Una vez que haya iniciado sesión en el portal de usuarios de Azure Stack Hub, asegúrese de que tiene una [suscripción a una oferta](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908), lo que le permite aprovisionar recursos de IaaS (Compute, Storage y Network).

4.  Una vez que tenga una suscripción disponible, vuelva al **panel** del portal de usuarios de Azure Stack Hub, seleccione **Create a resource** (Crear un recurso), seleccione **Compute** y, luego, seleccione el **elemento de la galería Windows Server 2019 Datacenter**.

5.  En la hoja **Basics** (Datos básicos), complete la información como se indica a continuación:

    a.  **Name**: VM001

    b.  **Username** (Nombre de usuario): localadmin

    c.  **Password** (Contraseña) y **Confirm password** (Confirmar contraseña): \<la contraseña que prefiera>

    d.  **Subscription** (Suscripción): \<la suscripción que prefiera, con los recursos compute/storage/network>.

    e.  **Resource group** (Grupo de recursos): storagetesting (crear nuevo)

    f.  Seleccione **Aceptar**.

6.  En la hoja **Choose a size** (Elegir un tamaño), seleccione **Standard_F8s_v2** y, después, **Select** (Seleccionar).

7.  En la hoja **Settings** (Configurar), seleccione la **red virtual** y, en la hoja **Create virtual network** (Crear red virtual), ajuste el espacio de direcciones para que sea **red 10.10.10.0/23** y actualice el intervalo de direcciones de subred para que sea **10.10.10.0/24** y seleccione **OK** (Aceptar).

8.  Seleccione la **dirección IP pública** y en la hoja **Create public IP address** (Crear dirección IP pública ), seleccione el botón de radio **Static** (Estática).

9.  En **Select public inbound ports** (Seleccionar puertos de entrada públicos), seleccione **RDP (3389)** .

10. Deje los restantes valores predeterminados y seleccione **OK** (Aceptar).

    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Lea el resumen, espere la validación y seleccione **OK** (Aceptar) para comenzar la implementación. La implementación debería finalizar en unos 10 minutos.

12. Una vez finalizada la implementación, en **Resource** (Recurso) seleccione el nombre de la máquina virtual, **VM001**, para abrir **Overview** (Información general).

    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. En DNS name (Nombre de DNS), seleccione **Configure** (Configurar) y especifique la etiqueta de un nombre DNS, **vm001**, seleccione **Save** (Guardar) y, después, seleccione **VM001**.

14. En el lado derecho de la hoja de información general, seleccione **storagetesting-vnet/default** en el texto de Virtual network/subnet (Red virtual/subred).

15. En la hoja storagetesting-vnet, seleccione **Subnets** (Subredes), luego **+Subnet** (+Subred) y, después, en la hoja nueva Add Subnet (Agregar subred), escriba la siguiente información y seleccione **OK** (Aceptar):

    a.  **Name** (Nombre): subnet2

    b.  **Intervalo de direcciones (bloque CIDR)** : 10.10.11.0/24

    c.  **Network Security Group** (Grupo de seguridad de red): None

    d.  **Tabla de rutas**: None

16. Una vez que se guarde, seleccione **VM001**.

17. En el lado izquierdo de la hoja de información general, seleccione **Networking** (Redes).

18. Seleccione **Attach network interface** (Asociar interfaz de red) y, después, seleccione **Create network interface** (Crear interfaz de red).

19. En la hoja **Create network interface** (Crear interfaz de red), escriba la siguiente información.

    a.  **Name** (Nombre): vm001nic2

    b.  **Subred**: Asegúrese de que la subred es 10.10.11.0/24

    c.  **Grupo de seguridad de red**: VM001-nsg

    d. **Resource group** (Grupo de recursos): storagetesting

20. Una vez que se haya asociado correctamente, seleccione **VM001** y, después, **Stop** (Detener) para apagar la máquina virtual.

21. Una vez que la máquina virtual se detenga (se desasigne), a la izquierda de la hoja de información general, seleccione **Networking** (Redes), **Attach network interface** (Asociar interfaz de red), después seleccione **vm001nic2** y **OK** (Aceptar). La tarjeta de interfaz de red adicional se agregará a la máquina virtual en unos instantes.

22. En la hoja **Networking** (Redes), seleccione la pestaña **vm001nic2** y, después, seleccione **Network Interface (Interfaz de red):vm001nic2**.

23. En la hoja de la interfaz de vm001nic, seleccione **IP configurations** (Configuraciones de IP) y, en el centro de la hoja, seleccione **ipconfig1**.

24. En la hoja configuración de ipconfig1, seleccione **Enabled** (Habilitada) en Public IP address (Dirección IP pública) y seleccione **Configure required settings** (Configurar los valores obligatorios), **Create new (Crear nuevo)** y escriba vm001nic2pip como nombre, seleccione **Static** (Estático), seleccione **OK** (Aceptar) y, después, **Save** (Guardar).

25. Una vez que se haya guardado correctamente, vuelva a la hoja de información general de VM001 y seleccione **Start** (Iniciar) para iniciar la máquina virtual de Windows Server 2019 configurada.

26. Una vez que se haya iniciado, **establezca una sesión de RDP** en VM001.

27. Una vez que se establezca la conexión dentro de la máquina virtual, abra una **ventana de línea de comandos** (como administrador) y escriba **hostname** para recuperar el nombre de equipo del sistema operativo. **Debe coincidir con VM001**. Anótelo para más adelante.

### <a name="configure-second-network-adapter-on-windows-server-2019-vm-on-azure-stack-hub"></a>Configuración de un segundo adaptador de red en la máquina virtual con Windows Server 2019 en Azure Stack Hub

De manera predeterminada, Azure Stack Hub asigna una puerta de enlace predeterminada a la primera interfaz de red (principal) conectada a la máquina virtual. Azure Stack Hub no asigna una puerta de enlace predeterminada a las interfaces de red adicionales (secundarias) conectadas a una máquina virtual. Por lo tanto, de manera predeterminada, no es posible comunicarse con recursos externos a la subred en la que se encuentra una interfaz de red secundaria. Sin embargo, las interfaces de red secundarias pueden comunicarse con recursos externos a su subred, aunque los pasos necesarios para habilitar la comunicación son diferentes para los distintos sistemas operativos.

1.  Si aún no tiene abierta una conexión, establezca una conexión de RDP en **VM001**.

2.  Abra la **ventana de línea de comandos** como administrador y ejecute **route print**, que debe devolver las dos interfaces (adaptadores de red de Hyper-V) dentro de esta máquina virtual.

    ![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

3.  Ahora ejecute **ipconfig** para ver la dirección IP que se asigna a la interfaz de red secundaria. En este ejemplo, la dirección IP 10.10.11.4 se asigna a la interfaz 6. No se devuelve ninguna dirección de puerta de enlace predeterminada para la interfaz de red secundaria.

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

4.  Para que todo el tráfico destinado a direcciones que están fuera de la subred de la interfaz de red secundaria se enrute a la puerta de enlace de la subred, ejecute el siguiente comando en la **ventana de línea de comandos:** .

    ```CMD  
    route add -p 0.0.0.0 MASK 0.0.0.0 <ipaddress> METRIC 5015 IF <interface>
    ```

    `<ipaddress>` es la dirección .1 de la subred actual y `<interface>` es el número de la interfaz.

    ![](./media/azure-stack-network-howto-extend-datacenter/image7.png)

5.  Para confirmar que la ruta agregada se encuentra en la tabla de rutas, escriba el comando **route print**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)

6.  La comunicación saliente también se puede validar mediante la ejecución de un comando ping:  
    `ping 8.8.8.8 -S 10.10.11.4`  
    La marca `-S` permite especificar una dirección de origen; en este caso, 10.10.11.4 es la dirección IP de la tarjeta de interfaz de red que ahora tiene una puerta de enlace predeterminada.

7.  Cierre la **ventana de la línea de comandos**.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Configuración del destino iSCSI de Windows Server 2019

Para este escenario, va a validar una configuración en la que el destino iSCSI de Windows Server 2019 es una máquina virtual que se ejecuta en Hyper-V, fuera del entorno de Azure Stack Hub. Esta máquina virtual se configurará con ocho procesadores virtuales, un solo archivo VHDX y, lo que es más importante, dos adaptadores de red virtual. En un escenario ideal, estos adaptadores de red tendrán diferentes subredes enrutables, pero en esta validación tendrán adaptadores de red en la misma subred.

![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

En el caso de su servidor de destino iSCSI, puede ser Windows Server 2016 o 2019, físico o virtual, que se ejecuta en Hyper-V, VMware o cualquier otro dispositivo que elija, como una SAN iSCSI física dedicada. Aquí hay que centrarse en la conectividad del sistema de Azure Stack Hub; sin embargo, es preferible tener varias rutas de acceso entre el origen y el destino, ya que eso proporciona más redundancia y permite usar funcionalidades más avanzadas, como MPIO, que mejoran el rendimiento.

Le animamos a que actualice su destino iSCSI de Windows Server 2019 con las actualizaciones acumulativas y correcciones más recientes, y reinicie el equipo si fuera necesario antes de continuar con la configuración de los recursos compartidos de archivos.

Tras la actualización y reinicialización puede configurar este servidor como destino iSCSI.

1.  Abra el **Administrador del servidor**, seleccione **Manage** (Administrar) y luego **Add Roles and Features** (Agregar roles y características).

2.  Una vez abierto, seleccione **Siguiente**, seleccione **Instalación basada en características o en roles** y recorra las distintas opciones hasta que llegue a la página **Seleccionar roles de servidor**.

3.  Expanda **Servicios de archivos y almacenamiento**, expanda **Servicios de iSCSI y archivo**, seleccione la casilla **Servidor del destino iSCSI**, acepte las solicitudes emergentes para agregar nuevas características y continúe hasta terminar.

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

    Al finalizar, cierre el **Administrador del servidor**.

4.  Abra el **Explorador de archivos**, vaya a C:\\ y **cree una carpeta** denominada **iSCSI**.

5.  Vuelva a abrir el **Administrador del servidor** y seleccione **File and Storage Services** (Servicios de archivos y almacenamiento) en el menú de la izquierda.

6.  Seleccione **iSCSI** y seleccione el vínculo "**Para crear un disco virtual iSCSI, inicie el Asistente para nuevo disco virtual**". Selecciónelo. Se abre un asistente.

7.  En la página **Seleccionar ubicación de disco virtual iSCSI**, seleccione el botón de radio **Escribir una ruta personalizada**, vaya a **C:\\iSCSI** y seleccione **Siguiente**.

8.  Asigne al disco virtual de iSCSI el nombre **iSCSIdisk1** y, opcionalmente, una descripción y, después, seleccione **Siguiente**.

9.  Establezca el tamaño del disco virtual en **10 GB**, seleccione **Tamaño fijo** y seleccione **Siguiente**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

10) Dado que se trata de un nuevo destino, seleccione **New iSCSI target** (Nuevo destino iSCSI) y seleccione **Siguiente**.

11) En la página **Especificar nombre de destino**, escriba **TARGET1** y seleccione **Siguiente**.

12) En la página **Especificar servidores de acceso**, seleccione **Agregar**. Se abre un cuadro de diálogo para especificar los **iniciadores** específicos que estarán autorizados a conectarse al destino iSCSI.

13) En la **ventana Agregar id. de iniciador**, seleccione **Enter a value for the selected type** (Escribir un valor para el tipo seleccionado) y en **Type** (Tipo) asegúrese de que IQN está seleccionado en el menú desplegable. Escriba **iqn.1991-05.com.microsoft:\<computername>** donde\<computername> es el **nombre de equipo** de **VM001** y seleccione **Siguiente**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

14) En la página **Habilitar autenticación**, deje los cuadros en blanco y, a continuación, seleccione **Siguiente**.

15) Confirme las selecciones, elija **Crear** y, después, cierre. El disco virtual iSCSI se debería haber creado en Administrador del servidor.

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>Configuración del iniciador iSCSI y E/S de múltiples rutas de Windows Server 2019

Para configurar el iniciador iSCSI, en primer lugar, vuelva a iniciar sesión en el **portal de usuarios de Azure Stack Hub** del sistema de **Azure Stack Hub** y vaya a la hoja de **información general** para buscar **VM001.**

1.  Establezca una conexión RDP a VM001. Una vez conectado, abra **Administrador del servidor**.

2.  Seleccione **Agregar roles y características** y acepte los valores predeterminados hasta llegar a la página **Características**.

3.  En la página **Características**, agregue **E/S de múltiples rutas** y seleccione **Siguiente**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image14.png)

4.  Marque la casilla **Reiniciar automáticamente el servidor de destino en caso necesario**, seleccione **Instalar** y, después, seleccione **Cerrar**. Lo más probable es que se necesite reiniciar, por lo que una vez que se haya completado, vuelva a conectarse a VM001.

5.  De nuevo en **Administrador del servidor**, espere a que se **complete la instalación de E/S de múltiples rutas**, seleccione **Cerrar**, **Herramientas** y seleccione **MPIO**.

6.  Seleccione la pestaña **Detectar múltiples rutas**, seleccione la casilla **Agregar compatibilidad con dispositivos iSCSI** y seleccione **Agregar** y, finalmente, seleccione **Sí** para **reiniciar** VM001. Si no recibe una ventana, seleccione **Aceptar** y reinicie el equipo manualmente.

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7.  Una vez que se haya reiniciado, establezca una **nueva conexión RDP a VM001**.

8.  Una vez conectado, abra el **Administrador del servidor**, seleccione **Herramientas** y, después, **Iniciador iSCSI**.

9.  Cuando se abra una ventana iSCSI de Microsoft, seleccione **Sí** para permitir que el servicio iSCSI se ejecute de forma predeterminada.

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

10. En la ventana de propiedades del iniciador iSCSI, seleccione la pestaña **Detección**.

11. Ahora agregará dos destinos, así que lo que primero que debe hacer es seleccionar el botón **Detectar portal**.

12. Escriba la primera dirección IP del servidor de destino iSCSI y seleccione **Opciones avanzadas**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. En la ventana **Configuración avanzada**, seleccione lo siguiente y, después, seleccione **Aceptar**.

    a.  **Adaptador local**: Iniciador iSCSI de Microsoft.

    b.  **IP del iniciador**: 10.10.10.4.

14. Vuelva a la ventana **Detectar portal de destino**, seleccione **Aceptar**.

15. Repita el proceso con la siguiente información:

    a. ** Dirección IP**: la segunda IP del destino iSCSI.

    b.  **Adaptador local**: Iniciador iSCSI de Microsoft.

    c.  **IP del iniciador**: 10.10.11.4.

16. Los portales de destino deben tener el siguiente aspecto, con sus propias direcciones IP de destinos iSCSI en la columna **Dirección**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

17. Vuelva a la pestaña **Destinos**, seleccione el destino iSCSI en el centro de la ventana y seleccione **Conectar**.

18. En el cuadro de diálogo **Conectarse al destino**, active la casilla **Habilitar múltiples rutas** y, luego, seleccione **Avanzadas**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

19. Escriba la siguiente información y seleccione **Aceptar** y, después, en la ventana **Conectarse al destino**, seleccione **Aceptar**.

    a.  **Adaptador local**: Iniciador iSCSI de Microsoft.

    b.  **IP del iniciador**: 10.10.10.4.

    c.  **IP del portal de destino**: \<la primera IP del destino iSCSI/3260>.

![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

1.  Repita el proceso con la segunda combinación de iniciador y destino.

    a. ** Adaptador local**: Iniciador iSCSI de Microsoft.

    b.  **IP del iniciador**: 10.10.11.4.

    c.  **IP del portal de destino:** : \<la segunda IP del destino iSCSI/3260>.

        ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

2.  Seleccione la pestaña **Volúmenes y dispositivos** y después **Autoconfigurar**. Ahora, debería aparecer la presentación de un volumen de E/S de múltiples rutas:

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

3.  Vuelva a la pestaña **Destinos** y seleccione **Dispositivos**, y debería ver dos conexiones al disco duro virtual de iSCSI individual que creó anteriormente.

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

4.  Seleccione el **botón MPIO** para ver más información sobre la directiva de equilibrio de carga y las rutas de acceso.

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

5.  Seleccione **Aceptar** tres veces para salir de las ventanas y del iniciador iSCSI.

6.  Abra Administración de discos (diskmgmt.msc) y aparecerá un mensaje con la ventana **Inicializar disco**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

7.  Seleccione **Aceptar** para aceptar los valores predeterminados, desplácese hacia abajo hasta el nuevo disco, haga clic con el botón derecho y seleccione **Nuevo volumen simple**.

8.  Siga el asistente aceptando los valores predeterminados. Cambie la etiqueta de volumen a **iSCSIdisk1** y, a continuación, seleccione **Finalizar**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

9.  A continuación, se da formato a la unidad y se presenta con una letra de unidad.

10. Abra el **Explorador de archivos** y seleccione **Este equipo** para ver la nueva unidad conectada a VM001.

### <a name="testing-external-storage-connectivity"></a>Pruebas de conectividad del almacenamiento externo

Para validar la comunicación y ejecutar una prueba de copia de archivos rudimentaria, en primer lugar, vuelva a iniciar sesión en el **portal de usuarios de Azure Stack Hub** en el sistema de **Azure Stack Hub** y vaya a la hoja de **información general** de **VM001**

1.  Seleccione **Conectar** para establecer una conexión RDP con **VM001**

2.  Abra el **Administrador de tareas** seleccione la pestaña **Rendimiento** y, después, ajuste la ventana a la parte derecha de la sesión de RDP.

3.  Abra **Windows PowerShell ISE** como administrador y ajústelo en el lado izquierdo de la sesión de RDP. En el lado derecho del ISE, cierre el panel **Comandos** y seleccione el botón **Script** para expandir el panel de scripts en blanco en la parte superior de la ventana de ISE.

4.  En esta máquina virtual no hay módulos nativos de PowerShell para crear un disco duro virtual, que usaremos como archivo grande para probar la transferencia de archivos al destino iSCSI. En este caso, ejecutaremos DiskPart para crear un archivo VHD. En el ISE, ejecute lo siguiente:

    1. `Start-Process Diskpart`

    2. Se abrirá una nueva ventana de CMD y, después, escriba:  
        `**Create vdisk file="c:\\test.vhd" type=fixed maximum=5120**`
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image27.png)
    
    3.  Tardará unos minutos en crearse. Una vez creado, para validar la creación, abra el **Explorador de archivos** y vaya a C:\\. Debería ver el nuevo archivo test.vhd, que tiene un tamaño de 5 GB.

    ![](./media/azure-stack-network-howto-extend-datacenter/image28.png)

    4. Cierre la ventana de CMD y vuelva al ISE y, después, escriba el siguiente comando en la ventana del script. Sustituya F:\\ por la letra de la unidad de destino iSCSI que se aplicó anteriormente.

    5. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    6. Seleccione la línea en la ventana de script y presione F8 para ejecutarla.

    7. Mientras se ejecuta el comando, observe los dos adaptadores de red y consulte la transferencia de datos que se realiza en los dos adaptadores de red en VM001. También debe tener en cuenta que cada adaptador de red debe compartir la carga uniformemente.

    ![](./media/azure-stack-network-howto-extend-datacenter/image29.png)

Este escenario se diseñó para resaltar la conectividad entre una carga de trabajo en ejecución en Azure Stack Hub y una matriz de almacenamiento externo, en este caso, un destino iSCSI basado en Windows Server. No se ha diseñado para ser una prueba de rendimiento, ni para reflejar los pasos que tendría que realizar si usara otro dispositivo basado en iSCSI, pero resalta algunos de los aspectos principales que tendría que realizar al implementar cargas de trabajo en Azure Stack Hub y conectarlas a sistemas de almacenamiento fuera del entorno de Azure Stack Hub.

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md)
