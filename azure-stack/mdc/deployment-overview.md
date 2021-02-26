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
ms.date: 02/17/2021
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 02/17/2021
ms.openlocfilehash: ad0a80f28b26e3c7da71860670feefd7efc15a0f
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647800"
---
# <a name="mdc-requirements-overview"></a>Información general sobre los requisitos de MDC

En esta guía de implementación se describen los requisitos necesarios para instalar y configurar un Centro de datos modular (MDC). 

Los objetivos de esta guía incluyen:

- Proporcionar una lista de comprobación previa a la implementación para comprobar que se cumplen todos los requisitos previos antes de la instalación de los componentes.
- Presentar los componentes clave de un Centro de datos modular.
- Validar la implementación del cliente.

Se requiere experiencia técnica con la virtualización, los servidores, los sistemas operativos, las redes y las soluciones de almacenamiento para comprender totalmente el contenido de esta guía. 

Esta guía se centra en la implementación de los componentes principales de Microsoft Azure Stack Hub y en los detalles de la solución de MDC. En esta guía no se explican los procedimientos operativos de Azure Stack Hub ni se explican todas las características disponibles en Azure Stack Hub. 

## <a name="introduction"></a>Introducción

MDC es una oferta integrada para Azure Stack Hub empaquetada en un contenedor estándar de envío de metal de 12 metros. El contenedor incluye una unidad de control climático, y un sistema de iluminación y alerta. Los componentes principales de Azure Stack Hub se instalan como tres pods independientes: Pod 1, bastidor 1 y bastidor 2, pod 2, bastidor 1 y bastidor 2 y pod 3, bastidor 1 y bastidor 2.

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

De forma general, el proceso de implementación de MDC consta de las fases siguientes:

### <a name="planning-phase"></a>Fase de planeación
1. Planeamiento de la alimentación del centro de datos.
1. Planeamiento de la configuración de red lógica de Azure Stack Hub.
1. Planeamiento de la [integración de la red del centro de datos](../operator/azure-stack-network.md).
1. Planeamiento de la integración de [identidades](../operator/azure-stack-identity-overview.md).
1. Planeamiento de la integración de [seguridad](../operator/azure-stack-security-foundations.md).
1. Planeamiento de los [certificados PKI](../operator/azure-stack-pki-certs.md).

### <a name="preparation-phase"></a>Fase de preparación
1. Recopilación del inventario.
1. Conexión de la alimentación y encendido de la solución.
1. Validación del estado del sistema de acondicionamiento de aire.
1. Validación del estado del sistema de supervisión y alerta de incendios.
1. Validación del estado del hardware físico.

### <a name="execution-phase--separately-for-each-of-the-three-pods"></a>Fase de ejecución (independiente para cada uno de los tres pods)
1. Configuración del host de ciclo de vida de hardware.
1. Configuración de conmutadores de red.
1. Integración de la red del centro de datos.
1. Configuración del hardware físico.
1. Configuración del almacenamiento de Isilon.
1. Implementación de la infraestructura del tejido de Azure Stack Hub.
1. Integración de identidades de centros de datos.
1. Instalación de complementos para la funcionalidad extendida.

### <a name="validation-phase--separately-for-each-of-the-three-pods"></a>Fase de validación (independiente para cada uno de los tres pods)
1. Validación del estado posterior a la implementación.
1. Registro de Azure Stack Hub en Microsoft.
1. Transición del cliente de Azure Stack Hub.