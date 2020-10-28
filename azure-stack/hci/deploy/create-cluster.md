---
title: Creación de un clúster de Azure Stack HCI mediante Windows Admin Center
description: Aprenda a administrar un clúster de servidor para Azure Stack HCI mediante Windows Admin Center.
author: v-dasis
ms.topic: how-to
ms.date: 10/17/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 927a57097eff9890dc8c546be9914e70dad5ec3c
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2020
ms.locfileid: "92179539"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Creación de un clúster de Azure Stack HCI mediante Windows Admin Center

> Se aplica a Azure Stack HCl, versión v20H2

En este artículo, aprenderá a usar Windows Admin Center para crear un clúster hiperconvergido de Azure Stack HCI que use Espacios de almacenamiento directo. El Asistente para crear clúster de Windows Admin Center hará la mayor parte del trabajo pesado. Si prefiere hacerlo con PowerShell, consulte [Creación de un clúster de Azure Stack HCl mediante PowerShell](create-cluster-powershell.md). El artículo de PowerShell también es una buena fuente de información sobre las operaciones en segundo plano del asistente y para solucionar problemas.

Tiene la opción de crear dos tipos de clúster:

- Clúster estándar con al menos dos nodos de servidor que residen en un único sitio.
- Clúster extendido con al menos cuatro nodos de servidor que abarcan dos sitios, con al menos dos nodos por sitio.

Para obtener más información sobre los clústeres extendidos, consulte [Información general sobre clústeres extendidos](../concepts/stretched-clusters.md).

Si está interesado en realizar pruebas de Azure Stack HCI, pero tiene hardware limitado o no tiene hardware de repuesto, consulte la [guía de evaluación de Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), donde le guiaremos a través de la experimentación de Azure Stack HCI mediante la virtualización anidada, ya sea en Azure o en un solo sistema físico local.

## <a name="before-you-run-the-wizard"></a>Antes ejecutar el asistente

Antes de ejecutar el Asistente para crear clúster, asegúrese de lo siguiente:

- Ha leído los requisitos de hardware y de otro tipo en [Antes de implementar Azure Stack HCl](before-you-start.md).
- Instale el sistema operativo de Azure Stack HCI en cada servidor del clúster. Consulte [Implementación del sistema operativo de Azure Stack HCI](operating-system.md).
- Use una cuenta que sea miembro del grupo de administradores local en cada servidor.
- Instale Windows Admin Center en un equipo o servidor de administración. Consulte [Instalación de Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install).
- En el caso de los clústeres extendidos, configure los dos sitios de antemano en Active Directory. Pero no se preocupe, el asistente también puede configurarlos.

Si va a ejecutar Windows Admin Center en un servidor (en lugar de en un equipo local), use una cuenta que sea miembro del grupo Administradores de puerta de enlace o el grupo Administradores locales en el servidor de Windows Admin Center.

Además, el equipo de administración de Windows Admin Center debe estar unido al mismo dominio de Active Directory en el que va a crear el clúster o a un dominio de plena confianza. Los servidores que se van a agrupar en clústeres aún no tienen que pertenecer al dominio. Se pueden agregar al dominio durante la creación del clúster.

Estos son los pasos principales del Asistente para crear clúster:

1. **Introducción** : garantiza que todos los servidores cumplen los requisitos previos y cuentan con las características que se necesitan para la combinación de clústeres.
1. **Redes** : asigna y configura los adaptadores de red y crea los conmutadores virtuales para cada servidor.
1. **Agrupación en clústeres** : valida que el clúster está configurado correctamente. En el caso de los clústeres extendidos, también configura los dos sitios.
1. **Almacenamiento** : configura Espacios de almacenamiento directo.

Una vez completado el asistente, configure el testigo del clúster, Regístrese en Azure y cree volúmenes (lo que también configura la replicación entre sitios si está creando un clúster extendido).

Empecemos:

