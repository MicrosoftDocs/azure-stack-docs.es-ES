---
title: Instalación de IoT Hub en Azure Stack Hub
description: Aprenda a instalar el proveedor de recursos de IoT Hub en Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 47d06bc7363a9ce8d4de8971bf26c38bfdb16bcc
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256122"
---
# <a name="how-to-install-iot-hub-on-azure-stack-hub"></a>Instalación de IoT Hub en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

En este artículo se muestra cómo descargar e instalar el proveedor de recursos de IoT Hub, haciendo que esté disponible para ofrecerlo a los clientes para la suscripción. El proceso de instalación de IoT Hub tarda aproximadamente 2 horas.

## <a name="download-iot-hub"></a>Descarga de IoT Hub

<!-- ### Connected Scenario -->
::: zone pivot="state-connected"
Si Azure Stack Hub puede acceder a Azure Marketplace, siga los pasos de esta sección para descargar e instalar IoT Hub y sus dependencias. 

Para descargar IoT Hub para una implementación conectada, siga estos pasos:

1. Inicie sesión en el portal de administración de Azure Stack Hub. 
2. Seleccione **Marketplace Management** (Administración de Marketplace) a la izquierda, después, seleccione **Proveedores de recursos** y, a continuación, haga clic en **+ Add from Azure** (+ Agregar desde Azure).

    [![Búsqueda del proveedor de recursos en Marketplace](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png)](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png#lightbox)

3. Si es necesario, filtre por "IoT Hub" y, a continuación, seleccione el paquete de **IoT Hub**.

    [![Proveedor de recursos de IoT Hub en Marketplace](../operator/media/iot-hub-rp-install/download1.png)](../operator/media/iot-hub-rp-install/download1.png#lightbox)

4. En la página del paquete de **IoT Hub**, seleccione **Descargar**.

    [![Detalles del paquete de IoT Hub](../operator/media/iot-hub-rp-install/download2.png)](../operator/media/iot-hub-rp-install/download2.png#lightbox)

5. Espere a que se complete la descarga del paquete. El estado aparecerá como **Descargando** y puede tardar hasta 10 minutos.

    [![Estado "Descargando" del paquete de IoT Hub](../operator/media/iot-hub-rp-install/download3.png)](../operator/media/iot-hub-rp-install/download3.png#lightbox)

6. Una vez descargado el paquete, el estado cambiará a **No instalado** en la página de **administración de Marketplace**.

    [![Paquete descargado de IoT Hub con el estado No instalado](../operator/media/iot-hub-rp-install/download4.png)](../operator/media/iot-hub-rp-install/download4.png#lightbox)
::: zone-end

<!-- ### Disconnected or partially connected scenario -->
::: zone pivot="state-disconnected"
Para descargar IoT Hub para una implementación desconectada o parcialmente conectada, descargue primero los paquetes en la máquina local. Una vez finalizada la descarga, puede importarlas en la instancia de Azure Stack Hub.

1. Si todavía no lo ha hecho, siga las instrucciones de [Descarga de elementos de Marketplace: escenario sin conexión o con conexión parcial](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected). Aquí puede descargar y ejecutar la herramienta de redifusión de Marketplace, que le permite descargar los paquetes de IoT Hub.
2. Una vez que se abra la ventana "Azure Marketplace Items" (Elementos de Azure Marketplace) de la herramienta de redifusión, busque y seleccione "IoT Hub" para descargar los paquetes necesarios en la máquina local.
3. Una vez finalizada la descarga, importe los paquetes a la instancia de Azure Stack Hub y publíquela en Marketplace.
::: zone-end

## <a name="install-iot-hub"></a>Instalación de IoT Hub

En la página de **administración de Marketplace**, siga estos pasos para instalar el paquete de IoT Hub:

1. Haga clic en la fila de **IoT Hub** y, a continuación, en **Iniciar la instalación**.

    [![Proveedor de recursos de IoT Hub en espera de la instalación](../operator/media/iot-hub-rp-install/install1.png)](../operator/media/iot-hub-rp-install/install1.png#lightbox)

2. Haga clic en **Instalar requisitos previos**.

    [![Requisitos previos de instalación del proveedor de recursos de IoT Hub](../operator/media/iot-hub-rp-install/install2.png)](../operator/media/iot-hub-rp-install/install2.png#lightbox)

3. Se puede realizar un seguimiento del estado de la instalación en el panel de notificaciones. Este paso tardará unos 10 minutos.

    [![Instalación de los requisitos previos del proveedor de recursos de IoT Hub](../operator/media/iot-hub-rp-install/install3.png)](../operator/media/iot-hub-rp-install/install3.png#lightbox)

4. Espere a que se complete la instalación de los requisitos previos. Este paso suele tardar entre 5-10 minutos según el entorno.

    [![Preparación de secretos del proveedor de recursos de IoT Hub](../operator/media/iot-hub-rp-install/install4.png)](../operator/media/iot-hub-rp-install/install4.png#lightbox)

5. Haga clic en **Agregar certificados** en **Prepare Secrets** (Preparar secretos). Proporcione el certificado pfx que se creó en los requisitos previos.

    [![Preparación de secretos del proveedor de recursos de IoT Hub: cargar certificado](../operator/media/iot-hub-rp-install/install5.png)](../operator/media/iot-hub-rp-install/install5.png#lightbox)

6. Busque y proporcione el archivo pfx que se creó y la contraseña (la entrada al script).

    [![Preparación de secretos del proveedor de recursos de IoT Hub: seleccionar certificado](../operator/media/iot-hub-rp-install/install6.png)](../operator/media/iot-hub-rp-install/install6.png#lightbox)

    [![Preparación de secretos del proveedor de recursos de IoT Hub: proporcionar contraseña](../operator/media/iot-hub-rp-install/install61.png)](../operator/media/iot-hub-rp-install/install61.png#lightbox)

7. Haga clic en **Instalar** en **Instalación del proveedor de recursos**.

    [![Preparación de secretos del proveedor de recursos de IoT Hub: finalizar](../operator/media/iot-hub-rp-install/install7.png)](../operator/media/iot-hub-rp-install/install7.png#lightbox)

8. Una vez iniciada la instalación, el estado de la implementación se puede encontrar en Marketplace o en el panel de notificaciones.

    [![Instalación del proveedor de recursos de IoT Hub en curso](../operator/media/iot-hub-rp-install/install8.png)](../operator/media/iot-hub-rp-install/install8.png#lightbox)

9. La instalación puede tardar entre 90 y 120 minutos. Espere hasta que finalice la instalación.

    [![Instalación del proveedor de recursos de IoT Hub finalizada](../operator/media/iot-hub-rp-install/install91.png)](../operator/media/iot-hub-rp-install/install91.png#lightbox)

    [![Proveedores de recursos de Marketplace: proveedores de recursos instalados](../operator/media/iot-hub-rp-install/install92.png)](../operator/media/iot-hub-rp-install/install92.png#lightbox)

El proveedor de recursos de IoT Hub se ha instalado correctamente. Para comenzar, realice los pasos siguientes:

1. Si es necesario, siga las instrucciones para [crear un plan, ofertas y la suscripción](./service-plan-offer-subscription-overview.md).

2. Si ya existe una suscripción, actualice la oferta o el plan asociado para que incluya el servicio **Microsoft.Devices**. Vaya a **Plan** -> **Choose the plan to update (Elegir el plan que desea actualizar)**  -> **Add Service and quota** (Agregar servicio y cuota).

3. Agregue el **servicio Microsoft.Devices** y haga clic en **Guardar**.

    [![Agregar servicio IoT Hub al plan](../operator/media/iot-hub-rp-install/pd2.png)](../operator/media/iot-hub-rp-install/pd2.png#lightbox)

4. Listo. Ahora se pueden crear instancias de IoT Hub.

## <a name="using-iot-hub"></a>Uso de IoT Hub

Para más información sobre cómo usar IoT Hub, consulte la [documentación de Azure IoT Hub](/azure/iot-hub).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la administración de IoT Hub en Azure Stack Hub, consulte [Administración de IoT Hub en Azure Stack Hub](iot-hub-rp-manage.md).