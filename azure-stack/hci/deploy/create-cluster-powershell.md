---
title: Creación de un clúster de Azure Stack HCI mediante Windows PowerShell
description: Aprenda a administrar un clúster hiperconvergido para Azure Stack HCI mediante Windows PowerShell.
author: v-dasis
ms.topic: how-to
ms.date: 08/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 4bd669e04f2b4b4e1ef173a3a44e52d8c6067a60
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064521"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-powershell"></a>Creación de un clúster de Azure Stack HCI mediante Windows PowerShell

> Se aplica a: Azure Stack HCI, versión v20H2

En este artículo, aprenderá a usar Windows PowerShell para crear un clúster hiperconvergido de Azure Stack HCI que use Espacios de almacenamiento directo. Si prefiere usar el asistente para la creación de clústeres en Windows Admin Center para crear el clúster, consulte [Creación del clúster con Windows Admin Center](create-cluster.md).

Puede elegir entre dos tipos de clúster:

- Clúster estándar con al menos dos nodos de servidor que residan ambos en un único sitio.
- Clúster extendido con al menos cuatro nodos de servidor que abarquen dos sitios, con dos nodos por sitio.

En este artículo, se creará un clúster de ejemplo denominado Cluster1 que consta de cuatro nodos de servidor denominados Server1, Server2, Server3 y Server4.

En el escenario de clúster extendido, usaremos ClusterS1 como nombre y usaremos los mismos cuatro nodos de servidor extendidos en los sitios Site1 y Site2.

Para obtener más información sobre los clústeres extendidos, consulte [Información general sobre clústeres extendidos](../concepts/stretched-clusters.md).

Si está interesado en realizar pruebas de Azure Stack HCI, pero tiene hardware limitado o no tiene hardware de repuesto, consulte la [guía de evaluación de Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), donde le guiaremos a través de la experimentación de Azure Stack HCI mediante la virtualización anidada, ya sea en Azure o en un solo sistema físico local.

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar, asegúrese de que:

- Ha leído [Requisitos del sistema de Azure Stack HCI](../concepts/system-requirements.md).
- Instale el sistema operativo de Azure Stack HCI en cada servidor del clúster. Consulte [Implementación del sistema operativo de Azure Stack HCI](operating-system.md).
- Use una cuenta que sea miembro del grupo de administradores local en cada servidor.
- Tiene permisos de Active Directory para crear objetos.

## <a name="using-windows-powershell"></a>Uso de Windows PowerShell

Puede ejecutar PowerShell localmente en una sesión de RDP en un servidor host, o bien puede ejecutar PowerShell de forma remota desde un equipo de administración. En este artículo se describe la opción remota.

Al ejecutar PowerShell desde un equipo de administración, incluya el parámetro `-Name` o `-Cluster` con el nombre del servidor o clúster que va a administrar. Además, puede que tenga que especificar el nombre de dominio completo (FQDN) al usar el parámetro `-ComputerName` para un nodo de servidor.

También necesitará los cmdlets de Herramientas de administración remota del servidor (RSAT) y los módulos de PowerShell para Hyper-V y los clústeres de conmutación por error. Si estos no están disponibles en la sesión de PowerShell en el equipo de administración, puede agregarlos con el siguiente comando: `Add-WindowsFeature RSAT-Clustering-PowerShell`.

## <a name="step-1-provision-the-servers"></a>Paso 1: Aprovisionamiento de los servidores

En primer lugar, nos conectaremos a cada uno de los servidores, los uniremos a un dominio (el mismo dominio en el que se encuentra el equipo de administración) y se instalarán los roles y las características necesarios.

### <a name="step-11-connect-to-the-servers"></a>Paso 1.1: Conexión con los servidores

Para conectarse a los servidores, primero debe tener conectividad de red, estar unido al mismo dominio o a un dominio de plena confianza, y tener permisos administrativos locales en los servidores.

Abra PowerShell y use el nombre de dominio completo o la dirección IP del servidor al que desea conectarse. Se le solicitará una contraseña después de ejecutar el siguiente comando en cada servidor (Server1, Server2, Server3 y Server4):

   ```powershell
   Enter-PSSession -ComputerName "Server1" -Credential "Server1\Administrator"
   ```

Este es otro ejemplo de cómo hacer lo mismo:

   ```powershell
   $myServer1 = "Server1"
   $user = "$myServer1\Administrator"

   Enter-PSSession -ComputerName $myServer1 -Credential $user
   ```

