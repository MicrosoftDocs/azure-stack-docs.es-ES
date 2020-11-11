---
title: Requisitos de Azure Kubernetes Service en Azure Stack HCI
description: Antes de empezar con Azure Kubernetes Service en Azure Stack HCI
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 09/22/2020
ms.openlocfilehash: 62b1b2983b2e41b5c6d991b4d53338a67bc5aeab
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064674"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Requisitos del sistema de Azure Kubernetes Service en Azure Stack HCI

> Se aplica a: Azure Stack HCI

En este artículo se describen los requisitos para configurar Azure Kubernetes Service en Azure Stack HCI y usarlo para crear clústeres de Kubernetes. Para obtener información general sobre Azure Kubernetes Service en Azure Stack HCI, consulte [Introducción a AKS en Azure Stack HCI](overview.md).

## <a name="determine-hardware-requirements"></a>Determinar los requisitos de hardware

Microsoft recomienda adquirir una solución de hardware o software de Azure Stack HCI validada de nuestros asociados. Estas soluciones se diseñan, se ensamblan y se validan con nuestra arquitectura de referencia para garantizar la compatibilidad y la confiabilidad, de modo que pueda empezar a utilizarlas rápidamente. Compruebe que los sistemas, componentes, dispositivos y controladores que usa estén certificados para Windows Server 2019 según el catálogo de Windows Server. Visite el sitio web de [soluciones de Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) para obtener soluciones validadas.

### <a name="general-requirements"></a>Requisitos generales

