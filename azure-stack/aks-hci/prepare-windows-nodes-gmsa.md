---
title: Preparación de nodos de Windows para la compatibilidad con las cuentas de servicio administradas de grupo
description: Aprenda a configurar cuentas de servicio administradas de grupo para contenedores en nodos con Windows.
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: fd08f288d816468610dadf7e5c063c08b63c0cf2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254711"
---
# <a name="prepare-windows-nodes-for-group-managed-service-account-support"></a>Preparación de nodos de Windows para la compatibilidad con las cuentas de servicio administradas de grupo

> Se aplica a: AKS en Azure Stack HCI, entorno en tiempo de ejecución de AKS en Windows Server 2019 Datacenter

Las cuentas de servicio administradas de grupo son un tipo específico de cuenta de Active Directory que proporciona:
- Administración automática de contraseñas
- Administración simplificada del nombre de entidad de seguridad de servicio (SPN)
- La capacidad de delegar la administración a otros administradores en varios servidores 

Para configurar cuentas de servicio administradas de grupo (gMSA) para pods y contenedores que se ejecutarán en los nodos de Windows, primero debe unir los nodos de Windows a un dominio de Active Directory.

Para habilitar la compatibilidad con la cuenta de servicio administrada de grupo, el nombre del clúster de Kubernetes debe tener menos de cuatro caracteres. Debe tener menos de cuatro caracteres porque la longitud máxima admitida para un nombre de servidor unido a un dominio es de 15 caracteres. La convención de nombres de clúster de AKS en Kubernetes en Azure Stack HCI para un nodo de trabajo agrega algunos caracteres predefinidos al nombre de nodo.

## <a name="join-the-worker-nodes-to-a-domain"></a>Unión de los nodos de trabajo a un dominio

Para unir los nodos de trabajo con Windows a un dominio, inicie sesión en un nodo de trabajo con Windows; para ello, ejecute `kubectl get` y anote el valor de `EXTERNAL-IP`.

```
kubectl get nodes -o wide
```  

A continuación, puede conectarse con SSH al nodo mediante `ssh Administrator@ip`. Para más información sobre cómo iniciar sesión mediante SSH, consulte [Solución de problemas de nodos de trabajo con Windows](troubleshoot.md#troubleshooting-windows-worker-nodes).

Una vez que haya iniciado sesión correctamente en el nodo de trabajo con Windows, ejecute el siguiente comando para unir el nodo a un dominio. Después, tendrá que reiniciar el nodo de trabajo con Windows. 

```
netdom.exe join %computername% /domain:DomainName /UserD:DomainName\UserName /PasswordD:Password
```  

Una vez que todos los nodos de trabajo con Windows se han unido a un dominio, siga los pasos detallados en [Configuración de GMSA para pods y contenedores con Windows](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/). Estos pasos le ayudarán a aplicar las definiciones de recursos y webhooks personalizados de la gMSA de Kubernetes en el clúster de Kubernetes.

Para más información sobre el contenedor Windows con gMSA, consulte [Creación de gMSA para contenedores de Windows](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Pasos siguientes

* [Uso de Azure Monitor para supervisar el clúster y la aplicación](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Uso de un volumen persistente en AKS en un clúster de Azure Stack HCI](persistent-volume.md).