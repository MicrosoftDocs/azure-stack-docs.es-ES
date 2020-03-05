---
title: Pruebas de comprobación de características interactivas
titleSuffix: Azure Stack Hub
description: Aprenda a crear pruebas de comprobación de características interactivas para Azure Stack Hub con validación como servicio.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a1ef4a77bdc4f4dd83a7bced78c85d64ae7836c3
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704733"
---
# <a name="interactive-feature-verification-testing"></a>Pruebas de comprobación de características interactivas  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Puede usar el marco de pruebas de comprobación de características interactiva para solicitar la realización de en su sistema. Cuando se solicita una prueba, Microsoft utiliza el marco para preparar las pruebas que requieren pasos manuales interactivos. Microsoft puede usar el marco de trabajo para encadenar varias pruebas automatizadas independientes.

En este artículo se describe un escenario manual simple. La prueba permite comprobar la sustitución de un disco en Azure Stack Hub. El marco recopila registros de diagnóstico en cada paso. Los problemas se pueden depurar a medida que se detectan. El marco también permite el uso compartido de registros que generan otras herramientas o procesos, así como proporcionar comentarios acerca del escenario.

> [!Important]  
> En este artículo se hace referencia a los pasos para realizar una prueba de identificación del disco. Esto es simplemente una demostración y los resultados recopilados desde el flujo de trabajo de Prueba superada no pueden usarse para la comprobación de la nueva solución.

## <a name="overview-of-interactive-testing"></a>Introducción a las pruebas interactivas

La realización de una prueba antes de sustituir un disco es un escenario común. En este ejemplo, la prueba tiene cinco pasos:

1. Cree un flujo de trabajo de **Test Pass** (Prueba superada).
2. Seleccione la **prueba de identificación de disco**.
3. Complete el paso manual cuando se le pida.
4. Compruebe el resultado del escenario.
5. Envíe el resultado de la prueba a Microsoft.

## <a name="create-a-new-test-pass"></a>Creación de una prueba

Si no tiene un flujo de trabajo de Prueba superada disponible, siga las instrucciones para [programar una prueba](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Programación de la prueba

1. Seleccione **Disk Identification Test**  (Prueba de identificación de disco).

    > [!Note]  
    > La versión de la prueba aumentará a medida que se realicen mejoras en la documentación y el material adjunto. Siempre se debe usar la versión más alta, salvo que Microsoft indique lo contrario.

    ![Prueba de identificación de disco: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2. Seleccione **Editar** y proporcione la contraseña y el nombre del usuario administrador del dominio.

3. Seleccione el agente de ejecución de pruebas o DVM apropiados en el que ejecutar la prueba.

    ![Seleccionar un agente de ejecución de pruebas: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4. Seleccione **Submit** (Enviar) para iniciar la prueba.

    ![Revisar y enviar prueba: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image6.png)

5. Acceda la interfaz de usuario para la prueba interactiva desde el agente seleccionado en el paso anterior.

    ![Prueba de identificación de disco: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image8.png)

6. En los vínculos **Documentation**(Documentación) y **Validation** (Validación) encontrará instrucciones de Microsoft para actuar en este escenario.

    ![Vínculos de Documentación y Validación en Prueba de identificación de disco](media/azure-stack-vaas-interactive-feature-verification/image9.png)

7. Seleccione **Next** (Siguiente).

    ![Seleccionar Siguiente: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image10.png)

8. Siga las instrucciones para ejecutar el script de comprobaciones previas.

    ![Ejecutar el script de comprobaciones previas: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image11.png)

9. Una vez que este script se haya completado correctamente, ejecute el escenario manual (sustitución del disco) tal como se indica en los vínculos **Documentación** y **Validación** de la pestaña **Información**.

    ![Ejecutar el escenario manual: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > No cierre el cuadro de diálogo mientras ejecuta el escenario manual.

10. Cuando haya terminado, siga las instrucciones para ejecutar el script de comprobaciones posteriores.

    ![Ejecutar el script de comprobaciones posteriores: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image13.png)

11. Cuando el escenario manual se haya completado correctamente (sustitución del disco), seleccione **Enviar**.

    ![Enviar la prueba de identificación de disco: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Si cierra la ventana, la prueba se detendrá antes de haber acabado.

12. Proporcione comentarios sobre la experiencia de las pruebas. Estas preguntas ayudarán a Microsoft a evaluar la tasa de éxito y la calidad de la versión del escenario.

    ![Proporcionar comentarios sobre la experiencia de prueba interactiva en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image15.png)

13. Adjunte los archivos de registro que desee enviar a Microsoft.

    ![Adjuntar archivos de registro: pruebas interactivas en Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image16.png)

14. Acepte el CLUF del envío de comentarios.

15. Seleccione **Submit** (Enviar) para enviar los resultados a Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y administración de pruebas en el portal de Azure Stack Hub Validation](azure-stack-vaas-monitor-test.md)
