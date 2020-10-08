---
title: Solución de problemas
description: Guía para la solución de problemas de Azure Kubernetes Service en Azure Stack HCI
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: e30d5ba784efc6453ce161bc2a87db7c728d3fce
ms.sourcegitcommit: 373e9e3e84eaa33331db9f78e52486fbb6beb907
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91592924"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Solución de problemas de Azure Kubernetes Service en Azure Stack HCI

Al crear o administrar un clúster de Kubernetes mediante Azure Kubernetes Service en Azure Stack HCI, en ocasiones pueden surgir problemas. A continuación se muestran las instrucciones de solución de problemas que le ayudarán a resolverlos. 

## <a name="troubleshooting-azure-stack-hci"></a>Solución de problemas de Azure Stack HCI
Para solucionar los problemas de los informes de validación de clústeres para la configuración de QoS (calidad del servicio) de red y almacenamiento en los servidores de un clúster de Azure Stack HCI y comprobar que se han definido reglas importantes, visite [Solución de problemas de informes de validación de clústeres](/azure-stack/hci/manage/validate-qos).

Para solucionar problemas con CredSSP, visite [Solución de problemas de CredSSP](/azure-stack/hci/manage/troubleshoot-credssp).

## <a name="troubleshooting-windows-admin-center"></a>Solución de problemas de Windows Admin Center
Este producto se encuentra actualmente en estado de versión preliminar pública, lo que significa que todavía está en desarrollo. En la actualidad, hay varios problemas con la extensión de Azure Kubernetes Service para Windows Admin Center: 
* Actualmente, cada servidor del clúster del sistema que usa para configurar Azure Kubernetes Service en Azure Stack HCI debe ser un servidor de confianza. Esto significa que Windows Admin Center debe ser capaz de realizar operaciones CredSSP en cada servidor del clúster, no solo en uno, o en algunos, de ellos. 
* Si se produce un error que dice `msft.sme.aks couldn't load` e indica que se ha producido un error al cargar los fragmentos, use la versión más reciente de Microsoft Edge o Google Chrome e inténtelo de nuevo.
* Antes de iniciar el asistente de instalación del host de Azure Kubernetes Service o el asistente de creación del clúster de Kubernetes, debe iniciar sesión en Azure a través de Windows Admin Center. Es posible que sea necesario volver a iniciar sesión durante el flujo de trabajo. Si tiene dificultades para iniciar sesión en Azure a través de Windows Admin Center, intente iniciar sesión en su cuenta de Azure desde otro origen, por ejemplo desde [Azure Portal](https://portal.azure.com/). Si sigue teniendo problemas, consulte el artículo sobre [problemas conocidos de Windows Admin Center](/windows-server/manage/windows-admin-center/support/known-issues) antes de ponerse en contacto con el servicio de soporte técnico.
* En la iteración actual de la implementación de Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center, solo el usuario que configuró el host de Azure Kubernetes Service puede crear clústeres de Kubernetes en el sistema. Para solucionar este problema, copie la carpeta `.wssd` del perfil de usuario que configuró el host de Azure Kubernetes Service en el perfil de usuario que va a iniciar el nuevo clúster de Kubernetes.
* Si recibe un error en cualquiera de los asistentes sobre una configuración incorrecta, realice las operaciones de limpieza del clúster. Esto puede implicar la eliminación del archivo `C:\Program Files\AksHci\mocctl.exe`.
* Para que CredSSP funcione correctamente en el asistente de creación de clústeres, Windows Admin Center se debe instalar y usar en la misma cuenta. Si se instala con una cuenta y, a continuación, se intenta utilizar con otra, se producirán errores.
* Durante la implementación del clúster, puede haber un problema con la transferencia del archivo helm.zip. Esto suele producir un error que indica que la ruta de acceso al archivo helm.zip no existe o no es válida. Para resolver este problema, vuelva atrás e intente de nuevo la implementación.
* Si la implementación se bloquea durante un período prolongado, es posible que tenga problemas de conectividad o CredSSP. Pruebe estos pasos para solucionar problemas de la implementación: 
    1.  En la máquina que ejecuta Windows Admin Center, ejecute el siguiente comando en una ventana de PowerShell: 
    ```PowerShell
    Enter-PSSession <servername>
    ```
    2.  Si este comando funciona, podrá conectarse al servidor y no habrá ningún problema de conectividad.
    
    3.  Si tiene problemas con CredSSP, ejecute este comando para probar la confianza entre la máquina de puerta de enlace y la máquina de destino: 
    ```PowerShell
    Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
    ``` 
    También puede ejecutar el siguiente comando para probar la confianza en el acceso a la puerta de enlace local: 
    ```PowerShell
    Enter-PSSession -computer localhost -credential (Get-Credential)
    ``` 
* Si usa Azure Arc y tiene varios identificadores de inquilino, ejecute el siguiente comando para especificar el inquilino deseado antes de la implementación. Si no lo hace, puede producirse un error de implementación.

```Azure CLI
az login –tenant <tenant>
```
* Si acaba de crear una cuenta de Azure y no ha iniciado sesión en ella en la máquina de puerta de enlace, puede que experimente problemas al registrar la puerta de enlace de Windows Admin Center con Azure. Para mitigar este problema, inicie sesión en su cuenta de Azure en otra pestaña o ventana del explorador y, a continuación, registre la puerta de enlace de Windows Admin Center en Azure.

### <a name="creating-windows-admin-center-logs"></a>Creación de registros de Windows Admin Center
Al informar sobre problemas de Windows Admin Center, es útil adjuntar los registros para que el equipo de desarrollo pueda diagnosticar el problema. Los errores de Windows Admin Center suelen presentarse de dos formas: eventos que aparecen en el visor correspondiente de la máquina que ejecuta Windows Admin Center o errores de JavaScript que aparecen en la consola del explorador. Para recopilar registros de Windows Admin Center, use el script `Get-SMEUILogs.ps1` que se incluye en el paquete de la versión preliminar pública. 
 
Para usar el script, ejecute el siguiente comando en el directorio de archivos donde se almacena el script: 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
El comando tiene los parámetros siguientes:
 
* **-ComputerNames**: Una lista de las máquinas de las que desea recopilar registros.
* **-Destination**: La máquina a la que le gustaría agregar los registros.
* **-HoursAgo**: Este parámetro define el número de horas transcurrido desde el tiempo de ejecución del script desde el que desea recopilar registros.
* **-NoCredentialPrompt**: Se trata de un conmutador para desactivar la solicitud de credenciales y usar las credenciales predeterminadas del entorno actual.
 
Si tiene dificultades para ejecutar este script, puede ejecutar el siguiente comando para ver el texto de ayuda: 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Solución de problemas de nodos de trabajo de Windows 
Para iniciar sesión en un nodo de trabajo de Windows, primero debe obtener la dirección IP del nodo mediante la ejecución de `kubectl get` y anotar el valor `EXTERNAL-IP`:

```PowerShell
kubectl get nodes -o wide
``` 
Conéctese mediante SSH al nodo con `ssh Administrator@ip`. Una vez conectado mediante SSH al nodo, puede ejecutar `net user administrator *` para actualizar la contraseña del administrador. 

## <a name="troubleshooting-linux-worker-nodes"></a>Solución de problemas de nodos de trabajo de Linux 
Para iniciar sesión en un nodo de trabajo de Linux, primero debe obtener la dirección IP del nodo. Para ello, ejecute `kubectl get` y anote el valor `EXTERNAL-IP`:

```PowerShell
kubectl get nodes -o wide
``` 
Conéctese mediante SSH al nodo mediante `ssh clouduser@ip`. 

## <a name="troubleshooting-azure-arc-for-kubernetes"></a>Solución de problemas de Azure Arc para Kubernetes
Para solucionar problemas en escenarios comunes relacionados con la conectividad, los permisos y los agentes de Arc, visite [Solución de problemas de Kubernetes habilitado para Azure Arc](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>Pasos siguientes
Si sigue teniendo problemas al usar Azure Kubernetes Service en Azure Stack HCI, envíe los errores mediante [GitHub](https://aka.ms/aks-hci-issues).  
