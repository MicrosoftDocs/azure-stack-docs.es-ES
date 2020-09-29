---
title: Inicio rápido para configurar Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center
description: Aprenda a configurar Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center.
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: fa07b755405fced34a404dbd2b2473450a8b8e26
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949404"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Inicio rápido: Configuración de Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center

> Se aplica a: Azure Stack HCI

En este inicio rápido aprenderá a configurar Azure Kubernetes Service en Azure Stack HCI mediante Windows Admin Center. Para usar PowerShell en su lugar, consulte [Configuración con PowerShell](setup-powershell.md).

La configuración implica realizar las siguientes tareas:

* Configure Windows Admin Center si aún no lo ha hecho.
* Instale la extensión de Azure Kubernetes Service en Azure Stack HCI para Windows Admin Center.
* Configure un host de Azure Kubernetes Service en el sistema en el que quiera implementar el clúster de Kubernetes.

Antes de empezar, asegúrese de que cumple todos los requisitos previos que se detallan en la página de [requisitos del sistema](.\system-requirements.md).

## <a name="setting-up-windows-admin-center"></a>Configuración de Windows Admin Center

Si aún no ha instalado Windows Admin Center, consulte [Instalación de Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/deploy/install). Para obtener una versión preliminar pública de Azure Kubernetes Service en Azure Stack HCI, debe descargar y ejecutar Windows Admin Center en una máquina Windows 10. Tenga en cuenta que en estos momentos solo el modo de escritorio de Windows Admin Center es compatible con Azure Kubernetes Service en Azure Stack HCI. Igualmente, recuerde que la funcionalidad Azure Kubernetes Service en Azure Stack HCI solo está disponible para la compilación de 2009 o una posterior de Windows Admin Center.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Instalación de la extensión de Azure Kubernetes Service

Una vez que haya obtenido los archivos de versión preliminar pública de Azure Kubernetes Service en Azure Stack HCI, debe guardar el archivo `.nupkg` de forma local o en un recurso compartido de SMB y agregar la ruta de acceso del archivo a la lista "Fuentes" del administrador de extensiones de Windows Admin Center. El archivo `.nupkg` es un paquete de NuGet que contiene la extensión de Windows Admin Center.

Para obtener acceso a la fuente de extensión existente, abra Windows Admin Center y seleccione el icono con forma de engranaje que está en la esquina superior derecha de la pantalla. Se le redirigirá al menú de configuración. Las fuentes de extensión se pueden encontrar en la sección **Puerta de enlace** del menú **Extensiones**. Vaya a la pestaña **Fuentes** y seleccione **Agregar**. En este panel, pegue la ruta de acceso al archivo a la copia de la extensión de Azure Kubernetes Service en Azure Stack HCI y seleccione **Agregar**. Si la ruta de acceso al archivo se ha agregado correctamente, recibirá una notificación de operación correcta. 

Ahora que ha agregado la fuente, la extensión de Azure Kubernetes Service en Azure Stack HCI aparecerá en la lista de extensiones disponibles. Una vez que la haya seleccionado, haga clic en la opción **Instalar** que se encuentra en la parte superior de la tabla para instalar esta extensión. Windows Admin Center se volverá a cargar una vez se haya completado la instalación. 

[ ![Vista de la lista de extensiones disponibles en el administrador de extensiones de Windows Admin Center.](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Configuración de un host de Azure Kubernetes Service

Debe completar un último paso para poder crear un clúster de Kubernetes. Configure un host de Azure Kubernetes Service en el sistema en el que quiera implementar el clúster de Kubernetes. Este sistema debe ser un clúster de Azure Stack HCI. 

> [!NOTE] 
> Tenga en cuenta que no se pueden configurar los hosts de Azure Kubernetes Service en dos sistemas independientes con la intención de combinarlos durante la creación del clúster de Kubernetes. 

Esta configuración se puede realizar con la nueva herramienta de Azure Kubernetes Service. 

Con esta herramienta podrá instalar y descargar los paquetes necesarios, así como crear un clúster de administración que proporcione los servicios básicos de Kubernetes y organice las cargas de trabajo de la aplicación. 

Comencemos: 
1. Seleccione **Configurar** para iniciar el asistente de configuración.
2. Revise los requisitos previos de la máquina en la que se ejecuta Windows Admin Center, el clúster de Azure Stack HCI al que está conectado y la red. Además, debe asegurarse de que haya iniciado sesión en una cuenta de Azure en Windows Admin Center. Cuando termine, seleccione **Siguiente**.
3. En la página **Comprobaciones del sistema** del asistente, realice las acciones necesarias, como conectar la puerta de enlace de Windows Admin Center a Azure. En este paso deberá comprobar que Windows Admin Center y el sistema que hospedará Azure Kubernetes Service tengan las configuraciones adecuadas para continuar. Cuando haya terminado de realizar esta acción, seleccione **Siguiente**.
4. Configure la máquina que hospedará Azure Kubernetes Service en el paso **Configuración del host**. Le recomendamos que seleccione la **opción de descarga automática de actualizaciones** en esta sección. Cuando haya terminado, seleccione **Siguiente**. En este paso el asistente le pedirá que configure los detalles siguientes:
    * Detalles del host, como el nombre del clúster de administración y una carpeta para almacenar las imágenes de VM.
    * Redes de VM, que se aplicarán en todas las VM Linux y Windows (nodos) que se creen a fin de ejecutar contenedores y organizar la administración de contenedores. 
    * Configuración del equilibrador de carga, que definirá el grupo de direcciones que se usan para los servicios externos.

    ![Se muestra el paso de configuración del host del asistente para host de Azure Kubernetes Service.](.\media\setup\host-configuration.png)

5. Regístrese en Azure y envíe los datos de diagnóstico a Microsoft en el paso **Registro de Azure**. Aunque en esta página se solicita un grupo de recursos y una suscripción a Azure, no se debe pagar ningún costo extra por la configuración y el uso de Azure Kubernetes Service en la versión preliminar pública. Los datos de diagnóstico que envíe a Microsoft se usarán para mantener el servicio protegido y actualizado, solucionar problemas y realizar mejoras en el producto. Cuando termine de seleccionar todo lo que necesite, seleccione **Siguiente**.
6. Revise todas las selecciones que haya hecho en el paso **Revisar y crear**. Si está satisfecho con las selecciones que ha realizado, seleccione la opción **Instalar** para iniciar la instalación del host. 
7. En la página **Progreso de la instalación**, puede ver el progreso de la instalación del host. Llegado a este punto, puede abrir la instancia de Windows Admin Center en una nueva pestaña y continuar con sus tareas de administración. 
8. Si la implementación se realiza correctamente, se mostrarán los pasos siguientes y se habilitará el botón **Finalizar**. Al seleccionar la opción **Download management cluster kubeconfig** (Descargar el clúster de administración kubeconfig) en **Siguientes pasos**, comenzará una descarga y no saldrá del asistente. 

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, instaló Windows Admin Center y la extensión de Azure Kubernetes Service para Azure Stack HCI. También configuró un host de Azure Kubernetes Service en el sistema en el que va a implementar un clúster de Kubernetes.

Ahora está listo para continuar con la [creación de un clúster de Kubernetes en Windows Admin Center](create-kubernetes-cluster.md).