1. En Windows Admin Center, en **All connections** (Todas las conexiones), haga clic en **Add** (Agregar).
1. En el panel **Add resources** (Agregar recursos), en **Clúster de Windows Server** (Windows Server cluster), seleccione **Create new** (Crear nuevo).
1. En **Choose cluster type** (Seleccionar tipo de clúster), seleccione **Azure Stack HCI** .

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Asistente para crear clúster: Opción de HCI" lightbox="media/cluster/create-cluster-type.png":::

1. En **Select server locations** (Seleccionar ubicaciones de servidor), seleccione una de las opciones siguientes:

    - **All servers in one site** (Todos los servidores de un sitio)
    - **Servers in two sites** (Servidores de dos sitios) (para un clúster extendido)

1. Cuando haya terminado, haga clic en **Crear** . Ahora verá el Asistente para crear clúster, como se muestra a continuación.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Asistente para crear clúster: Opción de HCI" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Paso 1: Primeros pasos

El paso 1 del asistente le guía por los pasos necesarios para asegurarse de que se cumplen todos los requisitos previos, se agregan los nodos de servidor, se instalan las características necesarias y, si es necesario, se reinicia cada servidor.

1. Revise los requisitos previos enumerados en el asistente para asegurarse de que cada nodo de servidor está listo para el clúster. Cuando termine, haga clic en **Siguiente** .
1. En la página **Add servers to the cluster** (Agregar servidores al clúster), escriba el nombre de usuario y la contraseña de su cuenta y, a continuación, haga clic en **Next** (Siguiente). Esta cuenta debe ser miembro del grupo de administradores local en cada servidor.
1. Escriba el nombre del primer servidor que quiera agregar y, a continuación, haga clic en **Add** (Agregar).
1. Repita el paso 3 para cada servidor que formará parte del clúster. Cuando termine, haga clic en **Siguiente** .
1. Si es necesario, en la página **Join the servers to a domain** (Unir los servidores a un dominio), especifique el dominio y una cuenta para unir los servidores al dominio. Después, puede cambiar el nombre de los servidores a nombres más descriptivos y hacer clic en **Next** (Siguiente).
1. Haga clic en **Install Features** (Instalar características). Cuando termine, haga clic en **Siguiente** .

    El asistente instala las siguientes características necesarias:

    - BitLocker
    - Protocolo de puente del centro de datos (para adaptadores de red RoCEv2)
    - Clústeres de conmutación por error
    - Servidor de archivos
    - Módulo FS-Data-Deduplication
    - Hyper-V
    - Módulo RSAT-AD-PowerShell
    - Réplica de almacenamiento (solo se instala para clústeres extendidos)

1. En **Install updates** (Instalar actualizaciones), si es necesario, haga clic en **Install updates** (Instalar actualizaciones). Cuando haya terminado, haga clic en **Siguiente** .
1. En **Solution updates** (Actualizaciones de la solución), si es necesario, haga clic en **Instalar extensión** . Cuando haya terminado, haga clic en **Siguiente** .
1. Haga clic en **Restart servers** (Reiniciar los servidores), si es necesario. Compruebe que se ha iniciado correctamente cada servidor.

## <a name="step-2-networking"></a>Paso 2: Redes

El paso 2 del asistente le guía a través de la configuración de conmutadores virtuales y otros elementos de red para el clúster.

> [!NOTE]
> Si surgen errores durante los pasos de conexión en redes o en los pasos relacionados con conmutadores virtuales, pruebe a hacer clic en **Apply and test** (Aplicar y probar) de nuevo.

1. Seleccione **Siguiente: Redes** .
1. En **Verify the network adapters** (Comprobar los adaptadores de red), espere a que aparezcan las casillas verdes junto a cada adaptador y seleccione **Siguiente** .

