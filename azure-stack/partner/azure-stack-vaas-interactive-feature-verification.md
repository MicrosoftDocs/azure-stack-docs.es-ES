---
title: Pruebas de comprobación de características interactivas en Azure Stack con validación como servicio
description: Aprenda a crear pruebas de comprobación de características interactiva para Azure Stack con validación como servicio.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c957d076fb4d90dc9a19d5b8fbaaddbdb55c789d
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884141"
---
# <a name="interactive-feature-verification-testing"></a>Pruebas de comprobación de características interactivas  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Puede usar el marco de pruebas de comprobación de características interactiva para solicitar la realización de en su sistema. Cuando se solicita una prueba, Microsoft utiliza el marco para preparar las pruebas que requieren pasos manuales interactivos. Microsoft puede usar el marco de trabajo para encadenar varias pruebas automatizadas independientes.

En este artículo se describe un escenario manual simple. La prueba comprueba la sustitución de un disco en Azure Stack. El marco recopila registros de diagnóstico en cada paso. Los problemas se pueden depurar a medida que se detectan. El marco también permite el uso compartido de registros que generan otras herramientas o procesos, así como proporcionar comentarios acerca del escenario.

> [!Important]  
> En este artículo se hace referencia a los pasos para realizar la identificación del disco. Esto es simplemente una demostración, ya que los resultados recopilados desde el flujo de trabajo Test Pass (Paso de prueba) no pueden usarse para la verificación de la nueva solución.

## <a name="overview-of-interactive-testing"></a>Introducción a las pruebas interactivas

La realización de una prueba antes de sustituir un disco es un escenario común. En este ejemplo, la prueba tiene cinco pasos:

1. Cree un flujo de trabajo de **Test Pass** (Prueba superada).
2. Seleccione la **prueba de identificación de disco**.
3. Complete el paso manual cuando se le pida.
4. Compruebe el resultado del escenario.
5. Envíe el resultado de la prueba a Microsoft.

## <a name="create-a-new-test-pass"></a>Creación de una prueba

Si no tiene un paso de prueba disponible, siga las instrucciones para [programar una prueba](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Programación de la prueba

1. Seleccione **Disk Identification Test**  (Prueba de identificación de disco).

    > [!Note]  
    > La versión de la prueba aumentará a medida que se realicen mejoras en la documentación y el material adjunto. Siempre se debe usar la versión más alta, salvo que Microsoft indique lo contrario.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Para especificar la contraseña y el nombre de usuario administrador de dominio, seleccione **Edit** (Editar).

1. Seleccione el agente de ejecución de pruebas o DVM apropiados en el que ejecutar la prueba.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Seleccione **Submit** (Enviar) para iniciar la prueba.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Acceda la interfaz de usuario para la prueba interactiva desde el agente seleccionado en el paso anterior.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. En los vínculos **Documentation**(Documentación) y **Validation** (Validación) encontrará instrucciones de Microsoft para actuar en este escenario.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Seleccione **Next** (Siguiente).

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Siga las instrucciones para ejecutar el script de comprobaciones previas.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Una vez que dicho script se haya completado correctamente, ejecute el escenario manual (sustitución del disco) tal como se indica en los vínculos **Documentation** (Documentación) y **Validation** (Validación) de la pestaña **Information** (Información).

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > No cierre el cuadro de diálogo mientras actúa en el escenario manual.

1. Cuando haya terminado, siga las instrucciones para ejecutar el script de comprobaciones posteriores.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. Cuando el escenario manual se haya completado correctamente (sustitución del disco), seleccione **Next** (Siguiente).

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Si cierra la ventana, la prueba se detendrá antes de haber acabado.

1. Proporcione comentarios sobre la experiencia de las pruebas. Estas preguntas ayudarán a Microsoft a evaluar la tasa de éxito y la calidad de la versión del escenario.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Adjunte los archivos de registro que desee enviar a Microsoft.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Acepte el CLUF del envío de comentarios.

1. Seleccione **Submit** (Enviar) para enviar los resultados a Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md)
