---
title: Adición o eliminación de servidores para un clúster de Azure Stack HCI
description: Aprenda agregar o quitar nodos de servidor de un clúster en Azure Stack HCI
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 11/06/2020
ms.openlocfilehash: 1caa5e6573137ec33680ea3a13e7beeda12de424
ms.sourcegitcommit: 08ef9545316798c9a21c2f9bc1da8c15cb648982
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360197"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Adición o eliminación de servidores para un clúster de Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2

Puede agregar o quitar servidores fácilmente de un clúster en Azure Stack HCI. Tenga en cuenta que cada nuevo servidor físico debe coincidir al máximo con el resto de los servidores del clúster en lo relativo a tipo de CPU, memoria, número de unidades y tipo y tamaño de estas.

Siempre que agregue o quite un servidor, también debe realizar la validación del clúster posteriormente para asegurarse de que el clúster funciona con normalidad. Esto se aplica a los clústeres no extendidos y extendidos.

## <a name="obtain-oem-hardware"></a>Obtener hardware de OEM

El primer paso es adquirir hardware de HCI nuevo a partir del OEM original. Haga siempre referencia a la documentación proporcionada por el OEM al agregar un nuevo hardware de servidor para su uso en el clúster.

1. Coloque el nuevo servidor físico en el bastidor y cabléelo de forma adecuada.
1. Habilite los puertos de conmutador físico y ajuste las listas de control de acceso e identificadores de VLAN si procede.
1. Configure la dirección IP correcta en el controlador de administración de placa base (BMC) y aplique toda la configuración de BIOS según las instrucciones del OEM.
1. Aplique la base de referencia del firmware actual a todos los componentes mediante las herramientas proporcionadas por el OEM.
1. Ejecute las pruebas de validación de OEM para garantizar la homogeneidad de los servidores de clúster existentes.

## <a name="add-a-server-to-a-cluster"></a>Adición de un servidor a un clúster

Una vez que el servidor se haya puesto en marcha correctamente, use Windows Admin Center para unir el servidor al clúster.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Pantalla Agregar servidor" lightbox="media/manage-cluster/add-server.png":::

1. En **Windows Admin Center**, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Servidores**.
1. En **Servidores**, seleccione la pestaña **Inventario**.
1. En la pestaña **Inventario**, seleccione **Agregar**.
1. En **Nombre del servidor**, escriba el nombre de dominio completo del servidor que desea agregar, haga clic en **Agregar** y, a continuación, haga clic en **Agregar** de nuevo en la parte inferior.
1. Compruebe que el servidor se ha agregado correctamente al clúster.

## <a name="remove-a-server-from-a-cluster"></a>Eliminación de un servidor de un clúster

Los pasos para quitar un servidor del clúster son parecidos a los pasos para agregarlo.

Tenga en cuenta que cuando se quita un servidor, también se quitan las máquinas virtuales, unidades y cargas de trabajo asociadas al servidor.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Cuadro de diálogo Quitar servidor" lightbox="media/manage-cluster/remove-server.png":::

1. En **Windows Admin Center**, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Servidores**.
1. En **Servidores**, seleccione la pestaña **Inventario**.
1. En la pestaña **Inventario**, seleccione el servidor que desea quitar y, a continuación, seleccione **Quitar**.
1. Para quitar también todas las unidades de servidor del bloque de almacenamiento, seleccione esa casilla.
1. Compruebe que el servidor se ha quitado correctamente del clúster.

Siempre que agregue o quite servidores de un clúster, asegúrese de ejecutar una prueba de validación del clúster después.

## <a name="add-server-pairs-to-a-stretched-cluster"></a>Adición de pares de servidores a un clúster extendido

Los clústeres extendidos requieren el mismo número de nodos de servidor y el mismo número de unidades en cada sitio. Al agregar un par de servidores a un clúster extendido, sus unidades se agregan inmediatamente al bloque de almacenamiento de ambos sitios en el clúster extendido. Si el bloque de almacenamiento de cada sitio no tiene el mismo tamaño en el momento de la adición, se rechaza. Esto se debe a que el tamaño del bloque de almacenamiento debe ser el mismo entre sitios.