1. En **Select management adapters** (Seleccionar adaptadores de administración), seleccione uno o dos adaptadores de administración para usarlos para cada servidor. Es obligatorio seleccionar al menos uno de los adaptadores con fines de administración, ya que el asistente requiere al menos una NIC física dedicada para la administración del clúster.  Una vez que se designa un adaptador para la administración, se excluye del resto del flujo de trabajo del asistente.

    Los adaptadores de administración tienen dos opciones de configuración:

    - **Un adaptador de red físico para la administración** . Para esta opción, se admite DHCP o la asignación de direcciones IP estáticas.

    - **Dos adaptadores de red físicos en equipo para la administración** . Si se agrupa un par de adaptadores, solo se admite la asignación de direcciones IP estáticas. Si los adaptadores seleccionados usan direccionamiento de DHCP (para uno de ellos o para ambos), la dirección IP de DHCP se convertirá en direcciones IP estáticas antes de la creación del conmutador virtual.

    Mediante el uso de adaptadores agrupados, tiene una única conexión a varios conmutadores, pero solo usa una dirección IP única. El equilibrio de carga está disponible y la tolerancia a errores es instantánea, en lugar de esperar a que se actualicen los registros DNS.

    Haga lo siguiente para cada adaptador:

    - Active la casilla **Description** (Descripción). Tenga en cuenta que todos los adaptadores están seleccionados y que el asistente puede ofrecerle una recomendación.
    - Desactive las casillas de los adaptadores que no quiera usar para la administración de clústeres.

    > [!NOTE]
    > Puede usar adaptadores de 1 GB como adaptadores de administración, pero se recomienda usar adaptadores de 10 GB o más rápidos para transportar el tráfico de almacenamiento y de carga de trabajo (VM).

1. Cuando se hayan realizado cambios, haga clic en **Apply and test** (Aplicar y probar).
1. En **Define networks** (Definir redes), asegúrese de que cada adaptador de red de cada servidor tiene una dirección IP estática única, una máscara de subred y un identificador de VLAN. Mantenga el mouse sobre cada elemento de tabla y escriba o cambie los valores según sea necesario. Cuando haya finalizado, haga clic en **Apply and test** (Aplicar y probar)

    > [!NOTE]
    > Para admitir la configuración del identificador de VLAN del clúster, todas las tarjetas de red de todos los servidores deben admitir la propiedad VLANID.

1. Espere a que la columna **Status** (Estado) muestre **Passed** (Superado) para cada servidor y, a continuación, haga clic en **Next** (Siguiente). Este paso comprueba la conectividad de red entre todos los adaptadores con la misma subred y el mismo identificador de VLAN. Las direcciones IP proporcionadas se transfieren desde el adaptador físico a los adaptadores virtuales una vez que se crean los conmutadores virtuales en el paso siguiente. La tarea puede tardar varios minutos en completarse, según el número de adaptadores configurados.

1. En **Conmutador virtual** , seleccione una de las siguientes opciones según corresponda. En función del número de adaptadores presente, es posible que no se muestren todas las opciones:

    - **Omitir la creación del conmutador virtual**
    - **Crear un conmutador virtual para usarlo para el almacenamiento y el proceso**
    - **Crear un conmutador virtual para usarlo solo para el proceso**
    - **Crear dos conmutadores virtuales**

    > [!NOTE]
    > Si va a implementar Controladora de red para SDN (en **Paso 5: SDN** del asistente), necesitará un conmutador virtual. Por lo tanto, si decide no crear un conmutador virtual aquí y no crea uno fuera del asistente, el asistente no implementará Controladora de red.

    En la tabla siguiente se muestra qué configuraciones de conmutador virtual se admiten y están habilitadas para varias configuraciones de adaptador de red:

    | Opción | 1-2 adaptadores | 3 adaptadores o más | adaptadores agrupados |
    | :------------- | :--------- |:--------| :---------|
    | conmutador único (proceso y almacenamiento) | enabled | enabled  | no admitido |
    | conmutador único (solo proceso) | no admitido| enabled | enabled |
    | dos conmutadores | no admitido | enabled | enabled |

1. Cambie el nombre de un conmutador y otras opciones de configuración según sea necesario y, a continuación, haga clic en **Apply and test** (Aplicar y probar). La columna **Status** (Estado) debe mostrar **Passed** (Superado) para cada servidor una vez creados los conmutadores virtuales.

## <a name="step-3-clustering"></a>Paso 3: Agrupación en clústeres

En el paso 3 del asistente, se asegura de que todo lo anterior se ha configurado correctamente, se configuran automáticamente dos sitios en el caso de las implementaciones de clústeres extendidos y, a continuación, se crea el clúster. También puede configurar los sitios de antemano en Active Directory.

