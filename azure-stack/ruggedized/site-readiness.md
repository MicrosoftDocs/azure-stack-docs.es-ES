---
title: Preparación del sitio para Azure Stack Hub Ruggedized | Microsoft Docs
description: Conozca las especificaciones para la preparación del sitio para una instancia de Azure Stack Hub Ruggedized.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 5a9eb7c73a30268eb4e53855e9ce277517019e4a
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874309"
---
# <a name="azure-stack-hub-ruggedized-site-readiness"></a>Preparación del sitio para Azure Stack Hub Ruggedized

En este tema se tratan los requisitos de caída de potencia de la PDU y del entorno para Azure Stack Hub Ruggedized. 

>[!NOTE]
>Estos valores están pensados únicamente con fines de planeamiento de instalaciones y son aproximados y conservadores. Los requisitos reales pueden variar.

## <a name="environmental-requirements"></a>Requisitos de entorno

En la tabla siguiente se muestran los requisitos del entorno para una solución de Azure Stack Hub Ruggedized con la siguiente configuración:

- Unidad de escalado de 14 TB
- Voltaje de entrada CA de 200 voltios
- Temperatura ambiente máxima de 35 C

*Tabla 1. Requisitos del entorno de configuración alta/baja*

| Object                         | Requisitos de Azure Stack Hub Ruggedized               |
|--------------------------------|--------------------------------|
|Temperatura de funcionamiento           | La temperatura de funcionamiento de Azure Stack Hub Ruggedized (con los requisitos del calentador): de -32°C (-25,6°F) a 43°C (109°F).    |
|Humedad           | Almacenamiento: del 5 al 95 % de humedad relativa con 33 C (91 F) como punto de rocío máximo. La atmósfera debe encontrarse sin condensación en todo momento. <br> Operativo: del 5 al 85 % de humedad relativa con 29 C (84,2 F) como punto de rocío máximo.
|Conectividad física           | Azure Stack Hub Ruggedized puede estar conectado físicamente a través de lo siguiente: <br>4x10G SR SFP+ <br>4x1000BASE-SX <br>4x 1000BASE-T
|Entrada de alimentación                     | Max 4.981 Kw, Avg 4.391 KW<br> Conector de entrada C13/C14<br> Entrada: 100-240V 50/60 Hz

## <a name="pdu-power-drop-requirements"></a>Requisitos de caída de potencia de PDU

En la tabla siguiente se enumeran las caídas de potencia necesarias para Azure Stack Hub Ruggedized.

*Tabla 2. Número necesario de caídas de potencia*

| Configuración  | Monofásica  | Delta trifásica |Wye trifásica |
|----------------|---------------|-------------------|----------------|
|High/low        | 2             | 2                 | 2              |

El sistema integrado Azure Stack le permite usar diferentes tipos de conectores de PDU para una mejor integración con su centro de datos. En la tabla siguiente se muestran los tipos de conectores:

*Tabla 3. Opciones de PDU y de conectores*

| Ubicación     | Monofásica                                | Delta trifásica                                   | Wye trifásica                                        |
|--------------|---------------------------------------------|-----------------------------------------------------|-----------------------------------------------------------|
|Norteamérica |- L630P<br>- L7-30P<br>- Russellstoll 3750DP |- Hubbell Pro CS8365L<br>- Russellstoll 9P54U2T/1100 |- Hubbell C530P6S<br>- ABL Sursum S52S0A<br>- Flying Leads |


