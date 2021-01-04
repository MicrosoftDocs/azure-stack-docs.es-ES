---
title: Apéndice de redes del Centro de datos modular (MDC)
description: Apéndices de redes de MDC.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/30/2019
ms.lastreviewed: 12/30/2019
ms.openlocfilehash: b18f962ce2348a6a0f90a2ad48c1ea84e01fe921
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598647"
---
# <a name="mdc-network-appendix"></a>Apéndice de red de MDC

El apéndice proporciona información de identidades y parámetros del dispositivo para el hardware de MDC.

## <a name="technical-device-parameters"></a>Parámetros de dispositivos técnicos

### <a name="cisco-93360yc-fx2--cisco-9348gc-fxp"></a>Cisco 93360YC-FX2 y Cisco 9348GC-FXP

| **Característica** | **Cisco Nexus 93360YC-FX2** | **Cisco Nexus 9348GC-FXP** |
|---|---|---|
| Puertos | 96 puertos x 1/10/25 Gbps y 12 puertos x 40/100 Gbps QSFP28 | 48 puertos x 1-GBASE-T, 4 puertos x 1/10/25 Gbps SFP28 y 2 puertos x 40/100 QSFP28 |
| Velocidades admitidas | 1/10/25 Gbps en enlaces de bajada, 40/100 Gbps en enlaces ascendentes, puertos admitidos por desglose, 97-108: 4x10/25 G | Velocidades de 100 Mbps y 1 Gbps |
| CPU | Cuatro núcleos | Cuatro núcleos |
| Memoria del sistema | Hasta 24 GB | 24 GB |
| Unidad SSD | 128 GB | 128 GB |
| Búfer del sistema | 40 MB | 40 MB |
| Puertos de administración | Dos puertos: Un puerto RJ-45 y uno SFP+ | Dos puertos: Un puerto RJ-45 y uno SFP+ |
| Puertos USB | Uno | Uno |
| Puertos serie RS-232 | Uno | Uno |
| Fuentes de alimentación (hasta dos) | 1200 W de corriente alterna (CA), 1200 W HVAC/HVDC | 350 W CA, 440 W CC |
| Alimentación típica (CA) | 404 W | 178 W |
| Potencia máxima (CA) | 900 W | 287 W |
| Voltaje de entrada (CA) | 100 V a 240 V | 100 V a 240 V |
| Voltaje de entrada (CA de alto voltaje [HVAC]) | 100 V a 277 V | 90 V a 305 V |
| Voltaje de entrada (CC) | -40 V a -72 V | \-36 V a -72 V |
| Voltaje de entrada (CC de alto voltaje [HVDC]) | -240 V a -380 V | 192 V a 400 V |
| Frecuencia (CA) | de 50 Hz a 60 Hz | de 50 Hz a 60 Hz |
| Ventiladores | Tres bandejas de ventilador | tres |
| Flujo de aire | Entrada y salida en el puerto | Entrada y salida en el puerto |
| Dimensiones físicas | 3,38 x 17,41 x 24,14 in (8,59 x 44,23 x 61,31 cm) | 1,72 x 17,3 x 19,7 in |
| (alto x ancho x profundidad) | | (4,4 x 43,9 x 49,9 cm) |
| Acústica | 76,7 dBA con una velocidad del ventilador del 40 %, 88,7 dBA con una velocidad del ventilador del 70 % y 97,4 dBA con una velocidad del ventilador de 100 % | 67,5 dBA con una velocidad del ventilador del 50 %, 73,2 dBA con una velocidad del ventilador del 70 % y 81,6 dBA con una velocidad del ventilador del 100 % |
| Cumplimiento de RoHS | Sí | Sí |
| MTBF | 320.040 horas | 257.860 horas |
| Imagen mínima de ACI | ACI-N9KDK9-14.1.2 | ACI-N9KDK9-13.0 |
| Imagen mínima de NX-OS | NXOS-9.3(1) | NXOS-703I7.1 |

### <a name="juniper-mx204"></a>MX204 de Juniper