> [!TIP]
> Al ejecutar los comandos de PowerShell desde el equipo de administración, es posible que obtenga un error como este: *WinRM no puede procesar la solicitud*. Para solucionarlo, use PowerShell para agregar cada servidor a la lista de hosts de confianza del equipo de administración. Esta lista admite caracteres comodín, como `Server*` por ejemplo.
>
> `Set-Item WSMAN:\Localhost\Client\TrustedHosts -Value Server1 -Force`
>  
> Para ver la lista de hosts de confianza, escriba `Get-Item WSMAN:\Localhost\Client\TrustedHosts`.  
>
> Para vaciar la lista, escriba `Clear-Item WSMAN:\Localhost\Client\TrustedHost`.  

### <a name="step-12-join-the-domain-and-add-domain-accounts"></a>Paso 1.2: Unión al dominio y adición de cuentas de dominio

Hasta ahora se ha conectado a cada nodo de servidor con la cuenta de administrador local `<ServerName>\Administrator`.

Para continuar, debe unir los servidores a un dominio y usar la cuenta de dominio que se encuentra en el grupo de administradores local en cada servidor.

Use el cmdlet `Enter-PSSession` para conectarse a cada servidor y ejecute el siguiente cmdlet, sustituyendo el nombre del servidor, el nombre de dominio y las credenciales de dominio:

```powershell  
Add-Computer -NewName "Server1" -DomainName "contoso.com" -Credential "Contoso\User" -Restart -Force  
```

Si la cuenta de administrador no es miembro del grupo Administradores de dominio, agréguela al grupo Administradores local de cada servidor, o bien, agréguela al grupo que usa para los administradores. Para ello, puede utilizar el siguiente comando:

```powershell
Add-LocalGroupMember -Group "Administrators" -Member "king@contoso.local"
```

### <a name="step-13-install-roles-and-features"></a>Paso 1.3: Instalación de roles y características

El siguiente paso es instalar los roles y las características de Windows necesarios en todos los servidores del clúster. Estos son los roles que se van a instalar:

- BitLocker
- Protocolo de puente del centro de datos (para adaptadores de red RoCEv2)
- Clústeres de conmutación por error
- Servidor de archivos
- Módulo FS-Data-Deduplication
- Hyper-V
- Módulo RSAT-AD-PowerShell
- Réplica de almacenamiento (solo se instala para clústeres extendidos)

Use el siguiente comando para cada servidor:

```powershell
Install-WindowsFeature -ComputerName "Server1" -Name "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-Clustering-PowerShell", "Storage-Replica" -IncludeAllSubFeature -IncludeManagementTools
```

Para ejecutar el comando en todos los servidores del clúster al mismo tiempo, use el siguiente script y modifique la lista de variables al principio para que se adapte a su entorno.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"
$FeatureList = "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-Clustering-PowerShell", "Storage-Replica"

# This part runs the Install-WindowsFeature cmdlet on all servers in $ServerList, passing the list of features in $FeatureList.
Invoke-Command ($ServerList) {
    Install-WindowsFeature -Name $Using:Featurelist -IncludeAllSubFeature -IncludeManagementTools
}
```
A continuación, reinicie todos los servidores:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Restart-Computer -ComputerName $ServerList
```

## <a name="step-2-configure-networking"></a>Paso 2: Configuración de las redes

Este paso permite configurar varios elementos de redes del entorno.

### <a name="disable-unused-networks"></a>Deshabilitar redes no usadas

Debe deshabilitar las redes desconectadas o no utilizadas para el tráfico de administración, almacenamiento o cargas de trabajo (por ejemplo, las máquinas virtuales). Aquí se describe cómo identificar las redes no utilizadas:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-NetAdapter -CimSession $Servers | Where-Object Status -eq Disconnected
```
Y aquí se indica cómo deshabilitarlas:

```powershell
Get-NetAdapter -CimSession $Servers | Where-Object Status -eq Disconnected | Disable-NetAdapter -Confirm:$False
```

### <a name="assign-virtual-network-adapters"></a>Asignación de adaptadores de red virtuales

A continuación, asignará los adaptadores de red virtuales para la administración y el resto del tráfico, como en el siguiente ejemplo. Debe configurar al menos un adaptador de red para la administración del clústeres.

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
Rename-VMNetworkAdapter -ManagementOS -Name $vSwitchName -NewName Management -ComputerName $Servers
Add-VMNetworkAdapter -ManagementOS -Name SMB01 -SwitchName $vSwitchName -CimSession $Servers
Add-VMNetworkAdapter -ManagementOS -Name SMB02 -SwitchName $vSwitchName -Cimsession $Servers
```

