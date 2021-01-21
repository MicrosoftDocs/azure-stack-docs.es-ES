---
title: Uso de DISKSPD para probar el rendimiento de almacenamiento de carga de trabajo
description: En este tema se proporcionan instrucciones sobre cómo usar DISKSPD para probar el rendimiento del almacenamiento de la carga de trabajo.
author: jasonnyi
ms.author: jasonyi
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 8d8a78d0a5faaa3c041e17c3c38f208132f19834
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254796"
---
# <a name="use-diskspd-to-test-workload-storage-performance"></a>Uso de DISKSPD para probar el rendimiento del almacenamiento de carga de trabajo

>Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se proporcionan instrucciones sobre cómo usar DISKSPD para probar el rendimiento del almacenamiento de la carga de trabajo. Tiene una configuración de clúster de Azure Stack HCI lista para su uso. Genial, pero, ¿cómo sabe si obtiene las métricas de rendimiento prometidas, ya sea en cuanto a latencia, rendimiento o IOPS? Aquí es donde resulta interesante activar DISKSPD. Después de leer este tema, sabrá cómo ejecutar DISKSPD, comprender un subconjunto de parámetros, interpretar la salida y obtener un reconocimiento general de las variables que afectan al rendimiento del almacenamiento de la carga de trabajo.

## <a name="what-is-diskspd"></a>¿Qué es DISKSPD?
DISKSPD es una herramienta de línea de comandos que genera E/S para realizar micropruebas comparativas. Genial, ¿qué significan todos estos términos? Cualquier persona que configure un clúster de Azure Stack HCI o un servidor físico tiene un motivo. Podría ser configurar un entorno de hospedaje web o ejecutar escritorios virtuales para empleados. Sea cual sea el caso de uso real, es probable que quiera simular una prueba antes de implementar la aplicación real. Sin embargo, la prueba de la aplicación en un escenario real suele ser difícil: aquí es donde entra en juego DISKSPD.

DISKSPD es una herramienta que se puede personalizar para crear sus propias cargas de trabajo sintéticas y probar la aplicación antes de la implementación. Lo más interesante de la herramienta es que ofrece la libertad de configurar y ajustar los parámetros para crear un escenario específico similar a la carga de trabajo real. DISKSPD le permite hacerse una idea de lo que puede hacer el sistema antes de la implementación. En esencia, DISKSPD, simplemente, emite un conjunto de operaciones de lectura y escritura.

Ahora ya sabe qué es DISKSPD, pero ¿cuándo debe usarlo? DISKSPD tiene dificultades para emular cargas de trabajo complejas. Sin embargo, DISKSPD es excelente cuando la carga de trabajo no se aproxima estrechamente mediante una copia de archivos de un solo subproceso y se necesita una herramienta sencilla que genere resultados de base de referencia aceptables.

## <a name="quick-start-install-and-run-diskspd"></a>Inicio rápido: instalación y ejecución de DISKSPD
Sin más demora, empecemos:

1. En el equipo de administración, abra PowerShell como administrador para conectarse al equipo de destino que quiere probar mediante DISKSPD y, a continuación, escriba el siguiente comando y presione Entrar.

     ```powershell
     Enter-PSSession -ComputerName <TARGET_COMPUTER_NAME>
    ```

    En este ejemplo, se está ejecutando una máquina virtual (VM) denominada "nodo1".

1. Para descargar la herramienta DISKSPD, escriba los siguientes comandos y presione Entrar:

     ```powershell
     $client = new-object System.Net.WebClient
    ```

     ```powershell
     $client.DownloadFile("https://github.com/Microsoft/diskspd/releases/download/v2.0.21a/DiskSpd-2.0.21a.zip","<ENTER_PATH>\DiskSpd-2.0.21a.zip")
    ```

1. Utilice el comando siguiente para descomprimir el archivo descargado:

     ```powershell
     Expand-Archive -LiteralPath <ENTERPATH>\DiskSpd-2.0.21a.zip -DestinationPath C:\DISKSPD
    ```

