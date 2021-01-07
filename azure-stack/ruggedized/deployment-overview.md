---
title: Implementación de Azure Stack Hub Ruggedized y configuración del servidor de administración del host de ciclo de vida de hardware (HLH) de Azure Stack Hub | Microsoft Docs
description: Aprenda los pasos para realizar una implementación correcta in situ de un dispositivo Azure Stack Hub Ruggedized, desde el planeamiento a la etapa posterior a la implementación.
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 0fd077f405256ff0a112dd9730cdc3e8fefb10f8
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867543"
---
# <a name="azure-stack-hub-ruggedized-deployment-overview"></a>Información general sobre la implementación de Azure Stack Hub Ruggedized

En esta guía de implementación se describen los pasos para instalar y configurar un dispositivo Azure Stack Hub Ruggedized. 

Los objetivos de esta guía incluyen:

- Proporcionar una lista de comprobación previa a la implementación para comprobar que se cumplen todos los requisitos previos antes de la instalación de los componentes.
- Especifique los componentes clave de Azure Stack Hub Ruggedized.
- Describir el procedimiento de instalación y configuración de los componentes clave.
- Validar la implementación del cliente.

Esta guía de implementación está pensada para el equipo profesional de campo de Microsoft responsable de la implementación de Azure Stack Hub Ruggedized en la ubicación del cliente.

Se requiere experiencia técnica con la virtualización, los servidores, los sistemas operativos, las redes y las soluciones de almacenamiento para comprender totalmente el contenido de esta guía. El ingeniero de implementación debe tener conocimientos de Microsoft Windows Server 2019 con Hyper-V, Azure Stack Hub, Azure y Microsoft PowerShell.

Esta guía se centra en la implementación de los componentes principales de Microsoft Azure Stack Hub y los componentes específicos de la solución Azure Stack Hub Ruggedized. En esta guía no se explican los procedimientos operativos de Azure Stack Hub ni se explican todas las características disponibles en Azure Stack Hub. Para más información, consulte la [guía del operador de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/).

## <a name="introduction"></a>Introducción

Azure Stack Hub Ruggedized es una oferta sólida con campos implementables para Microsoft Azure Stack Hub. Los componentes principales, como servidores y conmutadores, están contenidos en cajas de transporte denominadas pods.

Un pod es un contenedor de bastidor de 4U con dimensiones más pequeñas que un bastidor de 4U normal. Hay un pod de administración y dos pods de unidades de escalado (SU). El pod de administración incluye el host de ciclo de vida de hardware, dos conmutadores para la parte superior del rack (ToR) de 25 GbE y un conmutador de controlador de administración de placa base (BMC).

Cada pod de unidad de escalado tiene dos servidores R640 SU de Azure Stack Hub Ruggedized. Un servidor R640 ocupa un espacio en bastidor de 2U en el pod. Durante la implementación, los servidores de los pods de SU se conectan a los conmutadores BMC y ToR en el pod de administración.

## <a name="terminology"></a>Terminología

En la tabla siguiente se enumeran algunos de los términos que se usan en esta guía.

|Término   | Definición |
|-------|------------|
|Host de ciclo de vida de hardware (HLH)| HLH es el servidor físico que se usa para el arranque de la implementación inicial, así como para la administración de hardware, la compatibilidad y la copia de seguridad continuas de la infraestructura de Azure Stack Hub. HLH ejecuta Windows Server 2019 con la experiencia de escritorio y el rol de Hyper-V. El servidor se usa para hospedar herramientas de administración de hardware, herramientas de administración de conmutadores, kit de herramientas para asociados de Azure Stack Hub y la máquina virtual de implementación. |
|Máquina virtual de implementación (DVM)|  DVM es una máquina virtual que se crea en el HLH para la duración de la implementación del software de Azure Stack Hub. La máquina DVM ejecuta el motor de orquestación de software de Azure Stack Hub, llamado Enterprise Cloud Engine (ECE) para instalar y configurar el software de infraestructura del tejido de Azure Stack Hub en todos los servidores de unidades de escalado de Azure Stack Hub a través de la red.|
|Kit de herramientas para asociados de Azure Stack Hub|   Colección de herramientas de software que se usan para capturar parámetros de entrada específicos del cliente, y para iniciar la instalación y la configuración de Azure Stack Hub. Incluye la hoja de cálculo de implementación, que es una herramienta de interfaz gráfica de usuario (GUI) que se usa para capturar y almacenar parámetros configurables para la instalación de Azure Stack Hub. También incluye la herramienta del generador de configuración de red, que usa las entradas de la hoja de cálculo de implementación para generar archivos de configuración de red para todos los dispositivos de red físicos de la solución.|
|Paquete de extensión de OEM  |Paquete de firmware, controladores de dispositivos y herramientas de administración de hardware en un formato especializado, que Azure Stack Hub utiliza durante la implementación inicial y la actualización.|
|Controlador integrado de acceso remoto de Dell (iDRAC)|  Un iDRAC con un controlador de ciclo de vida es un controlador de administración de placa base insertado en cada servidor R640 de Azure Stack Hub Ruggedized. iDRAC proporciona funcionalidad de administración fuera de banda para ayudar a implementar, actualizar, supervisar y mantener servidores de Azure Stack Hub.|
|Unidad de escalado |Un componente básico de Azure Stack Hub que proporciona recursos de proceso y almacenamiento para la infraestructura y las cargas de trabajo del tejido de Azure Stack Hub. Consta de cuatro servidores R640 de Azure Stack Hub Ruggedized (también denominados nodos) y se pueden escalar dinámicamente hasta 16 nodos.|
|Pod    |En el contexto de Azure Stack Hub Ruggedized, un pod es un contenedor físico resistente diseñado para que lo lleven dos personas y que contiene soportes de montaje del bastidor y elementos amortiguadores de golpes para proteger el hardware de Azure Stack Hub Ruggedized de los riesgos del entorno físico. Incluye las cubiertas delantera y posterior de la caja de transporte que se pueden instalar y sellar para transportar el hardware. Una solución completa en la configuración mínima incluye tres pods.|


## <a name="deployment-overflow"></a>Desbordamiento de implementación

De forma general, el proceso de implementación de Azure Stack Hub Ruggedized consta de los pasos siguientes.

1. Fase de planeación:
   1. Planeamiento de la alimentación y refrigeración del centro de datos.
   1. Planeamiento de la configuración de red lógica de Azure Stack Hub.
   1. Planeamiento de la integración de la red del centro de datos.
   1. Planeamiento de la integración de identidades y seguridad.
   1. Planeamiento de los certificados PKI.
1. Fase de preparación:
   1. Desempaquetado y recopilación del inventario.
   1. Conexión de la alimentación y encendido de la solución.
   1. Validación del estado del hardware físico.
1. Fase de ejecución:
   1. Configuración del host de ciclo de vida de hardware.
   1. Configuración de conmutadores de red.
   1. Integración de la red del centro de datos.
   1. Configuración del hardware físico.
   1. Implementación de la infraestructura del tejido de Azure Stack Hub.
   1. Integración de identidades de centros de datos.
   1. Instalación de complementos para la funcionalidad extendida.
1. Fase de validación:
   1. Validación del estado posterior a la implementación.
   1. Registro de Azure Stack Hub en Microsoft.
   1. Transición del operador de Azure Stack Hub.
   
Cada uno de los temas anteriores se explica con más detalle en esta guía.
