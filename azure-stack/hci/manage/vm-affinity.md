---
title: Configuración de reglas de afinidad de máquinas virtuales con Windows PowerShell
description: Obtenga información sobre cómo configurar reglas de afinidad de máquinas virtuales con Windows PowerShell.
author: v-dasis
ms.topic: how-to
ms.date: 10/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e2cb3fa0efb11664924431ed0434547c832ceab4
ms.sourcegitcommit: 8122672409954815e472a5b251bb7319fab8f951
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92060171"
---
# <a name="create-server-and-site-affinity-rules-for-vms"></a>Creación de reglas de afinidad de sitio y servidor para máquinas virtuales

> Se aplica a Azure Stack HCI, versión 20H2

Con Windows Admin Center o Windows PowerShell, puede crear fácilmente reglas de afinidad y de antiafinidad para las máquinas virtuales de un clúster.

La afinidad es una regla que establece una relación entre dos o más grupos de recursos o roles, como máquinas virtuales, para mantenerlos juntos en el mismo servidor, clúster o sitio. La antiafinidad es lo contrario en el sentido de que se usa para mantener las máquinas virtuales o los grupos de recursos especificados apartados entre sí, como dos controladores de dominio ubicados en servidores o en sitios independientes para la recuperación ante desastres.

Las reglas de afinidad y antiafinidad se utilizan de forma similar a como Azure usa las zonas de disponibilidad. En Azure, puede configurar zonas de disponibilidad para mantener las máquinas virtuales en zonas independientes y alejarlas entre sí o en la misma zona entre sí.  

Mediante el uso de las reglas de afinidad y antiafinidad, cualquier máquina virtual en el clúster permanecerá en el mismo nodo de clúster o se evitará que esté en el mismo nodo.  De esta manera, la única manera de trasladar una máquina virtual fuera de un nodo sería manualmente.  Asimismo puede mantener las máquinas virtuales junto con su almacenamiento como, por ejemplo, con el volumen compartido de clúster (CSV) en el que reside su VHDX.

Al combinar reglas de afinidad y antiafinidad, también puede configurar un clúster extendido entre dos sitios y mantener las máquinas virtuales en el sitio en el que deben estar.

## <a name="using-windows-admin-center"></a>Uso de Windows Admin Center

Puede crear reglas básicas de afinidad y antiafinidad mediante Windows Admin Center.

:::image type="content" source="media/vm-affinity/vm-affinity-rules.png" alt-text="Pantalla Máquinas virtuales" lightbox="media/vm-affinity/vm-affinity-rules.png":::

1. En la página principal de Windows Admin Center, en **Todas las conexiones**, seleccione el servidor o clúster para el que desea crear la máquina virtual.
1. En **Herramientas**, seleccione **Configuración**.
1. En **Configuración**, seleccione **Reglas de afinidad** y, a continuación, seleccione **Crear regla** en **Reglas de afinidad**.
1. En **Nombre de la regla**: escriba un nombre para la regla.
1. En Tipo de regla, seleccione **Juntas (mismo servidor)** o **Separadas (servidores diferentes)** para colocar las máquinas virtuales en el mismo servidor o en servidores diferentes.
1. En **se aplica a**, seleccione las máquinas virtuales a las que se aplicará esta regla. Use el botón **Agregar** para agregar más máquinas virtuales a la regla.
1. Cuando haya terminado, haga clic en **Crear regla**.
1. Para eliminar una regla, selecciónela y haga clic en **Eliminar regla**.

## <a name="using-windows-powershell"></a>Uso de Windows PowerShell

Puede crear reglas más complejas con Windows PowerShell que con Windows Admin Center. Normalmente, las reglas se configuran desde un equipo remoto, en lugar de hacerlo en un servidor host de un clúster. Este equipo remoto se denomina equipo de administración.

Al ejecutar comandos de Windows PowerShell desde un equipo de administración, incluya el parámetro `-Name` o `-Cluster` con el nombre del clúster que va a administrar. Además, tendrá que especificar el nombre de dominio completo (FQDN) al usar el parámetro `-ComputerName` para un nodo de servidor.

### <a name="new-powershell-cmdlets"></a>Nuevos cmdlets de PowerShell

Para crear reglas de afinidad para clústeres, utilice los siguientes cmdlets nuevos de PowerShell:

#### <a name="new-clusteraffinityrule"></a>New-ClusterAffinityRule

El cmdlet **`New-ClusterAffinityRule`** se utiliza para crear nuevas reglas.  Con este comando se especificaría el nombre de la regla, así como el tipo de regla, donde:

**`-Name`** es el nombre de la regla.

Los valores de **`-RuleType`** son `SameFaultDomain` | `SameNode` | `DifferentFaultDomain` | `DifferentNode`

Ejemplo:

```powershell
New-ClusterAffinityRule -Name -RuleType SameFaultDomain -Cluster Cluster1
```

#### <a name="set-clusteraffinityrule"></a>Set-ClusterAffinityRule

El cmdlet **`Set-ClusterAffinityRule`** se utiliza para habilitar o deshabilitar una regla, donde:

**`-Name`** es el nombre de la regla que se va a habilitar o deshabilitar.

**`-Enabled`**  |  **`Disabled`** habilita o deshabilita la regla.

Ejemplo:

```powershell
Set-ClusterAffinityRule -Name -Enabled -Cluster Cluster1
```

#### <a name="get-clusteraffinityrule"></a>Get-ClusterAffinityRule

El cmdlet **`Get-ClusterAffinityRule`** se utiliza para mostrar la regla especificada y qué tipo es.  Si no se especifica **`-Name`** , se enumerarán todas las reglas.

Ejemplo:

```powershell
Get-ClusterAffinityRule -Name -Cluster Cluster1
```

#### <a name="add-clustergrouptoaffinityrule"></a>Add-ClusterGroupToAffinityRule

El cmdlet **`Add-ClusterGroupToAffinityRule`** se utiliza para agregar un nombre de grupo o rol de máquina virtual a una regla de afinidad específica, donde:

**`-Groups`** es el nombre del grupo o rol que se va a agregar a la regla.

**`-Name`** es el nombre de la regla que se va a agregar.

Ejemplo:

```powershell
Add-ClusterGroupToAffinityRule -Groups -Name -Cluster Cluster1
```

#### <a name="add-clustersharedvolumetoaffinityrule"></a>Add-ClusterSharedVolumeToAffinityRule

El cmdlet **`Add-ClusterSharedVolumeToAffinityRule`** permite que las máquinas virtuales permanezcan juntas con el volumen compartido de clúster en el que reside el VHDX, donde:

**`-ClusterSharedVolumes`** es el disco o discos CSV que quiere agregar a la regla.

**`-Name`** es el nombre de la regla a la que se va a agregar.

Ejemplo:

```powershell
Add-ClusterSharedVolumeToAffinityRule  -ClusterSharedVolumes -Name -Cluster Cluster1
```

#### <a name="remove-clusteraffinityrule"></a>Remove-ClusterAffinityRule

El **`Remove-ClusterAffinityRule`** elimina la regla especificada, donde **`-Name`** es el nombre de la regla.

Ejemplo:

```powershell
Remove-ClusterAffinityRule -Name -Cluster Cluster1
```

#### <a name="remove-clustergroupfromaffinityrule"></a>Remove-ClusterGroupFromAffinityRule

El cmdlet **`Remove-ClusterGroupFromAffinityRule`** quita un rol o grupo de máquinas virtuales de una regla específica pero no deshabilita ni elimina la regla, donde:

**`-Name`** es el nombre de la regla.

**`-Groups`** son los grupos o roles que desea quitar de la regla.

Ejemplo:

```powershell
Remove-ClusterGroupFromAffinityRule -Name -Groups -Cluster Cluster1
```

#### <a name="remove-clustersharedvolumefromaffinityrule"></a>Remove-ClusterSharedVolumeFromAffinityRule

El cmdlet **`Remove-ClusterSharedVolumeFromAffinityRule`** se usa para quitar los volúmenes compartidos de clúster de una regla específica pero no deshabilita ni elimina la regla, donde:

**`-ClusterSharedVolumes`** es el disco o discos CSV que desea quitar de la regla.

**`-Name`** es el nombre de la regla que se va a agregar.

Ejemplo:

```powershell
Remove-ClusterSharedVolumeFromAffinityRule -ClusterSharedVolumes -Name -Cluster Cluster1
```

