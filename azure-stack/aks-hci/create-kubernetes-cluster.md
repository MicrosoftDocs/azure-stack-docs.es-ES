---
title: Inicio rápido para crear un clúster de Kubernetes mediante Windows Admin Center
description: Obtenga información sobre cómo crear un clúster de Kubernetes mediante Windows Admin Center.
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: ddaac062a147440b4ce290ad988629c417a5932a
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949395"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Inicio rápido: creación de un clúster de Kubernetes en Azure Stack HCI mediante Windows Admin Center

> Se aplica a: Azure Stack HCI

Después de configurar el host de Azure Kubernetes Service, puede crear un clúster de Kubernetes mediante Windows Admin Center. Si en su lugar prefiere usar PowerShell, consulte [Creación de un clúster de Kubernetes con PowerShell](create-kubernetes-cluster-powershell.md).

Comencemos:

1. [Configure Azure Kubernetes Service](setup.md) y compruebe los [requisitos del sistema](system-requirements.md) si aún no lo ha hecho.
1. Para empezar a crear un clúster de Kubernetes en Windows Admin Center, haga clic en el botón **Agregar** de la pantalla de la puerta de enlace.
2. En el panel **Add or create resources** (Agregar o crear recursos) que se encuentra en **Kubernetes cluster (preview)** (Clúster de Kubernetes [versión preliminar]), seleccione **Crear nuevo** para iniciar el asistente del clúster de Kubernetes. Aunque el botón "Agregar" de los clústeres de Kubernetes existe en la versión preliminar pública, aún no funciona. Puede salir del asistente en cualquier momento del proceso de creación del clúster de Kubernetes, pero tenga en cuenta que el progreso no se guardará. 

    ![Muestra la hoja Add or create resources ("Agregar o crear recursos") de Windows Admin Center, que ahora incluye el nuevo icono para los clústeres de Kubernetes.](.\media\create-kubernetes-cluster\add-connection.png)

3. Revise los requisitos previos del sistema que hospedará el clúster de Kubernetes y los de Windows Admin Center. Cuando termine, seleccione **Siguiente**. 
4. En la página **Conceptos básicos**, configure la información sobre el clúster como, por ejemplo, la integración de Azure Arc, la información del host de Azure Kubernetes Service y el tamaño del grupo de nodos principal.  Información del host de Azure Kubernetes Service y tamaño del grupo de nodos principal. El campo del host de Azure Kubernetes Service requiere el nombre de dominio completo del clúster de Azure Stack HCI en el que quiere implementar el clúster de Kubernetes. Recuerde que debe completar la configuración del host en este sistema a través de la herramienta Azure Kubernetes Service. En la versión preliminar pública, el número de nodos no es editable y tendrá como valor predeterminado el 2. Sin embargo, el tamaño del nodo se puede configurar para cargas de trabajo de mayor tamaño. Cuando haya terminado, seleccione **Agregar**.

    ![Se muestra la página Aspectos básicos del asistente para clústeres de Kubernetes.](.\media\create-kubernetes-cluster\basics.png)

5. Puede configurar los grupos de nodos adicionales para ejecutar las cargas de trabajo en la página **Grupos de nodos**. Si usa la versión preliminar pública, puede agregar como máximo un grupo de nodos de Windows y un grupo de nodos de Linux (además del grupo de nodos del sistema). Cuando haya finalizado, seleccione **Siguiente**.
6. Especifique la configuración de red en la página **Redes**. Si selecciona la opción "Avanzado", puede personalizar el intervalo de direcciones que se usa al crear una red virtual para los nodos del clúster. Si selecciona la opción "Básico", se crearán redes virtuales para los nodos del clúster con un intervalo de direcciones predeterminado. Tenga en cuenta que la configuración de red (por ejemplo, el equilibrador de carga, la directiva de red y el enrutamiento de aplicaciones HTTP) no se puede cambiar en la versión preliminar pública. Cuando haya terminado, seleccione **Agregar**.

    ![Se muestra la página Redes del asistente para clústeres de Kubernetes.](.\media\create-kubernetes-cluster\networking.png)

7. En la página **Integración**, conecte el clúster con servicios adicionales, como el panel de Kubernetes, el almacenamiento persistente y Azure Monitor. El almacenamiento persistente es el único servicio que se debe configurar en esta página. En la versión preliminar pública, la ubicación de almacenamiento persistente se establecerá de forma predeterminada en la ubicación de almacenamiento seleccionada durante la configuración del host. Recuerde que este campo no se puede editar en este momento. Cuando haya finalizado, seleccione **Siguiente**.
8. Revise las selecciones que haya hecho en la página **Revisar y crear**. Cuando esté satisfecho, seleccione **Crear** para comenzar la implementación. El progreso de la implementación se mostrará en la parte superior de esta página. 
9. Una vez finalizada la implementación, verá cómo puede administrar el clúster en la página **Siguientes pasos**. Esta página también contiene la clave SSH y el token del panel de Kubernetes. Si decide deshabilitar el panel de Kubernetes o la integración de Azure Arc en el paso anterior, es posible que parte de la información y las instrucciones de esta página no estén disponibles o no funcionen.

> [!IMPORTANT] 
> Le recomendamos que guarde la clave SSH y el token del panel de Kubernetes en una ubicación segura y accesible.

En esta guía de inicio rápido, configuró un host de Azure Kubernetes Service e implementó un clúster de Kubernetes. 

Para obtener más información acerca de Azure Kubernetes Service en Azure Stack HCI, y saber cómo implementar y administrar aplicaciones de Linux en Azure Kubernetes Service en Azure Stack HCI, vaya a este tutorial.
