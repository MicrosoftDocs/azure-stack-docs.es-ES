---
title: Problemas conocidos de Azure Kubernetes Service en Azure Stack HCI
description: Problemas conocidos de Azure Kubernetes Service en Azure Stack HCI
author: abha
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 36c2d735f3652f4f195b4b9e1dda30fe8bce858c
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557011"
---
# <a name="known-issues-for-azure-kubernetes-service-on-azure-stack-hci-public-preview"></a>Problemas conocidos de Azure Kubernetes Service en Azure Stack HCI (versión preliminar pública)
En este artículo se describen problemas conocidos con la versión preliminar pública de Azure Kubernetes Service en Azure Stack HCI.

## <a name="recovering-from-a-failed-aks-on-azure-stack-hci-deployment"></a>Recuperación de una implementación con errores de Azure Kubernetes Service en Azure Stack HCI
Si tiene problemas de implementación o desea restablecer la implementación, asegúrese de cerrar todas las instancias de Windows Admin Center conectadas a Azure Kubernetes Service en Azure Stack HCI antes de ejecutar Uninstall-AksHci desde una ventana administrativa de PowerShell.

## <a name="when-using-kubectl-to-delete-a-node-the-associated-vm-might-not-be-deleted"></a>Al usar kubectl para eliminar un nodo, es posible que la máquina virtual asociada no se elimine
Experimentará este problema si hace esto:
* Creación de un clúster de Kubernetes
* Escalar el clúster a más de dos nodos
* Usar delete node <nombreDelNodo> de kubectl para eliminar un nodo 
* Ejecutar get nodes en kubectl. El nodo eliminado no aparece en la salida
* Abrir una ventana administrativa de PowerShell
* Ejecutar get-vm. El nodo eliminado todavía aparece en la lista

Esto hace que el sistema no reconozca el nodo que falta y que un nuevo nodo no se ponga en marcha. Este problema se solucionará en una futura versión.

## <a name="time-synchronization-must-be-configured-across-all-physical-cluster-nodes-and-in-hyper-v"></a>La sincronización de hora debe estar configurada en todos los nodos físicos de clúster y en Hyper-V
Para asegurarse de que gMSA y la autenticación de AD funcionen, cerciórese de que los nodos de clúster de Azure Stack HCI estén configurados para sincronizar su hora con un controlador de dominio u otro origen de hora, y que Hyper-V esté configurado para sincronizar la hora con todas las máquinas virtuales.

## <a name="special-active-directory-permissions-are-needed-for-domain-joined-azure-stack-hci-nodes"></a>Se necesitan permisos especiales de Active Directory para los nodos de Azure Stack HCI unidos a un dominio 
Los usuarios que implementan y configuran Azure Kubernetes Service en Azure Stack HCI deben tener el permiso "Control total" para crear objetos de AD en el contenedor de Active Directory en el que se crean los objetos de servidor y servicio. 

## <a name="get-akshcilogs-command-may-fail"></a>El comando Get-AksHciLogs puede producir un error
Con los clústeres grandes, el comando Get-AksHciLogs puede generar una excepción, producir un error al enumerar los nodos o no generar el archivo de salida c:\wssd\wssdlogs.zip.
Esto se debe a que el comando de PowerShell para comprimir un archivo `Compress-Archive` tiene un límite de tamaño de archivo de salida de 2 GB. Este problema se corregirá en una versión futura.

## <a name="azure-kubernetes-service-powershell-deployment-doesnt-check-for-available-memory-before-creating-a-new-target-cluster"></a>La implementación de PowerShell de Azure Kubernetes Service no comprueba la memoria disponible antes de crear un nuevo clúster de destino
Los comandos Aks-Hci de PowerShell no validan la memoria disponible en el servidor host antes de crear los nodos de Kubernetes. Esto puede dar lugar al agotamiento de la memoria y a que las máquinas virtuales no se inicien. Este error no está controlado actualmente de manera correcta, por lo que la implementación dejará de responder sin ningún mensaje de error claro.
Si la implementación deja de responder, abra `Eventviewer` y busque mensajes de error relacionados con Hyper-V que indiquen que no hay suficiente memoria para iniciar la máquina virtual.
Este problema se solucionará en una futura versión

## <a name="azure-kubernetes-service-deployment-fails-on-an-azure-stack-hci-configured-with-static-ips-vlans-sdn-or-proxies"></a>Se produce un error en la implementación de Azure Kubernetes Service en una instancia de Azure Stack HCI configurada con direcciones IP estáticas, VLAN, SDN o proxies.
Al implementar Azure Kubernetes Service en un clúster de Azure Stack HCI que tiene direcciones IP estáticas, VLAN, SDN o proxies, se produce un error en la implementación durante la creación del clúster. Este problema se corregirá en una versión futura.

## <a name="ipv6-must-be-disabled-in-the-hosting-environment"></a>IPv6 se debe deshabilitar en el entorno de hospedaje
Si las direcciones IPv4 e IPv6 están enlazadas a la NIC física, el servicio `cloudagent` para la agrupación en clústeres utilizará la dirección IPv6 para la comunicación. Otros componentes de la plataforma de implementación solo usan IPv4. Esto hará que Windows Admin Center no se pueda conectar al clúster y notificará un error de comunicación remota al intentar conectarse a la máquina.
Solución alternativa: Deshabilite IPv6 en los adaptadores de red físicos.
Este problema se solucionará en una futura versión

