---
title: Validación de un clúster de Azure Stack HCI
description: Descripción de la importancia de la validación del clúster y cuándo ejecutarlo en un clúster de Azure Stack HCI existente. Explore escenarios para la solución de problemas de un clúster de servidores actualizado.
author: JohnCobb1
ms.author: v-johcob
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 8a096af308901669def134e0dd281490c5ed0294
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572092"
---
# <a name="validate-an-azure-stack-hci-cluster"></a>Validación de un clúster de Azure Stack HCI

>Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Este artículo de procedimientos se centra en los motivos por los que la validación de clústeres es importante y cuándo ejecutarla en un clúster existente de Azure Stack HCI. Se recomienda realizar la validación de clústeres para los siguientes escenarios principales:
- Después de implementar un clúster de servidores, ejecute la herramienta Validate-DCB para probar las redes, y ejecute la validación de clústeres en Windows Admin Center.
- Después de actualizar un clúster de servidores, en función del escenario, ejecute ambas opciones de validación para solucionar los problemas del clúster.
- Después de configurar la replicación con Réplica de almacenamiento, compruebe que la replicación se esté realizando normalmente; para ello, compruebe algunos eventos en concreto y ejecute un par de comandos.
Para obtener información sobre cómo implementar un clúster de Azure Stack HCI, consulte [Implementación de Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct).

## <a name="what-is-cluster-validation"></a>¿Qué es la validación de un clúster?
La validación de un clúster está pensada para detectar problemas de hardware o de configuración antes de que un clúster pase a producción. La validación de un clúster ayuda a asegurar que la solución de Azure Stack HCI que va a implementar sea realmente confiable. También puede usar la validación de un clúster en clústeres de conmutación por error configurados, como herramienta de diagnóstico.

## <a name="specific-validation-scenarios"></a>Escenarios de validación específicos
En esta sección se describen los escenarios en los que la validación también es necesaria o útil.

- **Validación antes de configurar el clúster:**
  - **Un conjunto de servidores listo para convertirse en un clúster de conmutación por error:** Este es el escenario de validación más sencillo. Los componentes de hardware (sistemas, redes y almacenamiento) están conectados, pero los sistemas todavía no funcionan como clúster. La ejecución de pruebas en esta situación no afecta a la disponibilidad.
 
  - **VM de servidor:** En el caso de los servidores virtualizados en un clúster, ejecute la validación del clúster como lo haría en cualquier otro clúster nuevo. El requisito de ejecutar la característica es la misma así tenga:
    - Un "clúster de hosts" donde se produce la conmutación por error entre dos equipos físicos.
    - Un "clúster invitado" donde se produce la conmutación por error entre sistemas operativos invitados en el mismo equipo físico.
 
- **Validación después de que el clúster está configurado y en uso:**

  - **Antes de agregar un servidor al clúster:** Al agregar un servidor a un clúster, se recomienda encarecidamente validar el clúster. Especifique tanto los miembros del clúster existentes como el nuevo servidor al ejecutar la validación del clúster.
  
  - **Al agregar unidades:** Al agregar unidades adicionales al clúster, lo que es diferente de reemplazar las unidades con error o de crear discos o volúmenes virtuales que dependen de las unidades existentes, ejecute la validación del clúster para confirmar que el nuevo almacenamiento funcionará correctamente.

  - **Al realizar cambios que afecten al firmware o los controladores:** Si actualiza o realiza cambios en el clúster que afecten al firmware o a los controladores, debe ejecutar la validación del clúster para confirmar que la nueva combinación de hardware, firmware, controladores y software es compatible con la funcionalidad de clúster de conmutación por error.

  - **Después de restaurar un sistema desde una copia de seguridad:** Después de restaurar un sistema desde una copia de seguridad, ejecute la validación del clúster para confirmar que el sistema funcione correctamente como parte de un clúster.

