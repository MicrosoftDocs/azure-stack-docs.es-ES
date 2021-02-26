---
title: Cómo instalar Event Hubs en Azure Stack Hub
description: Aprenda a instalar el proveedor de recursos de Event Hubs en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/02/2020
ms.reviewer: jfggdl
ms.lastreviewed: 09/02/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 7f20866236099dadc7e6ca3e3c201572f51069ca
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562483"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>Cómo instalar Event Hubs en Azure Stack Hub

En este artículo se muestra cómo descargar e instalar el proveedor de recursos de Event Hubs, haciendo que esté disponible para ofrecerlo a los clientes para la suscripción. También debe completar los [requisitos previos de instalación de Event Hubs](event-hubs-rp-prerequisites.md) antes de continuar.

## <a name="download-packages"></a>Descarga de paquetes

Para poder instalar Event Hubs en Azure Stack Hub, debe descargar el proveedor de recursos y sus paquetes dependientes mediante la característica Administración de Marketplace. Si no está familiarizado con la característica Administración de Marketplace, consulte [Descarga de elementos de Marketplace desde Azure y publicación en Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). Esta sección le guía en el proceso de descarga de elementos de Azure Marketplace. 

> [!NOTE]
> El proceso de descarga puede tardar entre 30 minutos y 2 horas, según la latencia de red y los paquetes existentes en la instancia de Azure Stack Hub. 

