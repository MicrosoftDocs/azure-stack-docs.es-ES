---
title: Creación de un clúster de Azure Stack HCI mediante Windows Admin Center
description: Aprenda a administrar un clúster de servidor para Azure Stack HCI mediante Windows Admin Center.
author: v-dasis
ms.topic: how-to
ms.date: 12/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e33096b2667ad9d620e942b66934f341982e619b
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364224"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Creación de un clúster de Azure Stack HCI mediante Windows Admin Center

> Se aplica a Azure Stack HCl, versión v20H2

En este artículo, aprenderá a usar Windows Admin Center para crear un clúster de Azure Stack HCI que use Espacios de almacenamiento directo. El Asistente para crear clúster de Windows Admin Center hará la mayor parte del trabajo pesado. Si prefiere hacerlo con PowerShell, consulte [Creación de un clúster de Azure Stack HCl mediante PowerShell](create-cluster-powershell.md). El artículo de PowerShell también es una buena fuente de información sobre las operaciones en segundo plano del asistente y para solucionar problemas.

Tiene la opción de crear dos tipos de clúster:

- Clúster estándar con al menos dos nodos de servidor que residen en un único sitio.
- Clúster extendido con al menos cuatro nodos de servidor que abarcan dos sitios, con al menos dos nodos por sitio.

Para obtener más información sobre los clústeres extendidos, consulte [Información general sobre clústeres extendidos](../concepts/stretched-clusters.md).

Si está interesado en realizar pruebas de Azure Stack HCI, pero tiene hardware limitado o no tiene hardware de repuesto, consulte la [guía de evaluación de Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), donde le guiaremos a través de la experimentación de Azure Stack HCI mediante la virtualización anidada, ya sea en Azure o en un solo sistema físico local.

## <a name="before-you-run-the-wizard"></a>Antes ejecutar el asistente

Antes de ejecutar el Asistente para crear clúster, asegúrese de lo siguiente:

- Ha leído los requisitos de hardware y otros relacionados descritos en [Requisitos del sistema](../concepts/system-requirements.md).
- Ha leído los [requisitos de la red física](../concepts/physical-network-requirements.md) y los [requisitos de la red del host](../concepts/host-network-requirements.md) para Azure Stack HCI.
- Instale el sistema operativo de Azure Stack HCI en cada servidor del clúster. Consulte [Implementación del sistema operativo de Azure Stack HCI](operating-system.md).
- Use una cuenta que sea miembro del grupo de administradores local en cada servidor.
- Instale Windows Admin Center en un equipo o servidor de administración. Consulte [Instalación de Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install).
- En el caso de los clústeres extendidos, configure los dos sitios de antemano en Active Directory. Pero no se preocupe, el asistente también puede configurarlos.

Si va a ejecutar Windows Admin Center en un servidor (en lugar de en un equipo local), use una cuenta que sea miembro del grupo Administradores de puerta de enlace o el grupo Administradores locales en el servidor de Windows Admin Center.

Además, el equipo de administración de Windows Admin Center debe estar unido al mismo dominio de Active Directory en el que va a crear el clúster o a un dominio de plena confianza. Los servidores que se van a agrupar en clústeres aún no tienen que pertenecer al dominio. Se pueden agregar al dominio durante la creación del clúster.

Estos son los pasos principales del Asistente para crear clúster:

1. **Introducción**: garantiza que todos los servidores cumplen los requisitos previos y cuentan con las características que se necesitan para la combinación de clústeres.
1. **Redes**: asigna y configura los adaptadores de red y crea los conmutadores virtuales para cada servidor.
1. **Agrupación en clústeres**: valida que el clúster está configurado correctamente. En el caso de los clústeres extendidos, también configura los dos sitios.
1. **Almacenamiento**: configura Espacios de almacenamiento directo.

Una vez completado el asistente, configure el testigo del clúster, Regístrese en Azure y cree volúmenes (lo que también configura la replicación entre sitios si está creando un clúster extendido).

Antes de iniciar el asistente, asegúrese de tener instaladas las extensiones más recientes de Windows Admin Center, especialmente la extensión de creación de clústeres. Para ello:

1. Abra el centro de administración de Windows y haga clic en Settings (Configuración) (icono de engranaje) en la esquina superior derecha.
1. En **Configuración**, seleccione **Extensiones**.
1. Seleccione **Cluster Creation** (Creación de clústeres) y haga clic en **Install** (Instalar).
1. Seleccione **Cluster Manager** (Administrador de clústeres) y haga clic en **Install** (Instalar) también mientras está en él.

