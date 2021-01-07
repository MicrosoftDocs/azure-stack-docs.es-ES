---
title: Conceptos clave de la validación como servicio
titleSuffix: Azure Stack Hub
description: Conozca los conceptos clave de la validación como servicio de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7419cbe82e6bcd2eb79af486daf30975f6c8cdd0
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874666"
---
# <a name="validation-as-a-service-key-concepts"></a>Conceptos clave de la validación como servicio

En este artículo se describen los conceptos clave de la validación como servicio (VaaS).

## <a name="solutions"></a>Soluciones

Una solución VaaS representa una solución de Azure Stack Hub con una lista de materiales de hardware en particular. La solución VaaS actúa como contenedor para los flujos de trabajo que se ejecutan frente a la solución de Azure Stack Hub.

### <a name="create-a-solution-in-the-azure-stack-hub-validation-portal"></a>Creación de una solución en el portal de Azure Stack Hub Validation

1. Inicie sesión en el portal de [Azure Stack Hub Validation](https://azurestackvalidation.com).
2. En el panel de soluciones, seleccione **Nueva solución**.
3. Escriba un nombre para la solución. Para obtener sugerencias de nombres, consulte [Convenciones de nomenclatura para soluciones VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Seleccione **Guardar** para crear la solución.

## <a name="workflows"></a>Workflows

Un flujo de trabajo VaaS funciona dentro del contexto de una solución VaaS. Representa una serie de conjuntos de pruebas que actúan sobre la funcionalidad de implementación de Azure Stack Hub. Para cada actualización de software o implementación de una solución de Azure Stack Hub, se debe crear un flujo de trabajo.

Los flujos de trabajo se clasifican por tipo de escenario de prueba. En las pruebas no oficiales, el flujo de trabajo **Pruebas superadas** le permite seleccionar las pruebas de toda la documentación y el material adjunto de VaaS disponible. En las pruebas oficiales, el flujo de trabajo de **validación** tiene como destino escenarios de prueba específicos seleccionados por Microsoft.

![Iconos de flujo de trabajo de VaaS](media/tile_all-workflows.png)

> [!NOTE]
> El flujo de trabajo de **validación del paquete** actualmente admite dos escenarios: [Validación de paquetes OEM](azure-stack-vaas-validate-oem-package.md) y [Validación de las actualizaciones de software de Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Para más información sobre los tipos de flujo de trabajo, consulte [¿Qué es la validación como servicio de Azure Stack Hub?](azure-stack-vaas-overview.md)

### <a name="getting-started-with-vaas-workflows"></a>Introducción a los flujos de trabajo de VaaS

1. En el panel de soluciones, cree una solución o seleccione una existente. Esto actualiza y activa los iconos de flujos de trabajo.
2. Para crear un flujo de trabajo, seleccione **Iniciar** en cualquiera de los iconos. Para obtener información específica para cada flujo de trabajo, consulte los artículos siguientes:
    - Prueba superada: [Inicio rápido: Uso del portal de Azure Stack Hub Validation para programar la primera prueba](azure-stack-vaas-schedule-test-pass.md)
    - Validación de la solución: [Validación de una nueva solución de Azure Stack Hub](azure-stack-vaas-validate-solution-new.md)
    - Validación del paquete (actualización mensual): [Validación de las actualizaciones de software de Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Validación del paquete (firma del paquete) [Validación de paquetes de OEM](azure-stack-vaas-validate-oem-package.md)

3. Para administrar o supervisar un flujo de trabajo existente, seleccione **Administrar** en el icono del flujo de trabajo. Seleccione el nombre del flujo de trabajo y use el botón **Editar** para ver las propiedades o cambiar los parámetros comunes de las pruebas.

Para más información sobre las propiedades y los parámetros de los flujos de trabajo, consulte [Parámetros comunes del flujo de trabajo en la validación como servicio de Azure Stack Hub](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Pruebas

Una prueba en VaaS consta de un conjunto de operaciones que se ejecutan en una solución de Azure Stack Hub. Las pruebas tienen distintos propósitos identificados por categoría, como funcional o confiabilidad, y tienen como destino uno o varios servicios de Azure Stack Hub. Cada prueba define su propio conjunto de parámetros, algunos de los cuales se especifican mediante los parámetros comunes del flujo de trabajo que los contiene.

Para más información sobre cómo administrar y supervisar las pruebas, consulte [Supervisión y administración de pruebas en el portal de Azure Stack Hub Validation](azure-stack-vaas-monitor-test.md).

Para más información sobre los parámetros de prueba, consulte [Parámetros comunes del flujo de trabajo en la validación como servicio de Azure Stack Hub](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agentes

Un agente de VaaS dirige la ejecución de las pruebas. Dos tipos de agentes ejecutan las pruebas de VaaS:

- El **agente en la nube**. Es el agente predeterminado disponible en VaaS. No se necesita ninguna configuración, pero requiere conectividad de entrada a su entorno, y los puntos de conexión de Azure Stack Hub deben poder resolverse desde Internet. Algunas pruebas no son compatibles con el agente en la nube.
- Un **agente local**. Esto le permite ejecutar la validación en escenarios donde no es posible la conectividad de entrada a su entorno. Algunas pruebas requieren la ejecución a través del agente local.

Los agentes locales no están asociados a ninguna solución de Azure Stack Hub o VaaS en particular. Como práctica recomendada, deben ejecutarse fuera de un entorno de Azure Stack Hub.

Para obtener instrucciones sobre cómo agregar un agente local, consulte [Implementación del agente local](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos recomendados de la validación como servicio](azure-stack-vaas-best-practice.md)