Y compruebe que se han agregado y asignado correctamente:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-VMNetworkAdapter -CimSession $Servers -ManagementOS
```

### <a name="create-virtual-switches"></a>Creación de conmutadores virtuales

Se necesita un conmutador virtual para cada nodo de servidor del clúster. En el ejemplo siguiente, se crea un conmutador virtual con la funcionalidad SR-IOV mediante los adaptadores de red que están conectados (el estado es activo). Tener SR-IOV habilitada también puede ser útil, ya que es necesaria para los adaptadores de red virtuales de las máquinas virtuales habilitadas con RDMA.

Todos los adaptadores de red deben ser idénticos para poder agrupar las NIC.

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
```

Y para crear el conmutador virtual:

```powershell
Invoke-Command -ComputerName $Servers -ScriptBlock {New-VMSwitch -Name $using:vSwitchName -EnableEmbeddedTeaming $TRUE -EnableIov $true -NetAdapterName (Get-NetAdapter | Where-Object Status -eq Up ).InterfaceAlias}
```

Ahora, compruebe que el conmutador se ha creado correctamente:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-VMSwitch -CimSession $Servers | Select-Object Name, IOVEnabled, IOVS*
Get-VMSwitchTeam -CimSession $Servers
```

### <a name="configure-ip-addresses-and-vlans"></a>Configuración de direcciones IP y VLAN

Puede configurar una o dos subredes. Se prefieren dos subredes si desea evitar la sobrecarga de la interconexión del conmutador. Por ejemplo, el tráfico del almacenamiento SMB permanecerá en una subred dedicada a un conmutador físico.

### <a name="obtain-network-interface-information"></a>Obtención de información de la interfaz de red

Antes de poder establecer las direcciones IP de las tarjetas de interfaz de red, hay cierta información que necesita conocer primero, por ejemplo, el índice de interfaz (`ifIndex`), `Interface Alias` y `Address Family`. Anote estos datos para cada nodo de servidor ya que los necesitará más adelante.

Ejecute lo siguiente:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-NetIPInterface -ComputerName $ServerList
```