Ahora ya está listo, vamos a comenzar:

1. En Windows Admin Center, en **All connections** (Todas las conexiones), haga clic en **Add** (Agregar).
1. En el panel **Add or create resources** (Agregar o crear recursos), en **Server clusters** (Clústeres de servidores), seleccione **Create new** (Crear nuevo).
1. En **1. Choose cluster type** (Seleccionar tipo de clúster), seleccione **Azure Stack HCI**.

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Asistente para crear clúster: Opción de HCI" lightbox="media/cluster/create-cluster-type.png":::

1. En **Select server locations** (Seleccionar ubicaciones de servidor), seleccione una de las opciones siguientes:

    - **All servers in one site** (Todos los servidores de un sitio)
    - **Servers in two sites** (Servidores de dos sitios) (para un clúster extendido)

1. Cuando haya terminado, haga clic en **Crear**. Ahora verá el Asistente para crear clúster, como se muestra a continuación.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Asistente para crear clúster: Introducción" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Paso 1: Primeros pasos

El paso 1 del asistente le guía por los pasos necesarios para asegurarse de que se cumplen todos los requisitos previos, se agregan los nodos de servidor, se instalan las características necesarias y, si es necesario, se reinicia cada servidor.

1. Revise la sección **1.1 Comprobar los requisitos previos** que se muestran en el asistente para asegurarse de que cada nodo de servidor está listo para el clúster. Cuando termine, haga clic en **Siguiente**.
1. En **1.2 Agregar servidores**, escriba el nombre de usuario y la contraseña de su cuenta y, a continuación, haga clic en **Next** (Siguiente). Esta cuenta debe ser miembro del grupo de administradores local en cada servidor.
1. Escriba el nombre del primer servidor que quiera agregar y, a continuación, haga clic en **Add** (Agregar).
1. Repita el paso 3 para cada servidor que formará parte del clúster. Cuando termine, haga clic en **Siguiente**.
1. Si es necesario, en **1.3 Unirse a un dominio**, especifique el dominio al que desea unir los servidores y la cuenta que se va a usar. Si lo desea, puede cambiar el nombre de los servidores. A continuación, haga clic en **Siguiente**.
1. En **1.4 Instalar características**, revise y agregue características según sea necesario. Cuando termine, haga clic en **Siguiente**.

    El asistente instala las siguientes características necesarias:

    - BitLocker
    - Protocolo de puente del centro de datos (para adaptadores de red RoCEv2)
    - Clústeres de conmutación por error
    - Servidor de archivos
    - Módulo FS-Data-Deduplication
    - Hyper-V
    - Módulo RSAT-AD-PowerShell
    - Réplica de almacenamiento (se instala para clústeres extendidos)

1. En **1.5 Instalar actualizaciones**, haga clic en **Instalar actualizaciones** según sea necesario para instalar cualquier actualización del sistema operativo. Cuando haya terminado, haga clic en **Siguiente**.
1. En **1.6 Instalar actualizaciones de hardware**, haga clic en **Obtener actualizaciones** según sea necesario para obtener las actualizaciones de hardware disponibles del proveedor.
1. Siga los pasos específicos del proveedor para instalar las actualizaciones en el hardware. Estos pasos incluyen la realización de comprobaciones de simetría y cumplimiento en el hardware para garantizar una actualización correcta. Es posible que tenga que volver a ejecutar algunos pasos.
1. En **1.7 Reiniciar servidores**, haga clic en **Restart servers** (Reiniciar servidores) si es necesario. Compruebe que se ha iniciado correctamente cada servidor.

## <a name="step-2-networking"></a>Paso 2: Redes

El paso 2 del asistente le guía a través de la configuración de conmutadores virtuales, adaptadores de red y otros elementos de red para el clúster. Se admiten los adaptadores de red RDMA (iWARP y RoCE).

Para más información sobre RDMA y la red de hosts de Hyper-V para Azure Stack HCI, consulte [Requisitos de la red del host](../concepts/host-network-requirements.md).

> [!NOTE]
> Si surgen errores durante los pasos de conexión en redes o en los pasos relacionados con conmutadores virtuales, seleccione **Apply and test** (Aplicar y probar) de nuevo.

1. Seleccione **Siguiente: Redes**.
1. En **2.1 Comprobar los adaptadores de red**, espere a que aparezcan las casillas verdes junto a cada adaptador y, después, seleccione **Siguiente**.