Para que Azure Kubernetes Service en Azure Stack HCI funcione de forma óptima en un entorno de Active Directory, asegúrese de que se cumplan los siguientes requisitos: 

 - Asegúrese de que la sincronización de hora está configurada y de que la divergencia no supera los 2 minutos en todos los nodos del clúster y en el controlador de dominio. Para más información sobre cómo establecer la sincronización de hora, visite el [servicio de hora de Windows](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Asegúrese de que las cuentas de usuario que agregan, actualizan y administran clústeres de Azure Kubernetes Service en Azure Stack HCI tienen los permisos correctos en Active Directory. Si usa unidades organizativas para administrar directivas de grupo para servidores y servicios, las cuentas de usuario requerirán permisos de lista, lectura, modificación y eliminación en todos los objetos de la unidad organizativa. 

 - Se recomienda usar una unidad organizativa independiente para los servidores y servicios en los que se agregan los clústeres de Azure Kubernetes Service en Azure Stack HCI. Esto le permitirá controlar el acceso y los permisos con mayor precisión.

 - Si utiliza plantillas de GPO en contenedores de Active Directory, asegúrese de que la implementación de AKS-HCI esté exenta de esa directiva. La protección del servidor estará disponible en una versión preliminar posterior.

### <a name="compute-requirements"></a>Requisitos de proceso

 - Un clúster de Azure Stack HCI con un máximo de cuatro servidores en el clúster. Se recomienda que cada servidor del clúster tenga 24 núcleos de CPU y 512 GB de RAM como mínimo.

 - Aunque técnicamente puede ejecutar Azure Kubernetes Service en un servidor de un solo nodo de Azure Stack HCI, no se recomienda hacerlo.

 - Otros requisitos de proceso para Azure Kubernetes Service en Azure Stack HCI están en línea con los requisitos de Azure Stack HCI. Consulte [Requisitos del sistema de Azure Stack HCI](../hci/concepts/system-requirements.md#server-requirements) para más información sobre los requisitos del servidor de Azure Stack HCI.

 - Esta versión preliminar requiere la instalación del sistema operativo de Azure Stack HCI en cada servidor del clúster con las selecciones de idioma y región EN-US. Cambiarlos después de la instalación no es posible en este momento.

### <a name="network-requirements"></a>Requisitos de red 

Azure Kubernetes Service en Azure Stack HCI requiere una conexión de red confiable de baja latencia y gran ancho de banda entre cada nodo de servidor. Debe comprobar lo siguiente: 

 - Compruebe que tiene un conmutador virtual externo existente configurado si va a utilizar Windows Admin Center. En el caso de los clústeres de Azure Stack HCI, este conmutador debe ser el mismo en todos los nodos de clúster. 

 - Compruebe que ha deshabilitado IPv6 en todos los adaptadores de red. 

 - La red debe tener un servidor DHCP disponible para proporcionar direcciones TCP/IP a las máquinas virtuales y los hosts de máquina virtual. El servidor DHCP también debe contener información del host NTP y DNS. 

 - También se recomienda tener un servidor DHCP con un ámbito de direcciones IPv4 dedicado accesible para el clúster de Azure Stack HCI. Por ejemplo, puede reservar 10.0.1.1 para la puerta de enlace predeterminada, de 10.0.1.2 a 10.0.1.102 para Kubernetes Services y usar de 10.0.1.103 a 10.0.1.254 para las máquinas virtuales del clúster de Kubernetes. 

 - Las direcciones IPv4 que proporciona el servidor DHCP deben ser enrutables y tener una expiración de concesión de 7 días para evitar la pérdida de conectividad IP en caso de actualización de la máquina virtual o reaprovisionamiento.  

 - No se recomienda tener etiquetas de VLAN. Use puertos de acceso o sin etiquetar en los conmutadores de red del clúster de Azure Stack HCI. 

 - No se recomienda usar un grupo de direcciones IP virtuales estáticas dedicadas para el grupo de direcciones IP virtuales del equilibrador de carga durante la instalación. El grupo de direcciones IP de DHCP se usa para las máquinas virtuales, mientras que el grupo de direcciones IP virtuales se usa para el equilibrador de carga y debe ser enrutable. No es necesario que el grupo de direcciones IP de DHCP sea enrutable a la red externa de Internet.

 - La resolución de nombres DNS es necesaria para que todos los nodos puedan comunicarse entre sí. En el caso de la resolución de nombres externa de Kubernetes, se usan los servidores DNS que proporciona el servidor DHCP cuando se obtiene la dirección IP. Para la resolución de nombres interna de Kubernetes, se usa la solución predeterminada de Kubernetes basada en Core DNS. 

 - Para esta versión preliminar, no se admite el uso de un servidor proxy para conectarse a Windows Admin Center, a los nodos de clúster de Azure Stack HCI y a los nodos de clúster de Azure Kubernetes Service en Azure Stack HCI.

### <a name="network-port-and-url-requirements"></a>Requisitos de puerto de red y de dirección URL 

Al crear un clúster de Azure Kubernetes Service en Azure Stack HCI, se abrirán automáticamente los siguientes puertos de firewall en cada servidor del clúster. 


| Puerto de firewall               | Descripción         | 
| ---------------------------- | ------------ | 
| 45000           | Puerto de servidor wssdagent GPRC           |
| 45001             | Puerto de autenticación wssdagent GPRC  | 
| 55 000           | Puerto de servidor wssdcloudagent GPRC           |
| 55001             | Puerto de autenticación wssdcloudagent GPRC  | 


Las excepciones de URL de firewall son necesarias para la máquina de Windows Admin Center y todos los nodos del clúster de Azure Stack HCI. 

| URL        | Port | Servicio | Notas |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | Agente de descarga, WAC | Se usa para descargar los archivos binarios de Helm 
https://storage.googleapis.com/  | 443 | Cloud Init | Descarga de archivos binarios de Kubernetes 
https://azurecliprod.blob.core.windows.net/ | 443 | Cloud Init | Descarga de archivos binarios y contenedores 
https://aka.ms/installazurecliwindows | 443 | WAC | Descarga de la CLI de Azure 
*.api.cdp.microsoft.com, *.dl.delivery.mp.microsoft.com, *.emdl.ws.microsoft.com | 80, 443 | Descarga del agente | Descarga de metadatos 
*.dl.delivery.mp.microsoft.com, *.do.dsp.mp.microsoft.com. | 80, 443 | Descarga del agente | Descarga de imágenes de VHD 
ecpacr.azurecr.io | 443 | Kubernetes | Descarga de imágenes de contenedor 

### <a name="storage-requirements"></a>Requisitos de almacenamiento 

Las siguientes implementaciones de almacenamiento son compatibles con Azure Kubernetes Service en Azure Stack HCI: 

|  Nombre                         | Tipo de almacenamiento | Capacidad necesaria |
| ---------------------------- | ------------ | ----------------- |
| Clúster de Azure Stack HCI          | CSV          | 1 TB              |
| Azure Stack HCI de un solo nodo | Almacenamiento de conexión directa | 500 GB|

### <a name="review-maximum-supported-hardware-specifications"></a>Revisión de las especificaciones de hardware máximas admitidas 

No se admiten las implementaciones de Azure Kubernetes Service en Azure Stack HCI que superen las siguientes especificaciones: 

| Recurso                     | Máxima |
| ---------------------------- | --------|
| Servidores físicos por clúster | 4       |
| Clústeres de Kubernetes            | 4       |
| Total de máquinas virtuales (VM)          | 200     |

### <a name="windows-admin-center"></a>Windows Admin Center 

Windows Admin Center es la interfaz de usuario para crear y administrar Azure Kubernetes Service en Azure Stack HCI. Para usar Windows Admin Center con Azure Kubernetes Service en Azure Stack HCI, debe cumplir todos los criterios de la siguiente lista. 

#### <a name="on-your-windows-admin-center-system"></a>En el sistema de Windows Admin Center

La máquina que ejecuta la puerta de enlace de Windows Admin Center debe cumplir los siguientes requisitos: 

 - Windows 10 (no admitimos los servidores de Windows Admin Center en este momento).
 - 60 GB de espacio disponible
 - Estar registrado en Azure
 - Estar en el mismo dominio que el clúster de Azure Stack HCI.

## <a name="next-steps"></a>Pasos siguientes 

Después de cumplir todos los requisitos previos anteriores, puede configurar un host de Azure Kubernetes Service en Azure Stack HCI mediante:
 - [Windows Admin Center](setup.md)
 - [PowerShell](setup-powershell.md)
