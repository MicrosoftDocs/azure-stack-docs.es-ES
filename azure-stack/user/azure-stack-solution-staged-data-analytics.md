---
title: Creación de una solución de análisis de datos almacenados provisionalmente con Azure y Azure Stack | Microsoft Docs
description: Aprenda a crear una solución de análisis de datos almacenados provisionalmente con Azure y Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 2c2b06db3bb6144620b5fce1a7e3e76fdd06589a
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "63448561"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Tutorial: Creación de una solución de análisis de datos almacenados provisionalmente con Azure y Azure Stack 

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Aprenda a usar entornos locales y en la nube pública para satisfacer las necesidades de empresas con varias sedes. Azure Stack ofrece una solución rápida, segura y flexible para recopilar, procesar, almacenar y distribuir datos locales y remotos, especialmente en aquellos casos en que la seguridad, la confidencialidad, las directivas corporativas y los requisitos normativos pueden ser diferentes entre las distintas ubicaciones y usuarios.

Según este patrón, los clientes recopilan datos que requieren análisis en el mismo punto de recopilación para poder tomar decisiones rápidas. Esta recopilación de datos se produce con frecuencia sin acceso a Internet. Una vez restablecida la conectividad, puede que necesite un análisis intensivo del recurso de los datos para obtener información adicional. Puede analizar los datos aún cuando una nube pública esté retrasada o no disponible.