1. En **2.2 Seleccionar adaptadores de administración**, seleccione uno o dos adaptadores de administración para usarlos para cada servidor. Es obligatorio seleccionar al menos uno de los adaptadores con fines de administración, ya que el asistente requiere al menos una NIC física dedicada para la administración del clúster.  Una vez que se designa un adaptador para la administración, se excluye del resto del flujo de trabajo del asistente.

    :::image type="content" source="media/cluster/create-cluster-management-adapters.png" alt-text="Asistente para crear clúster: Selección de adaptadores de administración" lightbox="media/cluster/create-cluster-management-adapters.png":::

    Los adaptadores de administración tienen dos opciones de configuración:

    - **Un adaptador de red físico para la administración**. Para esta opción, se admite DHCP o la asignación de direcciones IP estáticas.

    - **Dos adaptadores de red físicos en equipo para la administración**. Si se agrupa un par de adaptadores, solo se admite la asignación de direcciones IP estáticas. Si los adaptadores seleccionados usan direccionamiento de DHCP (para uno de ellos o para ambos), las direcciones IP de DHCP se convertirán en direcciones IP estáticas antes de la creación del conmutador virtual.

    Mediante el uso de adaptadores agrupados, tiene una única conexión a varios conmutadores, pero solo usa una dirección IP única. El equilibrio de carga está disponible y la tolerancia a errores es instantánea, en lugar de esperar a que se actualicen los registros DNS.

    Haga lo siguiente para cada adaptador:

    - Active la casilla **Description** (Descripción). Tenga en cuenta que todos los adaptadores están seleccionados y que el asistente puede ofrecerle una recomendación.
    - Desactive las casillas de los adaptadores que no quiera usar para la administración de clústeres.

    > [!NOTE]
    > Puede usar adaptadores de 1 GB como adaptadores de administración, pero se recomienda usar adaptadores de 10 GB o más rápidos para transportar el tráfico de almacenamiento y de carga de trabajo (VM).

1. Cuando se hayan realizado cambios, haga clic en **Apply and test** (Aplicar y probar).
1. En **Define networks** (Definir redes), asegúrese de que cada adaptador de red de cada servidor tiene una dirección IP estática única, una máscara de subred y un identificador de VLAN. Mantenga el mouse sobre cada elemento de tabla y escriba o cambie los valores según sea necesario. Cuando haya finalizado, haga clic en **Apply and test** (Aplicar y probar)

    > [!NOTE]
    > Para admitir la configuración del identificador de VLAN del clúster, todos los adaptadores de red de todos los servidores deben admitir la propiedad VLANID.

1. Espere a que la columna **Status** (Estado) muestre **Passed** (Superado) para cada servidor y, a continuación, haga clic en **Next** (Siguiente). Este paso comprueba la conectividad de red entre todos los adaptadores con la misma subred y el mismo identificador de VLAN. Las direcciones IP proporcionadas se transfieren desde el adaptador físico a los adaptadores virtuales una vez que se crean los conmutadores virtuales en el paso siguiente. La tarea puede tardar varios minutos en completarse, según el número de adaptadores configurados.

1. En **2.3 Conmutador virtual**, seleccione una de las siguientes opciones según corresponda. En función de cuántos adaptadores de red haya, puede que no todas las opciones estén disponibles:

    - **Omitir la creación de un conmutador virtual**: elija si desea configurar los conmutadores virtuales más adelante.
    - **Crear un conmutador virtual para usarlo para el almacenamiento y el proceso**: decida si desea usar el mismo conmutador virtual para las máquinas virtuales y para Espacios de almacenamiento directo. Esta es la opción "convergente".
    - **Crear un conmutador virtual para usarlo solo para el proceso**: elija si desea usar un conmutador virtual solo para las máquinas virtuales.
    - **Crear dos conmutadores virtuales**: elija si desea un conmutador virtual dedicado para las máquinas virtuales y otro para Espacios de almacenamiento directo.

        :::image type="content" source="media/cluster/create-cluster-virtual-switches.png" alt-text="Asistente para crear clúster: conmutadores virtuales" lightbox="media/cluster/create-cluster-virtual-switches.png":::

    > [!NOTE]
    > Si va a implementar Controladora de red para SDN (en **Paso 5: SDN** del asistente), necesitará un conmutador virtual. Por lo tanto, si decide no crear un conmutador virtual aquí y no crea uno fuera del asistente, el asistente no implementará Controladora de red.

    En la tabla siguiente se muestra qué configuraciones de conmutador virtual se admiten y están habilitadas para varias configuraciones de adaptador de red:

    | Opción | 1-2 adaptadores | 3 adaptadores o más | adaptadores agrupados |
    | :------------- | :--------- |:--------| :---------|
    | conmutador único (proceso y almacenamiento) | enabled | enabled  | no admitido |
    | conmutador único (solo proceso) | no admitido| enabled | enabled |
    | dos conmutadores | no admitido | enabled | enabled |