1. Seleccione **Siguiente: Agrupación en clústeres**
1. En **Validate the cluster** (Validar el clúster), seleccione **Validate** (Validar). Es posible que la validación tarde varios minutos.

    Si aparece el mensaje emergente **Credential Security Service Provider (CredSSP)** (Proveedor del servicio de seguridad de credenciales [CredSSP]), seleccione **Yes** (Sí) para habilitar de forma temporal CredSSP para que el asistente pueda continuar. Una vez creado el clúster y finalizado el asistente, se deshabilitará CredSSP para aumentar la seguridad. Si tiene problemas con CredSSP, consulte [Solución de problemas de CredSSP](../manage/troubleshoot-credssp.md) para más información.

1. Revise todos los estados de validación, descargue el informe para obtener información detallada sobre los errores, realice cambios y, a continuación, haga clic en **Validate again** (Volver a validar) según sea necesario. Vuelva a repetir el proceso según sea necesario hasta que se superen todas las comprobaciones de validación.
1. En **Create the cluster** (Crear el cluster), escriba un nombre para el clúster.
1. En **Networks** (Redes), seleccione la configuración preferida.
1. En **IP addresses** (Direcciones IP), seleccione las direcciones IP dinámicas o estáticas que se usarán.
1. Cuando haya terminado, haga clic en **Create cluster** (Crear clúster).

1. En el caso de los clústeres extendidos, en **Assign servers to sites** (Asignar servidores a sitios), asigne un nombre a los dos sitios que se usarán.

1. A continuación, asigne cada servidor a un sitio. Configurará la replicación entre sitios más tarde. Al acabar, haga clic en **Apply** (Aplicar).

## <a name="step-4-storage"></a>Paso 4: Storage

El paso 4 del Asistente le guía a lo largo de la configuración de Espacios de almacenamiento directo para el clúster.

1. Seleccione **Siguiente: Storage** (Almacenamiento).
1. En **Verify drives** (Comprobar las unidades), haga clic en el icono **>** situado junto a cada servidor para comprobar que los discos funcionan y están conectados y, a continuación, haga clic en **Next** (Siguiente).
1. En **Clean drives** (Limpiar unidades), haga clic en **Clean drives** (Limpiar unidades) para vaciar las unidades de datos. Cuando esté listo, haga clic en **Next** (Siguiente).
1. En **Validate storage** (Validar almacenamiento), haga clic en **Next** (Siguiente).
1. Revise los resultados de la validación. Si todo es correcto, haga clic en **Next** (Siguiente).
1. En **Enable Storage Spaces Direct** (Habilitar Espacios de almacenamiento directo), haga clic en **Enable** (Habilitar).
1. Descargue el informe y revíselo. Si todo es correcto, haga clic en **Finish** (Finalizar).

Enhorabuena, ya tiene un clúster.

Una vez creado el clúster, el nombre del clúster puede tardar unos instantes en replicarse en el dominio, especialmente si los servidores del grupo de trabajo se han agregado recientemente a Active Directory. Aunque el clúster podría aparecer en Windows Admin Center, es posible que no esté disponible para conectarse todavía.

Si la resolución del clúster no se realiza correctamente después de un tiempo, en la mayoría de los casos, puede sustituir el nombre del servidor del clúster, en lugar del nombre del clúster.

## <a name="step-5-sdn-optional"></a>Paso 5: SDN (opcional)

Este paso opcional le guía a través de la configuración del componente de Controladora de red de [redes definidas por software (SDN)](../concepts/software-defined-networking.md). Una vez configurado el rol Controladora de red, se puede usar para configurar otros componentes de SDN, como el equilibrador de carga del software y la puerta de enlace de RAS.

> [!NOTE]
> SDN no se admite o no está disponible para los clústeres extendidos.

:::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="Asistente para crear clúster: Opción de HCI" lightbox="media/cluster/create-cluster-network-controller.png":::