## <a name="validate-networking"></a>Validación de las redes
La herramienta Microsoft Validate-DCB está diseñada para validar la configuración del Protocolo de puente del centro de datos (DCB) en el clúster. Para ello, la herramienta toma una configuración esperada como entrada y, a continuación, prueba cada servidor del clúster. En esta sección se explica cómo instalar y ejecutar la herramienta Validate-DCB, revisar los resultados y resolver los errores de red que identifique la herramienta.

En la red, el acceso directo a memoria remota (RDMA) sobre Ethernet convergente (RoCE) requiere tecnologías DCB para que el tejido de red no tenga pérdidas. Y, si bien iWARP no requiere DCB, aún así se recomienda. Sin embargo, la configuración de DCB puede ser compleja, donde se necesita una configuración exacta para:
- Cada servidor del clúster
- Cada puerto de red por el que pasa el tráfico RDMA en el tejido

### <a name="prerequisites"></a>Requisitos previos
- Información de configuración de red del clúster de servidores que desea validar, incluido:
    - Nombre del clúster de servidores o host
    - Nombre del conmutador virtual
    - Nombres de los adaptadores de red
    - Configuración del control de flujo basado prioridades (PFC) y la selección de transmisión mejorada (ETS)
- Una conexión a Internet para descargar el módulo de herramientas en Windows PowerShell de Microsoft.

### <a name="install-and-run-the-validate-dcb-tool"></a>Instalación y ejecución de la herramienta Validate-DCB
Para instalar y ejecutar la herramienta Validate-DCB:
1. En el equipo de administración, abra una sesión de Windows PowerShell como administrador y, después, use el siguiente comando para instalar la herramienta.

    ```powershell
    Install-Module Validate-DCB
    ```

1. Acepte las solicitudes para usar el proveedor de NuGet y acceda al repositorio para instalar la herramienta.
1. Una vez que PowerShell se conecte a la red de Microsoft para descargar la herramienta, escriba `Validate-DCB` y presione **ENTRAR** para iniciar el asistente de la herramienta.

    > [!NOTE]
    > Si no puede ejecutar el script de la herramienta Validate-DCB, es posible que tenga que ajustar las directivas de ejecución de PowerShell. Use el cmdlet Get-ExecutionPolicy para ver la configuración actual de la directiva de ejecución de scripts. Para obtener más información sobre la configuración de directivas de ejecución en PowerShell, consulte [Acerca de las directivas de ejecución](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7).

1. En la página del asistente para la configuración de Validate-DCB, haga clic en **Siguiente**.
1. En la página Clusters and Nodes (Clústeres y nodos), escriba el nombre del clúster de servidores que desea validar, seleccione **Resolver** para que se muestre en la página y, a continuación, seleccione **Siguiente**.

    :::image type="content" source="../media/validate/clusters-and-nodes.png" alt-text="Página de clústeres y nodos del asistente para la configuración de Validate-DCB":::

1. En la página Adaptadores:
   1. Active la casilla **vSwitch attached** (vSwitch conectado) y escriba el nombre del vSwitch.
   1. En **Nombre de adaptador**, escriba el nombre de cada NIC física, en **Host vNIC Name** (Nombre de vNIC host), el nombre de cada NIC virtual (vNIC) y, en **VLAN**, el identificador de VLAN que se usa para cada adaptador.
   1. Expanda el cuadro de lista desplegable **RDMA Type** (Tipo de RDMA) y seleccione el protocolo adecuado: **RoCE** o **iWARP**. Establezca también **Tramas gigantes** en el valor adecuado para la red y, a continuación, seleccione **Siguiente**.

    :::image type="content" source="../media/validate/adapters.png" alt-text="Página Adaptadores del asistente para la configuración de Validate-DCB" lightbox="../media/validate/adapters.png":::

    > [!NOTE]
    > - Para obtener más información sobre cómo SR-IOV mejora el rendimiento de la red, consulte [Información general de la virtualización de E/S de raíz única (SR-IOV)](/windows-hardware/drivers/network/overview-of-single-root-i-o-virtualization--sr-iov-).

