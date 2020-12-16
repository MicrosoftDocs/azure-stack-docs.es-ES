---
title: Inicio rápido para crear un clúster de Kubernetes mediante Windows Admin Center
description: Obtenga información sobre cómo crear un clúster de Kubernetes mediante Windows Admin Center.
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 7a9c1bf5d89348175b7ac25a149fe01f384964a2
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612410"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Inicio rápido: creación de un clúster de Kubernetes en Azure Stack HCI mediante Windows Admin Center

> Se aplica a: Azure Stack HCI, Windows Server 2019 Datacenter

Después de configurar el host de Azure Kubernetes Service, puede crear un clúster de Kubernetes mediante Windows Admin Center. Si en su lugar prefiere usar PowerShell, consulte [Creación de un clúster de Kubernetes con PowerShell](create-kubernetes-cluster-powershell.md).

Antes de continuar con el Asistente para crear clústeres de Kubernetes, asegúrese de que tiene [configurado Azure Kubernetes Service](setup.md) y compruebe los [requisitos del sistema ](system-requirements.md) si aún no lo ha hecho. Se puede acceder al Asistente para crear clústeres de Kubernetes desde la página [Todas las conexiones](#creating-a-kubernetes-cluster-from-the-all-connections-page) o el [panel del host de Azure Kubernetes Service](#creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard).

## <a name="creating-a-kubernetes-cluster-from-the-all-connections-page"></a>Creación de un clúster de Kubernetes desde la página Todas las conexiones 

Hay dos formas de crear un clúster de Kubernetes en Windows Admin Center. Una de estas opciones es hacerlo desde la página **Todas las conexiones**. Siga estos pasos y, después, continúe con la sección [Asistente para crear clústeres de Kubernetes](#the-kubernetes-cluster-create-wizard): 

1. Para empezar a crear un clúster de Kubernetes en Windows Admin Center, haga clic en el botón **Agregar** de la pantalla de la puerta de enlace. 
2. En el panel **Add or create resources** (Agregar o crear recursos) que se encuentra en **Kubernetes cluster (preview)** (Clúster de Kubernetes [versión preliminar]), seleccione **Crear nuevo** para iniciar el asistente del clúster de Kubernetes. Aunque el botón "Agregar" de los clústeres de Kubernetes existe en la versión preliminar pública, aún no funciona. Puede salir del asistente en cualquier momento del proceso de creación del clúster de Kubernetes, pero tenga en cuenta que el progreso no se guardará. 


    ![Muestra la hoja Add or create resources ("Agregar o crear recursos") de Windows Admin Center, que ahora incluye el nuevo icono para los clústeres de Kubernetes.](.\media\create-kubernetes-cluster\add-connection.png)
  
## <a name="creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard"></a>Creación de un clúster de Kubernetes desde el panel del host de Azure Kubernetes Service  

También puede crear un clúster de Kubernetes desde el panel del host de Azure Kubernetes Service. Este panel se puede encontrar en la herramienta Azure Kubernetes Service cuando está conectado al sistema que usa un host de Azure Kubernetes Service. Siga estos pasos y, después, continúe con la sección [Asistente para crear clústeres de Kubernetes](#the-kubernetes-cluster-create-wizard): 

1. Conéctese al sistema en el que quiere crear el clúster de Kubernetes y vaya a la herramienta **Azure Kubernetes Service**. Este sistema ya debe tener configurado un host de Azure Kubernetes Service.
2. Seleccione el botón **Agregar clúster** en el encabezado **Clúster de Kubernetes**.

![Muestra el panel de la herramienta Azure Kubernetes Service que aparece después de configurar un host de Azure Kubernetes Service.](.\media\setup\dashboard.png)
  
## <a name="the-kubernetes-cluster-create-wizard"></a>Asistente para crear clústeres de Kubernetes
Ha llegado al Asistente para crear clústeres de Kubernetes desde la página **Todas las conexiones** o la herramienta Azure Kubernetes Service. Comencemos:  

1. Revise los requisitos previos del sistema que hospedará el clúster de Kubernetes y los de Windows Admin Center. Cuando termine, seleccione **Siguiente**. 
2. En la página **Conceptos básicos**, configure la información sobre el clúster como, por ejemplo, la integración de Azure Arc, la información del host de Azure Kubernetes Service y el tamaño del grupo de nodos principal. El campo del host de Azure Kubernetes Service requiere el nombre de dominio completo del clúster de Azure Stack HCI o Windows Server 2019 que usó en la página de [configuración](setup.md). Recuerde que debe completar la configuración del host en este sistema a través de la herramienta Azure Kubernetes Service. En la versión preliminar pública, el número de nodos no es editable y tendrá como valor predeterminado el 2. Sin embargo, el tamaño del nodo se puede configurar para cargas de trabajo de mayor tamaño. Cuando haya terminado, seleccione **Agregar**.

 [ ![Se muestra la página Aspectos básicos del asistente para clústeres de Kubernetes.](.\media\create-kubernetes-cluster\basics.png) ](.\media\create-kubernetes-cluster\basics.png#lightbox)
 
3. Puede configurar los grupos de nodos adicionales para ejecutar las cargas de trabajo en la página **Grupos de nodos**. Si usa la versión preliminar pública, puede agregar como máximo un grupo de nodos de Windows y un grupo de nodos de Linux (además del grupo de nodos del sistema). Si desea habilitar la integración de Azure Arc más adelante en este asistente, necesitará al menos un grupo de nodos de Linux que no sea el grupo de nodos principal. Cuando haya finalizado, seleccione **Siguiente**.
4. Especifique la configuración de red en la página **Redes**. Si selecciona la opción "Avanzado", puede personalizar el intervalo de direcciones que se usa al crear una red virtual para los nodos del clúster. Si selecciona la opción "Aspectos básicos", se crearán redes virtuales para los nodos del clúster con un intervalo de direcciones predeterminado. Tenga en cuenta que la configuración de red (por ejemplo, el equilibrador de carga, la directiva de red y el enrutamiento de aplicaciones HTTP) no se puede cambiar en la versión preliminar pública. Cuando haya terminado, seleccione **Agregar**.

    [ ![Se muestra la página Redes del asistente para clústeres de Kubernetes.](.\media\create-kubernetes-cluster\networking.png) ](\media\create-kubernetes-cluster\networking.png#lightbox)

5. En la página **Integración**, conecte el clúster con servicios adicionales, como el almacenamiento persistente. Se le pedirá que configure el almacenamiento persistente en esta página. En la versión preliminar pública, la ubicación de almacenamiento persistente se establecerá de forma predeterminada en la ubicación de almacenamiento seleccionada durante la configuración del host. Recuerde que este campo no se puede editar en este momento. Cuando haya finalizado, seleccione **Siguiente**.
6. Revise las selecciones que haya hecho en la página **Revisar y crear**. Cuando esté satisfecho, seleccione **Crear** para comenzar la implementación. El progreso de la implementación se mostrará en la parte superior de esta página. 
7. Una vez finalizada la implementación, verá cómo puede administrar el clúster en la página **Siguientes pasos**. Esta página también contiene la clave SSH. Si decide deshabilitar la integración de Azure Arc en el paso anterior, es posible que parte de la información y las instrucciones de esta página no estén disponibles o no funcionen.

> [!IMPORTANT] 
> Le recomendamos que guarde la clave SSH en una ubicación segura y accesible.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, implementó un clúster de Kubernetes. Para obtener más información acerca de Azure Kubernetes Service en Azure Stack HCI, y saber cómo implementar y administrar aplicaciones para Linux en AKS en Azure Stack HCI, vaya al tutorial siguiente:

- [Tutorial: Implementación de aplicaciones Linux en Azure Kubernetes Service en Azure Stack HCI](deploy-linux-application.md)
