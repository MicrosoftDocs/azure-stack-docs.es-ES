---
title: Implementación de Azure Cognitive Services en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo implementar Azure Cognitive Services en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 1f8991fb25bb0b8c3a8159bc82abcc9b52d871c8
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986020"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Implementación de Azure Cognitive Services en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!Note]  
> Azure Cognitive Services en Azure Stack está en versión preliminar.

Puede usar Azure Cognitive Services con compatibilidad con contenedores en Azure Stack. La compatibilidad con contenedores en Azure Cognitive Services le permite usar las mismas API enriquecidas que están disponibles en Azure. El uso de contenedores ofrece flexibilidad en cuanto a dónde implementar y hospedar los servicios que se proporcionan en [contenedores de Docker](https://www.docker.com/what-container). La compatibilidad con contenedores está disponible actualmente en versión preliminar para un subconjunto de Azure Cognitive Services, incluidas las partes de [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) y [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

La creación de contenedores es un enfoque de distribución de software en el que una aplicación o servicio, incluidas sus dependencias y la configuración, se empaqueta como una imagen de contenedor. La imagen puede implementarse en un host de contenedor con pocas o ningunas modificaciones. Cada contenedor está aislado de otros contenedores y del sistema operativo subyacente. El propio sistema solo tiene los componentes necesarios para ejecutar la imagen. Un host de contenedor tiene una superficie menor que una máquina virtual. Además, es posible crear contenedores a partir de las imágenes para las tareas a corto plazo y quitarlos cuando ya no se necesiten.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Uso de contenedores con Azure Cognitive Services en Azure Stack

- **Control sobre datos**  
  Permita que los usuarios de la aplicación controlen sus datos mientras utilizan Cognitive Services. Puede proporcionar Cognitive Services a usuarios de la aplicación que no pueden enviar datos a Azure global o la nube pública.

- **Control sobre las actualizaciones del modelo**  
  Proporcione a los usuarios de la aplicación control sobre la versión y actualización de los modelos implementados en su solución.

- **Arquitectura portátil**  
  Habilite la creación de una arquitectura de aplicación portátil que le permita implementar su solución en la nube pública, en una nube privada en un entorno local o en el perímetro. Puede implementar su contenedor en Azure Kubernetes Service, Azure Container Instances o en un clúster de Kubernetes en Azure Stack. Para obtener más información, consulte [Implementación de Kubernetes en Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

- **Alto rendimiento y baja latencia**  
   Proporcione a los usuarios de la aplicación la capacidad de escalar cuando se produzcan picos de tráfico para lograr un alto rendimiento y una baja latencia. Permita que Cognitive Services se ejecute en Azure Kubernetes Service físicamente cerca de sus datos y lógica de aplicación.

Con Azure Stack, implemente contenedores de Cognitive Services en un clúster de Kubernetes junto con los contenedores de su aplicación para lograr una alta disponibilidad y escalado elástico. Puede desarrollar su aplicación combinando Cognitive Services con componentes creados en App Services, Functions, Blob Storage o bases de datos SQL o mySQL. 

Para más información sobre los contenedores de Cognitive Services, consulte [Compatibilidad con contenedores en Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Implementación de Azure Face API

En este artículo se describe cómo implementar Azure Face API en un clúster de Kubernetes en Azure Stack. Puede usar el mismo enfoque para implementar otros contenedores de Cognitive Services en un clúster de Kubernetes de Azure Stack.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, deberá hacer lo siguiente:

1.  Solicitar acceso al registro de contenedor para extraer imágenes del contenedor de Face del registro de contenedor de Azure Cognitive Services. Para más información, consulte la sección [Solicitud de acceso al registro de contenedor privado](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Preparar un clúster de Kubernetes en Azure Stack. Puede seguir el artículo [Implementación de Kubernetes en Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Creación de recursos de Azure

Cree un recurso de Cognitive Services en Azure para obtener una vista previa de los contenedores de Face, LUIS o Reconocer texto respectivamente. Deberá utilizar la clave de suscripción y la dirección URL del punto de conexión desde el recurso para crear una instancia de los contenedores de Cognitive Services.

1. Cree un recurso de Azure en Azure Portal. Si quiere obtener una vista previa del contenedor de Face, primero debe crear un recurso correspondiente de Face en Azure Portal. Para más información, consulte [Inicio rápido: Creación de una cuenta de Cognitive Services en Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  El recurso de Face o Computer Vision debe usar el plan de tarifa F0.

2. Obtenga la dirección URL del punto de conexión y la clave de suscripción para el recurso de Azure. Una vez creado el recurso de Azure, debe usar la clave de suscripción y la dirección URL del punto de conexión de ese recurso para crear una instancia del contenedor de Face, LUIS o Reconocer texto correspondiente para la vista previa.

## <a name="create-a-kubernetes-secret"></a>Creación de un secreto de Kubernetes 

Utilice el comando Kubectl create secret para acceder al registro de contenedor privado. Reemplace **&lt;username&gt;** por el nombre de usuario y **&lt;password&gt;** por la contraseña proporcionada en las credenciales que recibió del equipo de Azure Cognitive Services.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Preparación de un archivo de configuración YAML

El archivo de configuración YAML se usará para simplificar la implementación de la instancia de Cognitive Services en el clúster de Kubernetes.

El siguiente es un archivo de configuración YAML de ejemplo para implementar el servicio Face en Azure Stack:

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

En este archivo de configuración YAML, use el secreto que ha utilizado para obtener las imágenes de contenedor de Cognitive Services de Azure Container Registry. Utilizará el archivo secreto para implementar una réplica específica del contenedor. Asimismo, creará un equilibrador de carga para asegurarse de que los usuarios pueden acceder a este servicio externamente.

Detalles sobre los campos de claves:

| Campo | Notas |
| --- | --- |
| replicaNumber | Define las réplicas iniciales de instancias que se crearán. Puede escalarlo más adelante después de la implementación. |
| ImageLocation | Indica la ubicación de la imagen de contenedor de Cognitive Services específica en ACR. Por ejemplo, el servicio de Face: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |La dirección URL del punto de conexión anotada en el paso [Creación de un recurso de Azure](#create-azure-resources). |
| ApiKey | La clave de suscripción anotada en el paso [Creación de un recurso de Azure](#create-azure-resources). |
| SecretName | El nombre del secreto que acaba de anotar en el paso Creación de un secreto para acceder al registro de contenedor privado |

## <a name="deploy-the-cognitive-service"></a>Implementación de la instancia de Cognitive Services

Use el comando siguiente para implementar los contenedores de Cognitive Services:

```bash  
    Kubectl apply -f <yamlFineName>
```
Use el comando siguiente para supervisar cómo se implementa: 
```bash  
    Kubectl get pod - watch
```

## <a name="test-the-cognitive-service"></a>Prueba de la instancia de Cognitive Services

Acceda a la [especificación OpenAPI](https://swagger.io/docs/specification/about/) (anteriormente, especificación de Swagger), que describe las operaciones admitidas por un contenedor con instancias, desde el URI relativo **/swagger** de ese contenedor. Por ejemplo, el siguiente URI proporciona acceso a la especificación de OpenAPI del contenedor de Análisis de sentimiento del que se creó una instancia en el ejemplo anterior:

```HTTP  
http:<External IP>:5000/swagger
```

Puede obtener la dirección IP externa con el comando siguiente: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Prueba de los servicios con Python

Puede intentar validar las instancias de Cognitive Services en Azure Stack mediante la ejecución de algunos scripts de Python sencillos. Hay ejemplos de inicio rápido de Python oficiales para [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) y [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS) como referencia.

Debe tener en cuenta dos aspectos al ejecutar la aplicación Python en los servicios en ejecución en contenedores: 
1. Las instancias de Cognitive Services en un contenedor no necesitan subclaves para la autenticación, pero sí es necesario introducir cualquier cadena como marcador de posición para el SDK. 
2. Reemplace base_URL por la dirección IP del punto de conexión del servicio real. 

Este es un script de ejemplo de Python que usa el SDK de Python de servicios de Face para detectar y enmarcar caras en una imagen. 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Pasos siguientes

[Cómo instalar y ejecutar contenedores de Computer Vision API.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Cómo instalar y ejecutar contenedores de Face API.](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers)

[Cómo instalar y ejecutar contenedores de Text Analytics API.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Cómo instalar y ejecutar contenedores de Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
