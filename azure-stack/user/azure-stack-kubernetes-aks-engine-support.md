---
title: Directivas de compatibilidad para AKS-Engine en Azure Stack | Microsoft Docs
description: Este tema contiene las directivas de compatibilidad para AKS-Engine en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 6a9ce8551975ccc98bb6e47bfa07b703dfdd341c
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064952"
---
# <a name="support-policies-for-aks-engine-on-azure-stack"></a>Directivas de compatibilidad para AKS-Engine en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se proporciona información acerca de las directivas de compatibilidad y limitaciones de AKS-Engine en Azure Stack. También se ofrece información acerca del elemento de Marketplace de Kubernetes, de los componentes de código abierto de terceros y de la administración de seguridad o de revisiones. 

> [!IMPORTANT]
> AKS-Engine actualmente está en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="self-managed-kubernetes-clusters-on-azure-stack-with-aks-engine"></a>Clústeres de Kubernetes autoadministrados en Azure Stack con AKS-Engine

Los componentes de la nube de infraestructura como servicio (IaaS), como los de proceso o red, proporcionan a los usuarios acceso a opciones de personalización y a controles de bajo nivel. AKS-Engine permite al usuario establecer clústeres de Kubernetes que usan estos componentes de IaaS de forma transparente, los usuarios pueden acceder a todos los aspectos de sus implementaciones y modificarlos.

Al crear un clúster, el cliente define los nodos de trabajo y maestros de Kubernetes que crea AKS-Engine. Las cargas de trabajo de cliente se ejecutan en esos nodos. Los clientes poseen y pueden ver o modificar tanto los nodos maestros como los de trabajo. La modificación descuidada de los nodos puede causar la pérdida de datos y de cargas de trabajo, y que el clúster deje de ser funcional. Además, las operaciones de AKS-Engine, como las de actualización o escala, sobrescribirán todos los cambios inapropiados. Por ejemplo, si el clúster tiene pods estáticos, no se conservarán después de una operación de actualización de AKS-Engine.

Dado que los nodos del clúster de cliente ejecutan código privado y almacenan información confidencial, el Soporte técnico de Microsoft solo puede acceder a ellos con restricciones. El Soporte técnico de Microsoft no puede iniciar sesión en estos nodos, ejecutar comandos en ellos ni ver sus registros sin el consentimiento expreso o la asistencia del cliente.

## <a name="aks-engine-supported-areas"></a>Áreas con soporte de AKS-Engine

Microsoft proporciona soporte técnico para lo siguiente:

-  Problemas con los comandos de AKS-Engine: implementar, generar, actualizar y escalar. La herramienta debe ser coherente con su comportamiento en Azure.
-  Problemas con un clúster de Kubernetes implementado según la información que se proporciona en el artículo de [introducción a AKS-Engine](azure-stack-kubernetes-aks-engine-overview.md).
-  Problemas de conectividad con otros servicios de Azure Stack 
-  Problemas de conectividad de Kubernetes API
-  Problemas con la funcionalidad del proveedor de Kubernetes de Azure Stack y con la conectividad con Azure Resource Manager
-  Problemas con la configuración generada por AKS-Engine de artefactos nativos de Azure Stack, como equilibradores de carga, grupos de seguridad de red, redes virtuales, subredes, interfaces de red, tabla de rutas, conjuntos de disponibilidad, direcciones IP públicas, cuentas de almacenamiento y máquinas virtuales 
-  Problemas de latencia y rendimiento de la red
-  Problemas con la imagen base de AKS que usa AKS-Engine en las implementaciones desconectadas. 

## <a name="aks-engine-areas-not-supported"></a>Áreas de AKS-Engine no admitidas

Microsoft no proporciona soporte técnico para lo siguiente:

-  Uso de AKS-Engine en Azure.
-  Elemento de Marketplace de Kubernetes de Azure Stack.
-  Uso de las siguientes opciones y complementos de definición de clúster de AKS-Engine.
    -  Complementos no compatibles:  
            -  AAD Pod Identity  
            -  ACI Connector  
            -  Blobfuse Flex Volume  
            -  Cluster Autoscaler  
            -  Container Monitoring  
            -  KeyVault Flex Volume  
            -  NVIDIA Device Plugin  
            -  Rescheduler  
            -  SMB Flex Volume  
        
    -  Opciones de definición de clúster no compatibles:  
            -  Under KubernetesConfig:  
                    -  cloudControllerManagerConfig  
                    -  enableDataEncryptionAtRest  
                    -  enableEncryptionWithExternalKms  
                    -  enablePodSecurityPolicy  
                    -  etcdEncryptionKey  
                    -  useInstanceMetadata  
                    -  useManagedIdentity  
                    -  azureCNIURLLinux  
                    -  azureCNIURLWindows  
            -  Under masterProfile:  
                    -  availabilityZones  
            -  Under agentPoolProfiles:  
                    -  availabilityZones  
                    -  singlePlacementGroup  
                    -  scaleSetPriority  
                    -  scaleSetEvictionPolicy  
                    -  acceleratedNetworkingEnabled  
                    -  acceleratedNetworkingEnabledWindows

-  Los cambios de configuración de Kubernetes se mantienen fuera del almacén de configuración de Kubernetes etcd. Por ejemplo, los pods estáticos que se ejecutan en los nodos del clúster.
-  Preguntas acerca de cómo usar Kubernetes. Por ejemplo, el Soporte técnico de Microsoft no aconseja sobre la creación controladores de entrada personalizados, el uso de cargas de trabajo de aplicación o la aplicación de herramientas o paquetes de software de código abierto de terceros.
-  Proyectos de código abierto de terceros que no se proporcionan como parte del clúster de Kubernetes que implementa AKS-Engine. Estos proyectos pueden incluir Kubeadm, Kubespray, Native, Istio, Helm y Envoy, entre otros.
-  Uso de AKS-Engine en escenarios de casos de uso fuera de los especificados en [Escenarios admitidos con AKS-Engine](azure-stack-kubernetes-aks-engine-overview.md#supported-scenarios-with-the-aks-engine).
-  Software de terceros. Este software puede incluir herramientas de análisis de seguridad y dispositivos o software de red.
-  Problemas relacionados con la nube múltiple o con construcciones de varios proveedores. Por ejemplo, Microsoft no admite los problemas relacionados con la ejecución de una solución de proveedor en la nube federado para distintas audiencias.
-  Personalizaciones de red que no sean las que se enumeran en la sección [Áreas con soporte de AKS-Engine](#aks-engine-supported-areas).

##  <a name="security-issues-and-patching"></a>Problemas de seguridad y aplicación de revisiones

Si se encuentra un error de seguridad en uno o varios componentes de AKS-Engine o del proveedor de Kubernetes para Azure Stack, Microsoft ofrecerá una revisión para que los clientes la apliquen a los clústeres afectados para mitigar el problema. Como alternativa, el equipo proporciona a los usuarios instrucciones de actualización. Tenga en cuenta que las revisiones pueden requerir que el clúster se quede un tiempo sin actividad. Si se requieren reinicios, Microsoft se lo notificará a los clientes. Si los usuarios no aplican las revisiones según las instrucciones de Microsoft, el clúster seguirá siendo vulnerable al problema de seguridad.

## <a name="kubernetes-marketplace-item"></a>Elemento de Marketplace de Kubernetes

Los usuarios pueden descargar un elemento de Marketplace de Kubernetes, lo que les permite implementar clústeres de Kubernetes mediante AKS-Engine de forma indirecta mediante una plantilla en el portal de usuarios de Azure Stack, lo que resulta más sencillo que usar AKS-Engine directamente. Esta es una herramienta útil para configurar rápidamente clústeres para demostraciones, pruebas y desarrollo. No está previsto que se use en producción, por lo que no se incluye en el conjunto de elementos que admite Microsoft.

## <a name="preview-features"></a>Características en vista previa

Para las características y la funcionalidad que requieran pruebas ampliadas y comentarios de los usuarios, Microsoft publica nuevas características en versión preliminar o características con una marca de característica. Considere estas características como versión preliminar o características beta. Las características en versión preliminar o con marca de característica no son para producción. Los cambios continuos en las funcionalidades y el comportamiento, las soluciones de errores y otros cambios pueden dar lugar a inestabilidad en los clústeres y tiempo de inactividad. Estas características no son compatibles con Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [AKS-Engine en Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).