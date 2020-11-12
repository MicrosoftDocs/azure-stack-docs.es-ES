---
title: Solución de problemas de aplicaciones virtuales de red en Azure Stack Hub
description: Solucione problemas de conectividad de máquina virtual o de VPN al utilizar una aplicación virtual de red (NVA) en Microsoft Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 09/08/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 0facc0cc06ad3ff672531f1eeb7e31eee2f56ee0
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546895"
---
# <a name="troubleshoot-network-virtual-appliance-problems"></a>Solución de problemas de aplicaciones virtuales de red

Es posible que experimente problemas de conectividad con las máquinas virtuales o las VPN que utilizan una aplicación virtual de red en Azure Stack Hub.

Este artículo le ayuda a validar los requisitos básicos de la plataforma de Azure Stack Hub para las configuraciones de aplicaciones virtuales de red.

El proveedor de una aplicación virtual de red proporciona soporte técnico para esta y para su integración con la plataforma de Azure Stack Hub.

> [!NOTE]
> Si tiene un problema de conectividad o de enrutamiento que implica una aplicación virtual de red, debería [ponerse en contacto directamente con el proveedor de la misma](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Si en este artículo no se aborda el problema de su aplicación virtual de red con Azure Stack Hub, cree una [incidencia de soporte técnico de Azure Stack](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-an-nva-vendor"></a>Lista de comprobación para la solución de problemas con un proveedor de aplicaciones virtuales de red

- Actualizaciones para software de máquinas virtuales de aplicaciones virtuales de red.
- Funcionalidad y configuración de la cuenta de servicio.
- Rutas definidas por el usuario en subredes de red virtual que dirigen el tráfico a la aplicación virtual de red.
- Rutas definidas por el usuario en subredes de red virtual que dirigen el tráfico de la aplicación virtual de red.
- Tablas de enrutamiento y reglas en la aplicación virtual de red (por ejemplo, de NIC1 a NIC2).
- Seguimiento de las NIC de la aplicación virtual de red para comprobar la recepción y el envío del tráfico de red.

## <a name="basic-troubleshooting-steps"></a>Pasos básicos para solucionar problemas

1. Comprobación de la configuración básica.
2. Comprobación del rendimiento de la aplicación virtual de red.
3. Solución de problemas avanzados de red.

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Comprobación de los requisitos mínimos de la configuración de las aplicaciones virtuales de red en Azure

Cada aplicación virtual de red tiene requisitos básicos de configuración para funcionar correctamente en Azure Stack Hub. En esta sección se muestran los pasos para comprobar estas configuraciones básicas. Para más información, [póngase en contacto con el proveedor de la aplicación virtual de red](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Si los paquetes usan un túnel S2S, se encapsulan con encabezados adicionales, lo que aumenta el tamaño global de cada paquete.
>
> En este escenario, tiene que fijar el MSS de TCP en 1,350 bytes. Si los dispositivos VPN no admiten la fijación de MSS, puede establecer la opción MTU en la interfaz de túnel en 1,400 bytes.
>
> Para más información, consulte [Optimización del rendimiento de TCP/IP de la red virtual](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-the-nva"></a>Comprobación de si está habilitado el reenvío IP en la aplicación virtual de red

#### <a name="use-the-azure-stack-hub-portal"></a>Uso del portal de Azure Stack Hub

1. Busque el recurso de la aplicación virtual de red en el portal de Azure Stack Hub, seleccione las **redes** y, después, seleccione la interfaz de red.
2. En la página **Interfaz de red** , seleccione **Configuraciones de IP**.
3. Asegúrese de que está habilitado el reenvío IP.

#### <a name="use-powershell"></a>Uso de PowerShell

1. Ejecute el siguiente comando: Reemplace los valores de los corchetes angulares por su información.

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

2. Compruebe la propiedad **EnableIPForwarding**.

3. Si el reenvío IP no está habilitado, ejecute los siguientes comandos para habilitarlo:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-traffic-can-be-routed-to-the-nva"></a>Comprobación de si se puede enrutar el tráfico a la aplicación virtual de red

1. Busque una máquina virtual que esté configurada para redirigir el tráfico a la aplicación virtual de red.
1. Para comprobar que la aplicación virtual de red es el próximo salto, ejecute `Tracert <Private IP of NVA>` para Windows o `Traceroute <Private IP of NVA>`.
1. Si la aplicación virtual de red no aparece como próximo salto, compruebe y actualice las tablas de rutas de Azure Stack Hub.

Algunos sistemas operativos en el nivel de invitado pueden tener directivas de firewall para bloquear el tráfico ICMP. Actualice estas reglas de firewall para que los comandos anteriores funcionen.

### <a name="check-whether-traffic-can-reach-the-nva"></a>Comprobación de si el tráfico puede llegar a la aplicación virtual de red

1. Busque una máquina virtual que debiera estar conectada a la aplicación virtual de red.
1. Compruebe si los grupos de seguridad de red bloquean el tráfico. Para Windows, ejecute `ping` (ICMP) o `Test-NetConnection <Private IP of NVA>` (TCP). Para Linux, ejecute `Tcpping <Private IP of NVA>`.
1. Si los grupos de seguridad de red bloquean el tráfico, modifíquelos para que lo permitan.

### <a name="check-whether-the-nva-and-vms-are-listening-for-expected-traffic"></a>Comprobación de si la aplicación virtual de red y las máquinas virtuales están escuchando el tráfico esperado

1. Conéctese a la aplicación virtual de red mediante RDP o SSH y, después, ejecute el siguiente comando:

   **Windows**

   ```shell
   netstat -an
   ```

   **Linux**

   ```shell
   netstat -an | grep -i listen
   ```

1. Busque los puertos TCP utilizados por el software de la aplicación virtual que aparece en los resultados. Si no los ve, configure la aplicación en la aplicación virtual de red y la máquina virtual para que escuche y responda al tráfico que llega a esos puertos. [Póngase en contacto con el proveedor de la aplicación virtual de red para obtener ayuda](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Comprobación del rendimiento de la aplicación virtual de red

### <a name="validate-vm-cpu-usage"></a>Validación del uso de la CPU de la máquina virtual

Si el uso de la CPU se acerca al 100 por ciento, es posible que se produzca un problema que afecte a las colocaciones de paquetes de red.

Durante un pico de CPU, investigue qué proceso de la máquina virtual invitada está causando el uso elevado de la CPU. A continuación, mitigue ese uso elevado si es posible.

También es posible que tenga que cambiar el tamaño de la máquina virtual a un tamaño de SKU más grande o, para un conjunto de escalado de máquinas virtuales, aumentar el número de instancias.

Si necesita ayuda, [póngase en contacto con el proveedor de la aplicación virtual de red](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Validación de las estadísticas de red de la máquina virtual

Si la red de VM muestra picos o períodos de uso elevado, considere la posibilidad de aumentar el tamaño de la SKU de la máquina virtual para obtener un mayor rendimiento.

## <a name="advanced-network-administrator-troubleshooting"></a>Solución de problemas del administrador de red avanzado

### <a name="capture-a-network-trace"></a>Captura de un seguimiento de red

Mientras ejecuta [`PsPing`](/sysinternals/downloads/psping) o `Nmap`, capture un seguimiento de red simultáneo en las máquinas virtuales de origen y de destino, y en la aplicación virtual de red. Después, detenga el seguimiento.

1. Para capturar un seguimiento de red simultáneo, ejecute el siguiente comando:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

2. Utilice `PsPing` o `Nmap` desde la máquina virtual de origen a la máquina virtual de destino. Algunos ejemplos son `PsPing 10.0.0.4:80` o `Nmap -p 80 10.0.0.4`.

3. Abra el seguimiento de red desde la máquina virtual de destino mediante **tcpdump** o un analizador de paquetes de su elección. Aplique un filtro de visualización para la IP de la máquina virtual de origen desde la que ejecutó `PsPing` o `Nmap`. Un ejemplo de **netmon** de Windows es `IPv4.address==10.0.0.4`. Algunos ejemplos de Linux son `tcpdump -nn -r vmtrace.cap src` y `dst host 10.0.0.4`.

### <a name="analyze-traces"></a>Análisis de seguimientos

Si no ve la entrada de paquetes en el seguimiento de la máquina virtual de back-end, es probable que un grupo de seguridad de red o un enrutamiento definido por el usuario esté interfiriendo o que las tablas de enrutamiento de la aplicación virtual de red sean incorrectas.

Si ve que los paquetes entran, pero sin respuesta, puede que tenga que solucionar un problema con una aplicación de máquina virtual o con el firewall.

Si necesita ayuda, [póngase en contacto con el proveedor de la aplicación virtual de red](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Creación de una incidencia de soporte técnico

Si los pasos anteriores no solucionan el problema, cree una [incidencia de soporte técnico](../operator/azure-stack-manage-basics.md#where-to-get-support) y use la [herramienta de recopilación de registros a petición](../operator/azure-stack-diagnostic-log-collection-overview.md) para proporcionar registros.
