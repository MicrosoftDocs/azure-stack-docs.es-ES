---
title: Uso del firewall del centro de datos para SDN en Azure Stack HCI
description: Use este tema como introducción al firewall del centro de datos para redes definidas por software en Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 833780947bd698a0e39709668715372bd8508e90
ms.sourcegitcommit: 40d3f3f0ac088d1590d1fb64ca05ac1dabf4e00c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2020
ms.locfileid: "94881239"
---
# <a name="use-datacenter-firewall-for-software-defined-networking-in-azure-stack-hci"></a>Uso del firewall del centro de datos para redes definidas por software en Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se proporcionan instrucciones para configurar las listas de control de acceso (ACL) para administrar el flujo del tráfico de datos mediante un [firewall del centro de datos](../concepts/datacenter-firewall-overview.md) para redes definidas por software (SDN) en Azure Stack HCI con Windows PowerShell. Para habilitar y configurar el firewall del centro de datos, cree listas de control de acceso (ACL) que se apliquen a una subred o una interfaz de red. En los scripts de ejemplo de este tema se usan los comandos de Windows PowerShell exportados del módulo **NetworkController**. También puede usar Windows Admin Center para configurar y administrar las ACL.

## <a name="configure-datacenter-firewall-to-allow-all-traffic"></a>Configuración del firewall del centro de datos para permitir todo el tráfico

Una vez que implemente SDN, debe probar la conectividad de red básica en el nuevo entorno. Para ello, cree una regla para el firewall del centro de datos que permita todo el tráfico de red, sin restricciones.

Use las entradas de la tabla siguiente para crear un conjunto de reglas que permitan todo el tráfico de red entrante y saliente.

| IP de origen | IP de destino | Protocolo | Puerto de origen | Puerto de destino | Dirección | Acción | Priority |
|:---------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|    \*     |       \*       |   All    |     \*      |        \*        |  Entrada  | Allow  |   100    |
|    \*     |       \*       |   All    |     \*      |        \*        | Salida  | Allow  |   110    |

---

En este ejemplo, creará una ACL con dos reglas:

1. **AllowAll_Inbound**: permite que todo el tráfico de red pase a la interfaz de red en la que está configurada esta ACL.
2. **AllowAllOutbound**: permite que todo el tráfico pase fuera de la interfaz de red. Esta ACL, identificada por el identificador de recurso "AllowAll-1" ya está lista para usarse en subredes virtuales e interfaces de red.

En primer lugar, conéctese a uno de los nodos del clúster. Para ello, abra una sesión de PowerShell:

```PowerShell
Enter-PSSession <server-name>
```

A continuación, ejecute el script siguiente para crear la ACL:

```PowerShell
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"
$aclrule1 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule1.Properties = $ruleproperties
$aclrule1.ResourceId = "AllowAll_Inbound"
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "110"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"
$aclrule2 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule2.Properties = $ruleproperties
$aclrule2.ResourceId = "AllowAll_Outbound"
$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = @($aclrule1, $aclrule2)
New-NetworkControllerAccessControlList -ResourceId "AllowAll" -Properties $acllistproperties -ConnectionUri <NC REST FQDN>
```

>[!NOTE]
>La referencia de comandos de Windows PowerShell para la Controladora de red se encuentra en el tema [Cmdlets de la Controladora de red](/powershell/module/networkcontroller/).

## <a name="use-acls-to-limit-traffic-on-a-subnet"></a>Uso de ACL para limitar el tráfico en una subred
En este ejemplo, creará una ACL que impide que las máquinas virtuales (VM) dentro de la subred 192.168.0.0/24 se comuniquen entre sí. Este tipo de ACL es útil para limitar la posibilidad de que un atacante se extienda lateralmente dentro de la subred, a la vez que permite que las VM reciban solicitudes desde fuera de la subred, así como para comunicarse con otros servicios en otras subredes.

|   IP de origen    | IP de destino | Protocolo | Puerto de origen | Puerto de destino | Dirección | Acción | Priority |
|:--------------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|  192.168.0.1   |       \*       |   All    |     \*      |        \*        |  Entrada  | Allow  |   100    |
|       \*       |  192.168.0.1   |   All    |     \*      |        \*        | Salida  | Allow  |   101    |
| 192.168.0.0/24 |       \*       |   All    |     \*      |        \*        |  Entrada  | Block  |   102    |
|       \*       | 192.168.0.0/24 |   All    |     \*      |        \*        | Salida  | Block  |   103    |
|       \*       |       \*       |   All    |     \*      |        \*        |  Entrada  | Allow  |   104    |
|       \*       |       \*       |   All    |     \*      |        \*        | Salida  | Allow  |   105    |

