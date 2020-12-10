---
title: Actualización de clústeres de Azure Stack HCl
description: Cómo aplicar actualizaciones de firmware y del sistema operativo a Azure Stack HCl mediante Windows Admin Center y PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 001cf81721423aad770093c0fe5cf92ec6b66af8
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010828"
---
# <a name="update-azure-stack-hci-clusters"></a>Actualización de clústeres de Azure Stack HCl

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Al actualizar clústeres de Azure Stack HCI, el objetivo es mantener la disponibilidad actualizando solo un servidor del clúster a la vez. Muchas actualizaciones del sistema operativo requieren desconectar el servidor; por ejemplo, para reiniciar o actualizar software, como la pila de red. Se recomienda usar la Actualización compatible con clústeres (CAU), una característica que facilita la instalación de actualizaciones en todos los servidores del clúster, a la vez que mantiene las aplicaciones en ejecución. La Actualización compatible con clústeres automatiza la activación y desactivación del modo de mantenimiento en el servidor, mientras instala actualizaciones y reinicia el servidor, si es necesario. La Actualización compatible con clústeres es el método de actualización predeterminado que usa Windows Admin Center y también se puede iniciar con PowerShell.

Este tema se centra en el sistema operativo y las actualizaciones de software. Si necesita desconectar un servidor para realizar tareas de mantenimiento en el hardware, consulte [Desconexión de un servidor para realizar el mantenimiento](maintain-servers.md).

## <a name="update-a-cluster-using-windows-admin-center"></a>Actualización de un clúster con Windows Admin Center

Windows Admin Center facilita la actualización de un clúster y la aplicación de actualizaciones del sistema operativo y de la solución mediante una sencilla interfaz de usuario. Si ha adquirido un sistema integrado de un asociado de hardware de Microsoft, es fácil obtener los controladores, el firmware y otras actualizaciones más recientes directamente de Windows Admin Center mediante la instalación de las extensiones de actualización de asociado adecuadas. Si el hardware no se compró como sistema integrado, es posible que sea necesario realizar actualizaciones de firmware y de controlador por separado, de acuerdo con las recomendaciones del fabricante del hardware.

Windows Admin Center comprobará si el clúster está configurado correctamente para ejecutar la actualización compatible con clústeres (CAU) y, si es necesario, le preguntará si desea que Windows Admin Center configure la CAU automáticamente, incluida la instalación del rol de clúster de CAU y la habilitación de las reglas de firewall necesarias.

1. Al conectarse a un clúster, el panel de Windows Admin Center le avisará si uno o más servidores tienen actualizaciones listas para instalarse y proporcionará un vínculo para actualizar ahora. Como alternativa, puede seleccionar **Actualizaciones** en el menú **Herramientas** de la izquierda.
1. Para usar la herramienta Actualización compatible con clústeres en Windows Admin Center, debe habilitar el proveedor del servicio de seguridad de credenciales (CredSSP) y proporcionar credenciales explícitas. Cuando se le pregunte si CredSSP debe habilitarse, haga clic en **Yes** (Sí).
1. Especifique su nombre de usuario y contraseña y haga clic en **Continue** (Continuar).
1. Se mostrarán las actualizaciones disponibles. Haga clic en **Check Available Updates** (Comprobar las actualizaciones disponibles) para actualizar la lista.
1. Seleccione las actualizaciones que quiera instalar y haga clic en **Apply All Updates** (Aplicar todas las actualizaciones). Se instalarán las actualizaciones en todos los servidores del clúster. Si es necesario reiniciar, los roles de clúster, como las máquinas virtuales, se moverán primero a otro servidor para evitar interrupciones.
1. Para mejorar la seguridad, deshabilite CredSSP en cuanto termine de instalar las actualizaciones:
    - En Windows Admin Center, en **All connections** (Todas las conexiones), seleccione el primer servidor del clúster y, a continuación, seleccione **Connect** (Conectar).
    - En la página **Overview** (Información general), seleccione **Disable CredSSP** (Deshabilitar CredSSP) y, a continuación, en la ventana emergente **Disable CredSSP** (Deshabilitar CredSSP), seleccione **Yes** (Yes).

## <a name="update-a-cluster-using-powershell"></a>Actualización de un clúster con PowerShell

Para poder actualizar un clúster con la Actualización compatible con clústeres, primero debe instalar las **Herramientas de clúster de conmutación por error**, que forman parte de las **Herramientas de administración remota del servidor (RSAT)** e incluyen el software de Actualización compatible con clústeres. Si está actualizando un clúster existente, es posible que estas herramientas ya estén instaladas.

