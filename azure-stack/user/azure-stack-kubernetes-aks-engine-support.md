---
title: Directivas de compatibilidad para el motor de AKS en Azure Stack Hub
description: Este tema contiene las directivas de compatibilidad para el motor de AKS en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 09/10/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/0102020
ms.openlocfilehash: 389dd7110bfc5b8b42d1594c947ad522f4fbab98
ms.sourcegitcommit: f19aaf989d758f0712e1ef3cbc4abf90a1110554
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2020
ms.locfileid: "90026224"
---
# <a name="support-policies-for-aks-engine-on-azure-stack-hub"></a>Directivas de compatibilidad para el motor de AKS en Azure Stack Hub

En este artículo se proporciona información acerca de las directivas de compatibilidad y limitaciones del motor de AKS en Azure Stack Hub. También se ofrece información acerca del elemento de Marketplace de Kubernetes, de los componentes de código abierto de terceros y de la administración de seguridad o de revisiones. 

## <a name="self-managed-kubernetes-clusters-on-azure-stack-hub-with-aks-engine"></a>Clústeres de Kubernetes autoadministrados en Azure Stack Hub con el motor de AKS

Los componentes de la nube de infraestructura como servicio (IaaS), como los de proceso o red, proporcionan a los usuarios acceso a opciones de personalización y a controles de bajo nivel. El motor de AKS permite al usuario establecer clústeres de Kubernetes que usan estos componentes de IaaS de forma transparente, así los usuarios pueden acceder a todos los aspectos de sus implementaciones y modificarlos.

Al crear un clúster, el cliente define los nodos de trabajo y maestros de Kubernetes que crea el motor de AKS. Las cargas de trabajo de cliente se ejecutan en esos nodos. Los clientes poseen y pueden ver o modificar tanto los nodos maestros como los de trabajo. La modificación descuidada de los nodos puede causar la pérdida de datos y de cargas de trabajo, y que el clúster deje de ser funcional. Además, las operaciones del motor de AKS, como las de actualización o escala, sobrescribirán todos los cambios inapropiados. Por ejemplo, si el clúster tiene pods estáticos, no se conservarán después de una operación de actualización del motor de AKS.

Dado que los nodos del clúster de cliente ejecutan código privado y almacenan información confidencial, el Soporte técnico de Microsoft solo puede acceder a ellos con restricciones. El Soporte técnico de Microsoft no puede iniciar sesión en estos nodos, ejecutar comandos en ellos ni ver sus registros sin el consentimiento expreso o la asistencia del cliente.

## <a name="version-support"></a>Compatibilidad con versiones

La compatibilidad con la versión del motor de AKS sigue el mismo patrón establecido por el resto de la directiva de compatibilidad de Azure Stack Hub, es decir, la compatibilidad de una versión del motor de AKS en Azure Stack Hub se basa en la fórmula n-2. Por ejemplo, si la última versión del motor de AKS es v0.55.0, el conjunto de versiones admitidas es: 0.48.0, 0.51.0, 0.55.0. También es importante que siga la versión de actualización de Azure Stack Hub y la asignación correspondiente a la versión compatible con el motor de AKS que se mantiene en las [notas de la versión del motor de AKS](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping).

## <a name="aks-engine-supported-areas"></a>Áreas con soporte del motor de AKS

Microsoft proporciona soporte técnico para lo siguiente:

-  Problemas con los comandos del motor de AKS: implementar, generar, actualizar y escalar. La herramienta debe ser coherente con su comportamiento en Azure.
-  Problemas con un clúster de Kubernetes implementado según la información que se proporciona en el artículo de [introducción al motor de AKS](azure-stack-kubernetes-aks-engine-overview.md).
-  Problemas de conectividad con otros servicios de Azure Stack Hub. 
-  Problemas de conectividad de Kubernetes API.
-  Problemas con la funcionalidad del proveedor de Kubernetes de Azure Stack Hub y con la conectividad con Azure Resource Manager.
-  Problemas con la configuración generada por el motor de AKS de artefactos nativos de Azure Stack Hub, como equilibradores de carga, grupos de seguridad de red, redes virtuales, subredes, interfaces de red, tabla de rutas, conjuntos de disponibilidad, direcciones IP públicas, cuentas de almacenamiento y máquinas virtuales. 
-  Problemas de latencia y rendimiento de la red.
-  Problemas con la imagen base de AKS que usa el motor de AKS en las implementaciones desconectadas. 