---

La ACL creada por el siguiente script de ejemplo, identificada por el identificador de recurso **Subnet-192-168-0-0**, ahora se puede aplicar a una subred de red virtual que use la dirección de subred "192.168.0.0/24". Las reglas de ACL se aplican automáticamente a cualquier interfaz de red que esté conectada a la subred de la red virtual.

A continuación se incluye un script de ejemplo para crear esta ACL mediante la API de REST de la Controladora de red:

```PowerShell
import-module networkcontroller
$ncURI = "https://mync.contoso.local"
$aclrules = @()

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "192.168.0.1"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.1"
$ruleproperties.Priority = "101"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Outbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "192.168.0.0/24"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "102"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.0/24"
$ruleproperties.Priority = "103"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Outbound"

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "104"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "105"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Outbound"
$aclrules += $aclrule

$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = $aclrules

New-NetworkControllerAccessControlList -ResourceId "Subnet-192-168-0-0" -Properties $acllistproperties -ConnectionUri $ncURI
```

## <a name="add-an-acl-to-a-network-interface"></a>Adición de una ACL a una interfaz de red

Una vez que haya creado una ACL y la haya asignado a una subred virtual, puede que quiera invalidar esa ACL predeterminada en la subred virtual con una ACL específica para una interfaz de red individual. En este caso, se aplican ACL específicas directamente a las interfaces de red conectadas a redes VLAN, en lugar de a la red virtual. Si tiene ACL establecidas en la subred virtual conectada a la interfaz de red, se aplican ambas ACL y se priorizan las ACL de la interfaz de red por encima de las ACL de la subred virtual.

En este ejemplo, se muestra cómo agregar una ACL a una red virtual.

>[!TIP]
>También es posible agregar una ACL al mismo tiempo que se crea la interfaz de red.

1. Obtenga o cree la interfaz de red a la que agregará la ACL.

   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Obtenga o cree la ACL que agregará a la interfaz de red.

   ```PowerShell
   $acl = get-networkcontrolleraccesscontrollist -ConnectionUri $uri -ResourceId "AllowAllACL"
   ```

3. Asigne la ACL a la propiedad AccessControlList de la interfaz de red.

   ```PowerShell
    $nic.properties.ipconfigurations[0].properties.AccessControlList = $acl
   ```

4. Agregue la interfaz de red en la Controladora de red.

   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="remove-an-acl-from-a-network-interface"></a>Eliminación de una ACL de una interfaz de red

