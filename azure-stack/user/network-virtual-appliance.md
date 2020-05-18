---
title: Solución de problemas de aplicaciones virtuales de red en Azure Stack Hub
description: Solucione problemas y errores de conectividad de máquina virtual o de VPN al utilizar una aplicación virtual de red (NVA) de terceros en Microsoft Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5e42f9e1b099df2ab1dc1063b3bf2710f59ad66f
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342948"
---
# <a name="troubleshoot-network-virtual-appliance-issues"></a>Solución de problemas de dispositivos virtuales de red

Puede que experimente problemas y errores de conectividad de máquina virtual o de VPN al utilizar una aplicación virtual de red (NVA) de terceros en Microsoft Azure Stack Hub. En este artículo se proporcionan los pasos básicos para ayudarle a validar los requisitos básicos de la plataforma de Azure Stack Hub para las configuraciones de aplicación virtual de red.

El soporte técnico para las aplicaciones virtuales de red de terceros y su integración con la plataforma de Azure Stack Hub lo proporciona el proveedor de la aplicación virtual de red.

> [!NOTE]
> Si tiene un problema de conectividad o de enrutamiento que implica una aplicación virtual de red, debería [ponerse en contacto directamente con el proveedor de la aplicación virtual de red](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Si su problema con la aplicación virtual de red de Azure Stack Hub no se encuentra en este artículo, cree una [incidencia de soporte técnico de Azure Stack Hub](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista de comprobación para la solución de problemas con el proveedor de la aplicación virtual de red

- Actualizaciones de software para software de máquinas virtuales de aplicaciones virtuales de red.
- Funcionalidad y configuración de la cuenta de servicio.
- Rutas definidas por el usuario (UDR) en subredes de red virtual que dirigen el tráfico a la aplicación virtual de red.
- Rutas definidas por el usuario (UDR) en subredes de red virtual que dirigen el tráfico de la aplicación virtual de red.
- Tablas de enrutamiento y reglas en la aplicación virtual de red (por ejemplo, de NIC1 a NIC2).
- Seguimiento de las NIC de la aplicación virtual de red para comprobar la recepción y el envío del tráfico de red.

## <a name="basic-troubleshooting-steps"></a>Pasos básicos para solucionar problemas

- Comprobación de la configuración básica.
- Comprobación del rendimiento de la aplicación virtual de red.
- Solución de problemas avanzados de red.

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Comprobación de los requisitos mínimos de la configuración de las aplicaciones virtuales de red en Azure

Cada aplicación virtual de red tiene requisitos básicos de configuración para funcionar correctamente en Azure . En la sección siguiente se proporcionan los pasos para comprobar estas configuraciones básicas. Para más información, [póngase en contacto con el proveedor de la aplicación virtual de red](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Si se usa un túnel S2S, los paquetes se encapsulan con encabezados adicionales. Esto aumenta el tamaño global del paquete. En estos escenarios, debe fijar TCP **MSS** en **1350**. Si los dispositivos VPN no admiten la fijación de MSS, puede establecer la opción MTU en la interfaz de túnel en **1400** bytes. Para más información, consulte [Optimización del rendimiento de TCP/IP de la red virtual](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-nva"></a>Comprobación de si está habilitado el reenvío IP en la aplicación virtual de red

#### <a name="use-the-azure-stack-hub-portal"></a>Uso del portal de Azure Stack Hub

- Busque el recurso de la aplicación virtual de red en el portal de Azure Stack Hub, seleccione las **redes** y, después, seleccione la interfaz de red.
- En la página **Interfaz de red**, seleccione **Configuraciones de IP**.
- Asegúrese de que está habilitado el reenvío IP.

#### <a name="use-powershell"></a>Uso de PowerShell

- Ejecute el siguiente comando (reemplace los valores entre corchetes por su información):

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

- Compruebe la propiedad **EnableIPForwarding**.
- Si el reenvío IP no está habilitado, ejecute los siguientes comandos para habilitarlo:

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-the-traffic-can-be-routed-to-the-nva"></a>Compruebe si se puede enrutar el tráfico a la aplicación virtual de red.

- Búsqueda de una máquina virtual que esté configurada para redirigir el tráfico a la aplicación virtual de red
- Ejecute `Tracert <Private IP of NVA>` para Windows o `Traceroute <Private IP of NVA>` para seleccionar la aplicación virtual de red como próximo salto.
- Si la aplicación virtual de red no aparece como próximo salto, compruebe y actualice las tablas de rutas de Azure Stack Hub.

Algunos sistemas operativos en el nivel de invitado pueden tener directivas de firewall para bloquear el tráfico ICMP. Estas reglas de firewall deben actualizarse para que los comandos anteriores funcionen.

### <a name="check-whether-the-traffic-can-reach-the-nva"></a>Comprobación de si el tráfico puede llegar a la aplicación virtual de red

- Busque una máquina virtual que debiera estar conectada a la aplicación virtual de red.
- Ejecute `ping (ICMP)` o `Test-NetConnection (TCP) <Private IP of NVA>` para Windows y `Tcpping <Private IP of NVA>` para Linux, para comprobar si el tráfico está bloqueado por los grupos de seguridad de red.
- Si ese es el caso, modifique el grupo de seguridad de red para que permita el tráfico.

### <a name="check-whether-nva-and-vms-are-listening-for-expected-traffic"></a>Comprobación de si la aplicación virtual de red y las máquinas virtuales están escuchando el tráfico esperado

- Conéctese a la aplicación virtual de red mediante RDP o SSH y, después, ejecute el siguiente comando:

   **Windows**:
   ```shell
   netstat -an
   ```

   **Linux**:
   ```shell
   netstat -an | grep -i listen
   ```

- Si no ve el puerto TCP que utiliza el software de la aplicación virtual de red que aparece en los resultados, debe configurar la aplicación en la aplicación virtual de red y en la máquina virtual para que escuche y responda al tráfico que llega a esos puertos. [Póngase en contacto con el proveedor de la aplicación virtual de red para obtener ayuda, según sea necesario](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Comprobación del rendimiento de la aplicación virtual de red

### <a name="validate-vm-cpu"></a>Validación de la CPU de la máquina virtual

Si el uso de CPU se acerca al 100 por ciento, es posible que se produzca un problema que afecte a las colocaciones de paquetes de red. Durante un pico de CPU, investigue qué proceso de la máquina virtual invitada está causando la elevada CPU y lo solucione, si es posible. También es posible que tenga que cambiar el tamaño de la máquina virtual a un tamaño de SKU más grande o, para un conjunto de escalado de máquinas virtuales, aumentar el número de instancias. Para cada uno de estos problemas, [póngase en contacto con el proveedor de la aplicación virtual de red para obtener ayuda, según sea necesario](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Validación de las estadísticas de red de la máquina virtual

Si la red de VM utiliza picos o muestra períodos de uso elevado, es posible que también tenga que aumentar el tamaño de la SKU de la máquina virtual para obtener mayores capacidades de rendimiento.

## <a name="advanced-network-administrator-troubleshooting"></a>Solución de problemas del administrador de red avanzado

### <a name="capture-network-trace"></a>Captura del seguimiento de red

Capture un seguimiento de red simultáneo en la máquina virtual de origen, la aplicación virtual de red y la máquina virtual de destino mientras ejecuta [**PsPing**](/sysinternals/downloads/psping) o **Nmap** y, después, detenga el seguimiento.

- Para capturar un seguimiento de red simultáneo, ejecute el siguiente comando:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

- Utilice **PsPing** o **Nmap** desde la máquina virtual de origen a la máquina virtual de destino (por ejemplo: `PsPing 10.0.0.4:80` o `Nmap -p 80 10.0.0.4`).

- Abra el seguimiento de red desde la máquina virtual de destino mediante **tcpdump** o un analizador de paquetes de su elección. Aplique un filtro de visualización para la IP de la máquina virtual de origen desde la que ejecutó **PsPing** o **Nmap**, como `IPv4.address==10.0.0.4` (Windows netmon) o `tcpdump -nn -r vmtrace.cap src` o `dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Análisis de seguimientos

Si no ve la entrada de paquetes en el seguimiento de la máquina virtual de back-end, es probable que un grupo de seguridad de red o un enrutamiento definido por el usuario esté interfiriendo o que las tablas de enrutamiento de la aplicación virtual de red sean incorrectas.

Si ve que los paquetes entran, pero no hay ninguna respuesta, puede ser necesario resolver un problema de la aplicación de la máquina virtual o del firewall. Para cada uno de estos problemas, [póngase en contacto con el proveedor de la aplicación virtual de red para obtener ayuda, según sea necesario](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Creación de una incidencia de soporte técnico

Si ninguno de los pasos anteriores soluciona el problema, cree una [incidencia de soporte técnico](../operator/azure-stack-manage-basics.md#where-to-get-support) y use la [herramienta de recopilación de registros a petición](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) para proporcionar registros.
