---
title: Implementación de una solución de detección de afluencia de personas basada en IA con Azure y Azure Stack Hub
description: Aprenda a implementar una solución de detección de afluencia de personas basada en IA para analizar el tráfico de visitantes en las tiendas mediante Azure y Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: ba27bf06a7cf65bd40155a80c8d8877b2ac68805
ms.sourcegitcommit: b185ab34c4c799892948536dd6d1d1b2fc31174e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150300"
---
# <a name="deploy-an-ai-based-footfall-detection-solution-using-azure-and-azure-stack-hub"></a>Implementación de una solución de detección de afluencia de público basada en IA con Azure y Azure Stack Hub

En este artículo se describe cómo implementar una solución basada en inteligencia artificial que genere información útil a partir de acciones reales, mediante Azure, Azure Stack Hub y el Kit de desarrollo de inteligencia artificial de Custom Vision.

En esta solución, aprenderá cómo:

> [!div class="checklist"]
> - Implementar agrupaciones de aplicaciones nativas en la nube (CNAB) en el perímetro. 
> - Implementar una aplicación que abarque los límites de la nube.
> - Use el kit de desarrollo de IA de Custom Vision para inferencia en el perímetro.

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub es una extensión de Azure. Azure Stack Hub aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En el artículo [Consideraciones de diseño de aplicaciones híbridas](overview-app-design-considerations.md) se examinan los pilares de la calidad del software (selección de ubicación, escalabilidad, disponibilidad, resistencia, manejabilidad y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar a usar esta guía de implementación, asegúrese de:

- Consultar el tema [Patrón de detección de afluencia de compradores](pattern-retail-footfall-detection.md).
- Obtener acceso de usuario a un Kit de desarrollo de Azure Stack (ASDK) o una instancia de sistema integrada de Azure Stack Hub con:
  - [Azure App Service en el proveedor de recursos de Azure Stack Hub](../operator/azure-stack-app-service-overview.md) instalado. Necesita acceso de operador a la instancia de Azure Stack Hub o trabajar con su administrador para realizar la instalación.
  - Una suscripción a una oferta que proporcione App Service y la cuota de Storage. Necesita acceso de operador para crear una oferta.
- Obtener acceso a una suscripción de Azure.
  - Si no tiene ninguna suscripción de Azure, regístrese para una [cuenta de evaluación gratuita](https://azure.microsoft.com/free/) antes de empezar.
- Crear dos entidades de servicio en el directorio:
  - Una configurada para usarla con recursos de Azure, con acceso en el ámbito de la suscripción de Azure.
  - Otra configurada para usarla con recursos de Azure Stack Hub, con acceso en el ámbito de la suscripción de Azure Stack Hub.
  - Para más información sobre la creación de entidades de servicio y la autorización de acceso, consulte [Uso de una identidad de aplicación para acceder a recursos](../operator/azure-stack-create-service-principals.md). Si prefiere usar la CLI de Azure, vea [Creación de una entidad de servicio de Azure con la CLI de Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).
- Implemente Azure Cognitive Services en Azure o Azure Stack Hub.
  - En primer lugar [obtenga más información sobre Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).
  - Luego, visite [Implementación de Azure Cognitive Services en Azure Stack](../user/azure-stack-solution-template-cognitive-services.md) para implementar Cognitive Services en Azure Stack Hub. Primero debe registrarse para acceder a la versión preliminar.
- Clonar o descargar un kit de desarrollo de IA de Azure Custom Vision. Para detalles, consulte el [kit de desarrollo de IA de Vision](https://azure.github.io/Vision-AI-DevKit-Pages/).
- Registrarse para obtener una cuenta de Power BI.
- Una clave de suscripción de Face API de Azure Cognitive Services y una dirección URL de punto de conexión. Puede obtener ambas con la evaluación gratuita de [Pruebe Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). O bien, siga las instrucciones de [Creación de un recurso de Cognitive Services con Azure Portal](/azure/cognitive-services/cognitive-services-apis-create-account).
- Instalar los siguientes recursos de desarrollo:
  - [CLI de Azure 2.0](../user/azure-stack-version-profiles-azurecli2.md)
  - [Docker CE](https://hub.docker.com/search/?type=edition&offering=community)
  - [Porter](https://porter.sh/). Use Porter para implementar aplicaciones en la nube mediante los manifiestos del conjunto de CNAB que se proporcionan.
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [Azure IoT Tools para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  - [Extensión de Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - [Python](https://www.python.org/)

## <a name="deploy-the-hybrid-cloud-app"></a>Implementación de la aplicación en la nube híbrida

En primer lugar, use la CLI de Porter para generar un conjunto de credenciales y luego implemente la aplicación en la nube.  

1. Clone o descargue el código de ejemplo de la solución desde https://github.com/azure-samples/azure-intelligent-edge-patterns. 

1. Porter generará un conjunto de credenciales que automatizarán la implementación de la aplicación. Antes de ejecutar el comando de generación de credenciales, asegúrese de que dispone de lo siguiente:

    - Una entidad de servicio para acceder a los recursos de Azure, incluido el identificador de la entidad de servicio, la clave y el DNS del inquilino.
    - El identificador de suscripción de su suscripción de Azure.
    - Una entidad de servicio para acceder a los recursos de Azure Stack Hub, incluido el identificador de la entidad de servicio, la clave y el DNS del inquilino.
    - El identificador de suscripción de su suscripción de Azure Stack Hub.
    - Su clave de Face API de Azure Cognitive Services y una dirección URL de punto de conexión de recurso.

1. Ejecute el proceso de generación de credenciales de Porter y siga las indicaciones:

   ```porter
   porter creds generate --tag intelligentedge/footfall-cloud-deployment:0.1.0
   ```

1. Porter también requiere que se ejecute un conjunto de parámetros. Cree un archivo de texto de parámetro y escriba los siguientes pares de nombre/valor. Pregunte al administrador de Azure Stack Hub si necesita ayuda con cualquiera de los valores necesarios.

   > [!NOTE] 
   > El valor `resource suffix` se usa para asegurarse de que los recursos de la implementación tienen nombres únicos en Azure. Debe ser una cadena única de letras y números, de no más de ocho caracteres.

    ```porter
    azure_stack_tenant_arm="Your Azure Stack Hub tenant endpoint"
    azure_stack_storage_suffix="Your Azure Stack Hub storage suffix"
    azure_stack_keyvault_suffix="Your Azure Stack Hub keyVault suffix"
    resource_suffix="A unique string to identify your deployment"
    azure_location="A valid Azure region"
    azure_stack_location="Your Azure Stack Hub location identifier"
    powerbi_display_name="Your first and last name"
    powerbi_principal_name="Your Power BI account email address"
    ```
   Guarde el archivo de texto y tome nota de su ruta de acceso.

1. Ya está listo para implementar la aplicación en la nube híbrida mediante Porter. Ejecute el comando de instalación y vea cómo se implementan los recursos en Azure y Azure Stack Hub:

    ```porter
    porter install footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"
    ```

1. Una vez completada la implementación, anote los siguientes valores:
    - La cadena de conexión de la cámara.
    - La cadena de conexión de la cuenta de almacenamiento de imágenes.
    - Los nombres del grupo de recursos.

## <a name="prepare-the-custom-vision-ai-devkit"></a>Preparación del Kit de desarrollo de inteligencia artificial de Custom Vision

A continuación, configure el kit de desarrollo de AI de Custom Vision, tal como se muestra en el [inicio rápido del kit de desarrollo de IA de Vision](https://azure.github.io/Vision-AI-DevKit-Pages/docs/quick_start/). También debe configurar y probar la cámara mediante la cadena de conexión proporcionada en el paso anterior.

## <a name="deploy-the-camera-app"></a>Implementación de la aplicación de la cámara

Use la CLI de Porter para generar un conjunto de credenciales y luego implemente la aplicación de la cámara.

1. Porter generará un conjunto de credenciales que automatizarán la implementación de la aplicación. Antes de ejecutar el comando de generación de credenciales, asegúrese de que dispone de lo siguiente:

    - Una entidad de servicio para acceder a los recursos de Azure, incluido el identificador de la entidad de servicio, la clave y el DNS del inquilino.
    - El identificador de suscripción de su suscripción de Azure.
    - La cadena de conexión de la cuenta de almacenamiento de imágenes que se proporciona al implementar la aplicación en la nube.

1. Ejecute el proceso de generación de credenciales de Porter y siga las indicaciones:

    ```porter
    porter creds generate --tag intelligentedge/footfall-camera-deployment:0.1.0
    ```

1. Porter también requiere que se ejecute un conjunto de parámetros. Cree un archivo de texto de parámetro y escriba el texto siguiente. Pregunte al administrador de Azure Stack Hub si no conoce algunos de los valores requeridos.

    > [!NOTE]
    > El valor `deployment suffix` se usa para asegurarse de que los recursos de la implementación tienen nombres únicos en Azure. Debe ser una cadena única de letras y números, de no más de ocho caracteres.

    ```porter
    iot_hub_name="Name of the IoT Hub deployed"
    deployment_suffix="Unique string here"
    ```

    Guarde el archivo de texto y tome nota de su ruta de acceso.

4. Ya está listo para implementar la aplicación de la cámara mediante Porter. Ejecute el comando de instalación y vea cómo se crea la implementación de IoT Edge.

    ```porter
    porter install footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
    ```

5. Para comprobar que la implementación de la cámara ha finalizado viendo la fuente de cámara en `https://<camera-ip>:3000/`, donde `<camara-ip>` es la dirección IP de la cámara. Este paso puede tardar hasta diez minutos.

## <a name="configure-azure-stream-analytics"></a>Configuración de Azure Stream Analytics

Ahora que los datos fluyen hacia Azure Stream Analytics desde la cámara, es necesario que lo autorice manualmente para que se comuniquen con Power BI.

1. En Azure Portal, abra **Todos los recursos** y el trabajo *process-footfall\[yoursuffix\]* .

2. En la sección **Topología de trabajo** del panel de trabajos de Stream Analytics, seleccione la opción **Salidas**.

3. Seleccione el receptor de salida de **salida de tráfico**.

4. Seleccione **Renovar autorización** e inicie sesión en su cuenta de Power BI.
  
    ![Mensaje de renovación de autorización en Power BI](./media/solution-deployment-guide-retail-footfall-detection/image2.png)

5. Guarde la configuración de salida.

6. Vaya al panel **Información general** y seleccione **Iniciar** para empezar a enviar datos a Power BI.

7. Seleccione **Ahora** como la hora de inicio de la salida del trabajo y seleccione **Iniciar**. El estado del trabajo se puede ver en la barra de notificación.

## <a name="create-a-power-bi-dashboard"></a>Creación de un panel de Power BI

1. Una vez que el trabajo se inicia correctamente, vaya a [Power BI](https://powerbi.com/) e inicie sesión con su cuenta profesional o educativa. Si la consulta del trabajo de Stream Analytics genera resultados, el conjunto de datos *footfall-dataset* que creó existe en la pestaña **Conjuntos de datos**.

2. En el área de trabajo de Power BI, seleccione **+ Crear** para crear un nuevo panel denominado *Footfall Analysis* (Análisis de afluencia de público).

3. En la parte superior de la ventana, seleccione **Agregar icono**. Luego, seleccione **Datos de transmisión personalizados** y **Siguiente**. Elija **footfall-dataset** en **Sus conjuntos de datos**. Seleccione **Tarjeta** en la lista desplegable **Tipo de visualización** y agregue **age** a **Campos**. Seleccione **Siguiente** para escribir el nombre del icono y, después, seleccione **Aplicar** para crear el icono.

4. Puede agregar campos y tarjetas adicionales según sea necesario.

## <a name="test-your-solution"></a>Prueba de la solución

Observe cómo cambian los datos en las tarjetas que creó en Power BI cuando diferentes personas caminan frente a la cámara. Las inferencias pueden tardar hasta veinte segundos en aparecer una vez grabadas.

## <a name="remove-your-solution"></a>Protección de la solución

Si desea quitar la solución, ejecute los siguientes comandos mediante Porter y utilice los mismos archivos de parámetro que creó para la implementación:

```porter
porter uninstall footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"

porter uninstall footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [consideraciones de diseño de aplicaciones híbridas]. (overview-app-design-considerations.md)
- Revise y proponga mejoras en [el código de este ejemplo en GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis).