En este ejemplo, se muestra cómo quitar una ACL de una interfaz de red. Al quitar una ACL, se aplica el conjunto predeterminado de reglas a la interfaz de red. El conjunto predeterminado de reglas permite todo el tráfico saliente, pero bloquea el entrante. Si quiere permitir todo el tráfico entrante, debe seguir el [ejemplo](#add-an-acl-to-a-network-interface) anterior para agregar una ACL que permita todo el tráfico entrante y saliente.

1. Obtenga la interfaz de red desde la que se quitará la ACL.
   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Asigne $null a la propiedad AccessControlList de ipConfiguration.
   ```PowerShell
   $nic.properties.ipconfigurations[0].properties.AccessControlList = $null
   ```

3. Agregue el objeto de la interfaz de red en la Controladora de red.
   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="firewall-auditing"></a>Auditoría de firewall

La auditoría de firewall es una nueva funcionalidad para el firewall del centro de datos que registra cualquier flujo que procesan las reglas de firewall de SDN. Se registran todas las ACL que tienen habilitado el registro. Los archivos de registro deben estar en una sintaxis coherente con los [registros de flujo de Azure Network Watcher](/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Estos registros se pueden usar para realizar diagnósticos o archivar para su posterior análisis.

A continuación se incluye un script de ejemplo para habilitar la auditoría de firewall en los servidores host. Actualice las variables del principio y ejecútelo en un clúster de Azure Stack HCI con la [Controladora de red](../concepts/network-controller-overview.md) implementada:

```PowerShell
$logpath = "C:\test\log1"
$servers = @("sa18n22-2", "sa18n22-3", "sa18n22-4")
$uri = "https://sa18n22sdn.sa18.nttest.microsoft.com"

# Create log directories on the hosts
invoke-command -Computername $servers  {
    param(
        $Path
    )
    mkdir $path    -force
} -argumentlist $LogPath

# Set firewall auditing settings on Network Controller
$AuditProperties = new-object Microsoft.Windows.NetworkController.AuditingSettingsProperties
$AuditProperties.OutputDirectory = $logpath
set-networkcontrollerauditingsettingsconfiguration -connectionuri $uri -properties $AuditProperties -force  | out-null

# Enable logging on each server
$servers = get-networkcontrollerserver -connectionuri $uri
foreach ($s in $servers) {
    $s.properties.AuditingEnabled = @("Firewall")
    new-networkcontrollerserver -connectionuri $uri -resourceid $s.resourceid -properties $s.properties -force | out-null
}
```

Una vez habilitado, aparece un nuevo archivo en el directorio especificado de cada host aproximadamente una vez por hora.  Debe procesar periódicamente estos archivos y quitarlos de los hosts.  El archivo actual tiene una longitud igual a cero y se bloquea hasta que se vacía en la marca de hora siguiente:

```syntax
PS C:\test\log1> dir

    Directory: C:\test\log1

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/19/2018   6:28 AM          17055 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL122803093.json
-a----        7/19/2018   7:28 AM           7880 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL132803173.json
-a----        7/19/2018   8:28 AM           7867 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL142803264.json
-a----        7/19/2018   9:28 AM          10949 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL152803360.json
-a----        7/19/2018   9:28 AM              0 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL162803464.json
```

Estos archivos contienen una secuencia de eventos de flujo; por ejemplo:

```syntax
{
    "records": [
        {
            "properties":{
                "Version":"1.0",
                "flows":[
                    {
                        "flows":[
                            {
                                "flowTuples":["1531963580,192.122.0.22,192.122.255.255,138,138,U,I,A"],
                                "portId":"9",
                                "portName":"7290436D-0422-498A-8EB8-C6CF5115DACE"
                            }
                        ],
                        "rule":"Allow_Inbound"
                    }
                ]
            },
            "operationName":"NetworkSecurityGroupFlowEvents",
            "resourceId":"394f647d-2ed0-4c31-87c5-389b8c0c8132",
            "time":"20180719:L012620622",
            "category":"NetworkSecurityGroupFlowEvent",
            "systemId":"d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a"
            },
```

Tenga en cuenta que el registro solo tiene lugar para las reglas que tienen el valor **Logging** definido como **Enabled**; por ejemplo:

```syntax
{
    "Tags":  null,
    "ResourceRef":  "/accessControlLists/AllowAll",
    "InstanceId":  "4a63e1a5-3264-4986-9a59-4e77a8b107fa",
    "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
    "ResourceMetadata":  null,
    "ResourceId":  "AllowAll",
    "Properties":  {
                       "ConfigurationState":  null,
                       "ProvisioningState":  "Succeeded",
                       "AclRules":  [
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Inbound",
                                            "InstanceId":  "ba8710a8-0f01-422b-9038-d1f2390645d7",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Inbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "101",
                                                               "Description":  null,
                                                               "Type":  "Inbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        },
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Outbound",
                                            "InstanceId":  "068264c6-2186-4dbc-bbe7-f504c6f47fa8",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Outbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "110",
                                                               "Description":  null,
                                                               "Type":  "Outbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        }
                                    ],
                       "IpConfigurations":  [

                                            ],
                       "Subnets":  [
                                       {
                                           "ResourceMetadata":  null,
                                           "ResourceRef":  "/virtualNetworks/10_0_1_0/subnets/Subnet1",
                                           "InstanceId":  "00000000-0000-0000-0000-000000000000",
                                           "Etag":  null,
                                           "ResourceId":  null,
                                           "Properties":  null
                                       }
                                   ]
                   }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Introducción al firewall del centro de datos](../concepts/datacenter-firewall-overview.md)
- [Introducción a la controladora de red](../concepts/network-controller-overview.md)
- [SDN en Azure Stack HCI](../concepts/software-defined-networking.md)