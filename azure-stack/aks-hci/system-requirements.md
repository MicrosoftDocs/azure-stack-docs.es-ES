---
title: Requisitos de Azure Kubernetes Service en Azure Stack HCI
description: Antes de empezar con Azure Kubernetes Service en Azure Stack HCI
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 12/02/2020
ms.openlocfilehash: 3a4ad6203ba14188ff24629f07775285417c306b
ms.sourcegitcommit: 0e2c814cf2c154ea530a4e51d71aaf0835fb2b5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918653"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Requisitos del sistema de Azure Kubernetes Service en Azure Stack HCI

> Se aplica a: Azure Stack HCI, Windows Server 2019 Datacenter

En este artículo se describen los requisitos para configurar Azure Kubernetes Service en Azure Stack HCI o Windows Server 2019 Datacenter, y usarlo para crear clústeres de Kubernetes. Para obtener información general sobre Azure Kubernetes Service en Azure Stack HCI, consulte [Introducción a AKS en Azure Stack HCI](overview.md).

## <a name="determine-hardware-requirements"></a>Determinar los requisitos de hardware

Microsoft recomienda adquirir una solución de hardware o software de Azure Stack HCI validada de nuestros asociados. Estas soluciones se diseñan, se ensamblan y se validan con nuestra arquitectura de referencia para garantizar la compatibilidad y la confiabilidad, de modo que pueda empezar a utilizarlas rápidamente. Compruebe que los sistemas, componentes, dispositivos y controladores que usa estén certificados para Windows Server 2019 según el catálogo de Windows Server. Visite el sitio web de [soluciones de Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) para obtener soluciones validadas.

## <a name="general-requirements"></a>Requisitos generales