#### <a name="configure-one-subnet"></a>Configuración de una subred

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$StorNet="172.16.1."
$StorVLAN=1
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $Servers){
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    $IP++
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN -Access -ManagementOS -CimSession $Servers
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN -Access -ManagementOS -CimSession $Servers
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="configure-two-subnets"></a>Configuración de dos subredes

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$StorNet1="172.16.1."
$StorNet2="172.16.2."
$StorVLAN1=1
$StorVLAN2=2
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $Servers){
    New-NetIPAddress -IPAddress ($StorNet1+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    New-NetIPAddress -IPAddress ($StorNet2+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN1 -Access -ManagementOS -CimSession $Servers
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN2 -Access -ManagementOS -CimSession $Servers
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="verify-vlan-ids-and-subnets"></a>Comprobación de los identificadores de VLAN y las subredes

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
#verify ip config
Get-NetIPAddress -CimSession $servers -InterfaceAlias vEthernet* -AddressFamily IPv4 | Sort-Object -Property PSComputername | ft pscomputername,interfacealias,ipaddress -AutoSize -GroupBy PSComputerName

#Verify that the VlanID is set
Get-VMNetworkAdapterVlan -ManagementOS -CimSession $servers | Sort-Object -Property Computername | Format-Table ComputerName,AccessVlanID,ParentAdapter -AutoSize -GroupBy ComputerName
```

## <a name="step-3-prep-for-cluster-setup"></a>Paso 3: Preparación de la configuración del clúster

A continuación, compruebe que los servidores estén listos para la agrupación en clústeres. Como comprobación de integridad en primer lugar, considere la posibilidad de ejecutar los siguientes comandos para asegurarse de que los servidores ya no pertenecen a un clúster:

Use `Get-ClusterNode` para mostrar todos los nodos:

```powershell
Get-ClusterNode
```

Use `Get-ClusterResource` para mostrar todos los nodos de clúster:

```powershell
Get-ClusterResource
```

Use `Get-ClusterNetwork` para mostrar todas las redes de clúster:

```powershell
Get-ClusterNetwork
```

### <a name="step-31-prepare-drives"></a>Paso 3.1: Preparación de las unidades

Antes de habilitar Espacios de almacenamiento directo, asegúrese de que las unidades están vacías. Ejecute el siguiente script para quitar las particiones antiguas u otros datos.

> [!WARNING]
> Este script quitará permanentemente los datos de todas las unidades excepto la unidad de arranque del sistema Azure Stack HCI.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"

Invoke-Command ($ServerList) {
    Update-StorageProviderCache
    Get-StoragePool | ? IsPrimordial -eq $false | Set-StoragePool -IsReadOnly:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Remove-StoragePool -Confirm:$false -ErrorAction SilentlyContinue
    Get-PhysicalDisk | Reset-PhysicalDisk -ErrorAction SilentlyContinue
    Get-Disk | ? Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -ne RAW | % {
        $_ | Set-Disk -isoffline:$false
        $_ | Set-Disk -isreadonly:$false
        $_ | Clear-Disk -RemoveData -RemoveOEM -Confirm:$false
        $_ | Set-Disk -isreadonly:$true
        $_ | Set-Disk -isoffline:$true
    }
    Get-Disk | Where Number -Ne $Null | Where IsBoot -Ne $True | Where IsSystem -Ne $True | Where PartitionStyle -Eq RAW | Group -NoElement -Property FriendlyName
} | Sort -Property PsComputerName, Count
```

### <a name="step-32-test-cluster-configuration"></a>Paso 3.2: Comprobación de la configuración del clúster

En este paso, se asegurará de que los nodos del servidor estén configurados correctamente para crear un clúster. El cmdlet `Test-Cluster` se usa para ejecutar pruebas para comprobar que la configuración es adecuada para funcionar como un clúster hiperconvergido. En el ejemplo siguiente se usa el parámetro `-Include`, con las categorías de pruebas concretas especificadas. Esto garantiza que las pruebas correctas están incluidas en la validación.

```powershell
Test-Cluster -Cluster –Node "Server1", "Server2", "Server3", "Server4" –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
```

## <a name="step-4-create-the-cluster"></a>Paso 4: Creación del clúster

Ahora está listo para crear un clúster con los nodos de servidor que ha validado en los pasos anteriores.

Al crear el clúster, recibirá una advertencia con el siguiente mensaje: `"There were issues while creating the clustered role that may prevent it from starting. For more information, view the report file below."`. Puede omitir esta advertencia sin ningún problema. Se debe a que no hay ningún disco disponible para el testigo de clúster que creará más adelante. 

> [!NOTE]
> Si los servidores usan direcciones IP estáticas, modifique el siguiente comando para reflejar la dirección IP estática agregando el parámetro siguiente y especificando la dirección IP: `–StaticAddress <X.X.X.X>;`.

```powershell
 New-Cluster –Name "Cluster1" –Node "Server1", "Server2", "Server3", "Server4" –NoStorage
```

Enhorabuena, ahora se ha creado el clúster.

Una vez creado el clúster, el nombre del clúster puede tardar unos instantes en replicarse en el dominio, especialmente si los servidores del grupo de trabajo se han agregado recientemente a Active Directory. Aunque el clúster podría aparecer en Windows Admin Center, es posible que no esté disponible para conectarse todavía.

Si la resolución del clúster no se realiza correctamente después de un tiempo, en la mayoría de los casos podrá conectarse mediante el nombre de uno de los servidores agrupados, en lugar del nombre del clúster.

## <a name="step-5-set-up-sites-stretched-cluster"></a>Paso 5: Configuración de sitios (clúster extendido)

Esta tarea solo es aplicable si va a crear un clúster extendido entre dos sitios.

### <a name="step-51-create-sites"></a>Paso 5.1: Creación de sitios

En el cmdlet siguiente, *FaultDomain* es simplemente otro nombre para un sitio. En este ejemplo se usa "ClusterS1" como nombre del clúster extendido.

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site1"
```

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site2"
```

Use el cmdlet `Get-ClusterFaultDomain` para comprobar que se han creado ambos sitios para el clúster.

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-52-assign-server-nodes"></a>Paso 5.2: Asignación de nodos de servidor

A continuación, asignaremos los cuatro nodos de servidor a sus sitios respectivos. En el ejemplo siguiente, Server1 y Server2 se asignan a Site1, mientras que Server3 y Server4 se asignan a Site2.

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server1", "Server2" -Parent "Site1"
```

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server3", "Server4" -Parent "Site2"
```

Mediante el cmdlet `Get-ClusterFaultDomain`, compruebe que los nodos están en los sitios correctos.

```powershell
Get-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-53-set-a-preferred-site"></a>Paso 5.3: Establecimiento de un sitio preferido

También puede definir un sitio *preferido* global, lo que significa que los recursos y grupos especificados deben ejecutarse en el sitio preferido.  Este valor se puede definir en el nivel del sitio mediante el siguiente comando:  

```powershell
(Get-Cluster).PreferredSite = "Site1"
```

La especificación de un sitio preferido para clústeres extendidos presenta las siguientes ventajas:

- **Arranque en frío** : durante un arranque en frío, las máquinas virtuales se colocan en el sitio preferido.

- **Votación de cuórum**
  - Mediante el uso de un cuórum dinámico, la ponderación se reduce primero en el sitio pasivo (replicado) para garantizar que el sitio preferido sobreviva si todo lo demás sigue igual. Además, los nodos de servidor se eliminan del sitio pasivo en primer lugar durante la reagrupación después de eventos tales como errores de conectividad de red asimétrica.

  - Durante una división de quórum de dos sitios, si no se puede establecer contacto con el testigo del clúster, se elegirá automáticamente el sitio preferido. Los nodos de servidor del sitio pasivo abandonan la pertenencia al clúster. Esto permite que el clúster sobreviva a una pérdida simultánea del 50 % de los votos.

El sitio preferido también puede configurarse en el rol del clúster o en el nivel del grupo. En este caso, se puede configurar un sitio preferido diferente para cada grupo de máquinas virtuales. Esto permite que un sitio esté activo y sea el preferido para máquinas virtuales específicas.

## <a name="step-6-enable-storage-spaces-direct"></a>Paso 6: Habilitar Espacios de almacenamiento directo

Después de crear el clúster, use el cmdlet `Enable-ClusterStorageSpacesDirect`, que habilitará Espacios de almacenamiento directo y hará lo siguiente automáticamente:

- **Crear un grupo de almacenamiento:** crea un bloque de almacenamiento para el clúster con un nombre similar a "Cluster1 Storage pool".

- **Crear un disco de historial de rendimiento del clúster:** crea un disco virtual de historial de rendimiento del clúster en el bloque de almacenamiento.

- **Crear volúmenes de datos y de registro:** crea un volumen de datos y un volumen de registro en el bloque de almacenamiento.

- **Configurar memorias caché con Espacios de almacenamiento directo:** si hay más de un tipo de elemento multimedia (unidad) disponible para Espacios de almacenamiento directo, habilita los dispositivos de caché más rápidos (lectura y escritura en la mayoría de los casos).

- **Crear niveles:** crea dos niveles como niveles predeterminados. Uno se denomina "Capacity" y, el otro, "Performance". El cmdlet analiza los dispositivos y configura cada nivel con la combinación de tipos de dispositivo y resistencia.

En el caso de los clústeres extendidos, el cmdlet `Enable-ClusterStorageSpacesDirect` también hará lo siguiente:

- Comprobar si se han configurado los sitios.
- Determinar qué nodos están en qué sitios.
- Determinar qué almacenamiento tiene disponible cada nodo.
- Comprobar si la característica Réplica de almacenamiento está instalada en cada nodo.
- Crear un bloque de almacenamiento para cada sitio e identificarlo por el nombre del sitio.
- Crear volúmenes de datos y de registro en cada bloque de almacenamiento (uno por sitio).

El siguiente comando habilita Espacios de almacenamiento directo. También puede especificar un nombre descriptivo para un bloque de almacenamiento, como se muestra aquí:

```powershell
$session = New-CimSession -Cluster "Cluster1" | Enable-ClusterStorageSpacesDirect -PoolFriendlyName "Cluster1 Storage Pool"
```

Para ver los grupos de almacenamiento, use lo siguiente:

```powershell
Get-StoragePool -CimSession $session
```

Enhorabuena, ha creado un clúster básico.

## <a name="after-you-create-the-cluster"></a>Después de crear el clúster

Ahora que ha terminado, todavía hay algunas tareas importantes que debe completar:

- Configurar un testigo del clúster. Consulte [Configuración de un testigo del clúster](witness.md).
- Crear los volúmenes. Consulte [Creación de volúmenes](../manage/create-volumes.md).
- En el caso de los clústeres extendidos, cree volúmenes y configure la replicación mediante una réplica de almacenamiento. Consulte [Creación de volúmenes y configuración de la replicación para clústeres extendidos](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Pasos siguientes

- Registrar el clúster con Azure. Consulte [Administración del registro de Azure](../manage/manage-azure-registration.md).
- Realice una validación final del clúster. Consulte [Validación de un clúster de Azure Stack HCI](validate.md)
- Aprovisionar las VM. Consulte [Administración de máquinas virtuales en Azure Stack HCI mediante PowerShell](../manage/vm-powershell.md).
- También puede crear un clúster mediante Windows Admin Center. Consulte [Creación de un clúster de Azure Stack HCI mediante Windows Admin Center](create-cluster.md).