1. Cambie el nombre de un conmutador y otras opciones de configuración según sea necesario y, a continuación, haga clic en **Apply and test** (Aplicar y probar). La columna **Status** (Estado) debe mostrar **Passed** (Superado) para cada servidor una vez creados los conmutadores virtuales.

1. El paso **2.4 RDMA** es opcional. Si usa RDMA, active la casilla **Configure RDMA (Recommended)** [Configurar RDMA (recomendable)] y haga clic en **Siguiente**.

    :::image type="content" source="media/cluster/create-cluster-rdma.png" alt-text="Asistente para crear clúster: configuración de RDMA" lightbox="media/cluster/create-cluster-rdma.png":::

    Para más información acerca de cómo asignar reservas de ancho de banda, consulte la sección [Asignación de ancho de banda para el tráfico](../concepts/host-network-requirements.md#traffic-bandwidth-allocation) de [Requisitos de la red del host](../concepts/host-network-requirements.md).

1. Seleccione **Avanzado** y, a continuación, active la casilla **Data Center Bridging (DCB)** .

1. En **Cluster heartbeat** (Latido del clúster), asigne un nivel de prioridad y un porcentaje de reserva de ancho de banda.

1. En **Storage** (Almacenamiento), asigne un nivel de prioridad y un porcentaje de reserva de ancho de banda.

1. Cuando termine, seleccione **Apply changes** (Aplicar cambios) y haga clic en **Next** (Siguiente).

1. En **2.5 Definir redes**, revise y edite los campos de nombre, dirección IP, máscara de subred, identificador de VLAN y puerta de enlace predeterminada para cada adaptador mostrado.

    :::image type="content" source="media/cluster/create-cluster-define-networks.png" alt-text="Asistente para crear clúster: definición de redes" lightbox="media/cluster/create-cluster-define-networks.png":::

1. Cuando haya finalizado, haga clic en **Apply and test** (Aplicar y probar) Es posible que necesite **volver a intentar la prueba de conectividad** si el estado no es correcto para un adaptador.

## <a name="step-3-clustering"></a>Paso 3: Agrupación en clústeres

En el paso 3 del asistente, se asegura de que todo lo anterior se ha configurado correctamente, se configuran automáticamente dos sitios en el caso de las implementaciones de clústeres extendidos y, a continuación, se crea el clúster. También puede configurar los sitios de antemano en Active Directory.

1. Seleccione **Siguiente: Agrupación en clústeres**
1. En **3.1 Validar el clúster**, seleccione **Validate** (Validar). Es posible que la validación tarde varios minutos.

    Si aparece el mensaje emergente **Credential Security Service Provider (CredSSP)** (Proveedor del servicio de seguridad de credenciales [CredSSP]), seleccione **Yes** (Sí) para habilitar de forma temporal CredSSP para que el asistente pueda continuar. Una vez creado el clúster y finalizado el asistente, se deshabilitará CredSSP para aumentar la seguridad. Si tiene problemas con CredSSP, consulte [Solución de problemas de CredSSP](../manage/troubleshoot-credssp.md) para más información.

1. Revise todos los estados de validación, descargue el informe para obtener información detallada sobre los errores, realice cambios y, a continuación, haga clic en **Validate again** (Volver a validar) según sea necesario. También puede **descargar un informe**. Vuelva a repetir el proceso según sea necesario hasta que se superen todas las comprobaciones de validación. Cuando todo esté correcto, haga clic en **Next** (Siguiente).
1. En **3.2 Crear el cluster**, escriba un nombre para el clúster.

    :::image type="content" source="media/cluster/create-cluster.png" alt-text="Asistente para crear clúster: creación de clúster" lightbox="media/cluster/create-cluster.png":::

1. En **IP address** (Dirección IP), seleccione las direcciones IP dinámicas o estáticas que se usarán.
1. Seleccione **Advanced** (Avanzadas). Aquí tiene un par de opciones:

    - **Register the cluster with DNS and Active Directory** (Registrar el clúster con DNS y Active Directory)
    - **Add eligible storage to the cluster (recommended)** [Agregar almacenamiento apto para el clúster (recomendable)]

1. En **Networks** (Redes), seleccione si desea **Use all networks (recommended)** [Usar todas las redes (recomendable)] o **Specify one or more networks not to use** (Especificar una o varias redes que no desea utilizar).

1. Cuando haya terminado, haga clic en **Create cluster** (Crear clúster).

1. En el caso de los clústeres extendidos, en **3.3 Asignar servidores a sitios**, asigne un nombre a los dos sitios que se usarán.

1. A continuación, asigne cada servidor a un sitio. Configurará la replicación entre sitios más tarde. Cuando haya finalizado, haga clic en **Apply changes** (Aplicar cambios).

## <a name="step-4-storage"></a>Paso 4: Storage

El paso 4 del Asistente le guía a lo largo de la configuración de Espacios de almacenamiento directo para el clúster.

1. Seleccione **Siguiente: Storage** (Almacenamiento).
1. En **4.1 Limpiar unidades**, puede seleccionar opcionalmente **Erase drives** (Borrar unidades) si resulta adecuado para la implementación.
1. En **4.2 Comprobar las unidades**, haga clic en el icono **>** situado junto a cada servidor para comprobar que los discos funcionan y están conectados. Si todo es correcto, haga clic en **Next** (Siguiente).
1. En **4.3 Validar almacenamiento**, haga clic en **Next** (Siguiente).
1. Descargue y revise el informe de validación. Si todo es correcto, haga clic en **Next** (Siguiente). Si no es así, ejecute **Validate again** (Validar de nuevo).
1. En **4.4 Habilitar Espacios de almacenamiento directo**, haga clic en **Enable** (Habilitar).
1. Descargue y revise el informe. Si todo es correcto, haga clic en **Finish** (Finalizar). 
1. Seleccione **Go to connections list** (Ir a la lista de conexiones).
1. Después de unos minutos, debería ver el clúster en la lista. Selecciónelo para ver la página de información general del clúster.

El nombre del clúster puede tardar unos instantes en replicarse en el dominio, especialmente si los servidores del grupo de trabajo se han agregado recientemente a Active Directory. Aunque el clúster podría aparecer en Windows Admin Center, es posible que no esté disponible para conectarse todavía.

Si la resolución del clúster no se realiza correctamente después de un tiempo, en la mayoría de los casos, puede sustituir el nombre del servidor, en lugar del nombre del clúster.

## <a name="step-5-sdn-optional"></a>Paso 5: SDN (opcional)

Este paso opcional le guía a través de la configuración del componente de Controladora de red de [redes definidas por software (SDN)](../concepts/software-defined-networking.md). Una vez configurada la controladora de red, puede configurar otros componentes de SDN, como el equilibrador de carga por software (SLB) y la puerta de enlace de RAS, según sea necesario.

> [!NOTE]
> El asistente no admite la configuración de SLB y la puerta de enlace de RAS para SDN. Puede usar scripts de SDN Express para configurar estos componentes. Para obtener información sobre cómo hacerlo, consulte el [repositorio de GitHub de SDNExpress](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts).

> [!NOTE]
> SDN no se admite para los clústeres extendidos.

1. Seleccione **Siguiente: SDN**.

    :::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="Asistente de creación de clústeres: Controladora de red de SDN" lightbox="media/cluster/create-cluster-network-controller.png":::

1. En **5.1 Definir el clúster de Controladora de red**, en **Host**, escriba un nombre para Controladora de red. Este es el nombre DNS que usan los clientes de administración (como Windows Admin Center) para comunicarse con la controladora de red.
1. Especifique una ruta de acceso al archivo VHD de Azure Stack HCI. Use **Examinar** para encontrarlo más rápido.
1. Especifique el número de máquinas virtuales que se dedicarán a Controladora de red. Se recomiendan al menos tres máquinas virtuales para lograr una alta disponibilidad.
1. En **Network** (Red), escriba el identificador de VLAN de la red de administración. La controladora de red necesita conectividad con la misma red de administración que los hosts para comunicarse y configurar los hosts.

    > [!NOTE]
    > Las máquinas virtuales de Controladora de red usan el conmutador virtual que se usa para la administración de clústeres si está disponible; de lo contrario, usan el conmutador virtual "Compute" como el resto de las máquinas virtuales del clúster. Para más información, consulte la sección [Requisitos de Controladora de red](../concepts/network-controller.md#network-controller-requirements) en [Plan de implementación de Controladora de red](../concepts/network-controller.md).

1. En **VM network addressing** (Direcciones de red de VM), seleccione **DHCP** o **Estáticas**.
1. Si seleccionó **DHCP**, escriba el nombre de las máquinas virtuales de la controladora de red.
1. Si seleccionó **Static** (Estáticas), especifique lo siguiente:
    1. Dirección IP.
    1. Prefijo de la subred.
    1. Puerta de enlace predeterminada.
    1. Uno o varios servidores DNS. Haga clic en **Agregar** para agregar servidores DNS adicionales.
1. En **Credenciales**, escriba el nombre de usuario y la contraseña usados para unir las máquinas virtuales de Controladora de red al dominio del clúster.
1. Escriba la contraseña administrativa local para estas máquinas virtuales.
1. En **Avanzado**, escriba la ruta de acceso a las máquinas virtuales.
1. Escriba los valores de **inicio del grupo de direcciones MAC** y **final del grupo de direcciones MAC**.
1. Cuando termine, haga clic en **Siguiente**.
1. En **Deploy the Network Controller** (Implementar Controladora de red), espere hasta que el asistente complete su trabajo. Permanezca en esta página hasta que se completen todas las tareas en curso. Haga clic en **Finalizar**.

1. Una vez creadas las máquinas virtuales de la controladora de red, configure las actualizaciones de DNS dinámico para el nombre del clúster de la controladora de red en el servidor DNS. Para obtener información sobre cómo hacerlo, consulte [Configurar el registro de DNS dinámico para la controladora de red](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

1. Si se produce un error en la implementación de Controladora de red, haga lo siguiente antes de volver a intentarlo:

- Detenga y elimine todas las máquinas virtuales de Controladora de red que haya creado el asistente.  

- Limpie los puntos de montaje de todos los discos duros virtuales que haya creado el asistente.  

- Asegúrese de tener al menos 50-100 GB de espacio libre en los hosts de Hyper-V.  

## <a name="after-you-complete-the-wizard"></a>Después de finalizar el asistente

Una vez completado el asistente, todavía hay algunas tareas importantes que debe completar.

La primera tarea consiste en deshabilitar el protocolo de proveedor de compatibilidad para seguridad de credenciales (CredSSP) en cada servidor por motivos de seguridad. Recuerde que CredSSP debe estar habilitado para el asistente. Si tiene problemas con CredSSP, consulte [Solución de problemas de CredSSP](../manage/troubleshoot-credssp.md) para más información.

1. En Windows Admin Center, en **All connections** (Todas las conexiones), seleccione el clúster que acaba de crear.
1. En **Herramientas**, seleccione **Servidores**.
1. En el panel derecho, seleccione el primer servidor del clúster.
1. En **Información general**, seleccione **Disable CredSSP** (Deshabilitar CredSSP). Verá que el banner **CredSSP ENABLED** (CredSSP HABILITADO) de la parte superior desaparece.
1. Repita los pasos 3 y 4 para cada servidor del clúster.

Estas son las otras tareas que tendrá que hacer:

- Configurar un testigo del clúster. Consulte [Configuración de un testigo del clúster](witness.md).
- Crear los volúmenes. Consulte [Creación de volúmenes](../manage/create-volumes.md).
- Para los clústeres extendidos, cree sus volúmenes y configure la replicación. Consulte [Creación de volúmenes de clústeres extendidos y configuración de la replicación](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Pasos siguientes

- Registrar el clúster con Azure. Consulte [Administración del registro de Azure](../manage/manage-azure-registration.md).
- Realice una validación final del clúster. Consulte [Validación de un clúster de Azure Stack HCI](validate.md)
- Aprovisionar las VM. Consulte [Administración de máquinas virtuales en Azure Stack HCI mediante Windows Admin Center](../manage/vm.md).
- También puede implementar un clúster mediante PowerShell. Consulte [Creación de un clúster de Azure Stack HCI mediante PowerShell](create-cluster-powershell.md).
- También puede implementar Controladora de red mediante PowerShell. Consulte [Implementación de Controladora de red mediante PowerShell](network-controller-powershell.md).