1. En la página Protocolo de puente del centro de datos, modifique los valores para que coincidan con la configuración de la organización en cuanto a **Prioridad**, **Nombre de directiva**y **Bandwidth Reservation** (Reserva de ancho de banda) y, a continuación, seleccione **Siguiente**.

    :::image type="content" source="../media/validate/data-center-bridging.png" alt-text="Página Protocolo de puente del centro de datos del asistente para la configuración de Validate-DCB" lightbox="../media/validate/data-center-bridging.png":::

    > [!NOTE]
    > La selección de RDMA sobre RoCE en la página anterior del asistente requiere DCB para la confiabilidad de la red en todas las NIC y switchports.

1. En la página Guardar e implementar, en el cuadro **Ruta de archivo de configuración**, guarde el archivo de configuración con una extensión .ps1 en una ubicación en la que pueda utilizarlo de nuevo más adelante si es necesario y, a continuación, seleccione **Exportar** para comenzar la ejecución de la herramienta Validate-DCB.

   - De manera opcional, puede implementar el archivo de configuración completando la sección **Deploy Configuration to Nodes** (Implementar la configuración en los nodos) de la página, que incluye la capacidad de usar una cuenta de Azure Automation para implementar la configuración y, después, validarla. Consulte [Creación de una cuenta de Azure Automation](/azure/automation/automation-quickstart-create-account) para empezar a trabajar con Azure Automation.

    :::image type="content" source="../media/validate/save-and-deploy.png" alt-text="Página Guardar e implementar del asistente para la configuración de Validate-DCB":::

### <a name="review-results-and-fix-errors"></a>Revisión de los resultados y corrección de los errores
La herramienta Validate-DCB genera resultados en dos unidades:
1. Los resultados [Global Unit] muestran los requisitos previos y requisitos para ejecutar las pruebas modales.
1. Los resultados [Modal Unit] proporcionan comentarios sobre la configuración de cada host de clúster y sobre los procedimientos recomendados.

En este ejemplo se muestran resultados de exámenes correctos de un solo servidor para todos los requisitos previos, y las pruebas de unidades modales indicando un recuento de errores (FailedConunt) de 0.

:::image type="content" source="../media/validate/global-unit-and-modal-unit-results.png" alt-text="Resultados de pruebas de unidades globales y unidades modales de Validate-DCB":::

En los pasos siguientes se muestra cómo identificar un error del paquete gigante desde vNIC SMB02 y cómo corregirlo:
1. Los resultados de los exámenes de la herramienta Validate-DCB muestran un recuento de errores de 1.

    :::image type="content" source="../media/validate/failed-count-error-1.png" alt-text="Resultados de exámenes de la herramienta Validate-DCB que muestran un recuento de errores de 1":::

1. Al volver por los resultados se muestra un error en rojo que indica que el paquete gigante para vNIC SMB02 en el host S046036 se ha establecido en el tamaño predeterminado de 1514, pero debe establecerse en 9014.

    :::image type="content" source="../media/validate/jumbo-packet-setting-error.png" alt-text="Resultado de exámenes de la herramienta Validate-DCB que muestra un error de configuración de tamaño del paquete gigante":::

1. Al revisar las propiedades **Avanzadas** de vNIC SMB02 en el host S046036, se muestra que el paquete gigante está establecido en el valor predeterminado **Disabled** (Deshabilitado).

    :::image type="content" source="../media/validate/hyper-v-advanced-properties-jumbo-packet-setting.png" alt-text="Propiedades avanzadas de la configuración del paquete gigante de Hyper-V del host del servidor":::

1. Para corregir el error, es necesario habilitar la característica de paquetes gigantes y cambiar su tamaño a 9014 bytes. Al ejecutar el examen de nuevo en el host S046036 se confirma este cambio al devolverse un recuento de errores de 0.

    :::image type="content" source="../media/validate/jumbo-packet-error-fix-confirmation.png" alt-text="Resultados del examen de Validate-DCB que confirman que la configuración del paquete gigante del host del servidor se ha corregido":::

Para obtener más información sobre cómo resolver errores que identifica la herramienta Validate-DCB, vea el vídeo siguiente.