::: zone pivot="state-connected"
Para un escenario conectado, descargue los elementos de Azure Marketplace directamente al Marketplace de Azure Stack Hub:

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
2. Seleccione **Marketplace Management** (Administración de Marketplace) a la izquierda.
3. Seleccione **Proveedores de recursos**.
4. Seleccione **+ Add from Azure** (+ Agregar desde Azure).
5. Busque "Event Hubs" en la barra de búsqueda.
6. Seleccione la fila "Event Hubs" en los resultados de la búsqueda. 
7. En la página de descarga de "Event Hubs", seleccione la versión de Event Hubs que desee instalar y, a continuación, seleccione **Download** (Descargar) en la parte inferior de la página. 
   [![Paquetes de administración de Marketplace](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

Tenga en cuenta que se descargan paquetes de software adicionales junto con Event Hubs, entre los que se incluyen:

- Complemento de Microsoft Azure Stack Hub RP Windows Server (SOLO INTERNO)
- Configuración de estado deseado de PowerShell
::: zone-end

::: zone pivot="state-disconnected"
En un escenario sin conexión o con conexión parcial, descargue los paquetes en la máquina local y luego impórtelos en el Marketplace de Azure Stack Hub:

1. Si todavía no lo ha hecho, siga las instrucciones de [Descarga de elementos de Marketplace: escenario sin conexión o con conexión parcial](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected). Aquí puede descargar y ejecutar la herramienta de redifusión de Marketplace, que le permite descargar los paquetes de Event Hubs.
2. Una vez que se abra la ventana "Azure Marketplace Items" (Elementos de Azure Marketplace) de la herramienta de redifusión, busque y seleccione "Event Hubs" para descargar los paquetes necesarios en la máquina local.
3. Una vez finalizada la descarga, importe los paquetes a la instancia de Azure Stack Hub y publíquela en Marketplace. 
::: zone-end

## <a name="installation"></a>Instalación 

1. Si todavía no lo ha hecho, inicie sesión en el portal de administración de Azure Stack Hub, seleccione **Administración de Marketplace** a la izquierda y seleccione **Proveedores de recursos**.
2. Una vez que Event Hubs y el resto de software necesario se hayan descargado, **Administración de Marketplace** mostrará los paquetes "Event Hubs" con el estado "Not Installed" (No instalado). Puede haber otros paquetes que muestren el estado "Downloaded" (Descargado). Seleccione la fila "Event Hubs" que desea instalar.
   [![Paquetes descargados de administración de Marketplace](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
3. La página del paquete de instalación de Event Hubs muestra un banner azul en la parte superior. Seleccione el banner para iniciar la instalación de Event Hubs.
   [![Event Hubs en administración de Marketplace: comenzar instalación](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>Requisitos previos de instalación

1. A continuación, se le transferirá a la página de instalación. Seleccione **Install Prerequisites** (Instalar requisitos previos) para iniciar el proceso de instalación.
   ![Event Hubs en administración de Marketplace: instalar los requisitos previos](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. Espere hasta que la instalación de los requisitos previos sea correcta. Verá una marca de verificación verde junto a **Install prerequisites** (Instalar requisitos previos) antes de continuar con el paso siguiente.

   ![Event Hubs en administración de Marketplace: instalación correcta de los requisitos previos](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>Preparar secretos 

1. En el paso **2. Prepare secrets** (Preparar secretos), seleccione **Add certificate** (Agregar certificado) y aparecerá el panel **Add a certificate** (Agregar un certificado).
   ![Event Hubs en administración de Marketplace: preparar secretos](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. Seleccione el botón browse (examinar) en **Add a certificate** (Agregar un certificado), a la derecha del campo de nombre de archivo de certificado.
3. Seleccione el archivo de certificado .pfx que ha adquirido al completar los requisitos previos. Para más información, consulte [los requisitos previos de la instalación](event-hubs-rp-prerequisites.md). 

4. Escriba la contraseña que proporcionó para crear una cadena segura para el certificado SSL de Event Hubs. A continuación, seleccione **Agregar**.
   ![Event Hubs en administración de Marketplace: agregar certificado](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>Instalación del proveedor de recursos

1. Cuando la instalación del certificado se realice correctamente, verá una marca de verificación verde junto a **Prepare secrets** (Preparar secretos) antes de continuar con el siguiente paso. Ahora, seleccione el botón **Install** (Instalar) junto a **3 Install resource provider** (Instalar proveedor de recursos).
   ![Event Hubs en administración de Marketplace: iniciar instalación de RP](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. A continuación, verá la siguiente página, que indica que se está instalando el proveedor de recursos de Event Hubs.
   [![Event Hubs en administración de Marketplace: instalación de RP](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. Espere a la notificación de que la instalación se completó. Este proceso suele tardar una o varias horas, en función del tipo de Azure Stack Hub. 
   [![Event Hubs en administración de Marketplace: instalación de RP finalizada](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. Compruebe que la instalación de Event Hubs se ha realizado correctamente; para ello, vuelva a la página **Resource Providers** (Proveedores de recursos) de **Marketplace Management** (Administración de Marketplace). El estado de Event Hubs debería mostrar "Installed" (Instalado).
   ![Event Hubs disponible en administración de Marketplace](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="next-steps"></a>Pasos siguientes

Para que los usuarios puedan implementar recursos de Event Hubs, debe crear uno o varios planes, ofertas y suscripciones. 

- Si esta es la primera vez que está ofreciendo un servicio, comience con el tutorial [Oferta de un servicio a los usuarios](tutorial-offer-services.md). Después, continúe con el siguiente tutorial, [Prueba de una oferta de un servicio](tutorial-test-offer.md).
- Una vez que esté familiarizado con el concepto de ofrecer un servicio, cree una oferta y un plan que incluya el proveedor de recursos de Event Hubs. A continuación, cree una suscripción para los usuarios o proporcióneles la información de la oferta para que pueda crear las suyas propias. Como referencia, también puede seguir la serie de artículos de [introducción a los servicios, los planes, las ofertas y las suscripciones de Azure Stack Hub](service-plan-offer-subscription-overview.md).

Para comprobar si hay actualizaciones, consulte [Procedimientos para actualizar Event Hubs en Azure Stack Hub](resource-provider-apply-updates.md).

Si necesita quitar el proveedor de recursos, consulte [Eliminación del proveedor de recursos de Event Hubs](event-hubs-rp-remove.md).

Para más información acerca de la experiencia del usuario, consulte [Información general sobre Event Hubs en Azure Stack Hub](../user/event-hubs-overview.md) en los documentos de usuario.