1. Cambie el directorio al directorio DISKSPD y busque el archivo ejecutable adecuado para el sistema operativo Windows que se está ejecutando en el equipo de destino.

    En este ejemplo, se usa la versión amd64.

    > [!NOTE]
    > También puede descargar la herramienta DISKSPD directamente desde el [repositorio de GitHub](https://github.com/microsoft/diskspd) que contiene el código abierto y una página wiki que detalla todos los parámetros y especificaciones. En el repositorio, en **Versiones**, seleccione el vínculo para descargar automáticamente el archivo ZIP.

    En el archivo ZIP, verá tres subcarpetas: amd64 (sistemas de 64 bits), x86 (sistemas de 32 bits) y ARM64 (sistemas de ARM). Estas opciones le permiten ejecutar la herramienta en todas las versiones de cliente o servidor de Windows.

    :::image type="content" source="media/diskspd/download-directory.png" alt-text="Directorio para descargar el archivo ZIP de DISKSPD." lightbox="media/diskspd/download-directory.png":::

1. Ejecute DISKSPD con el siguiente comando de PowerShell. Reemplace todo el contenido entre corchetes, incluidos los propios corchetes, con la configuración adecuada.

    ```powershell
     .\[INSERT_DISKSPD_PATH] [INSERT_SET_OF_PARAMETERS] [INSERT_CSV_PATH_FOR_TEST_FILE] > [INSERT_OUTPUT_FILE.txt]
    ```

    Este es un comando de ejemplo que puede ejecutar:

    ```powershell
     .\diskspd -t2 -o32 -b4k -r4k -w0 -d120 -Sh -D -L -c5G C:\ClusterStorage\test01\targetfile\IO.dat > test01.txt
    ```

    > [!NOTE]
    > Si no tiene un archivo de prueba, use el parámetro **-c** para crear uno. Si usa este parámetro, asegúrese de incluir el nombre del archivo de prueba al definir la ruta de acceso. Por ejemplo: [INSERT_CSV_PATH_FOR_TEST_FILE] = C:\ClusterStorage\CSV01\IO.dat. En el comando de ejemplo, IO.dat es el nombre de archivo de prueba y test01.txt es el nombre de archivo de salida de DISKSPD.

## <a name="specify-key-parameters"></a>Especificación de parámetros clave
¿A que ha sido fácil? Lamentablemente, eso no es todo. Analicemos lo que hemos hecho. En primer lugar, hay varios parámetros con los que puede jugar y pueden ser muy específicos. Sin embargo, usamos el siguiente conjunto de parámetros de base de referencia:

> [!NOTE]
> Los parámetros de DISKSPD distinguen entre mayúsculas y minúsculas.

**-t2**: indica el número de subprocesos por archivo de destino o de prueba. Este número suele basarse en el número de núcleos de CPU. En este caso, se usaron dos subprocesos para probar todos los núcleos de CPU.

**-o32**: indica el número de solicitudes de E/S pendientes por destino por subproceso. Esto también se conoce como la profundidad de la cola y, en este caso, se usaron 32 para probar la CPU.

**-b4K**: indica el tamaño de bloque en bytes, KiB, MiB o GiB. En este caso, se usó el tamaño de bloque de 4K para simular una prueba de E/S aleatoria.

**-r4K**: indica que la E/S aleatoria está alineada con el tamaño especificado en bytes, KiB, MiB, GIB o bloques (invalida el parámetro **-s**). Se usó el tamaño de bytes de 4K común para alinearlo correctamente con el tamaño de bloque.

**-w0**: especifica el porcentaje de operaciones que son solicitudes de escritura (-w0 equivale a 100 % de lectura). En este caso, se usó un 0 % de escrituras para una prueba simple.

**-d120**: esto especifica la duración de la prueba, sin incluir la recuperación ni la preparación. El valor predeterminado es 10 segundos, pero se recomienda usar al menos 60 para cualquier carga de trabajo seria. En este caso, se usaron 120 segundos para minimizar los valores atípicos.

**-Suw**: deshabilita el almacenamiento en caché de escritura de software y hardware (equivalente a **-Sh**).

**-D**: captura las estadísticas de IOPS, como la desviación estándar, en intervalos de milisegundos (por subproceso y destino).

**-L**: mide las estadísticas de latencia.

**-c5g**: establece el tamaño de archivo de muestra que se usa en la prueba. Se puede establecer en bytes, KiB, MiB, GiB o bloques. En este caso, se usó un archivo de destino de 5 GB.

Para obtener una lista completa de parámetros, consulte el [repositorio de GitHub](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

## <a name="understand-the-environment"></a>Descripción del entorno
El rendimiento depende en gran medida del entorno. Por lo tanto, ¿qué es nuestro entorno? Nuestra especificación implica un clúster de Azure Stack HCI con bloque de almacenamiento y Espacios de almacenamiento directo (S2D). Más concretamente, hay cinco VM: DC, nodo1, nodo2, nodo3 y el nodo de administración. El propio clúster es un clúster de tres nodos con una estructura de resistencia reflejada triple. Por lo tanto, se mantienen tres copias de datos. Cada "nodo" del clúster es una VM Standard_B2ms con un límite máximo de IOPS de 1920. Dentro de cada nodo hay cuatro unidades SSD P30 prémium con un límite máximo de IOPS de 5000. Por último, cada unidad SSD tiene 1 TB de memoria.

El archivo de prueba se genera en el espacio de nombres unificado que el Volumen compartido de clúster (CSV) proporciona (C:\ClusteredStorage) para usar todo el grupo de unidades.

>[!NOTE]
> El entorno de ejemplo *no* cuenta con Hyper-V ni con una estructura de virtualización anidada.

Como verá, es totalmente posible alcanzar de forma independiente el valor máximo de IOPS o ancho de banda en VM o el límite de la unidad. Por lo tanto, es importante comprender el tamaño de la VM y el tipo de unidad, ya que ambos tienen un límite máximo de IOPS y de ancho de banda. Esta información ayuda a localizar cuellos de botella y a comprender los resultados de rendimiento. Para obtener más información sobre el tamaño que puede ser adecuado para la carga de trabajo, consulte los siguientes recursos:

- [Tamaños de VM](/azure/virtual-machines/sizes-general?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Tipos de disco](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="understand-the-output"></a>Descripción de la salida
Preparado con los conocimientos de los parámetros y el entorno, está listo para interpretar la salida. En primer lugar, el objetivo de la prueba anterior era agotar las IOPS, independientemente de la latencia. De este modo, puede ver visualmente si alcanza el límite de IOPS artificial dentro de Azure. Si quiere visualizar gráficamente el valor total de IOPS, use Windows Admin Center o el Administrador de tareas.

En el siguiente diagrama se muestra el aspecto del proceso de DISKSPD en nuestro entorno de ejemplo. Muestra un ejemplo de una operación de escritura de 1 MiB desde un nodo que no es de coordinador. La estructura de resistencia de tres vías, junto con la operación de un nodo que no es coordinador, conduce a dos saltos de red, lo que reduce el rendimiento. Si se pregunta qué es un nodo de coordinador, no se preocupe. Lo descubrirá en la sección [Aspectos que se deben tener en cuenta](#things-to-consider). Los cuadrados rojos representan los cuellos de botella de la VM y la unidad.

:::image type="content" source="media/diskspd/environment.png" alt-text="Entorno de ejemplo que se usa para probar el rendimiento con DISKSPD." lightbox="media/diskspd/environment.png":::

Ahora que tiene una descripción visual, vamos a examinar las cuatro secciones principales de la salida del archivo .txt:
1. Configuración de entrada
   
    En esta sección se describe el comando que ejecutó, los parámetros de entrada y los detalles adicionales sobre la serie de pruebas.

    :::image type="content" source="media/diskspd/command-input-parameters.png" alt-text="La salida de ejemplo muestra parámetros de entrada y comandos." lightbox="media/diskspd/command-input-parameters.png":::

1. Detalles de uso de CPU
   
    En esta sección se resalta información como el tiempo de prueba, el número de subprocesos, el número de procesadores disponibles y el uso medio de cada núcleo de CPU durante la prueba. En este caso, hay dos núcleos de CPU con un promedio aproximado del 4,67 % del uso.

    :::image type="content" source="media/diskspd/cpu-details.png" alt-text="Detalles de CPU de ejemplo." lightbox="media/diskspd/cpu-details.png":::

1. Total de E/S
   
    Esta sección contiene tres subsecciones. En la primera sección se resaltan los datos de rendimiento generales, incluidas las operaciones de lectura y escritura. En las secciones segunda y tercera se dividen las operaciones de lectura y escritura en categorías independientes.

    En este ejemplo, puede ver que el número total de E/S fue 234408 durante el espacio de 120 segundos. Por lo tanto, IOPS = 234408/120 = 1953,30. La latencia media fue de 32,763 milisegundos y el rendimiento, de 7,63 MiB/s. A partir de la información anterior, sabemos que un valor de IOPS de 1953,30 se acerca al límite de 1920 IOPS de la VM Standard_B2ms. ¿No se lo cree? Si vuelve a ejecutar esta prueba con parámetros diferentes; p. ej., si aumenta la profundidad de la cola, observará que los resultados siguen estando limitados en este número.

    Las tres últimas columnas muestran la desviación estándar de IOPS en 17,72 (parámetro de -D), la desviación estándar de la latencia a 20,994 milisegundos (del parámetro -L) y la ruta de acceso del archivo.

      :::image type="content" source="media/diskspd/total-io.png" alt-text="En el ejemplo se muestran los datos totales de rendimiento de E/S." lightbox="media/diskspd/total-io.png":::

    A partir de los resultados, puede determinar rápidamente que la configuración del clúster es terrible. Puede ver que alcanza la limitación de VM de 1920 antes que la limitación de SSD de 5000. Si estuviera limitado por el SSD, en lugar de la VM, podría haber aprovechado hasta 20 000 IOPS (4 unidades * 5000) mediante la expansión del archivo de prueba en varias unidades.

    Al final, debe decidir qué valores son aceptables para su carga de trabajo específica. En la figura siguiente se muestran algunas relaciones importantes que le ayudarán a tener en cuenta los inconvenientes:

    :::image type="content" source="media/diskspd/tradeoffs.png" alt-text="Figura que muestra los inconvenientes de las relaciones de la carga de trabajo." lightbox="media/diskspd/tradeoffs.png":::

    La segunda relación de la figura es importante y a veces se conoce como la ley del menor. Esta ley presenta la idea de que hay tres características que rigen el comportamiento del proceso y que solo es necesario cambiar una para influir en las otras dos y, por tanto, en todo el proceso. Por lo tanto, si no está satisfecho con el rendimiento del sistema, tiene tres dimensiones de libertad para influir en él. En nuestro ejemplo, la ley del menor dicta que el valor de IOPS es el "rendimiento" (operaciones entrada y salida por segundo), la latencia es el "tiempo en cola" y la profundidad de la cola es el "inventario".

1. Análisis del percentil de latencia
   
    En esta última sección se detallan las latencias de percentil por tipo de operación de rendimiento de almacenamiento, del valor mínimo al máximo.

    Esta sección es importante porque determina la "calidad" de IOPS. Revela el número de operaciones de E/S que pudieron alcanzar un determinado valor de latencia. Depende de usted decidir cuál es la latencia aceptable para ese percentil.

    Además, los "nueves" hacen referencia al número de nueves. Por ejemplo, "3 nueves" es equivalente al percentil 99. El número de nueves expone el número de operaciones de E/S que se ejecutaron en ese percentil. Finalmente, llegará a un punto en el que ya no tiene sentido tomarse en serio los valores de latencia. En este caso, puede ver que los valores de latencia permanecen constantes después de "4 nueves". En este momento, el valor de latencia se basa en una sola operación de E/S de las 234 408 operaciones.

    :::image type="content" source="media/diskspd/storage-performance.png" alt-text="En el ejemplo se muestran latencias de percentil por tipo de operación de rendimiento de almacenamiento." lightbox="media/diskspd/storage-performance.png":::

## <a name="things-to-consider"></a>Aspectos que se deben tener en cuenta:
Ahora que ha empezado a usar DISKSPD, hay varios aspectos que se deben tener en cuenta para obtener resultados de la prueba reales. Esto incluye prestar mucha atención a los parámetros establecidos, el mantenimiento del espacio de almacenamiento y las variables, la propiedad de CSV y la diferencia entre DISKSPD y la copia de archivos.

### <a name="diskspd-vs-real-world"></a>DISKSPD frente al mundo real
La prueba artificial de DISKSPD ofrece resultados relativamente comparables para la carga de trabajo real. Sin embargo, debe prestar mucha atención a los parámetros que establezca y si coinciden con su escenario real. Es importante comprender que las cargas de trabajo sintéticas nunca representarán perfectamente la carga de trabajo real de la aplicación durante la implementación.

### <a name="preparation"></a>Preparación
Antes de ejecutar una prueba de DISKSPD, hay algunas acciones recomendadas. Esto incluye comprobar el mantenimiento del espacio de almacenamiento, comprobar el uso de los recursos para que otro programa no interfiera con la prueba y preparar el administrador de rendimiento si quiere recopilar datos adicionales. Sin embargo, dado que el objetivo de este tema es ejecutar DISKSPD rápidamente, no profundiza en los detalles de estas acciones. Para obtener más información, consulte [Prueba del rendimiento de los espacios de almacenamiento mediante cargas de trabajo sintéticas en Windows Server](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn894707(v=ws.11)).

### <a name="variables-that-affect-performance"></a>Variables que afectan al rendimiento
El rendimiento del almacenamiento es algo delicado. Es decir, hay muchas variables que pueden afectar al rendimiento. Y, por lo tanto, es probable que se encuentre con un número que no coincida con sus expectativas. A continuación se resaltan algunas de las variables que afectan al rendimiento, aunque no se trata de una lista completa:
- Ancho de banda de red
- Opción de resistencia
- Configuración del disco de almacenamiento: NVME, SSD, HDD
- Búfer de E/S
- Cache
- Configuración de RAID
- Saltos de red
- Velocidades de los ejes del disco duro

### <a name="csv-ownership"></a>Propiedad de CSV
Un nodo se conoce como propietario del volumen o nodo de **coordinación** (un nodo no coordinador sería el nodo que no posee un volumen específico). A cada volumen estándar se le asigna un nodo y los demás nodos pueden acceder a este volumen mediante saltos de red, lo que da lugar a un rendimiento más lento (mayor latencia).

Del mismo modo, un volumen compartido de clústeres (CSV) también tiene un "propietario". Sin embargo, un CSV es "dinámico" en el sentido de que saltará y cambiará la propiedad cada vez que reinicie el sistema (RDP). Como resultado, es importante confirmar que DISKSPD se ejecuta desde el nodo coordinador que posee el CSV. En caso contrario, puede que tenga que cambiar manualmente la propiedad de CSV.

Para confirmar la propiedad de CSV:
1. Compruebe la propiedad ejecutando el siguiente comando de PowerShell:

    ```powershell
     Get-ClusterSharedVolume
    ```

1. Si la propiedad de CSV es incorrecta (por ejemplo, si está en el Nodo1, pero el Nodo2 es el que posee el CSV), mueva el CSV al nodo correcto mediante la ejecución del siguiente comando de PowerShell:

    ```powershell
     Get-ClusterSharedVolume <INSERT_CSV_NAME> | Move-ClusterSharedVolume <INSERT _NODE_NAME>
    ```
### <a name="file-copy-vs-diskspd"></a>Comparación de la copia de archivo y DISKSPD
Algunos usuarios consideran que pueden "probar el rendimiento de almacenamiento" al copiar y pegar un archivo gigantesco y medir el tiempo que tarda ese proceso. Lo más probable es que la razón principal de este enfoque sea que es sencillo y rápido. La idea no está mal, en el sentido de que prueba una carga de trabajo específica, pero es difícil clasificar este método como "prueba del rendimiento de almacenamiento".

Si el objetivo real es probar el rendimiento de la copia de archivos, puede ser una razón perfectamente válida para usar este método. Sin embargo, si el objetivo es medir el rendimiento del almacenamiento, se recomienda no utilizar este método. Puede considerar el proceso de copia de archivos como si usara un conjunto diferente de "parámetros" (por ejemplo, de cola, paralelización, etc.) específico para los servicios de archivo.

En el siguiente resumen breve se explica por qué es posible que el uso de la copia de archivos para medir el rendimiento del almacenamiento no proporcione los resultados que busca:
- **Es posible que las copias de archivos no estén optimizadas.** Se producen dos niveles de paralelismo: interno y externo. Internamente, si la copia de archivo está dirigida a un destino remoto, el motor de CopyFileEx aplica cierta paralelización. Externamente, hay diferentes maneras de invocar el motor de CopyFileEx. Por ejemplo, las copias del Explorador de archivos son de un solo subproceso, pero Robocopy es multiproceso. Por estos motivos, es importante comprender si las implicaciones de la prueba son las que está buscando.
- **Cada copia tiene dos lados.** Cuando, simplemente, copia y pega un archivo, es posible que esté usando dos discos: el de origen y el de destino. Si uno es más lento que el otro, básicamente, mide el rendimiento del disco más lento. Hay otros casos en los que la comunicación entre el origen, el destino y el motor de copia puede afectar al rendimiento de maneras únicas.
    
    Para obtener más información, consulte [Uso de la copia de archivos para medir el rendimiento del almacenamiento](/archive/blogs/josebda/using-file-copy-to-measure-storage-performance-why-its-not-a-good-idea-and-what-you-should-do-instead?epi=je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q&irclickid=_rcvu3tufjwkftzjukk0sohzizm2xiezdpnxvqy9i00&irgwc=1&OCID=AID2000142_aff_7593_1243925&ranEAID=je6NUbpObpQ&ranMID=24542&ranSiteID=je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q&tduid=(ir__rcvu3tufjwkftzjukk0sohzizm2xiezdpnxvqy9i00)(7593)(1243925)(je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q)()).

## <a name="experiments-and-common-workloads"></a>Experimentos y cargas de trabajo comunes
En esta sección se incluyen otros ejemplos, experimentos y tipos de carga de trabajo.

### <a name="confirming-the-coordinator-node"></a>Confirmación del nodo de coordinación
Como se mencionó anteriormente, si la VM que está probando actualmente no posee el CSV, observará una caída del rendimiento (IOPS, rendimiento y latencia), al contrario que al probarla cuando el nodo posee el CSV. Esto se debe a que cada vez que se emite una operación de E/S, el sistema realiza un salto de red al nodo de coordinación para realizar esa operación.

En el caso de una situación reflejada tridireccional de tres nodos, las operaciones de escritura siempre realizan un salto de red porque necesitan almacenar datos en todas las unidades de los tres nodos. Por lo tanto, las operaciones de escritura siempre realizan un salto de red. Sin embargo, si utiliza una estructura de resistencia diferente, esto podría cambiar.

Este es un ejemplo:
- **Ejecución en el nodo local:** .\DiskSpd-2.0.21a\amd64\diskspd.exe -t4 -o32 -b4k -r4k -w0 -Sh -D -L C:\ClusterStorage\test01\targetfile\IO.dat
- **Ejecución fuera del nodo local:** .\DiskSpd-2.0.21a\amd64\diskspd.exe -t4 -o32 -b4k -r4k -w0 -Sh -D -L C:\ClusterStorage\test01\targetfile\IO.dat

En este ejemplo, puede ver claramente en los resultados de la siguiente figura que la latencia disminuye, las IOPS aumentan y el rendimiento crece cuando el nodo de coordinación posee el CSV.

:::image type="content" source="media/diskspd/coordinator-node-data.png" alt-text="Ejemplo que muestra la salida de datos del nodo de coordinación." lightbox="media/diskspd/coordinator-node-data.png":::

### <a name="online-transaction-processing-oltp-workload"></a>Carga de trabajo de procesamiento de transacciones en línea (OLTP)
Las consultas de la carga de trabajo de procesamiento transaccional en línea (OLTP) (actualización, inserción y eliminación) se centran en las tareas orientadas a transacciones. A diferencia del procesamiento analítico en línea (OLAP), OLTP depende de la latencia de almacenamiento. Dado que cada operación emite pocas E/S, lo que le interesa es el número de operaciones por segundo que puede admitir.

Puede diseñar una prueba de carga de trabajo de OLTP para centrarse en un rendimiento de E/S bajo y aleatorio. Para estas pruebas, céntrese en cuánto puede forzar el rendimiento al mismo tiempo que mantiene latencias aceptables.

La opción de diseño básica para esta prueba de carga de trabajo debe incluir, como mínimo:
- Un tamaño de bloque de 8 KB => es similar al tamaño de página que usa SQL Server para sus archivos de datos.
- 70 % lectura, 30 % escritura = > es similar al comportamiento típico de OLTP.

### <a name="online-analytical-processing-olap-workload"></a>Carga de trabajo de procesamiento analítico en línea (OLAP)
Las cargas de trabajo de OLAP se centran en la recuperación y el análisis de datos, lo que permite a los usuarios realizar consultas complejas para extraer datos multidimensionales. Al contrario que en OLTP, estas cargas de trabajo no distinguen la latencia de almacenamiento. Hacen hincapié en la puesta en cola de muchas operaciones sin tener en cuenta el ancho de banda. Como resultado, las cargas de trabajo OLAP suelen generar mayores tiempos de procesamiento.

Puede diseñar una prueba de carga de trabajo de OLAP para centrarse en un rendimiento de E/S grande y secuencial. Para estas pruebas, céntrese en el volumen de datos procesados por segundo, en lugar del número de IOPS. Los requisitos de latencia también son menos importantes, pero esto es subjetivo.

La opción de diseño básica para esta prueba de carga de trabajo debe incluir, como mínimo:
- Tamaño de bloque de 512 KB => es similar al tamaño de E/S común cuando SQL Server carga un lote de 64 páginas de datos para un recorrido de tabla mediante la técnica de lectura anticipada.
- 1 subproceso por archivo = > actualmente, debe limitar las pruebas a un subproceso por archivo, ya que pueden surgir problemas en DISKSPD al probar varios subprocesos secuenciales.
    Si usa más de un subproceso (por ejemplo, dos) y el parámetro **-s**, los subprocesos comenzarán de forma no determinista a emitir operaciones de E/S acumuladas dentro de la misma ubicación. Esto se debe a que cada uno realiza el seguimiento de su propio desplazamiento secuencial.

    Para resolver el problema, existen dos “soluciones”.
    - La primera solución implica el uso del parámetro **-si**. Con este parámetro, ambos subprocesos comparten un único desplazamiento entrelazado para que los subprocesos emitan de forma cooperativa un único patrón secuencial de acceso al archivo de destino. Esto permite que ningún punto del archivo se manipule más de una vez. Sin embargo, dado que todavía compiten para emitir su operación de E/S en la cola, las operaciones pueden llegar desordenadas.

        Esta solución funciona bien si un subproceso recibe una limitación de CPU. Es recomendable interactuar con un segundo subproceso en un segundo núcleo de CPU para ofrecer más E/S de almacenamiento al sistema de CPU para saturarlo aún más.

    - La segunda solución implica el uso de -T\<offset>\. Esto le permite especificar el tamaño del desplazamiento (brecha entre E/S) entre las operaciones de E/S realizadas en el mismo archivo de destino por subprocesos diferentes. Por ejemplo, los subprocesos suelen empezar en el desplazamiento 0, pero esta especificación permite distanciarlos para que no se superpongan. En cualquier entorno multiproceso, los subprocesos, probablemente, estarán en distintas partes del destino de trabajo y esto es una forma de simular esa situación.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información y ejemplos detallados sobre cómo optimizar la configuración de resistencia, consulte también:
- [OLTP y OLAP](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn894707(v=ws.11))
- [Opción de resistencia](https://techcommunity.microsoft.com/t5/storage-at-microsoft/volume-resiliency-and-efficiency-in-storage-spaces-direct/ba-p/425831)