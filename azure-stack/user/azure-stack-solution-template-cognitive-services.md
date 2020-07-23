---
title: Implementación de Azure Cognitive Services en Azure Stack Hub
description: Obtenga información sobre cómo implementar Azure Cognitive Services en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 05/21/2020
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 05/21/2020
ms.openlocfilehash: 217dc3a46d277aa8abf57379224dcad0fca4d983
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86566760"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack-hub"></a>Implementación de Azure Cognitive Services en Azure Stack Hub

Puede usar Azure Cognitive Services con compatibilidad con contenedores en Azure Stack Hub. La compatibilidad con contenedores en Azure Cognitive Services le permite usar las mismas API enriquecidas que están disponibles en Azure. El uso de contenedores ofrece flexibilidad en cuanto a dónde implementar y hospedar los servicios que se proporcionan en [contenedores de Docker](https://www.docker.com/what-container). 

La creación de contenedores es un método de distribución de software en el que una aplicación o servicio, incluidas sus dependencias y configuración, se empaquetan como una imagen de contenedor. La imagen puede implementarse en un host de contenedor con pocas o ningunas modificaciones. Cada contenedor está aislado de otros contenedores y del sistema operativo subyacente. El propio sistema solo tiene los componentes necesarios para ejecutar la imagen. Un host de contenedor tiene una superficie menor que una máquina virtual. También puede crear contenedores a partir de imágenes para las tareas a corto plazo, ya que se pueden quitar cuando dejen de necesitarse.

La compatibilidad con contenedores está disponible actualmente para un subconjunto de servicios de Azure Cognitive Services:

- Language Understanding
- Text Analytics (Sentiment 3.0)

> [!IMPORTANT]
> Un subconjunto de Azure Cognitive Services para Azure Stack Hub se encuentra actualmente en versión preliminar pública.
> Se ofrece la versión preliminar sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La compatibilidad con contenedores se encuentra actualmente en versión preliminar para un subconjunto de Azure Cognitive Services:

- Lectura: reconocimiento óptico de caracteres \[(OCR)
- Extracción de la frase clave
- Detección de idiomas
- Anomaly Detector
- Form Recognizer
- Voz a texto (personalizada, estándar)
- Texto a voz (personalizada, estándar)

## <a name="use-containers-with-cognitive-services-on-azure-stack-hub"></a>Uso de contenedores con Azure Cognitive Services en Azure Stack Hub

- **Control sobre datos**  
  Permita que los usuarios de la aplicación controlen sus datos mientras utilizan Cognitive Services. Puede proporcionar Cognitive Services a las usuarios de la aplicación que no puedan enviar datos a Azure global o a la nube pública.

- **Control sobre las actualizaciones del modelo**  
  Proporcione a los usuarios de la aplicación actualizaciones de las versiones a los modelos implementados en su solución.

- **Arquitectura portátil**  
  Habilite la creación de una arquitectura de aplicación portátil que le permita implementar su solución en la nube pública, en una nube privada en un entorno local o en el perímetro. Puede implementar su contenedor en Azure Kubernetes Service, Azure Container Instances o en un clúster de Kubernetes en Azure Stack Hub. Para más información, consulte [Implementación de Kubernetes en Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md).

- **Alto rendimiento y baja latencia**  
   Proporcione a los usuarios de la aplicación la capacidad de escalar cuando se produzcan picos de tráfico para lograr un alto rendimiento y una baja latencia. Permita que Cognitive Services se ejecute en Azure Kubernetes Service físicamente cerca de sus datos y lógica de aplicación.

Con Azure Stack Hub, implemente contenedores de Cognitive Services en un clúster de Kubernetes junto con los contenedores de su aplicación para lograr una alta disponibilidad y un escalado elástico. A la hora de desarrollar la aplicación, puede combinar Cognitive Services con componentes creados en App Services, Functions, Blob Storage, SQL o bases de datos mySQL.

Para más información sobre los contenedores de Cognitive Services, consulte [Compatibilidad con contenedores en Azure Cognitive Services](/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Implementación de Azure Face API

En este artículo se describe cómo implementar Azure Face API en un clúster de Kubernetes en Azure Stack Hub. Puede usar el mismo enfoque para implementar otros contenedores de Cognitive Services en clústeres de Kubernetes de Azure Stack Hub.

## <a name="prerequisites"></a>Prerrequisitos

Antes de comenzar, necesitará lo siguiente:

1.  Solicitar acceso al registro de contenedor para extraer imágenes del contenedor de Face del registro de contenedor de Azure Cognitive Services. Para más información, consulte [Solicitud de acceso al registro de contenedor privado](/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Preparar un clúster de Kubernetes en Azure Stack Hub. Puede seguir el artículo [Implementación de Kubernetes en Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Creación de recursos de Azure

Cree un recurso de Cognitive Services en Azure para obtener una vista previa de los contenedores de Face, LUIS o Reconocer texto. Deberá utilizar la clave de suscripción y la dirección URL del punto de conexión desde el recurso para crear una instancia de los contenedores de Cognitive Services.

1. Cree un recurso de Azure en Azure Portal. Si quiere obtener una vista previa del contenedor de Face, primero debe crear un recurso correspondiente de Face en Azure Portal. Para más información, consulte [Inicio rápido: Creación de una cuenta de Cognitive Services en Azure Portal](/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  El recurso de Face o Computer Vision debe usar el plan de tarifa F0.

2. Obtenga la dirección URL del punto de conexión y la clave de suscripción para el recurso de Azure. Una vez que cree el recurso de Azure, use la clave de suscripción y la dirección URL del punto de conexión de dicho recurso para crear una instancia del contenedor de Face, LUIS o Reconocer texto correspondiente para la vista previa.

## <a name="create-a-kubernetes-secret"></a>Creación de un secreto de Kubernetes 

Utilice el comando create secret de Kubectl para acceder al registro de contenedor privado. Reemplace `<username>` por el nombre de usuario y `<password>` por la contraseña proporcionada en las credenciales que recibió del equipo de Azure Cognitive Services.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Preparación de un archivo de configuración YAML

Use el archivo de configuración YAML para simplificar la implementación de la instancia de Cognitive Services en el clúster de Kubernetes.

El siguiente es un archivo de configuración YAML de ejemplo para implementar el servicio Face en Azure Stack Hub:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

En este archivo de configuración YAML, use el secreto que ha utilizado para obtener las imágenes de contenedor de Cognitive Services de Azure Container Registry. El archivo de secreto se usa para implementar una réplica específica del contenedor. También puede crear un equilibrador de carga para asegurarse de que los usuarios pueden acceder a este servicio externamente.

Detalles sobre los campos de claves:

| Campo | Notas |
| --- | --- |
| replicaNumber | Define las réplicas iniciales de instancias que se crearán. Puede escalarlo más adelante, después de la implementación. |
| ImageLocation | Indica la ubicación de la imagen de contenedor de Cognitive Services específica en ACR. Por ejemplo, el servicio de Face: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |La dirección URL del punto de conexión anotada en el paso [Creación de un recurso de Azure](#create-azure-resources) |
| ApiKey | La clave de suscripción anotada en el paso [Creación de un recurso de Azure](#create-azure-resources) |
| SecretName | Nombre del secreto que ha creado en el paso [Creación de un secreto de Kubernetes](#create-a-kubernetes-secret) |

## <a name="deploy-the-cognitive-service"></a>Implementación de la instancia de Cognitive Services

Use el comando siguiente para implementar los contenedores de Cognitive Services:

```bash  
    Kubectl apply -f <yamlFileName>
```
Use el comando siguiente para supervisar cómo se implementa: 
```bash  
    Kubectl get pod - watch
```

## <a name="configure-http-proxy-settings"></a>Configuración de los valores del proxy HTTP

Los nodos de trabajo necesitan un proxy y el protocolo SSL. Para configurar un proxy HTTP para realizar solicitudes de salida, use estos dos argumentos:

- **HTTP_PROXY**: el proxy que se va a utilizar, por ejemplo, `https://proxy:8888`
- **HTTP_PROXY_CREDS**: las credenciales necesarias para autenticarse en el proxy, por ejemplo, `username:password`.

### <a name="set-up-the-proxy"></a>Configuración del proxy

1. Agregue un archivo `http-proxy.conf` a ambas ubicaciones:
    - `/etc/system/systemd/docker.service.d/`
    - `/cat/etc/environment/`

2. Compruebe que puede iniciar sesión en el contenedor con las credenciales proporcionadas por el equipo de Cognitive Services y realizar una `docker pull` en el siguiente contenedor: 

    `docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest`

    Ejecutar:

    `docker run hello-world pull`

### <a name="ssl-interception-setup"></a>Configuración de la interceptación de SSL

1. Agregue el certificado de **intercepción de https** a `/usr/local/share/ca-certificates` y actualice el almacén con `update-ca-certificates`. 

## <a name="test-the-cognitive-service"></a>Prueba de la instancia de Cognitive Services

Acceda a la [especificación OpenAPI](https://swagger.io/docs/specification/about/) desde el identificador URI relativo **/swagger** de dicho contenedor. Esta especificación, conocida anteriormente como la especificación Swagger, describe las operaciones que admite un contenedor con instancias. Por ejemplo, el siguiente URI proporciona acceso a la especificación de OpenAPI del contenedor de Análisis de sentimiento del que se creó una instancia en el ejemplo anterior:

```HTTP  
http:<External IP>:5000/swagger
```

Puede obtener la dirección IP externa con el comando siguiente: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Prueba de los servicios con Python

Puede intentar validar las instancias de Cognitive Services en Azure Stack Hub mediante la ejecución de algunos scripts de Python sencillos. Hay ejemplos oficiales del inicio rápido de Python para [Computer Vision](/azure/cognitive-services/computer-vision/home), [Face](/azure/cognitive-services/face/overview), [Text Analytics](/azure/cognitive-services/text-analytics/overview) y [Language Understanding](/azure/cognitive-services/luis/luis-container-howto) (LUIS) que puede consultar.

Hay dos aspectos que se deben tener en cuenta cuando se usan aplicaciones de Python para validar los servicios que se ejecutan en contenedores: 
1. Las instancias de Cognitive Services de los contenedores no necesitan subclaves para la autenticación, pero requieren cualquier cadena como marcador de posición para el SDK. 
2. Reemplace base_URL por la dirección IP del punto de conexión de servicio real.

Este es un script de ejemplo de Python que usa el SDK de Python de servicios de Face para detectar y enmarcar caras en una imagen:

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack Hub
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Pasos siguientes

[Cómo instalar y ejecutar contenedores de Computer Vision API.](/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Cómo instalar y ejecutar contenedores de Face API.](/azure/cognitive-services/face/face-how-to-install-containers)

[Cómo instalar y ejecutar contenedores de Text Analytics API.](/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Cómo instalar y ejecutar contenedores de Language Understanding (LUIS)](/azure/cognitive-services/luis/luis-container-howto)
