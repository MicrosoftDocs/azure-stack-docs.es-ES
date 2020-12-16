---
title: Inicio rápido para configurar Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center
description: Aprenda a configurar Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center.
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 99f3e98ff24044c74c9065bb98965dcf640c4032
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612546"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Inicio rápido: Configuración de Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center

> Se aplica a: Azure Stack HCI, Windows Server 2019 Datacenter

En este inicio rápido aprenderá a configurar Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center. Para usar PowerShell en su lugar, consulte [Configuración con PowerShell](setup-powershell.md).

La configuración implica realizar las siguientes tareas:

* Descarga de Azure Kubernetes Service en Azure Stack HCI
* Configure Windows Admin Center si aún no lo ha hecho.
* Instale la extensión de Azure Kubernetes Service para Windows Admin Center.
* Configure un host de Azure Kubernetes Service en el sistema en el que quiera implementar el clúster de Kubernetes.

Antes de empezar, asegúrese de que cumple todos los requisitos previos que se detallan en la página de [requisitos del sistema](.\system-requirements.md).

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>Descarga de Azure Kubernetes Service en Azure Stack HCI

Si aún no ha descargado la versión preliminar del software, consulte [Evaluación de AKS en Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). Se le pedirá que descargue AKS en Azure Stack HCI así como Windows Admin Center.

## <a name="setting-up-windows-admin-center"></a>Configuración de Windows Admin Center

Si aún no ha instalado Windows Admin Center, consulte [Instalación de Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install). Para obtener una versión preliminar pública de Azure Kubernetes Service en Azure Stack HCI, debe descargar y ejecutar Windows Admin Center en una máquina Windows 10. Igualmente, recuerde que la funcionalidad Azure Kubernetes Service en Azure Stack HCI solo está disponible para la compilación de 2009 o una posterior de Windows Admin Center.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Instalación de la extensión de Azure Kubernetes Service

Una vez que haya obtenido los archivos de versión preliminar pública de Azure Kubernetes Service en Azure Stack HCI, debe guardar el archivo `.nupkg` de forma local o en un recurso compartido de SMB y agregar la ruta de acceso del archivo a la lista "Fuentes" del administrador de extensiones de Windows Admin Center. El archivo `.nupkg` es un paquete de NuGet que contiene la extensión de Windows Admin Center.

Para obtener acceso a la fuente de extensión existente, abra Windows Admin Center y seleccione el icono con forma de engranaje que está en la esquina superior derecha de la pantalla. Se le redirigirá al menú de configuración. Las fuentes de extensión se pueden encontrar en la sección **Puerta de enlace** del menú **Extensiones**. Vaya a la pestaña **Fuentes** y seleccione **Agregar**. En este panel, pegue la ruta de acceso al archivo a la copia de la extensión de Azure Kubernetes Service y seleccione **Agregar**. Si la ruta de acceso al archivo se ha agregado correctamente, recibirá una notificación de operación correcta. 

Ahora que hemos agregado la fuente, la extensión de Azure Kubernetes Service aparecerá en la lista de extensiones disponibles. Una vez que la haya seleccionado, haga clic en la opción **Instalar** que se encuentra en la parte superior de la tabla para instalar esta extensión. Windows Admin Center se volverá a cargar una vez se haya completado la instalación. 

[ ![Vista de la lista de extensiones disponibles en el administrador de extensiones de Windows Admin Center.](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Configuración de un host de Azure Kubernetes Service

Debe completar un último paso para poder crear un clúster de Kubernetes. Configure un host de Azure Kubernetes Service en el sistema en el que quiera implementar el clúster de Kubernetes. Este sistema puede ser un clúster de Windows Server 2019 Datacenter, una instancia de un solo nodo de Windows Server 2019 Datacenter o un clúster de Azure Stack HCl de entre 2 y 4 nodos. 

> [!NOTE] 
> Tenga en cuenta que no se pueden configurar los hosts de Azure Kubernetes Service en dos sistemas independientes con la intención de combinarlos durante la creación del clúster de Kubernetes. 

Esta configuración se puede realizar con la nueva herramienta de Azure Kubernetes Service. 

Con esta herramienta podrá instalar y descargar los paquetes necesarios, así como crear un clúster de administración que proporcione los servicios básicos de Kubernetes y organice las cargas de trabajo de la aplicación. 


Ahora que hemos comprobado la configuración del sistema, vamos a empezar: 
1. Seleccione **Configurar** para iniciar el asistente de configuración.
2. Revise los requisitos previos de la máquina en la que se ejecuta Windows Admin Center, el clúster al que está conectado y la red. Además, asegúrese de que ha iniciado sesión en una cuenta de Azure en Windows Admin Center y de que la suscripción de Azure que está planeando usar no ha expirado. Cuando haya finalizado, seleccione **Siguiente**.
3. En la página **Comprobaciones del sistema** del asistente, realice las acciones necesarias, como conectar la puerta de enlace de Windows Admin Center a Azure. En este paso deberá comprobar que Windows Admin Center y el sistema que hospedará Azure Kubernetes Service tengan las configuraciones adecuadas para continuar. Cuando haya terminado de realizar esta acción, seleccione **Siguiente**.
4. Configure la máquina que hospedará Azure Kubernetes Service en el paso **Configuración del host**. Le recomendamos que seleccione la **opción de descarga automática de actualizaciones** en esta sección. Cuando haya terminado, seleccione **Siguiente**. En este paso el asistente le pedirá que configure los detalles siguientes:
    * Detalles del host, como el nombre del clúster de administración y una carpeta para almacenar las imágenes de VM.
    * Redes de VM, que se aplicarán en todas las VM Linux y Windows (nodos) que se creen a fin de ejecutar contenedores y organizar la administración de contenedores. 
    * Configuración del equilibrador de carga, que definirá el grupo de direcciones que se usan para los servicios externos.

    ![Se muestra el paso de configuración del host del asistente para host de Azure Kubernetes Service.](.\media\setup\host-configuration.png)

5. Revise todas las selecciones en el paso **Revisar y crear**. Si está satisfecho con las selecciones que ha realizado, seleccione la opción **Siguiente** para iniciar la instalación del host. 
6. En la página **Progreso de la instalación**, puede ver el progreso de la instalación del host. Llegado a este punto, puede abrir la instancia de Windows Admin Center en una nueva pestaña y continuar con sus tareas de administración. 
7. Si la implementación se completa correctamente, se le presentará un panel de administración donde podrá crear y administrar los clústeres de Kubernetes. Este panel, al igual que el resto de Azure Kubernetes Service en Azure Stack HCl, se encuentra en una versión preliminar y se actualizará con funcionalidad adicional en futuras versiones. 
 
  ![Muestra el panel de administración de Azure Kubernetes Service en Azure Stack HCI.](.\media\setup\dashboard.png)
 
## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, instaló Windows Admin Center y la extensión de Azure Kubernetes Service. También configuró un host de Azure Kubernetes Service en el sistema en el que va a implementar un clúster de Kubernetes.

Ahora está listo para continuar con la [creación de un clúster de Kubernetes en Windows Admin Center](create-kubernetes-cluster.md).