:::row:::
    :::column:::
        **Diseño**
    :::column-end:::
    :::column:::
        Capacidad del sistema

        Orientación de la ranura

        Montaje
    :::column-end:::
    :::column span="2":::
        3 Tbps

        N/D

        Frontal o centrado
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Especificación física**
    :::column-end:::
    :::column:::
        Dimensiones (ancho x alto x profundidad)

        Peso totalmente cargado

        Peso descargado
    :::column-end:::
    :::column span="2":::
        17,45" x 8,71" x 24,5" (44,3 x 22,1 x 62,2 cm)

        130 lb/59 kg

        52 lb/23,6 kg
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Motor de enrutamiento**
    :::column-end:::
    :::column:::
        Memoria predeterminada

        Número de núcleos
    :::column-end:::
    :::column span="2":::
        2x16 MB de almacenamiento flash NOR; 64 GB de RAM DDR4; SSD de 2 x 50 GB

        6 núcleos
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Redundancia**
    :::column-end:::
    :::column:::
        Componentes
    :::column-end:::
    :::column span="2":::
        Fuentes de alimentación, RE, ventiladores
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Medioambiental**
    :::column-end:::
    :::column:::
        Flujo de aire

        Temperatura de funcionamiento

        Humedad operativa

        Altitud de funcionamiento
    :::column-end:::
    :::column span="2":::
        De lado a lado

        32 a 115 °F (0 a 46 °C) en el nivel del mar

        5 % a 90 %

        10 000 ft (3048 m)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Certificaciones**
    :::column-end:::
    :::column:::
        NEBS
    :::column-end:::
    :::column span="2":::
        • GR-1089-Core EMC y seguridad eléctrica

        • Red de enlace común (CBN)

        • Código eléctrico nacional (NEC)

        • Protección física GR-63-Core
    :::column-end:::
:::row-end:::

### <a name="dell-emc-s4148f-on"></a>Dell EMC S4148F-ON

Características:

- 48 puertos 10 Gigabit Ethernet SFP+
- 6 puertos 40 Gigabit Ethernet QSFP+
- Un puerto de consola/administración RJ45 con señal RS232 
- Un puerto de consola RJ45 micro-USB-B 
- Un puerto Ethernet de administración RJ45 10/100/1000Base-T 

Especificaciones:

- Tamaño: 
  - Una RU, 1,75" (alto) x 17" (ancho) x 18" (profundidad) [4,4 cm (alto) x 43,1 cm (ancho) x 45,7 cm (profundidad)] 
  - S4112: 1,7" (alto) x 8,28" (ancho) x 18" (profundidad) [4,125 cm (alto) x 20,9 cm (ancho) x 45 cm (profundidad)] 
- Fuente de alimentación: 100–240 V CA, 50/60 Hz
- Fuente de alimentación (CC), aplicable a S4412: nominal -40 V CC a -72 V CC
- Consumo de corriente máximo por sistema: 6 A/5 A a 100/120 V CA; 3 A/2,5 A a 200/240 V CA
- S4112: 2 A/1,7 A a 100/120 V CA; 1 A/0,8 A a 200/240 V CA 
- S4112 (CC): -40 V/5 A, -48 V/4,2 A, -72 V/2,8 A 

Especificaciones de funcionamiento máximas:
- Temperatura de funcionamiento: 41 a 104 °F (5 a 40 °C)
- Humedad operativa: 5 % - 85 % (HR), sin condensación 

Especificaciones no operativas máximas:
- Temperatura de almacenamiento: -40 a 149 °F (-40 a 65 °C) 
- Humedad de almacenamiento: 5 % - 95 % (HR), sin condensación


## <a name="identity"></a>Identidad

Todos los dispositivos de red están diseñados para funcionar con el sistema de control de acceso del controlador de acceso al terminal (TACACS). TACACS proporciona una experiencia de identidad centralizada. El servidor TACACS debe proporcionarse como parte de la configuración inicial.

Observe que, una vez que el servidor TACACS no esté disponible, los dispositivos volverán a las identidades locales que se enumeran a continuación. Durante el funcionamiento normal, esas cuentas están deshabilitadas.


Las siguientes cuentas de usuario se cierran por dispositivo:

| Dispositivo | Nombre de usuario | Contraseña predeterminada |
|---------|--------------|----------------------|
| Perimetral 1 | root | Asignada por el cliente |
| Perimetral 2 | Root | Asignada por el cliente |
| Tor 1 | Root | Asignada por el cliente |
| Tor 2 | Root | Asignada por el cliente |
| BMC | Root | Asignada por el cliente |
| PDU 1 | root | Asignada por el cliente |
| PDU 2 | root | Asignada por el cliente |
| Serie | Root | Asignada por el cliente |
| Avocent | Root & Admin | Asignada por el cliente |