1. Seleccione **Siguiente: SDN** .
1. En **Host** , escriba un nombre para Controladora de red.
1. Especifique una ruta de acceso al archivo VHD de Azure Stack HCI. Use **Examinar** para encontrarlo más rápido.
1. Especifique el número de máquinas virtuales que se dedicarán a Controladora de red. Se recomiendan entre tres y cinco máquinas virtuales para lograr una alta disponibilidad.
1. En **Red** , escriba el identificador de VLAN.
1. En **VM network addressing** (Direcciones de red de VM), seleccione **DHCP** o **Estáticas** .
1. Si seleccionó **DHCP** , escriba el nombre y la dirección IP de las máquinas virtuales de Controladora de red.
1. Si seleccionó **Estáticas** , haga lo siguiente:
    1. Especifique un prefijo de subred.
    1. Especifique la puerta de enlace predeterminada.
    1. Especifique uno o varios servidores DNS. Haga clic en **Agregar** para agregar servidores DNS adicionales.
1. En **Credenciales** , escriba el nombre de usuario y la contraseña usados para unir las máquinas virtuales de Controladora de red al dominio del clúster.
1. Escriba la contraseña administrativa local para estas máquinas virtuales.
1. En **Avanzado** , escriba la ruta de acceso a las máquinas virtuales.
1. Escriba los valores de **inicio del grupo de direcciones MAC** y **final del grupo de direcciones MAC** .
1. Cuando termine, haga clic en **Siguiente** .
1. Espere hasta que el asistente complete su trabajo. Permanezca en esta página hasta que se completen todas las tareas en curso. Haga clic en **Finalizar** .

Si se produce un error en la implementación de Controladora de red, haga lo siguiente antes de volver a intentarlo:

- Detenga y elimine todas las máquinas virtuales de Controladora de red que haya creado el asistente.  

- Limpie los puntos de montaje de todos los discos duros virtuales que haya creado el asistente.  

- Asegúrese de tener al menos 50-100 GB de espacio libre en los hosts de Hyper-V.  

## <a name="after-you-complete-the-wizard"></a>Después de finalizar el asistente

Una vez completado el asistente, todavía hay algunas tareas importantes que debe completar.

La primera tarea consiste en deshabilitar el protocolo de proveedor de compatibilidad para seguridad de credenciales (CredSSP) en cada servidor por motivos de seguridad. Recuerde que CredSSP debe estar habilitado para el asistente. Si tiene problemas con CredSSP, consulte [Solución de problemas de CredSSP](../manage/troubleshoot-credssp.md) para más información.

1. En Windows Admin Center, en **All connections** (Todas las conexiones), seleccione el clúster que acaba de crear.
1. En **Herramientas** , seleccione **Servidores** .
1. En el panel derecho, seleccione el primer servidor del clúster.
1. En **Información general** , seleccione **Disable CredSSP** (Deshabilitar CredSSP). Verá que el banner **CredSSP ENABLED** (CredSSP HABILITADO) de la parte superior desaparece.
1. Repita los pasos 3 y 4 para cada servidor del clúster.

Estas son las otras tareas que tendrá que hacer:

- Configurar un testigo del clúster. Consulte [Configuración de un testigo del clúster](witness.md).
- Crear los volúmenes. Consulte [Creación de volúmenes](../manage/create-volumes.md).
- En el caso de los clústeres extendidos, cree volúmenes y configure la replicación mediante una réplica de almacenamiento. Consulte [Creación de volúmenes y configuración de la replicación para clústeres extendidos](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Pasos siguientes

- Registrar el clúster con Azure. Consulte [Administración del registro de Azure](../manage/manage-azure-registration.md).
- Realice una validación final del clúster. Consulte [Validación de un clúster de Azure Stack HCI](validate.md)
- Aprovisionar las VM. Consulte [Administración de máquinas virtuales en Azure Stack HCI mediante Windows Admin Center](../manage/vm.md).
- También puede implementar un clúster mediante PowerShell. Consulte [Creación de un clúster de Azure Stack HCI mediante PowerShell](create-cluster-powershell.md).
- También puede implementar Controladora de red mediante PowerShell. Consulte [Implementación de Controladora de red mediante PowerShell](network-controller-powershell.md).