En este tutorial, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Crear el blob de almacenamiento de datos sin procesar.
> - Crear una función de Azure Stack para mover los datos limpios de Azure Stack a Azure.
> - Crear una función desencadenada por Blob Storage.
> - Crear una cuenta de almacenamiento de Azure Stack que contenga un blob y una cola.
> - Crear una función desencadenada por la cola.
> - Probar la función desencadenada por la cola.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack es una extensión de Azure. Azure Stack aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En las notas del producto [Consideraciones de diseño para aplicaciones híbridas](https://aka.ms/hybrid-cloud-applications-pillars) se revisan los pilares de la calidad de software (selección de ubicación, escalabilidad, disponibilidad, resistencia, manejabilidad y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.

## <a name="prerequisites"></a>Requisitos previos

Se requiere cierta preparación para crear esta solución:

-   Una instancia de Azure Stack instalada y en funcionamiento (puede encontrar más información aquí: [Azure Stack overview](azure-stack-storage-overview.md) (Introducción a Azure Stack)

-   Una suscripción de Azure. (Cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   Descargue e instale el [Explorador de Microsoft Azure Storage](https://storageexplorer.com/).

-   Deberá proporcionar sus propios datos para que los procesen las funciones. Los datos se deben generar y estar disponibles para cargarlos en el contenedor de blobs de almacenamiento de Azure Stack.

## <a name="issues-and-considerations"></a>Problemas y consideraciones

### <a name="scalability-considerations"></a>Consideraciones sobre escalabilidad

Las funciones de Azure y las soluciones de almacenamiento se escalan para satisfacer las demandas de procesamiento y volumen de datos. Para más información sobre la escalabilidad y los destinos de Azure, consulte la [documentación de escalabilidad de Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Consideraciones sobre disponibilidad

El almacenamiento es la consideración de disponibilidad principal para este patrón. Se requiere una conexión mediante vínculos rápidos para el procesamiento y distribución de volúmenes de datos grandes.

### <a name="manageability-considerations"></a>Consideraciones sobre la manejabilidad

Analice cómo le permitirán las herramientas de desarrollo y el control de código fuente administrar la solución.

## <a name="create-the-raw-data-storage-blob"></a>Creación del blob de almacenamiento de datos sin procesar

La cuenta de almacenamiento y el contenedor de blobs contienen todos los datos originales generados por las actividades locales, incluido la actividad de máquinas y empleados, los datos de la sede, las métricas de producción y otros informes.

1.  Inicie sesión en el [*portal de Azure Stack*](https://portal.local.azurestack.external/).

2.  En el portal de Azure Stack, expanda el menú de la izquierda para abrir el menú de servicios y elija **Todos los servicios**. Desplácese hacia abajo hasta **Almacenamiento** y elija **Cuentas de almacenamiento**. En la ventana Storage Accounts (Cuentas de almacenamiento), elija **Add** (Agregar).

3.  Use la siguiente información para la cuenta:

     a.  Nombre: **Su elección**

    b.  Modelo de implementación: **Resource Manager**

    c.  Tipo de cuenta: **Storage (uso general V1)**

    d.  Ubicación: **Oeste de EE. UU.**

    e.  Replicación: **Almacenamiento con redundancia local (LRS)**

    f.  Rendimiento: **Estándar**

    g.  Se requiere transferencia segura: **Deshabilitada**

    h.  Suscripción: Elegir una

    i.  Grupos de recursos: Especifique un nuevo grupo de recursos o seleccione un grupo de recursos existente

    j.  Configuración de redes virtuales: **Deshabilitada**

4.  Seleccione **Crear** para crear la cuenta de almacenamiento.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  Una vez creado, seleccione el nombre de la cuenta de almacenamiento.

6.  En la hoja de la cuenta, en el encabezado **BLOB SERVICE**, seleccione **Contenedores**.

7.  En la parte superior de la hoja, seleccione **+ Contenedor** y **Contenedor**.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  Nombre: **la que elija**

9.  Nivel de acceso público: **Contenedor** (acceso de lectura anónimo para contenedores y blobs)

10.  Seleccione **Aceptar**.

## <a name="create-an-azure-stack-function"></a>Creación de una función de Azure Stack

Cree una función de Azure Stack para mover los datos limpios de Azure Stack a Azure.

### <a name="create-the-azure-stack-function-app"></a>Creación de la aplicación de función de Azure Stack

1. Inicie sesión en el [portal de Azure Stack](https://portal.local.azurestack.external).
2. Seleccione **Todos los servicios**.
3. Seleccione **Function Apps** en el grupo **Web + Mobile** (Web y móvil).

4.  Cree la aplicación de función mediante la configuración especificada en la tabla debajo de la imagen.

    | Configuración | Valor sugerido | DESCRIPCIÓN |
    | ---- | ---- | ---- |
    | Nombre de la aplicación | Nombre único globalmente | Nombre que identifica la nueva Function App. Los caracteres válidos son `a`-`z`, `0``-9` y `-`. |
    | Subscription | Su suscripción | Suscripción en la que se creará esta nueva aplicación de función. |
    | **Grupo de recursos** |  |  |
    | myResourceGroup | Nombre para el nuevo grupo de recursos en el que se va a crear la Function App. |  |
    | SO |  Windows | El hospedaje sin servidor actualmente solo está disponible cuando se ejecuta en Windows. |
    | **Plan de hospedaje** |  |  |
    | Plan de consumo | Plan de hospedaje que define cómo se asignan los recursos a la Function App. En el Plan de consumo predeterminado, los recursos se agregan dinámicamente según lo requieran sus funciones. En este hospedaje sin servidor, solo paga por el tiempo durante el cual se ejecutan las funciones. |  |
    | Ubicación | La región más cercana | Elija una región cerca de usted o cerca de otros servicios a los que tendrán acceso las funciones. |
    | **Cuenta de almacenamiento** |  |  |
    | \<cuenta de almacenamiento que creó anteriormente> | Nombre de la nueva cuenta de almacenamiento usada por Function App. Los nombres de cuenta de almacenamiento deben tener una longitud de entre 3 y 24 caracteres. El nombre solo puede contener números y letras minúsculas. También puede usar una cuenta existente. |  |

    **Ejemplo:**

    ![Definir nueva configuración de Function App](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  Seleccione **Crear** para aprovisionar e implementar la aplicación de función.

6.  Seleccione el icono de notificación de la esquina superior derecha del portal y observe el mensaje **Implementación correcta**.

    ![Definir nueva configuración de Function App](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  Seleccione **Ir al recurso** para ver la nueva aplicación de función.

![Function App creada correctamente.](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Incorporación de una función a la aplicación de función de Azure Stack

1.  Para crear una función, haga clic en **Funciones** y, luego, en el botón **+ Nueva función**.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  Seleccione **Desencadenador de temporizador**.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  Seleccione **C\#** como el idioma y el nombre de la función: `upload-to-azure`  Establezca la programación en `0 0 * * * *`, que en la notación de CRON es cada hora.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Creación de una función desencadenada por Blob Storage

1. Expanda la aplicación de función y seleccione el botón **+**, que se encuentra junto a **Funciones**.

2. En el campo de búsqueda, escriba `blob` y seleccione el lenguaje que desee para la plantilla **Desencadenador de blobs**.

   ![Elija la plantilla de desencadenador de Blob Storage.](media/azure-stack-solution-staged-data-analytics/image10.png)

3. Use la configuración que se especifica en la tabla siguiente:

   | Configuración | Valor sugerido | DESCRIPCIÓN |
   | ------- | ------- | ------- |
   | NOMBRE | Único en la Function App | Nombre de la función desencadenada por este blob. |
   | Ruta de acceso | \<ruta de acceso desde la ubicación de almacenamiento anterior> | Ubicación de Blob Storage que se está supervisando. El nombre de archivo del blob se pasa en el enlace como parámetro name. |
   | Conexión de la cuenta de almacenamiento | Conexión de la aplicación de función | Puede usar la conexión de cuenta de almacenamiento que ya usa la aplicación de función o crear una nueva. |

   **Ejemplo:**

   ![Cree la función desencadenada por Blob Storage.](media/azure-stack-solution-staged-data-analytics/image11.png)

4. Seleccione **Crear** para crear la función.

### <a name="test-the-function"></a>Prueba de la función

1.  En Azure Portal, vaya a la función. Expanda los **registros** de la parte inferior de la página y asegúrese de que el streaming de registros no está en pausa.

2.  Abra el Explorador de Storage y conéctese a la cuenta de almacenamiento que creó al principio de esta sección.

3.  Expanda la cuenta de almacenamiento, **Contenedores de blobs**, y el blob que creó anteriormente. Seleccione **Cargar** y, después, **Cargar archivos**.

    ![Cargue un archivo en el contenedor de blobs.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  En el cuadro de diálogo Cargar archivos, seleccione el campo Archivos. Busque un archivo en la máquina local, como un archivo de imagen, selecciónelo y haga clic en **Abrir** y, después, en **Cargar**.

5.  Vuelva a los registros de función y compruebe que se ha leído el blob.

    **Ejemplo:**

    ![Vea el mensaje en los registros.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Creación de una cuenta de almacenamiento de Azure Stack

Cree una cuenta de almacenamiento de Azure Stack que contenga un blob y una cola.

### <a name="storage-blob--data-archiving"></a>Archivado de datos de blob de almacenamiento

Esta cuenta de almacenamiento hospedará dos contenedores. Estos contenedores consisten en un blob que se usa para contener los datos de archivo y una cola que se utiliza para el procesamiento de datos asignados para la distribución de la oficina central.

Utilice los pasos y valores que se han descrito anteriormente para crear otra cuenta de almacenamiento y otro contenedor de blobs como almacenamiento de archivos.

### <a name="storage-queue--filtered-data-holding"></a>Conservación de datos filtrados de la cola de almacenamiento

1.  Utilice los pasos y valores que se han descrito anteriormente para acceder a la nueva cuenta de almacenamiento.

2.  En la sección de información general sobre la cuenta de almacenamiento, seleccione **Cola**.

3.  Seleccione **+ Cola** y rellene el campo **Nombre** con un nombre para la nueva cola.

4.  Seleccione **Aceptar**.

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Texto alternativo](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>Crear una función desencadenada por la cola

1.  Utilice los pasos descritos en la sección anterior de creación de la función para crear una función de Azure Stack adicional con un desencadenador de cola en lugar de un desencadenador de blob.

2.  Use la configuración que se especifica en la tabla siguiente:

    | Configuración | Valor sugerido | DESCRIPCIÓN |
    | ------- | ------- | ------- |
    | NOMBRE | Único en la Function App | Nombre de la función desencadenada por la cola. |
    | Ruta de acceso | \<ruta de acceso desde la ubicación de almacenamiento anterior> | Ubicación de almacenamiento que se está supervisando. El nombre de archivo de la cola se pasa en el enlace como parámetro name. |
    | Conexión de la cuenta de almacenamiento | Conexión de la aplicación de función | Puede usar la conexión de cuenta de almacenamiento que ya usa la aplicación de función o crear una nueva. |

3.  Seleccione **Crear** para crear la función.

## <a name="test-the-queue-triggered-function"></a>Prueba de la función desencadenada por la cola

1. En el portal de Azure Stack, vaya a la función. Expanda los **registros** de la parte inferior de la página y asegúrese de que el streaming de registros no está en pausa.

2. Abra el Explorador de Storage y conéctese a la cuenta de almacenamiento que creó al principio de esta sección.

3. Expanda la cuenta de almacenamiento, **Contenedores de blobs**, y el blob que creó anteriormente. Seleccione **Cargar** y, después, **Cargar archivos**.

   ![Cargue un archivo en el contenedor de blobs.](media/azure-stack-solution-staged-data-analytics/image12.png)

4. En el cuadro de diálogo Cargar archivos, seleccione el campo Archivos. Busque un archivo en la máquina local, como un archivo de imagen, selecciónelo y haga clic en **Abrir** y, después, en **Cargar**.

5. Vuelva a los registros de función y compruebe que se ha leído el blob.

   **Ejemplo:**

   ![Vea el mensaje en los registros.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Datos compatibles almacenados y a los que se accede de forma segura

Puede almacenar, procesar, distribuir y acceder a los datos globales de una empresa mediante Azure y el análisis de datos almacenados provisionalmente de Azure Stack y las directivas de punto de conexión personalizado. Las actividades de los empleados y las máquinas de la oficina remota, los datos de instalaciones y las métricas empresariales se compilan, se almacenan, se prueba su conformidad con las normas y se distribuyen según las directivas de la empresa y la legislación regional.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