> [!VIDEO https://www.youtube.com/embed/cC1uACvhPBs]

## <a name="validate-the-cluster"></a>Validación del clúster
Siga estos pasos para validar los servidores de un clúster existente en Windows Admin Center.

1. En Windows Admin Center, en **Todas las conexiones**, seleccione el clúster de Azure Stack HCI que desea validar y, a continuación, seleccione **Conectar**.

    En el panel **Cluster Manager** (Administrador de clústeres) se muestra información general sobre el clúster.

1. En el panel **Cluster Manager** (Administrador de clústeres), en **Herramientas**, seleccione **Servidores**.
1. En la página **Inventario**, seleccione los servidores del clúster, expanda el submenú **Más** y seleccione **Validar clúster**.
1. En la ventana emergente **Validar clúster**, seleccione **Sí**.

    :::image type="content" source="../media/validate/validate-cluster-pop-up.png" alt-text="Ventana emergente Validar clúster":::

1. En la ventana emergente **Credential Security Service Provider (CredSSP)** (Proveedor de servicios de seguridad de credenciales [CredSSP]), seleccione **Sí**.
1. Proporcione sus credenciales para habilitar **CredSSP** y, después, seleccione **Continuar**.<br> La validación del clúster se ejecuta en segundo plano y le proporciona una notificación cuando se completa, momento en el que puede ver el informe de validación, tal como se describe en la sección siguiente.

> [!NOTE]
> Una vez validados los servidores del clúster, deberá deshabilitar CredSSP por motivos de seguridad.

### <a name="disable-credssp"></a>Deshabilitación de CredSSP
Después de haber validado correctamente el clúster de servidores, tendrá que deshabilitar el protocolo de Proveedor de compatibilidad para seguridad de credenciales (CredSSP) en cada servidor por motivos de seguridad. Para obtener más información, consulte [CVE-2018-0886](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-0886).

1. En Windows Admin Center, en **Todas las conexiones**, seleccione el primer servidor del clúster y, a continuación, seleccione **Conectar**.
1. En la página **Información general**, seleccione **Disable CredSSP** (Deshabilitar CredSSP) y, a continuación, en la ventana emergente **Disable CredSSP** (Deshabilitar CredSSP), seleccione **Sí**.

    El resultado del paso 2 quita el banner rojo **CredSSP HABILITADO** en la parte superior de la página **Información general** del servidor y deshabilita CredSSP en los otros servidores.

### <a name="view-validation-reports"></a>Visualización del informe de validación
Ahora está listo para ver el informe de validación del clúster.

Hay un par de formas de acceder a los informes de validación:
- En la página **Inventario**, expanda el submenú **Más** y, a continuación, seleccione **View validation reports** (Ver informes de validación).


- En la parte superior derecha de **Windows Admin Center**, seleccione el icono de campana **Notificaciones** para mostrar el panel **Notificaciones**.
Seleccione el aviso **Successfully validated cluster** (Clúster validado correctamente) y, a continuación, seleccione **Go to Failover Cluster validation report** (Ir al informe de validación de clúster de conmutación por error).

> [!NOTE]
> El proceso de validación del clúster de servidores puede tardar algún tiempo en completarse. No cambie a otra herramienta en Windows Admin Center mientras se ejecuta el proceso. En el panel **Notificaciones**, una barra de estado situada debajo del aviso **Validar clúster** indica cuándo se completa el proceso.

## <a name="validate-the-cluster-using-powershell"></a>Validación del clúster mediante PowerShell
También puede usar Windows PowerShell para ejecutar pruebas de validación en el clúster de servidores y ver los resultados. Puede ejecutar pruebas tanto antes como después de configurar un clúster.

Para ejecutar una prueba de validación en un clúster de servidores, emita los cmdlets **Get-Cluster** y **Test-Cluster** <server clustername> de PowerShell en el equipo de administración, o bien ejecute solo el cmdlet **Test-Cluster** directamente en el clúster:

```PowerShell
$Cluster = Get-Cluster -Name 'server-cluster1'
Test-Cluster -InputObject $Cluster -Verbose
```

Para más ejemplos e información de uso, consulte la documentación de referencia de [Test-Cluster](/powershell/module/failoverclusters/test-cluster?view=win10-ps).

## <a name="validate-replication-for-storage-replica"></a>Validación de la replicación para Réplica de almacenamiento
Si usa Réplica de almacenamiento para replicar volúmenes en un clúster extendido o de clúster a clúster, hay varios eventos y cmdlets que puede usar para obtener el estado de replicación. 

En el escenario siguiente, se configura Réplica de almacenamiento mediante la creación de grupos de replicación (RG) para dos sitios y, a continuación, se especifican los volúmenes de datos y de registro tanto para los nodos de servidor de origen de Site1 (Server1, Server2) como para los nodos de servidor de destino (replicados) en Site2 (Server3, Server4).

Para determinar el progreso de la replicación para Server1 en Site1, ejecute el comando Get-WinEvent y examine los eventos 5015, 5002, 5004, 1237, 5001 y 2200:

```powershell
Get-WinEvent -ComputerName Server1 -ProviderName Microsoft-Windows-StorageReplica -max 20
```

Para Server3 en Site2, ejecute el siguiente comando `Get-WinEvent` para ver los eventos de Réplica de almacenamiento que muestran la creación de la asociación. Este evento indica el número de bytes copiados y el tiempo insumido. Por ejemplo:

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | Where-Object {$_.ID -eq "1215"} | FL
```

Para Server3 en Site2, ejecute el comando `Get-WinEvent` y examine los eventos 5009, 1237, 5001, 5015, 5005 y 2200 para entender el progreso del procesamiento. No debería haber ninguna advertencia de error en esta secuencia. Habrá muchos eventos 1237, lo que indica progreso.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Como alternativa, el grupo de servidores de destino de la réplica indica el número de bytes que quedan por copiar en todo momento, y se puede consultar a través de PowerShell con `Get-SRGroup`. Por ejemplo:

```powershell
(Get-SRGroup).Replicas | Select-Object numofbytesremaining
```

Para el nodo Server3 en Site2, ejecute el siguiente comando y examine los eventos 5009, 1237, 5001, 5015, 5005 y 2200 para comprender el progreso de la replicación. No debería haber advertencias de error. Sin embargo, habrá muchos eventos "1237", que simplemente indican el progreso.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Como script de progreso que no finalizará:

```powershell
while($true) {
$v = (Get-SRGroup -Name "Replication2").replicas | Select-Object numofbytesremaining
[System.Console]::Write("Number of bytes remaining: {0}`r", $v.numofbytesremaining)
Start-Sleep -s 5
}
```

Para obtener el estado de replicación en el clúster extendido, use `Get-SRGroup` y `Get-SRPartnership`:

```powershell
Get-SRGroup -Cluster ClusterS1
```

```powershell
Get-SRPartnership -Cluster ClusterS1
```

```powershell
(Get-SRGroup).replicas -Cluster ClusterS1
```

Una vez confirmada la replicación de datos correcta entre sitios, puede crear las VM y otras cargas de trabajo.

## <a name="see-also"></a>Consulte también
- Pruebas de rendimiento frente a cargas de trabajo sintéticas en un espacio de almacenamiento recién creado mediante DiskSpd.exe. Para obtener más información, consulte [Prueba del rendimiento de los espacios de almacenamiento mediante cargas de trabajo sintéticas en Windows Server](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn894707(v=ws.11)).
- Evaluación de Windows Server es un servicio Premier disponible para los clientes que quieren que Microsoft revise sus instalaciones de Windows Server 2019. Para obtener más información, póngase en contacto con el servicio de soporte técnico Premier de Microsoft. Para obtener más información, consulte [Introducción a las evaluaciones a petición de Windows Server (servidor, seguridad, Hyper-V, clúster de conmutación por error, IIS)](/services-hub/health/getting-started-windows-server).