## <a name="aks-engine-areas-not-supported"></a>Áreas del motor de AKS no admitidas

Microsoft no proporciona soporte técnico para lo siguiente:

-  Uso del motor de AKS en Azure.
-  Elemento de Marketplace de Kubernetes de Azure Stack Hub.
-  Uso de las siguientes opciones y complementos de definición de clúster del motor de AKS.
    -  Complementos no compatibles:  
            -  Azure AD Pod Identity  
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
-  Proyectos de código abierto de terceros que no se proporcionan como parte del clúster de Kubernetes que implementa el motor de AKS. Estos proyectos pueden incluir Kubeadm, Kubespray, Native, Istio, Helm y Envoy, entre otros.
-  Uso del motor de AKS en escenarios de casos de uso fuera de los especificados en [Escenarios admitidos con el motor de AKS](azure-stack-kubernetes-aks-engine-overview.md#supported-scenarios-with-the-aks-engine).
-  Software de terceros. Este software puede incluir herramientas de análisis de seguridad y dispositivos o software de red.
-  Problemas relacionados con la nube múltiple o con construcciones de varios proveedores. Por ejemplo, Microsoft no admite los problemas relacionados con la ejecución de una solución de proveedor en la nube federado para distintas audiencias.
-  Personalizaciones de red que no sean las que se enumeran en la sección [Áreas con soporte del motor de AKS](#aks-engine-supported-areas).
-  En los entornos de producción solo se deben usar clústeres de Kubernetes de alta disponibilidad, es decir, clústeres implementados con tres maestros y tres nodos agente como mínimo. Menos no se admite en las implementaciones de producción.

##  <a name="security-issues-and-patching"></a>Problemas de seguridad y aplicación de revisiones

Si se encuentra un error de seguridad en uno o varios componentes del motor de AKS o del proveedor de Kubernetes para Azure Stack Hub, Microsoft ofrecerá una revisión para que los clientes la apliquen a los clústeres afectados para mitigar el problema. Como alternativa, el equipo proporciona a los usuarios instrucciones de actualización. Tenga en cuenta que las revisiones pueden requerir que el clúster se quede un tiempo sin actividad. Si se requieren reinicios, Microsoft se lo notificará a los clientes. Si los usuarios no aplican las revisiones según las instrucciones de Microsoft, el clúster seguirá siendo vulnerable al problema de seguridad.

## <a name="kubernetes-marketplace-item"></a>Elemento de Marketplace de Kubernetes

Los usuarios pueden descargar un elemento de Marketplace de Kubernetes, que les permite implementar clústeres de Kubernetes mediante el motor de AKS de forma indirecta por medio de una plantilla en el portal de usuarios de Azure Stack Hub. Este enfoque resulta más sencillo que usar el motor de AKS directamente. El elemento de Marketplace de Kubernetes es una herramienta útil para configurar rápidamente clústeres para demostraciones, pruebas y desarrollo. No está previsto que se use en producción, por lo que no se incluye en el conjunto de elementos que admite Microsoft.

## <a name="preview-features"></a>Características en vista previa

Para las características y la funcionalidad que requieran pruebas ampliadas y comentarios de los usuarios, Microsoft publica nuevas características en versión preliminar o características con una marca de característica. Considere estas características como versión preliminar o características beta. Las características en versión preliminar o con marca de característica no son para producción. Los cambios continuos en las funcionalidades y el comportamiento, las soluciones de errores y otros cambios pueden dar lugar a inestabilidad en los clústeres y tiempo de inactividad. Estas características no son compatibles con Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).