## <a name="moving-virtual-machines-between-azure-stack-hci-cluster-nodes-quickly-leads-to-vm-startup-failures"></a>El traslado de máquinas virtuales entre los nodos de clúster de Azure Stack HCI conduce rápidamente a errores en el inicio de las máquinas virtuales
Al usar la herramienta de administración de clústeres para trasladar una máquina virtual de un nodo (nodo A) a otro nodo (nodo B) del clúster de Azure Stack HCI, es posible que la máquina virtual no se inicie en el nuevo nodo. Después de mover la máquina virtual de nuevo al nodo original, esta no se inicia allí.
Este problema se produce porque la lógica para limpiar la primera migración se ejecuta de forma asincrónica. Como resultado, la lógica de "actualización de la ubicación de la máquina virtual" de Azure Kubernetes Service busca la máquina virtual en la instancia original de Hyper-V en el nodo A y la elimina, en lugar de anular su registro.
Solución alternativa: Asegúrese de que la máquina virtual se ha iniciado correctamente en el nuevo nodo antes de trasladarla de nuevo al nodo original.
Este problema se solucionará en una futura versión

## <a name="load-balancer-in-azure-kubernetes-service-requires-dhcp-reservation"></a>El equilibrador de carga de Azure Kubernetes Service requiere reserva de DHCP
La solución de equilibrio de carga de Azure Kubernetes Service en Azure Stack HCI usa DHCP para asignar direcciones IP a los puntos de conexión de servicio. Si la dirección IP cambia para el punto de conexión de servicio debido a un reinicio del servicio, la concesión de DHCP expira debido al breve tiempo de expiración. Por lo tanto, el servicio dejará de estar accesible porque la dirección IP de la configuración de Kubernetes es diferente de la que se encuentra en el punto de conexión. Esto puede dar lugar a que el clúster de Kubernetes deje de estar disponible.
Para solucionar este problema, use un grupo de direcciones MAC para los puntos de conexión del servicio con carga equilibrada y reserve direcciones IP específicas para cada dirección MAC del grupo.
Este problema se corregirá en una versión futura.

## <a name="cannot-deploy-azure-kubernetes-service-to-an-environment-that-has-separate-storage-and-compute-clusters"></a>No se puede implementar Azure Kubernetes Service en un entorno que tiene clústeres de almacenamiento y de proceso independientes
Windows Admin Center no implementará Azure Kubernetes Service en un entorno con clústeres de almacenamiento y de proceso independientes, ya que espera que el mismo clúster sea el que proporcione los recursos de proceso y almacenamiento. En la mayoría de los casos, no encontrará los archivos CSV expuestos por el clúster de proceso y se negará a continuar con la implementación.
Este problema se corregirá en una versión futura.

## <a name="windows-admin-center-only-supports-azure-kubernetes-service-for-azure-stack-hci-in-desktop-mode"></a>Windows Admin Center solo admite Azure Kubernetes Service para Azure Stack HCI en modo de escritorio
En la versión preliminar, todas las funciones de Azure Kubernetes Service para Azure Stack HCI solo se admiten en el modo de escritorio de Windows Admin Center. La puerta de enlace de Windows Admin Center debe estar instalada en un equipo con Windows 10. Para más información acerca de las opciones de instalación, visite la [documentación de Windows Admin Center](/windows-server/manage/windows-admin-center/plan/installation-options). Estos escenarios adicionales se admitirán en una versión futura.

## <a name="azure-kubernetes-service-host-setup-fails-in-windows-admin-center-if-reboots-are-required"></a>Se producirá un error en la instalación del host de Azure Kubernetes Service en Windows Admin Center si se requieren reinicios
Se producirá un error en el asistente para la instalación del host de Azure Kubernetes Service si es necesario reiniciar uno o más servidores para instalar roles como PowerShell o Hyper-V. La solución actual es salir del asistente e intentarlo de nuevo en el mismo sistema después de que los servidores vuelvan a estar en línea. Este problema se corregirá en una versión futura.

## <a name="azure-registration-step-in-azure-kubernetes-service-host-setup-asks-to-try-again"></a>El paso del registro de Azure en la instalación del host de Azure Kubernetes Service pide que se vuelva a intentar
Si usa Windows Admin Center para configurar el host de Azure Kubernetes Service, es posible que se le pida que vuelva a intentarlo después de escribir la información necesaria en la página de registro de Azure. Puede que tenga que volver a iniciar sesión en Azure en la puerta de enlace de Windows Admin Center para continuar con este paso. Este problema se corregirá en una versión futura.

## <a name="windows-admin-center-doesnt-have-an-arc-offboarding-experience"></a>Windows Admin Center no incluye una experiencia de retirada de Arc
Windows Admin Center no tiene actualmente ningún proceso para retirar un clúster de Azure Arc. Para eliminar los agentes de Arc de un clúster que se ha destruido, vaya al grupo de recursos del clúster en Azure Portal y elimine manualmente el contenido de Arc. Para eliminar los agentes de Arc de un clúster que todavía está en ejecución, los usuarios deben ejecutar el siguiente comando:
```PowerShell
az connectedk8s delete
```

## <a name="when-setting-up-an-azure-kubernetes-service-host-using-windows-admin-center-setup-may-fail-if-file-explorer-is-open"></a>Al configurar un host de Azure Kubernetes Service mediante Windows Admin Center, se puede producir un error en la configuración si el explorador de archivos está abierto
Si el Explorador de archivos está abierto y está en el directorio **C:\Archivos de Files\AksHci** al llegar al paso "Revisar y crear", es posible que se produzca el error "El proceso no pudo acceder al archivo" C:\Archivos de Files\AksHci\wssdcloudagent.exe". Esto se debe a que otro proceso lo está usando. Para evitar este error, cierre el Explorador de archivos o vaya a otro directorio antes de llegar a este paso. 