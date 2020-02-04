---
title: Introducción a la validación como servicio de Azure Stack Hub
description: Una introducción a la validación como servicio de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95cee26239a4549b87360e62c61a34449f6a0774
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884069"
---
# <a name="what-is-validation-as-a-service-for-azure-stack-hub"></a>¿Qué es la validación como servicio de Azure Stack Hub?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

La validación como servicio (VaaS) es un servicio nativo de Azure diseñado para los asociados de soluciones que trabajan conjuntamente con Microsoft en la construcción de ofertas de Azure Stack Hub. Los asociados de soluciones pueden usar el servicio para comprobar que sus soluciones cumplen los requisitos de Microsoft y que funcionan con Azure Stack Hub como se esperaba.

Los usos principales de VaaS son:

- Validar nuevas soluciones de Azure Stack Hub
- Validar cambios en el software de Azure Stack Hub
- Firmar digitalmente los paquetes de asociados de soluciones usados durante la implementación
- Obtener una vista previa de la documentación y el material adjunto de pruebas de VaaS

## <a name="validate-a-new-azure-stack-hub-solution"></a>Validación de una nueva solución de Azure Stack Hub

Los asociados usan el flujo de trabajo de **validación de soluciones** para comprobar las nuevas soluciones de Azure Stack Hub. La solución debe superar las pruebas de componentes de Azure Stack Hub de Hardware Lab Kit (HKL). Para certificar una gama de configuraciones de hardware, el flujo de trabajo se debe ejecutar dos veces para cada nueva solución: una vez cada una para las configuraciones mínima y máxima.

Para más información, consulte [Validar nuevas soluciones de Azure Stack Hub](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-hub-software"></a>Validación de los cambios en el software de Azure Stack Hub

Los asociados usan el flujo de trabajo de **validación de paquetes** para comprobar que su solución funciona con la actualización la más reciente de software de Azure Stack Hub. El flujo de trabajo de validación de paquetes se debe ejecutar en un entorno de hardware recomendado por Microsoft, donde se hayan usado la revisión y la actualización para aplicar la actualización. Se recomienda también ejecutar el flujo de trabajo en la compilación de línea base.

Para más información, consulte [Validación de las actualizaciones de software de Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Obtención de paquetes de asociados de soluciones con firma digital

Además de validar las actualizaciones de Azure Stack Hub, puede usar el flujo de trabajo de **validación de paquetes** para validar las actualizaciones de paquetes de personalización de OEM, que incluyen controladores específicos del asociado de Azure Stack Hub, así como firmware y otro software que se usa durante la implementación del software de Azure Stack Hub. Implemente el paquete que está validando para ver si tiene la versión actual del software de Azure Stack Hub con al menos la solución de tamaño mínimo que se admitirá. El paquete se envía a VaaS antes de ejecutar las pruebas. Si se superan las pruebas, indique a [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) que el paquete ha completado las pruebas y que se deber firmar digitalmente con la firma digital de Azure Stack Hub. Microsoft firma el paquete y notifica al asociado de Azure Stack Hub que el paquete está disponible para descarga en el portal de VaaS.

Para más información, consulte [Validación de paquetes de OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Vista previa de la documentación y el material adjunto de pruebas de VaaS

Microsoft presenta periódicamente nuevas características en Azure Stack Hub. Como parte del proceso de desarrollo para comercializar estas características, se presenta nueva documentación y material adjunto de pruebas en el flujo de trabajo de **pruebas superadas**. El flujo de trabajo de pruebas superadas incluye documentación y material adjunto de pruebas de otros flujos de trabajo para permitir la ejecución de pruebas no oficiales. No use el flujo de trabajo de pruebas superadas para enviar resultados para su aprobación. Use el flujo de trabajo de validación de soluciones y de validaciones de paquetes para obtener la aprobación oficial de su solución.

Para más información, consulte [Inicio rápido: Use el portal de validación como servicio para programar la primera prueba](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Resumen de las pruebas del flujo de trabajo de validación

| Flujo de trabajo de validación | Pruebas necesarias |
|----|------------|
| [Validación de la solución nueva](azure-stack-vaas-validate-solution-new.md) | Cloud Simulation Engine<br>Compute SDK Operational Suite<br>Disk Identification Test<br>KeyVault Extension SDK Operational Suite<br>KeyVault SDK Operational Suite<br>Network SDK Operational Suite<br>Storage Account SDK Operational Suite<br> |
| [Validación de paquetes de OEM](azure-stack-vaas-validate-oem-package.md) | Comprobación del paquete de extensión de OEM<br>Cloud Simulation Engine |
| [Validación de la actualización mensual](azure-stack-vaas-validate-microsoft-updates.md) | Comprobación de la actualización mensual de Azure Stack Hub<br>Cloud Simulation Engine<br> |

## <a name="next-steps"></a>Pasos siguientes

- [Set up your Validation as a Service resources](azure-stack-vaas-set-up-resources.md) (Configuración de los recursos de validación como servicio)
- Más información sobre [Conceptos clave de la validación como servicio](azure-stack-vaas-key-concepts.md)