### <a name="existing-powershell-cmdlets"></a>Cmdlets de PowerShell existentes

Con la llegada de los nuevos cmdlets, también hemos agregado nuevos modificadores a algunos cmdlets existentes.

#### <a name="move-clustergroup"></a>Move-ClusterGroup

El nuevo modificador `-IgnoreAffinityRule` omite la regla y mueve el grupo de recursos de clúster a otro nodo de clúster. Para más información sobre este cmdlet, consulte [Move-ClusterGroup](/powershell/module/failoverclusters/move-clustergroup).

Ejemplo:

```powershell
Move-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

> [!NOTE]
> Si una regla de movimiento es válida, es decir, se admite, también se moverán todos los grupos y roles afectados.  Si el movimiento de una máquina virtual infringe una regla, pero se necesita una sola vez, use el modificador `-IgnoreAffinityRule` para permitir que se produzca el movimiento. En este caso, se mostrará una advertencia de infracción de la máquina virtual. Después, puede volver a habilitar la regla según sea necesario.

#### <a name="start-clustergroup"></a>Start-ClusterGroup

El nuevo modificador `-IgnoreAffinityRule` omite la regla y activa el grupo de recursos de clúster en su ubicación actual. Para más información sobre este cmdlet, consulte [Start-ClusterGroup](/powershell/module/failoverclusters/start-clustergroup).

Ejemplo:

```powershell
Start-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

## <a name="affinity-rule-examples"></a>Ejemplos de reglas de afinidad

Las reglas de afinidad son reglas de "unión" que mantienen los recursos en el mismo servidor, clúster o sitio. Estos son algunos escenarios comunes de configuración de las reglas de afinidad.

### <a name="scenario-1"></a>Escenario 1

Supongamos que tiene una máquina virtual con SQL Server y una máquina virtual de servidor web. Estas dos máquinas virtuales tienen que permanecer siempre en el mismo sitio, pero no necesariamente deben estar en el mismo nodo de clúster en el sitio.  Con `SameFaultDomain`, esto es posible, como se muestra a continuación:

```powershell
New-ClusterAffinityRule -Name WebData -Ruletype SameFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData -Enabled 1 -Cluster Cluster1
```

Para ver esta regla y cómo está configurada, use el cmdlet **`Get-ClusterAffinityRule`** para ver la salida:

```powershell
Get-ClusterAffinityRule -Name WebData -Cluster Cluster1

Name        RuleType          Groups        Enabled
----        ---------         ------        -------
WebData     SameFaultDomain   {SQL1, WEB1}     1
```

### <a name="scenario-2"></a>Escenario 2

Vamos a usar el mismo escenario anterior, salvo que se va a especificar que las máquinas virtuales deben residir en el mismo nodo de clúster, pero no necesariamente en el mismo sitio. Con `SameNode`, puede establecerlo de la siguiente manera:

```powershell
New-ClusterAffinityRule -Name WebData1 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData1 -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData1 -Enabled 1 -Cluster Cluster1
```

Para ver la regla y cómo está configurada, utilice el cmdlet **`Get-ClusterAffinityRule`** para ver la salida:

```powershell
Get-ClusterAffinityRule -Name WebData1 -Cluster Cluster1

Name    RuleType    Groups        Enabled
----    --------    ------        -------
DC      SameNode    {SQL1, WEB1}     1
```

## <a name="anti-affinity-rule-examples"></a>Ejemplos de reglas de antiafinidad

Las reglas de antiafinidad son reglas de "separación" que separan los recursos y los colocan en diferentes servidores, clústeres o sitios.

### <a name="scenario-1"></a>Escenario 1
Supongamos que cuenta con dos máquinas virtuales que ejecutan SQL Server en el mismo clúster multisitio de Azure Stack HCI.  Cada máquina virtual emplea una gran cantidad de recursos de memoria, de CPU y de almacenamiento.  Si las dos terminan en el mismo nodo, esto puede causar problemas de rendimiento con una o ambas, ya que compiten por los ciclos de almacenamiento, CPU y memoria.  Mediante el uso de una regla de antiafinidad con `DifferentNode` como tipo de regla, estas máquinas virtuales se mantienen siempre en nodos de clúster diferentes.  

Los comandos de ejemplo para esto serían:

```powershell
New-ClusterAffinityRule -Name SQL -Ruletype DifferentNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,SQL2 –Name SQL -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL -Enabled 1 -Cluster Cluster1
```

Para ver la regla y cómo está configurada, utilice el cmdlet **`Get-ClusterAffinityRule`** para ver la salida:

```powershell
Get-ClusterAffinityRule -Name SQL -Cluster Cluster1

Name    RuleType        Groups        Enabled
----    -----------     -------       -------
SQL     DifferentNode   {SQL1, SQL2}     1
```

### <a name="scenario-2"></a>Escenario 2

Supongamos que tiene un clúster extendido de Azure Stack HCI con dos sitios (dominios de error). Tiene dos controladores de dominio que desea conservar en sitios independientes. Con una regla de antiafinidad con `DifferentFaultDomain` como tipo de regla, estos controladores de dominio permanecerán siempre en sitios diferentes.  Los comandos de ejemplo para esto serían:

```powershell
New-ClusterAffinityRule -Name DC -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name DC -Cluster Cluster1

Set-ClusterAffinityRule -Name DC -Enabled 1 -Cluster Cluster1
```

Para ver esta regla y cómo está configurada, use el cmdlet **`Get-ClusterAffinityRule`** para ver la salida:

```powershell
Get-ClusterAffinityRule -Name DC -Cluster Cluster1

Name    RuleType                Groups        Enabled
----    --------                -------       -------
DC      DifferentFaultDomain    {DC1, DC2}       1
```

## <a name="combined-rule-examples"></a>Ejemplos de reglas combinadas

Si se combinan las reglas de afinidad y antiafinidad, se puede configurar fácilmente varias combinaciones de máquinas virtuales en un clúster de varios sitios.  En este escenario, cada sitio tiene tres máquinas virtuales: SQL Server (SQL), servidor web (WEB) y controlador de dominio (DC).  Para cada una de las combinaciones, puede usar reglas de afinidad con `SameFaultDomain` para mantenerlas todas en el mismo sitio.  También puede establecer los controladores de dominio de cada sitio con reglas antiafinidad y `DifferentFaultDomain` para mantener las máquinas virtuales del controlador de dominio en sitios independientes, como se muestra a continuación:

```powershell
New-ClusterAffinityRule -Name Site1Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name Site2Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name TrioApart -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1,DC1 –Name Site1Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2,WEB2,DC2 –Name Site2Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name TrioApart -Cluster Cluster1

Set-ClusterAffinityRule -Name Site1Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name Site2Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name TrioApart -Enabled 1 -Cluster Cluster1
```

Para ver las reglas y cómo están configuradas, use el cmdlet **`Get-ClusterAffinityRule`** sin el modificador `-Name` y puede ver todas las reglas creadas y sus resultados.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
```

## <a name="storage-affinity-rules"></a>Reglas de afinidad de almacenamiento

También puede mantener una máquina virtual y su VHDX en un volumen compartido de clúster (CSV) en el mismo nodo de clúster. Esto evitará que se produzca la redirección de CSV, lo que puede ralentizar el inicio o la detención de una máquina virtual.  Teniendo en cuenta el escenario combinado de afinidad y antiafinidad anterior, puede mantener la máquina virtual con SQL y el volumen compartido de clúster en el mismo nodo de clúster.  Para ello, utilice los comandos siguientes:

```powershell
New-ClusterAffinityRule -Name SQL1CSV1 -Ruletype SameNode -Cluster Cluster1

New-ClusterAffinityRule -Name SQL2CSV2 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1 –Name SQL1CSV1 -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2 –Name SQL2CSV2 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV1 -Name SQL1CSV1 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV2 -Name SQL2CSV2 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL1CSV1 -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL2CSV2 -Enabled 1 -Cluster Cluster1
```

Para ver estas reglas y cómo se configuran, use el cmdlet **`Get-ClusterAffinityRule`** sin el modificador -Name y vea la salida.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
SQL1CSV1    SameNode               {SQL1, <CSV1-GUID>}  1
SQL2CSV2    SameNode               {SQL2, <CSV2-GUID>}  1
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a administrar las máquinas virtuales. Consulte [Administración de máquinas virtuales en Azure Stack HCI mediante Windows Admin Center](../manage/vm.md).
