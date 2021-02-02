---
title: Implementación en la sucursal y el perímetro en Azure Stack HCl
description: En este tema se proporcionan instrucciones sobre cómo planear, configurar e implementar escenarios de sucursal y perímetro en el sistema operativo de Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 672a97a9804de324edde7c3802849a32ea44c0c4
ms.sourcegitcommit: dd34ae1c6207aafb5218c31658123e913f51bf7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98691068"
---
# <a name="deploy-branch-office-and-edge-on-azure-stack-hci"></a>Implementación en la sucursal y el perímetro en Azure Stack HCl

>Se aplica a: Azure Stack HCI, versión 20H2

En este tema se proporcionan instrucciones sobre cómo planear, configurar e implementar escenarios de sucursal y perímetro en el sistema operativo de Azure Stack HCI. La guía coloca la organización para ejecutar cargas de trabajo complejas y de alta disponibilidad en máquinas virtuales (VM) y contenedores en implementaciones perimetrales y de sucursal remotas. La informática en el perímetro desplaza la mayor parte del procesamiento de datos de un sistema centralizado al perímetro de la red, más cerca de un dispositivo o sistema que requiere datos rápidamente.

Use Azure Stack HCl para ejecutar aplicaciones virtualizadas y cargas de trabajo con alta disponibilidad en el hardware recomendado. El hardware es compatible con los clústeres que se componen de dos servidores configurados con resistencia anidada para el almacenamiento, un testigo de clúster de unidad USB simple y de bajo costo y administración mediante Windows Admin Center basado en explorador. Para obtener más información sobre la creación de un testigo de clúster de dispositivo USB, consulte [Implementar un testigo de recurso compartido de archivos](https://docs.microsoft.com/windows-server/failover-clustering/file-share-witness).

Azure IoT Edge mueve el análisis en la nube y lógica de negocios personalizada a los dispositivos para que pueda centrarse en la información empresarial, en lugar de en la administración de los datos. Azure IoT Edge combina la IA, la nube y la informática perimetral en cargas de trabajo en la nube en contenedores, como Azure Cognitive Services, Machine Learning, Stream Analytics y Functions. Las cargas de trabajo se pueden ejecutar en dispositivos que van desde un Raspberry Pi hasta un servidor perimetral convergente. Use [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) para administrar las aplicaciones y dispositivos perimetrales.

Agregar Azure IoT Edge a las implementaciones perimetrales y de sucursal de Azure Stack HCI moderniza el entorno para admitir la el marco de implementación de aplicaciones de [canalización de CI/CD](https://docs.microsoft.com/azure/iot-edge/how-to-continuous-integration-continuous-deployment). El personal de DevOps de la organización puede implementar e iterar las aplicaciones en contenedores que crea y admite el departamento de TI mediante las herramientas y los procesos de administración de VM tradicionales.

Características principales de Azure IoT Edge:
- Software de código abierto de Microsoft.
- Se ejecuta en Windows o Linux.
- Se ejecuta "en el perímetro" para proporcionar respuestas casi en tiempo real.
- Mecanismos de software y hardware seguros.
- Disponible en el [kit de herramientas de IA para Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge)
- Compatibilidad con la programación abierta para: Java, .NET Core 2.0, Node.js, C y Python
- Compatibilidad con la conectividad sin conexión e intermitente
- Administración nativa desde Azure IoT Hub

Para obtener más información, consulte [¿Qué es Azure IoT Edge?](https://docs.microsoft.com/azure/iot-edge/about-iot-edge)

## <a name="deploy-branch-office-and-edge"></a>Implementación de sucursales y perímetros
En esta sección se describe de forma general cómo adquirir hardware para implementaciones perimetrales y en sucursales en Azure Stack HCI y cómo usar Windows Admin Center para la administración. También se explica la implementación de Azure IoT Edge para administrar contenedores en la nube.

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>Paso 1: adquirir hardware del Catálogo de Azure Stack HCI
En primer lugar, deberá adquirir el hardware. La forma más fácil de hacerlo es encontrar su asociado de hardware de Microsoft preferido en el [Catálogo de Azure Stack HCI](https://hcicatalog.azurewebsites.net) y adquirir un sistema integrado con el sistema operativo de Azure Stack HCI preinstalado. En el catálogo, puede filtrar para ver el hardware del proveedor optimizado para este tipo de carga de trabajo.

En caso contrario, tendrá que implementar el sistema operativo de Azure Stack HCI en su propio hardware. Para obtener más información acerca de las opciones de implementación de Azure Stack HCI y la instalación de Windows Admin Center, consulte [Implementación del sistema operativo de Azure Stack HCI](./operating-system.md).

A continuación, use Windows Admin Center para [crear un clúster de Azure Stack HCI](./create-cluster.md).

### <a name="step-2-set-up-azure-monitor-in-windows-admin-center"></a>Paso 2: Configuración de Azure Monitor en Windows Admin Center
En Windows Admin Center, configure Azure Monitor para extraer conclusiones sobre el mantenimiento de la aplicación, la red y el servidor de la implementación perimetral y de sucursal de Azure Stack HCI.

Para obtener más información, consulte [Supervisión de Azure Stack HCI con Azure Monitor](../manage/azure-monitor.md).

También puede usar Windows Admin Center para configurar servicios híbridos adicionales de Azure, como Backup, File Sync, Site Recovery, VPN de punto a sitio, Update Management y Security Center.

### <a name="step-3-use-container-based-apps-and-iot-data-processing"></a>Paso 3: Usar aplicaciones basadas en contenedores y procesamiento de datos de IoT
Ya está listo para usar el desarrollo de aplicaciones moderno basado en contenedores y el procesamiento de datos de IoT. Use Windows Admin Center para los pasos descritos en esta sección a fin de implementar una VM que ejecute Azure IoT Edge.

Para obtener más información, consulte [¿Qué es Azure IoT Edge?](https://docs.microsoft.com/azure/iot-edge/about-iot-edge)

Para implementar Azure IoT Edge en Azure Stack HCI:
1. Use Windows Admin Center para [crear una nueva VM en Azure Stack HCI](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/manage-virtual-machines#create-a-new-virtual-machine).

    Para obtener información sobre las versiones de sistema operativo, los tipos de VM, las arquitecturas de procesador y los requisitos del sistema, consulte [sistemas admitidos de Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support).

1. Si aún no tiene ninguna cuenta de Azure, inicie una [cuenta gratuita](https://azure.microsoft.com/free).
1. En Azure Portal, [cree un una instancia de Azure IoT Hub](https://docs.microsoft.com/azure/iot-edge/quickstart#create-an-iot-hub).
1. En Azure Portal, [registre un dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart#register-an-iot-edge-device).

    >[!NOTE]
    > El dispositivo IoT Edge está en una VM que ejecuta Windows o Linux en Azure Stack HCI.

1. En la VM que creó en el paso 1, [instale e inicie el entorno de ejecución de IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart#install-and-start-the-iot-edge-runtime).

   >[!IMPORTANT]
   > Necesita la cadena de dispositivo que creó en el paso 4 para conectar el entorno en tiempo de ejecución a Azure IoT Hub.

1. [Implementación de un módulo](https://docs.microsoft.com/azure/iot-edge/quickstart#deploy-a-module) en Azure IoT Edge.

    En esta sección, puede obtener e implementar módulos preintegrados de la sección [Módulos de IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) de Azure Marketplace.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de las sucursales y el perímetro, así como sobre Azure IoT Edge, consulte:
- [Inicio rápido: Implementación del primer módulo de IoT Edge en un dispositivo virtual Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux?view=iotedge-2018-06&preserve-view=true)
- [Inicio rápido: Implementación del primer módulo de IoT Edge en un dispositivo Windows](https://docs.microsoft.com/azure/iot-edge/quickstart?view=iotedge-2018-06&preserve-view=true)