A diferencia de los clústeres no extendidos, solo se pueden agregar servidores a un clúster extendido o quitarlos de él mediante Windows PowerShell. Con los cmdlets [Get-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/get-clusterfaultdomainxml) y [Set-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterfaultdomainxml), primero debe modificar la información del sitio (dominio de error) antes de agregar los servidores.

A continuación, puede agregar el par de servidores a cada sitio simultáneamente mediante el cmdlet [Add-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode), lo que permite agregar también las unidades de cada nuevo servidor al mismo tiempo.

Normalmente, los clústeres se administran desde un equipo remoto, en lugar de hacerlo en un servidor de un clúster. Este equipo remoto se denomina equipo de administración.

> [!NOTE]
> Al ejecutar comandos de PowerShell desde un equipo de administración, incluya el parámetro `-Cluster` con el nombre del clúster que administra.

Empecemos:

1. Use los siguientes cmdlets de PowerShell para determinar el estado del clúster:

    Devuelve la lista de servidores activos en el clúster:

     ```powershell
    Get-ClusterNode
    ```

    Devuelve las estadísticas del bloque de almacenamiento del clúster:

    ```powershell
    Get-StoragePool pool*
    ```

    Enumera los servidores que están en el sitio (dominio de error):

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Abra el archivo `Sites.xml` en el Bloc de notas o en otro editor de texto:

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. Navegue hasta la ubicación en la que se encuentra el archivo `Sites.xml` localmente en el equipo de administración y abra el archivo. El archivo `Sites.xml` será similar al siguiente:

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
        </Site>
    <Topology>
    ```

1. Con este ejemplo, agregaría un servidor a cada sitio (`Server5`, `Server6`) de la manera siguiente:

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
            <Node Name="Server5" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
            <Node Name="Server6" Description="" Location="">
        </Site>
    <Topology>
    ```

1. Modifique la información del sitio actual (dominio de error).  El primer comando establece una variable para obtener el contenido del archivo `Sites.xml` y mostrarlo. El segundo comando establece la modificación en función de la variable `$XML`.

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Compruebe que las modificaciones que ha realizado son correctas:

    ```
    Get-ClusterFaultDomain
    ```

1. Agregue el par de servidores al clúster mediante el cmdlet `Add-ClusterNode`:

    ```
    Add-ClusterNode -Name Server5,Server6
    ```

Una vez que los servidores se han agregado correctamente, las unidades asociadas se agregan automáticamente a los bloques de almacenamiento de cada sitio. Por último, el Servicio de mantenimiento crea un trabajo de almacenamiento para incluir las nuevas unidades.

## <a name="remove-server-pairs-from-a-stretched-cluster"></a>Eliminación de pares de servidores de un clúster extendido

Quitar un par de servidores de un clúster extendido es un proceso similar a agregar un par de servidores, pero con el cmdlet [Remove-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode) en su lugar.

1. Use los siguientes cmdlets de PowerShell para determinar el estado del clúster:

    Devuelve la lista de servidores activos en el clúster:

     ```powershell
    Get-ClusterNode
    ```

    Devuelve las estadísticas del bloque de almacenamiento del clúster:

    ```powershell
    Get-StoragePool pool*
    ```

    Enumera los servidores que están en el sitio (dominio de error):

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Abra el archivo `Sites.xml` en el Bloc de notas o en otro editor de texto:

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. En el archivo `Sites.xml` del ejemplo anterior, quite la entrada XML `<Node Name="Server5" Description="" Location="">` y `<Node Name="Server6" Description="" Location="">` para cada sitio.
1. Modifique la información del sitio actual (dominio de error) mediante los dos cmdlets siguientes:

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Compruebe que las modificaciones que ha realizado son correctas:

    ```
    Get-ClusterFaultDomain
    ```
1. Quite los pares de servidores del clúster mediante el cmdlet `Remove-ClusterNode`:

    ```
    Remove-ClusterNode -Name Server5,Server6
    ```

Una vez que los servidores se han quitado correctamente, las unidades asociadas se quitan automáticamente de los bloques de cada sitio. Por último, el Servicio de mantenimiento crea un trabajo de almacenamiento para quitar estas unidades.

## <a name="next-steps"></a>Pasos siguientes

- Debe validar el clúster después de agregar o quitar un servidor. Para más información, consulte [Validación del clúster](../deploy/validate.md).