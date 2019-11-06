---
title: Ampliación del centro de datos en Azure Stack | Microsoft Docs
description: Aprenda a ampliar el centro de datos en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 94a9398a68be06d4735e2c082e8dc0a02281b6eb
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064970"
---
# <a name="how-to-extend-the-data-center-on-azure-stack"></a>Ampliación del centro de datos en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se ofrece información sobre la infraestructura de almacenamiento de Azure Stack, lo que le ayudará a decidir cómo integrar Azure Stack en un entorno de red existente. En Azure Stack, el espacio de almacenamiento es limitado. El almacenamiento se puede integrar con otro almacenamiento fuera de Azure Stack y en su centro de datos local. Después de explicar de forma general la ampliación del centro de datos, el artículo presenta dos escenarios diferentes. Puede conectarse a un servidor de almacenamiento de archivos de Windows. También puede conectarse a un servidor iSCSI de Windows.

## <a name="overview-of-extending-storage-to-azure-stack"></a>Introducción a la ampliación del almacenamiento a Azure Stack

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

13. En DNS name (Nombre de DNS), seleccione **Configure** (Configurar) y especifique la etiqueta de un nombre de DNS, **vm001**, y seleccione **Save** (Guardar) y, después, seleccione **VM001** en la ruta de navegación para volver a la hoja *Overview* (Información general).

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

4)  Expanda **File and Storage Services** (Servicios de archivos y de almacenamiento), expanda **File & iSCSI Services** (Servicios de archivos y de iSCSI) y seleccione el cuadro **File Server** (Servidor de archivos). Al finalizar, cierre el **Administrador del servidor**.

5)  Abra el **Administrador del servidor** y seleccione **File and Storage Services** (Servicios de archivos y almacenamiento).

6)  Select **Shares** (Recursos compartidos).

7)  En el **cuadro Shares** (Recursos compartidos), seleccione el vínculo **To create a file share, start the New Share Wizard** (Para crear un recurso compartido de archivos, inicie el Asistente para nuevo recurso compartido).

8)  En el cuadro **New Share Wizard** (Asistente para nuevo recurso compartido), seleccione **SMB Share – Quick** (Recurso compartido SMB - Rápido), luego **Next** (Siguiente), recorra el asistente y seleccione el **volumen C:\\\\** .

9)  Asigne un nombre al recurso compartido, **TestStorage**, y seleccione **Next** (Siguiente).

10) Vuelva al **New Share Wizard** (Asistente para nuevo recurso compartido), seleccione **Next** (Siguiente), luego **Create** (Crear) y, después, **Close** (Cerrar).

Ya ha creado un recurso compartido de archivos en el servidor de archivos.

### <a name="testing-file-storage-performance-and-connectivity"></a>Prueba del rendimiento y la conectividad del almacenamiento de archivos

Para comprobar la comunicación y ejecutar varias pruebas rudimentarias, vuelva a registrarse en el portal de usuarios del sistema de **Azure Stack** y vaya a la hoja de **información general** de **VM001**.

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

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack](azure-stack-network-differences.md)  