Para que Azure Kubernetes Service en Azure Stack HCI o en Windows Server 2019 Datacenter funcione de forma óptima en un entorno de Active Directory, asegúrese de que se cumplan los siguientes requisitos: 

 - Asegúrese de que la sincronización de hora está configurada y de que la divergencia no supera los 2 minutos en todos los nodos del clúster y en el controlador de dominio. Para más información sobre cómo establecer la sincronización de hora, consulte el [servicio de hora de Windows](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Asegúrese de que las cuentas de usuario que agregan actualizaciones y administran clústeres de Azure Kubernetes Service en Azure Stack HCI o en Windows Server 2019 Datacenter tienen los permisos correctos en Active Directory. Si usa unidades organizativas para administrar directivas de grupo para servidores y servicios, las cuentas de usuario requerirán permisos de lista, lectura, modificación y eliminación en todos los objetos de la unidad organizativa. 

 - Se recomienda usar una unidad organizativa independiente para los servidores y los servicios en los que se agregan los clústeres de Azure Kubernetes Service en Azure Stack HCI o Windows Server 2019 Datacenter. El uso de una unidad organizativa independiente le permitirá controlar el acceso y los permisos con mayor precisión.

 - Si usa plantillas de GPO en contenedores de Active Directory, asegúrese de que la implementación de AKS-HCI esté exenta de la directiva. La protección del servidor estará disponible en una versión preliminar posterior.

## <a name="compute-requirements"></a>Requisitos de proceso

 - Un clúster de Azure Stack HCI o un clúster de conmutación por error de Windows Server 2019 Datacenter con cuatro servidores como máximo en el clúster. Se recomienda que cada servidor del clúster tenga 24 núcleos de CPU y 256 GB de RAM como mínimo.

 - Aunque técnicamente puede ejecutar Azure Kubernetes Service en un servidor de un solo nodo de Windows Server 2019 Datacenter, no se recomienda hacerlo. Sin embargo, puede ejecutar Azure Kubernetes Service en un servidor de un solo nodo de Windows Server 2019 Datacenter con fines de evaluación.

 - Otros requisitos de proceso para Azure Kubernetes Service en Azure Stack HCI están en línea con los requisitos de Azure Stack HCI. Consulte [Requisitos del sistema de Azure Stack HCI](../hci/concepts/system-requirements.md#server-requirements) para más información sobre los requisitos del servidor de Azure Stack HCI.

 - Esta versión preliminar requiere la instalación del sistema operativo de Azure Stack HCI en cada servidor del clúster con las selecciones de idioma y región EN-US. Cambiarlos después de la instalación no es posible en este momento.

## <a name="network-requirements"></a>Requisitos de red 

Los siguientes requisitos se aplican a un clúster de Azure Stack HCI, así como a un clúster de conmutación por error de Windows Server 2019 Datacenter: 

 - Compruebe que tiene un conmutador virtual externo existente configurado si va a utilizar Windows Admin Center. En el caso de los clústeres de Azure Stack HCI, este conmutador y su nombre deben ser los mismos en todos los nodos de clúster. 

 - Compruebe que ha deshabilitado IPv6 en todos los adaptadores de red. 

 - Para una implementación correcta, los nodos de clúster de Azure Stack HCI y las máquinas virtuales de clúster de Kubernetes deben tener conectividad externa a Internet. 

 - La resolución de nombres DNS es necesaria para que todos los nodos puedan comunicarse entre sí. En el caso de la resolución de nombres externa de Kubernetes, use los servidores DNS que proporciona el servidor DHCP cuando se obtiene la dirección IP. Para la resolución de nombres interna de Kubernetes, use la solución predeterminada de Kubernetes basada en Core DNS. 

 - Para esta versión preliminar, se proporciona solo compatibilidad con una sola VLAN para toda la implementación. 

 - En esta versión preliminar, tenemos compatibilidad limitada del proxy con los clústeres de Kubernetes creados con PowerShell. 
  
### <a name="network-port-and-url-requirements"></a>Requisitos de puerto de red y de dirección URL 

Al crear un clúster de Azure Kubernetes Service en Azure Stack HCI, se abrirán automáticamente los siguientes puertos de firewall en cada servidor del clúster. 


| Puerto de firewall               | Descripción         | 
| ---------------------------- | ------------ | 
| 45000           | Puerto de servidor wssdagent GPRC           |
| 45001             | Puerto de autenticación wssdagent GPRC  | 
| 55 000           | Puerto de servidor wssdcloudagent GPRC           |
| 65000             | Puerto de autenticación wssdcloudagent GPRC  | 



Las excepciones de URL de firewall son necesarias para la máquina de Windows Admin Center y todos los nodos del clúster de Azure Stack HCI. 

| URL        | Port | Servicio | Notas |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | Agente de descarga, WAC | Se usa para descargar los archivos binarios de Helm 
https://storage.googleapis.com/  | 443 | Cloud Init | Descarga de archivos binarios de Kubernetes 
https://azurecliprod.blob.core.windows.net/ | 443 | Cloud Init | Descarga de archivos binarios y contenedores 
https://aka.ms/installazurecliwindows | 443 | WAC | Descarga de la CLI de Azure 
https://:443 | 443 | TCP | Se usa para admitir los agentes de Azure Arc. 
*.api.cdp.microsoft.com, *.dl.delivery.mp.microsoft.com, *.emdl.ws.microsoft.com | 80, 443 | Descarga del agente | Descarga de metadatos 
*.dl.delivery.mp.microsoft.com, *.do.dsp.mp.microsoft.com. | 80, 443 | Descarga del agente | Descarga de imágenes de VHD 
ecpacr.azurecr.io | 443 | Kubernetes | Descarga de imágenes de contenedor 
git://:9418 | 9418 | TCP | Se usa para admitir los agentes de Azure Arc. 

## <a name="storage-requirements"></a>Requisitos de almacenamiento 

Las siguientes implementaciones de almacenamiento son compatibles con Azure Kubernetes Service en Azure Stack HCI: 

|  Nombre                         | Tipo de almacenamiento | Capacidad necesaria |
| ---------------------------- | ------------ | ----------------- |
| Clúster de Azure Stack HCI          | CSV          | 1 TB              |
| Clúster de conmutación por error de Windows Server 2019 Datacenter          | CSV          | 1 TB              |
| Windows Server 2019 Datacenter de un solo nodo | Almacenamiento de conexión directa | 500 GB|

## <a name="review-maximum-supported-hardware-specifications"></a>Revisión de las especificaciones de hardware máximas admitidas 

No se admiten las implementaciones de Azure Kubernetes Service en Azure Stack HCI que superen las siguientes especificaciones: 

| Recurso                     | Máxima |
| ---------------------------- | --------|
| Servidores físicos por clúster | 4       |
| Clústeres de Kubernetes            | 4       |
| Total de máquinas virtuales (VM)          | 200     |

## <a name="windows-admin-center-requirements"></a>Requisitos de Windows Admin Center

Windows Admin Center es la interfaz de usuario para crear y administrar Azure Kubernetes Service en Azure Stack HCI. Para usar Windows Admin Center con Azure Kubernetes Service en Azure Stack HCI, debe cumplir todos los criterios de la siguiente lista. 

Estos son los requisitos de la máquina que ejecuta la puerta de enlace de Windows Admin Center: 

 - Máquina con Windows 10 o Windows Server (no se admite la ejecución de Windows Admin Center en el clúster de Azure Stack HCI ni en el de Windows Server 2019 Datacenter en este momento)
 - 60 GB de espacio disponible
 - Estar registrado en Azure
 - En el mismo dominio que el clúster de Azure Stack HCI o el de Windows Server 2019 Datacenter

## <a name="next-steps"></a>Pasos siguientes 

Después de cumplir todos los requisitos previos anteriores, puede configurar un host de Azure Kubernetes Service en Azure Stack HCI mediante:
 - [Windows Admin Center](setup.md)
 - [PowerShell](setup-powershell.md)