Para probar si un clúster de conmutación por error está correctamente configurado para aplicar actualizaciones de software mediante la Actualización compatible con clústeres, ejecute el cmdlet de PowerShell **Test-CauSetup**, que realiza un examen del Analizador de procedimientos recomendados (BPA) del clúster de conmutación por error y del entorno de red, y le alerta de cualquier advertencia o error:

```PowerShell
Test-CauSetup -ClusterName Cluster1
```

Si necesita instalar características, herramientas o roles, consulte las secciones siguientes. En caso contrario, vaya a [Buscar actualizaciones con PowerShell](#check-for-updates-with-powershell).

### <a name="install-failover-clustering-and-failover-clustering-tools-using-powershell"></a>Instalación de clústeres de conmutación por error y herramientas de clústeres de conmutación por error con PowerShell

Para comprobar si un clúster o servidor tiene la característica Clústeres de conmutación por error y las Herramientas de clúster de conmutación por error ya instaladas, emita el cmdlet de PowerShell **`Get-WindowsFeature`** desde el equipo de administración (o ejecútelo directamente en el clúster o servidor, omitiendo el parámetro -ComputerName):

```PowerShell
Get-WindowsFeature -Name Failover*, RSAT-Clustering* -ComputerName Server1
```

Asegúrese de que el estado de instalación sea Installed (Instalado) y de que aparece una X antes de Clústeres de conmutación por error y Módulo Clúster de conmutación por error para Windows PowerShell:

```
Display Name                                            Name                       Install State
------------                                            ----                       -------------
[X] Failover Clustering                                 Failover-Clustering            Installed
        [X] Failover Clustering Tools                   RSAT-Clustering                Installed
            [X] Failover Cluster Module for Windows ... RSAT-Clustering-Powe...        Installed
            [ ] Failover Cluster Automation Server      RSAT-Clustering-Auto...        Available
            [ ] Failover Cluster Command Interface      RSAT-Clustering-CmdI...        Available
```

Si la característica de Clústeres de conmutación por error no está instalada, instálela en cada servidor del clúster con el cmdlet **`Install-WindowsFeature`** mediante los parámetros -IncludeAllSubFeature y -IncludeManagementTools:

```PowerShell
Install-WindowsFeature –Name Failover-Clustering -IncludeAllSubFeature –IncludeManagementTools -ComputerName Server1
```

Este comando también instalará el Módulo Clúster de conmutación por error para PowerShell, que incluye cmdlets de PowerShell para administrar clústeres de conmutación por error, y el módulo de Actualización compatible con clústeres para PowerShell, para la instalación de actualizaciones de software en clústeres de conmutación por error.

Si la característica Clústeres de conmutación por error ya está instalada, pero el Módulo de clústeres de conmutación por error para Windows PowerShell no lo está, simplemente, instálela en cada servidor del clúster con el cmdlet **Install-WindowsFeature**:

```PowerShell
Install-WindowsFeature –Name RSAT-Clustering-PowerShell -ComputerName Server1
```

### <a name="choose-an-updating-mode"></a>Elección de un modo de actualización

La Actualización compatible con clústeres puede coordinar toda la operación de actualización de clústeres de dos modos:  
  
-   **Modo de actualización automática**: en este modo, el rol en clúster de la Actualización compatible con clústeres se configura como una carga de trabajo en el clúster de conmutación por error que se va a actualizar y se establece la programación de actualización correspondiente. El clúster se actualiza automáticamente a horas programadas mediante un perfil de ejecución de actualización personalizado o predeterminado. Durante la ejecución de actualización, el proceso del coordinador de actualizaciones se inicia en el nodo que actualmente es propietario del rol en clústeres de la Actualización compatible con clústeres y el proceso realiza actualizaciones secuencialmente en cada nodo de clúster. Para actualizar el nodo del clúster actual, el rol en clústeres de la Actualización compatible con clústeres conmuta por error a otro nodo de clúster y un nuevo proceso del coordinador de actualizaciones del nodo asume el control de la ejecución de la actualización. En el modo de actualización automática, la Actualización compatible con clústeres puede actualizar el clúster de conmutación por error mediante un proceso de actualización de un extremo a otro totalmente automatizado. Un administrador también puede desencadenar actualizaciones a petición en este modo o, simplemente, usar el enfoque de actualización remota, si lo desea.
  
-   **Modo de actualización remota** Para este modo, un equipo de administración remoto (normalmente, un equipo con Windows 10) que tenga conectividad de red con el clúster de conmutación por error, pero que no sea miembro del clúster de conmutación por error, se configura con las Herramientas de clúster de conmutación por error. Desde el equipo de administración remoto, llamado coordinador de actualizaciones, el administrador desencadena una actualización a petición mediante un perfil de ejecución de actualización personalizado o predeterminado. El modo de actualización remota resulta útil para supervisar el progreso en tiempo real durante la ejecución de la actualización y en los clústeres que se ejecutan en instalaciones Server Core.  

   > [!NOTE]
   > A partir de la actualización de octubre de 2018 de Windows 10, RSAT se incluye como un conjunto de "características a petición" desde Windows 10. Simplemente vaya a **Configuración > Aplicaciones > Aplicaciones y características > Características opcionales > Agregar una característica > RSAT: Herramientas de clústeres de conmutación por error** y seleccione **Instalar**. Para ver el progreso de la instalación, haga clic en el botón Atrás para ver el estado en la página "Administrar características opcionales". La característica instalada se conservará en las diferentes actualizaciones de versión de Windows 10. Para instalar RSAT para Windows 10 antes de la actualización de octubre de 2018, [descargue un paquete de RSAT](https://www.microsoft.com/download/details.aspx?id=45520).

### <a name="add-cau-cluster-role-to-the-cluster"></a>Adición de un rol de clúster de CAU al clúster

El rol del clúster de Actualización compatible con clústeres es necesario para el modo de actualización automática. Si usa Windows Admin Center para realizar las actualizaciones, se agregará automáticamente el rol de clúster.

El cmdlet **`Get-CauClusterRole`** incluye las propiedades de configuración del rol de clúster de la Actualización compatible con clústeres en el clúster especificado.

```PowerShell
Get-CauClusterRole -ClusterName Cluster1
```

Si aún no se ha configurado el rol en el clúster, verá el mensaje de error siguiente:

```Get-CauClusterRole : The current cluster is not configured with a Cluster-Aware Updating clustered role.```

Para agregar el rol de clúster de la Actualización compatible con clústeres para el modo de actualización automática con PowerShell, use el cmdlet **`Add-CauClusterRole`** y proporcione los [parámetros](/powershell/module/clusterawareupdating/add-cauclusterrole#parameters) correspondientes, como en el ejemplo siguiente:

```PowerShell
Add-CauClusterRole -ClusterName Cluster1 -MaxFailedNodes 0 -RequireAllNodesOnline -EnableFirewallRules -VirtualComputerObjectName Cluster1-CAU -Force -CauPluginName Microsoft.WindowsUpdatePlugin -MaxRetriesPerNode 3 -CauPluginArguments @{ 'IncludeRecommendedUpdates' = 'False' } -StartDate "3/2/2020 3:00:00 AM" -DaysOfWeek 4 -WeeksOfMonth @(3) -verbose
```

   > [!NOTE]
   > El comando anterior se debe ejecutar desde un equipo de administración o un controlador de dominio.

### <a name="enable-firewall-rules-to-allow-remote-restarts"></a>Habilitación de reglas de firewall para permitir reinicios remotos

Necesitará permitir que los servidores se reinicien de forma remota durante el proceso de actualización. Si usa Windows Admin Center para realizar las actualizaciones, las reglas del firewall de Windows se actualizarán automáticamente en cada servidor para permitir reinicios remotos. Si está actualizando con PowerShell, habilite el grupo de reglas de firewall de apagado remoto en el Firewall de Windows o pase el parámetro -EnableFirewallRules al cmdlet, como en el ejemplo anterior.

## <a name="check-for-updates-with-powershell"></a>Búsqueda de actualizaciones con PowerShell

Puede usar el cmdlet **`Invoke-CAUScan`** para examinar servidores en busca de actualizaciones aplicables y obtener una lista del conjunto inicial de actualizaciones que se aplican a cada servidor de un clúster especificado:

```PowerShell
Invoke-CauScan -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -Verbose
```

La generación de la lista puede tardar unos minutos en completarse. La lista de vista previa solo incluye un conjunto inicial de actualizaciones; no incluye las actualizaciones que se pueden aplicar una vez instaladas las actualizaciones iniciales.

## <a name="install-updates-with-powershell"></a>Instalación de actualizaciones con PowerShell

Para buscar en servidores actualizaciones aplicables y realizar una ejecución de actualización completa en el clúster especificado, use el cmdlet **`Invoke-CAURun`** :

```PowerShell
Invoke-CauRun -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -MaxFailedNodes 1 -MaxRetriesPerNode 3 -RequireAllNodesOnline -EnableFirewallRules -Force
```

Este comando realiza un examen y una ejecución de actualización completa en el clúster denominado Cluster1. Este cmdlet usa el complemento Microsoft.WindowsUpdatePlugin y requiere que todos los nodos del clúster estén en línea antes de ejecutar el cmdlet. Además, este cmdlet no permite más de tres reintentos por nodo antes de marcar el nodo como con errores y no permite que se produzca un error en más de un nodo antes de marcar toda la ejecución de la actualización como con errores. También permite que las reglas de firewall permitan que los servidores se reinicien de forma remota. Dado que el comando especifica el parámetro Force, el cmdlet se ejecuta sin mostrar mensajes de confirmación.

El proceso de ejecución de la actualización incluye los siguientes pasos: 
- Buscar y descargar las actualizaciones correspondientes en cada servidor del clúster.
- Mover los roles en clústeres que se están ejecutando actualmente fuera de cada servidor.
- Instalar las actualizaciones en cada servidor.
- Reiniciar el servidor, si las actualizaciones que se instalan lo requieren.
- Devolver los roles en clústeres al servidor original.

El proceso de ejecución de actualización también incluye garantizar que se mantiene el cuórum, comprobar si hay actualizaciones adicionales que solo se pueden instalar después de instalar el conjunto inicial de actualizaciones y guardar un informe con las acciones realizadas.

## <a name="check-on-the-status-of-an-updating-run"></a>Comprobación del estado de una ejecución de actualización

Un administrador puede obtener información de resumen sobre una ejecución de actualización en curso. Para ello, debe ejecutar el cmdlet **`Get-CauRun`** :

```PowerShell
Get-CauRun -ClusterName Cluster1
```

A continuación se incluye una salida de muestra:

```
RunId                   : 834dd11e-584b-41f2-8d22-4c9c0471dbad 
RunStartTime            : 10/13/2019 1:35:39 PM 
CurrentOrchestrator     : NODE1 
NodeStatusNotifications : { 
Node      : NODE1 
Status    : Waiting 
Timestamp : 10/13/2019 1:35:49 PM 
} 
NodeResults             : { 
Node                     : NODE2 
Status                   : Succeeded 
ErrorRecordData          : 
NumberOfSucceededUpdates : 0 
NumberOfFailedUpdates    : 0 
InstallResults           : Microsoft.ClusterAwareUpdating.UpdateInstallResult[] 
}
```

## <a name="perform-a-fast-offline-update-of-all-servers-in-a-cluster"></a>Realización de una actualización rápida y sin conexión de todos los servidores de un clúster

Este método permite desconectar todos los servidores de un clúster a la vez y actualizarlos todos al mismo tiempo. Esto ahorra tiempo durante el proceso de actualización, pero la desventaja es el tiempo de inactividad de los recursos hospedados.

Si hay una actualización de seguridad crítica que necesita aplicar rápidamente, o debe asegurarse de que las actualizaciones se completen dentro de la ventana de mantenimiento, este método puede ser adecuado. Este proceso desactiva el clúster de Azure Stack HCI, actualiza los servidores y vuelve a activarlo todo.

1. Planee la ventana de mantenimiento.
2. Desconecte los discos virtuales.
3. Detenga el clúster para desconectar el bloque de almacenamiento. Ejecute el cmdlet **STOP-Cluster** o use Windows Admin Center para detener el clúster.
4. Establezca el servicio de clúster en **Deshabilitado** en Services.msc en cada servidor. Esto impide que el servicio de clúster se inicie mientras se está actualizando.
5. Aplique la actualización acumulativa de Windows Server y todas las actualizaciones de pila de mantenimiento necesarias a todos los servidores. Puede actualizar todos los servidores al mismo tiempo; no es necesario esperar, porque el clúster está inactivo.
6. Reinicie los servidores y asegúrese de que todo es correcto.
7. Vuelva a establecer el servicio de clúster en **Automático** en cada servidor.
8. Inicie el clúster. Ejecute el cmdlet **Start-Cluster** o use Windows Admin Center.

   Espere unos minutos.  Asegúrese de que el estado del bloque de almacenamiento es correcto.

9. Vuelva a poner en línea los discos virtuales.
10. Para supervisar el estado de los discos virtuales ejecute los cmdlets **Get-Volume** y **Get-VirtualDisk**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Actualización compatible con clústeres (CAU)](/windows-server/failover-clustering/cluster-aware-updating)
- [Actualización del firmware de la unidad en Espacios de almacenamiento directo](/windows-server/storage/update-firmware)
- [Validación de un clúster de Azure Stack HCI](../deploy/validate.md)