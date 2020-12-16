---
title: Apéndice sobre las redes de Azure Stack Hub Ruggedized
description: Apéndices sobre las redes de Azure Stack Hub Ruggedized.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 2a5acdf50a310fe2bda89e4472c38af2c75f9487
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941230"
---
# <a name="azure-stack-hub-ruggedized-network-appendix"></a>Apéndice sobre las redes de Azure Stack Hub Ruggedized

El apéndice proporciona información de identidades y parámetros del dispositivo para el hardware de Azure Stack Hub Ruggedized.

## <a name="technical-device-parameters"></a>Parámetros de dispositivos técnicos

### <a name="dell-s5048f-on"></a>Dell S5048F-ON

Puertos: 

- 48 puertos SFP28 Ethernet con 25 Gigabit de velocidad de línea. 
- Seis puertos QSFP28 Ethernet con 100 Gigabit de velocidad de línea. 
- Un puerto de consola y administración RJ45 con señal RS232.
- Un puerto de consola opcional Micro USB tipo B. 
- Un puerto Ethernet 10/100/1000 Base-T usado como puerto de administración. 
- Un puerto USB tipo A para el almacenamiento masivo externo. 

Especificaciones:

- Tamaño: 1 RU: 1,72"(al.) x 17,1"(an.) x 18"(pr.)  (4,4(al.) x 43,4(an.) x 45,7(pr.) cm). 
- Peso: 22 libras (9,98 kg) 
- Nivel de presión de sonido ISO 7779 A ponderado: 59,6 dBA a 73,4°F (23°C).  
- Fuente de alimentación: 100–240 VAC 50/60 Hz. 
- Salida térmica máxima: 1956 BTU/h. 
- Consumo de corriente máximo por sistema: 
  - 5,73 A/4,8 A a 100/120 V Corriente alterna (AC). 
  - 2,87 A/2,4 A a 200/240 V AC. 
- Consumo máximo de energía: 573 W (AC). 
- Consumo de energía típico: 288 W (AC) con todos los medios ópticos cargados.  
- Especificaciones de funcionamiento máximas: 
  - Temperatura de funcionamiento: de 32 a 113°F (de 0 a 45°C). 
  - Humedad operativa: 10 % - 90 % (HR), sin condensación. 
  - Aire fresco compatible a 45 C. 
- Especificaciones no operativas máximas: 
  - Temperatura de almacenamiento: -40 a 158°F (-40 a 70°C). 
  - Humedad de almacenamiento: 5 % - 95 % (HR), sin condensación.

### <a name="dell-3048-on"></a>Dell 3048-ON

Puertos:

- 48 puertos 1000 BASE-T de velocidad de línea.  
- Cuatro puertos SFP+ de 10 GbE de velocidad de línea. 
- Un puerto de consola y administración RJ45 con señal RS232.  

Especificaciones:

- Tamaño: 1 RU: 1,71"(al.) x 17,09"(an.) x 12,6"(pr.)  (4,4(al.) x 43,4(an.) x 32,0(pr.) cm).  
- Peso: 12,8 libras (5,84 kg) con una fuente de alimentación; 14,8 libras (6,74 kg) con dos fuentes de alimentación. 
- Nivel de presión de sonido ISO 7779 A ponderado: \<36 dBA a 78,8°F (26°C). 
- Fuente de alimentación: 90–264 VAC, 50/60 Hz. 
  1) Flujo de aire hacia delante de CA.  
  2) Flujo de aire inverso de CA. 
- Salida térmica máxima: 290 BTU/h. 
- Consumo de corriente máximo por sistema:  
  - \<1 A a 100/120 VAC. 
  - \<0,5 A a 200/240 VAC.  
- Consumo máximo de energía: 87 W. 
- Consumo de energía típico: 65 W. 
- Especificaciones de funcionamiento máximas:  
  - Temperatura de funcionamiento: de 32 a 113°F (de 0 a 45°C).  
  - Humedad operativa: 5 % - 85 % (HR), sin condensación.    
  - Altitud de funcionamiento: de 0 a 10 000 pies sobre el nivel de mar.  
- Especificaciones no operativas máximas: 
  - Temperatura de almacenamiento: -40 a 158°F (-40 a 70°C). 
  - Humedad de almacenamiento: 5 % - 95 % (HR), sin condensación.   

## <a name="identity"></a>Identidad

Todos los dispositivos de red están diseñados para funcionar con el sistema de control de acceso del controlador de acceso al terminal (TACACS). TACACS proporciona una experiencia de identidad centralizada. El servidor TACACS debe proporcionarse como parte de la configuración inicial.

Observe que, una vez que el servidor TACACS no esté disponible, los dispositivos volverán a las identidades locales que se enumeran a continuación. Durante el funcionamiento normal, esas cuentas están deshabilitadas.

Las siguientes cuentas de usuario se cierran por dispositivo:

| Dispositivo | Nombre de usuario | Contraseña predeterminada     |
|--------|----------|----------------------|
| Tor 1  | Root     | Asignada por el cliente |
| Tor 2  | Root     | Asignada por el cliente |
| BMC    | Root     | Asignada por el cliente |
