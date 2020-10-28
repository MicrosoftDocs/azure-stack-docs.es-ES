---
title: Información general sobre la implementación del Centro de datos modular (MDC) y configuración para el servidor de administración del host de ciclo de vida de hardware (HLH) de Azure Stack Hub | Microsoft Docs
description: Obtenga más información acerca de lo que cabe esperar de una implementación correcta in situ de un Centro de datos modular (MDC), desde el planeamiento hasta la etapa posterior a la implementación.
services: azure-stack
documentationcenter: ''
author: asganesh
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 60361a3e44b5ad54c63e6a94223d75a7b5106b9e
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253934"
---
# <a name="mdc-deployment-overview"></a>Introducción a la implementación del Centro de datos modular

En esta guía de implementación se describen los pasos para instalar y configurar un Centro de datos modular (MDC). En esta guía también se describe el proceso automatizado para configurar el servidor de administración del host de ciclo de vida de hardware (HLH) de Azure Stack Hub para la implementación de Azure Stack Hub.

Los objetivos de esta guía incluyen:

- Proporcionar una lista de comprobación previa a la implementación para comprobar que se cumplen todos los requisitos previos antes de la instalación de los componentes.
- Presentar los componentes clave de un Centro de datos modular.
- Describir el procedimiento de instalación y configuración de los componentes clave.
- Validar la implementación del cliente.

Se requiere experiencia técnica con la virtualización, los servidores, los sistemas operativos, las redes y las soluciones de almacenamiento para comprender totalmente el contenido de esta guía. El ingeniero de implementación debe tener conocimientos de Microsoft Windows Server 2019 con Hyper-V, Azure Stack Hub, Azure y Microsoft PowerShell.

Esta guía se centra en la implementación de los componentes principales de Microsoft Azure Stack Hub y en los detalles de la solución de MDC. En esta guía no se explican los procedimientos operativos de Azure Stack Hub ni se explican todas las características disponibles en Azure Stack Hub. Para más información, consulte la [guía del operador de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/).

## <a name="introduction"></a>Introducción

MDC es una oferta integrada para Azure Stack Hub empaquetada en un contenedor estándar de envío de metal de 12 metros. El contenedor incluye una unidad de control climático, y un sistema de iluminación y alerta. Los componentes principales de Azure Stack Hub, como servidores y conmutadores, se instalan en seis bastidores físicos que se organizan de forma lógica en tres pods independientes.

Cada pod consta de dos bastidores 42U. Un pod incluye los conmutadores para la parte superior del rack (ToR), los conmutadores perimetrales y un conmutador de controlador de administración de placa base (BMC). Además, cada pod incluye un host de ciclo de vida de hardware (HLH) y un concentrador de puerto serie. La capacidad básica de proceso y almacenamiento se proporciona a través de unidades de escalado (SU) de Azure Stack Hub que constan de ocho servidores REA (Rugged Edge Appliance) R840. 48 nodos de almacenamiento de Isilon proporcionan capacidad de almacenamiento adicional. La configuración física de todos los pods es idéntica.

## <a name="terminology"></a>Terminología

En la tabla siguiente se enumeran algunos de los términos que se usan en esta guía.

|Término    |Definición |
|-------|-----------|
|Host de ciclo de vida de hardware (HLH)|    HLH es el servidor físico que se usa para el arranque de la implementación inicial, así como para la administración de hardware, la compatibilidad y la copia de seguridad continuas de la infraestructura de Azure Stack Hub. HLH ejecuta Windows Server 2019 con la experiencia de escritorio y el rol de Hyper-V. El servidor se usa para hospedar herramientas de administración de hardware, herramientas de administración de conmutadores, kit de herramientas para asociados de Azure Stack Hub y la máquina virtual de implementación. |
|Máquina virtual de implementación (DVM)|    DVM es una máquina virtual que se crea en el HLH para la duración de la implementación del software de Azure Stack Hub. La máquina DVM ejecuta el motor de orquestación de software de Azure Stack Hub, llamado Enterprise Cloud Engine (ECE) para instalar y configurar el software de infraestructura del tejido de Azure Stack Hub en todos los servidores de unidades de escalado de Azure Stack Hub a través de la red.|
|Kit de herramientas para asociados de Azure Stack Hub|    Colección de herramientas de software que se usan para capturar parámetros de entrada específicos del cliente, y para iniciar la instalación y la configuración de Azure Stack Hub. Incluye la hoja de cálculo de implementación, que es una herramienta de interfaz gráfica de usuario (GUI) que se usa para capturar y almacenar parámetros configurables para la instalación de Azure Stack Hub. También incluye la herramienta del generador de configuración de red, que usa las entradas de la hoja de cálculo de implementación para generar archivos de configuración de red para todos los dispositivos de red físicos de la solución.|
|Paquete de extensión de OEM    |Paquete de firmware, controladores de dispositivos y herramientas de administración de hardware en un formato especializado, que Azure Stack Hub utiliza durante la implementación inicial y la actualización.|
|Concentrador de puerto serie    |Dispositivo físico instalado en cada pod que proporciona acceso de red a los puertos serie de los conmutadores de red con fines de implementación y administración.|
|Unidad de escalado    |Un componente básico de Azure Stack Hub que proporciona recursos de proceso y almacenamiento para la infraestructura y las cargas de trabajo del tejido de Azure Stack Hub. Cada pod incluye ocho servidores MDC R840, también denominados nodos.|
|Almacenamiento de Isilon |    Componente de Azure Stack Hub específico de la solución de MDC. Isilon proporciona almacenamiento de archivos y blobs adicional para las cargas de trabajo de Azure Stack Hub. Cada pod incluye 48 nodos de almacenamiento de Isilon.|
|Pod    |En el contexto de MDC, un pod es una unidad lógica independiente que consta de dos bastidores físicos interconectados. Una solución completa incluye tres pods instalados en un solo contenedor.|





## <a name="deployment-workflow"></a>Flujo de trabajo de implementación

De forma general, el proceso de implementación de MDC consta de los pasos siguientes:

1. Fase de planeación:
   1. Planeamiento de la alimentación del centro de datos.
   1. Planeamiento de la configuración de red lógica de Azure Stack Hub.
   1. Planeamiento de la integración de la red del centro de datos.
   1. Planeamiento de la integración de identidades y seguridad.
   1. Planeamiento de los certificados PKI.
1. Fase de preparación:
   1. Recopilación del inventario.
   1. Conexión de la alimentación y encendido de la solución.
   1. Validación del estado del sistema de acondicionamiento de aire.
   1. Validación del estado del sistema de supervisión y alerta de incendios.
   1. Validación del estado del hardware físico.
1. Fase de ejecución (independiente para cada uno de los tres pods):
   1. Configuración del host de ciclo de vida de hardware.
   1. Configuración de conmutadores de red.
   1. Integración de la red del centro de datos.
   1. Configuración del hardware físico.
   1. Configuración del almacenamiento de Isilon.
   1. Implementación de la infraestructura del tejido de Azure Stack Hub.
   1. Integración de identidades de centros de datos.
   1. Instalación de complementos para la funcionalidad extendida.
1. Fase de validación (independiente para cada uno de los tres pods):
   1. Validación del estado posterior a la implementación.
   1. Registro de Azure Stack Hub en Microsoft.
   1. Transición del operador de Azure Stack Hub.
  
Cada uno de los temas anteriores se explica con más detalle en esta guía.
