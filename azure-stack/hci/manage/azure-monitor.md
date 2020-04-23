---
title: Supervisión de Azure Stack HCI con Azure Monitor
description: Supervise servidores y configure alertas con Azure Monitor desde Windows Admin Center.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 04/03/2020
ms.openlocfilehash: 9dcb6050b4980b476d15552c92ff5445c72bbc55
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "80750922"
---
# <a name="monitor-azure-stack-hci-with-azure-monitor"></a>Supervisión de Azure Stack HCI con Azure Monitor

> Se aplica a: Windows Server 2019

[Azure Monitor](/azure/azure-monitor/overview) recopila, analiza y actúa con los datos de telemetría de una variedad de recursos entre los que se incluyen servidores y máquinas virtuales Windows, tanto en el entorno local como en la nube. Aunque Azure Monitor extrae datos de las máquinas virtuales y de otros recursos de Azure, este artículo se centra en cómo funciona Azure Monitor con servidores locales y máquinas virtuales que se ejecutan en Azure Stack HCI y, concretamente, con Windows Admin Center.

## <a name="how-does-azure-monitor-work"></a>¿Cómo funciona Azure Monitor?
:::image type="content" source="media/monitor/azure-monitor-diagram.png" alt-text="Diagrama del funcionamiento de Azure Monitor" border="false":::
Los datos generados desde servidores locales de Windows Server se recopilan en un área de trabajo de Log Analytics en Azure Monitor. En un área de trabajo puede habilitar varias soluciones de supervisión: conjuntos de lógica que proporcionan información para un escenario determinado. Por ejemplo, Azure Update Management, Azure Security Center y Azure Monitor para VM son soluciones de supervisión que se pueden habilitar dentro de un área de trabajo.

Cuando se habilita una solución de supervisión en un área de trabajo de Log Analytics, todos los servidores que envían notificaciones a esa área de trabajo empiezan a recopilar datos pertinentes para esa solución, de modo que la esta pueda generar información para todos los servidores del área de trabajo.

Para recopilar datos de telemetría en un servidor local e insertarlos en el área de trabajo de Log Analytics, Azure Monitor requiere la instalación de Microsoft Monitoring Agent (MMA). Ciertas soluciones de supervisión también requieren un agente secundario. Por ejemplo, Azure Monitor para VM también depende de un agente ServiceMap para la funcionalidad adicional que proporciona esta solución.

Algunas soluciones, como Azure Update Management, dependen también de Azure Automation, lo que le permite administrar recursos de forma centralizada en entornos que pueden ser de Azure o no. Por ejemplo, Azure Update Management usa Azure Automation para programar y coordinar la instalación de actualizaciones en las máquinas de su entorno, de forma centralizada, desde Azure Portal.

## <a name="what-data-does-azure-monitor-collect"></a>¿Qué datos recopila Azure Monitor?

Todos los datos recopilados por Azure Monitor pueden clasificarse en uno de los dos tipos fundamentales: métricas y registros.

1. Las [métricas](/azure/azure-monitor/platform/data-platform#metrics) son valores numéricos que describen algún aspecto de un sistema en un momento dado. Las métricas son ligeras y capaces de admitir escenarios de tiempo casi real. Los datos recopilados por Azure Monitor aparecen directamente en la página de **información general** de Azure Portal.

    :::image type="content" source="media/monitor/metrics.png" alt-text="Imagen de la ingesta de métricas en el explorador de métricas" border="false":::

2. Los [registros](/azure/azure-monitor/platform/data-platform#logs) contienen distintos tipos de datos organizados en grupos de registros, donde cada tipo tiene diferentes conjuntos de propiedades. Los datos de telemetría, como los eventos y los seguimientos, se almacenan como registros junto con los datos de rendimiento para poder analizarlos de forma combinada. Los datos de registro recopilados por Azure Monitor se pueden analizar con [consultas](/azure/azure-monitor/log-query/log-query-overview) que recuperan, consolidan y analizan rápidamente los datos recopilados. Puede crear y probar consultas mediante [Log Analytics](/azure/azure-monitor/log-query/portals) en Azure Portal y después analizar los datos directamente mediante estas herramientas o guardar las consultas para usarlas con las [visualizaciones](/azure/azure-monitor/visualizations) o las [reglas de alertas](/azure/azure-monitor/platform/alerts-overview).

    :::image type="content" source="media/monitor/logs.png" alt-text="Imagen de la ingesta de registros en Log Analytics" border="false":::

## <a name="how-does-windows-admin-center-enable-you-to-use-azure-monitor"></a>¿Cómo le permite Windows Admin Center usar Azure Monitor?

En Windows Admin Center puede habilitar tres soluciones de supervisión:

- [Azure Monitor para clústeres](#onboard-your-cluster-using-windows-admin-center)
- [Azure Update Management](/windows-server/manage/windows-admin-center/azure/azure-update-management) (en la herramienta **Actualizaciones**)
- Azure Monitor for VM (en la configuración del servidor), anteriormente Virtual Machines Insights

Puede empezar a usar Azure Monitor desde cualquiera de estas herramientas. Si no ha usado Azure Monitor antes, Windows Admin Center aprovisionará automáticamente un área de trabajo de Log Analytics (y una cuenta de Azure Automation, si es necesario), e instalará y configurará Microsoft Monitoring Agent en el servidor de destino. A continuación, se instalará la solución correspondiente en el área de trabajo.

Por ejemplo, si primero va a la herramienta **Actualizaciones** para configurar Azure Update Management, Windows Admin Center hará lo siguiente:

1. Instalará Microsoft Monitoring Agent en la máquina.
2. Creará el área de trabajo de Log Analytics y la cuenta de Azure Automation (porque en este caso se necesita una cuenta de Azure Automation).
3. Instalará la solución Update Management en el área de trabajo recién creada.

Si desea agregar otra solución de supervisión desde Windows Admin Center en el mismo servidor, Windows Admin Center simplemente instalará esa solución en el área de trabajo existente a la que está conectado el servidor. Windows Admin Center instalará además cualquier otro agente necesario.

Si se conecta a un servidor diferente, pero ya ha configurado un área de trabajo de Log Analytics (ya sea a través de Windows Admin Center o manualmente en Azure Portal), también podrá instalar Microsoft Monitoring Agent en el servidor y conectarlo a un área de trabajo existente. Al conectar un servidor a un área de trabajo, esta empieza automáticamente a recopilar datos y generar informes para las soluciones instaladas en esa área de trabajo.

## <a name="azure-monitor-for-virtual-machines-aka-virtual-machine-insights"></a>Azure Monitor for VM (anteriormente Virtual Machine Insights)

Al configurar Azure Monitor para VM en **Configuración del servidor**, Windows Admin Center habilita la solución Azure Monitor para VM, también conocida como Virtual Machine Insights. Esta solución le permite supervisar el estado y los eventos del servidor, crear alertas de correo electrónico, obtener una vista consolidada del rendimiento del servidor en todo el entorno y visualizar las aplicaciones, sistemas y servicios conectados a un servidor determinado.

> [!NOTE]
> A pesar de su nombre, Virtual Machine Insights funciona para servidores físicos y máquinas virtuales.

Con la oferta de 5 GB gratis de datos al mes por cliente de Azure Monitor, puede probar fácilmente un servidor o dos sin preocuparse de pagar nada. Siga leyendo para conocer más ventajas de la incorporación de servidores en Azure Monitor como, por ejemplo, la obtención de una vista consolidada del rendimiento de los sistemas en los servidores de su entorno.

## <a name="onboard-your-cluster-using-windows-admin-center"></a>Incorporación del clúster mediante Windows Admin Center

La manera más sencilla de incorporar el clúster a Azure Monitor es mediante el flujo de trabajo automatizado de Windows Admin Center que configura el Servicio de mantenimiento y Log Analytics y, a continuación, instala Microsoft Monitoring Agent.

:::image type="content" source="media/monitor/onboarding.gif" alt-text="Imagen de la incorporación del clúster en Azure Monitor":::

En la página de información general de una conexión del servidor, haga clic en el botón nuevo **Administrar alertas** o vaya a **Configuración del servidor > Supervisión y alertas**. En esta página, incorpore el servidor a Azure Monitor haciendo clic en **Configurar** y completando el panel de configuración. Admin Center se encarga de aprovisionar el área de trabajo de Azure Log Analytics, instalar el agente necesario y asegurarse de que la solución Virtual Machine Insights está configurada. Una vez terminado, el servidor enviará los datos del contador de rendimiento a Azure Monitor, lo que le permitirá ver y crear alertas de correo electrónico basadas en este servidor, desde Azure Portal.

## <a name="onboard-your-cluster-manually-using-powershell"></a>Incorporación manual del clúster mediante PowerShell

Si prefiere incorporar el clúster manualmente, siga estos pasos.

### <a name="configure-health-service"></a>Configuración del Servicio de mantenimiento

Lo primero que debe hacer es configurar el clúster. Como sabe, el [Servicio de mantenimiento](/windows-server/failover-clustering/health-service-overview) mejora la supervisión diaria y la experiencia operativa de los clústeres que ejecutan Espacios de almacenamiento directo.

Como hemos visto anteriormente, Azure Monitor recopila registros de cada nodo que se está ejecutando en el clúster. Por lo tanto, tenemos que configurar el Servicio de mantenimiento para que escriba en un canal de eventos, que es:

```
Event Channel: Microsoft-Windows-Health/Operational
Event ID: 8465
```

Para configurar el Servicio de mantenimiento, ejecute:

```PowerShell
get-storagesubsystem clus* | Set-StorageHealthSetting -Name "Platform.ETW.MasTypes" -Value "Microsoft.Health.EntityType.Subsystem,Microsoft.Health.EntityType.Server,Microsoft.Health.EntityType.PhysicalDisk,Microsoft.Health.EntityType.StoragePool,Microsoft.Health.EntityType.Volume,Microsoft.Health.EntityType.Cluster"
```

Al ejecutar el cmdlet anterior, se indica a la configuración de mantenimiento que empiece a escribir eventos en el canal de eventos *Microsoft-Windows-Health/Operational*.

### <a name="configure-log-analytics"></a>Configuración de Log Analytics

Ahora que ha configurado el registro correcto en el clúster, el siguiente paso es configurar correctamente Log Analytics.

A modo de información general, [Azure Log Analytics](/azure/azure-monitor/platform/agent-windows) puede recopilar datos directamente de los equipos Windows físicos o virtuales en un centro de datos o en otro entorno en la nube dentro de un único repositorio para llevar a cabo una correlación y un análisis detallados.

Para comprender la configuración compatible, revise los [sistemas operativos Windows admitidos](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) y la [configuración del firewall de red](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

#### <a name="login-in-to-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en Azure Portal en [https://portal.azure.com](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

#### <a name="create-a-workspace"></a>Crear un área de trabajo

Para obtener más detalles sobre los pasos que se indican a continuación, consulte la [documentación de Azure Monitor](/azure/azure-monitor/learn/quick-collect-windows-computer).

1. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.

    :::image type="content" source="media/monitor/azure-portal-01.png" alt-text="Azure Portal":::

2. Haga clic en **Crear** y, a continuación, seleccione opciones para los elementos siguientes:

   * Escriba el nombre del nuevo **área de trabajo de Log Analytics**, como por ejemplo *DefaultLAWorkspace*. 
   * Seleccione una **suscripción** a la que vincularlo en la lista desplegable si la opción predeterminada seleccionada no es adecuada.
   * Para **Grupo de recursos**, seleccione un grupo de recursos existente que contenga una o más máquinas virtuales de Azure.

    :::image type="content" source="media/monitor/create-loganalytics-workspace-02.png" alt-text="Creación de una hoja de recursos de Log Analytics":::

3. Después de proporcionar la información necesaria en el panel **Área de trabajo de Log Analytics**, haga clic en **Aceptar**.  

Mientras se comprueba la información y se crea el espacio de trabajo, puede realizar un seguimiento de su progreso en **Notificaciones** en el menú.

#### <a name="obtain-workspace-id-and-key"></a>Obtención de la clave y el identificador de área de trabajo
Antes de instalar Microsoft Monitoring Agent para Windows, necesita la clave y el identificador del área de trabajo de Log Analytics.  El asistente de configuración necesita esta información para configurar el agente de la forma adecuada y asegurarse de que puede comunicarse correctamente con Log Analytics.  

1. En Azure Portal, haga clic en **Todos los servicios**, en la esquina superior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
2. En la lista de áreas de trabajo de Log Analytics, seleccione *DefaultLAWorkspace* (creada antes).
3. Seleccione **Configuración avanzada**.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Configuración avanzada de Log Analytics":::
4. Seleccione **Orígenes conectados** y **Servidores Windows**.
5. Encontrará los valores a la derecha de **Id. del área de trabajo** y **Clave principal**. Guarde ambos temporalmente y cópielos y péguelos en el editor que prefiera por ahora.

### <a name="installing-the-mma-on-windows"></a>Instalación de Microsoft Monitoring Agent en Windows
En los siguientes pasos se instala y configura Microsoft Monitoring Agent.

> [!IMPORTANT]
> Asegúrese de instalar este agente en cada servidor del clúster y de indicar que desea que el agente se ejecute en el inicio de Windows.

1. En la página **Servidores Windows**, seleccione la versión de **Descargar el agente de Windows** que descargar según la arquitectura del procesador del sistema operativo Windows.
2. Ejecute el programa de instalación para instalar al agente en el equipo.
3. En la página **principal**, haga clic en **Siguiente**.
4. En la página **Términos de licencia**, lea la licencia y haga clic en **Acepto**.
5. En la página e **Carpeta de destino**, cambie o mantenga la carpeta de instalación predeterminada y haga clic en **Siguiente**.
6. En la página **Opciones de instalación del agente**, elija la opción para conectar el agente a Azure Log Analytics y luego haga clic en **Siguiente**.
7. En la página **Azure Log Analytics**, pegue el **identificador del área de trabajo** y la **clave del área de trabajo (clave principal)** que copió anteriormente. Si el equipo necesita comunicarse a través de un servidor proxy con el servicio de Log Analytics, haga clic en **Avanzado** y proporcione la dirección URL y el número de puerto del servidor proxy. Si el servidor proxy requiere autenticación, escriba el nombre de usuario y la contraseña para autenticar con el servidor proxy y, luego, haga clic en **Siguiente**.  
8. Haga clic en **Siguiente** cuando haya terminado de proporcionar las opciones de configuración necesarias.
    :::image type="content" source="media/monitor/log-analytics-mma-setup-laworkspace.png" alt-text="pegar identificador del área de trabajo y clave principal":::
9. En la página **Preparado para instalar**, revise las opciones seleccionadas y haga clic en **Instalar**.
10. En la página **La configuración finalizó correctamente**, haga clic en **Finalizar**.

Una vez completado el proceso, el **Agente de administración de Microsoft** aparece en el **Panel de control**. Puede revisar la configuración y comprobar que el agente esté conectado a Log Analytics. Al conectarse, en la pestaña **Azure Log Analytics**, el agente muestra un mensaje que indica: **Microsoft Monitoring Agent se ha conectado correctamente al servicio Microsoft Log Analytics.**

:::image type="content" source="media/monitor/log-analytics-mma-laworkspace-status.png" alt-text="Estado de la conexión de MMA en Log Analytics":::

Para comprender la configuración compatible, revise los [sistemas operativos Windows admitidos](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) y la [configuración del firewall de red](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements).

## <a name="setting-up-alerts-using-windows-admin-center"></a>Configuración de alertas mediante Windows Admin Center

Una vez que haya conectado el servidor a Azure Monitor, puede usar los hipervínculos inteligentes que se encuentran en la página **Configuración > Supervisión y alertas** para ir a Azure Portal. En Windows Admin Center, puede configurar fácilmente las alertas predeterminadas que se aplicarán a todos los servidores del área de trabajo de Log Analytics. Windows Admin Center habilita automáticamente la recopilación de contadores de rendimiento, por lo que puede [crear una nueva alerta](/azure/azure-monitor/platform/alerts-log) mediante la personalización de una de las muchas consultas predefinidas o escribir las suyas propias.

:::image type="content" source="media/monitor/setup1.gif" alt-text="Captura de pantalla de la configuración de alertas":::

Estas son las alertas y sus condiciones predeterminadas que puede modificar:

| Nombre de la alerta                | Condición predeterminada                                  |
|---------------------------|----------------------------------------------------|
| Uso de CPU           | Más de 85 % durante 10 minutos                            |
| Uso de la capacidad del disco | Más de 85 % durante 10 minutos                            |
| Uso de memoria        | Memoria disponible inferior a 100 MB durante 10 minutos   |
| Latido                 | Menos de 2 latidos durante 5 minutos                   |
| Error crítico del sistema     | Cualquier alerta crítica en el registro de eventos del sistema del clúster |
| Alerta del Servicio de mantenimiento      | Cualquier error del servicio de mantenimiento del clúster            |

Una vez configuradas las alertas en Windows Admin Center, puede ver las alertas en el área de trabajo de Log Analytics en Azure.

:::image type="content" source="media/monitor/setup2.gif" alt-text="Captura de pantalla de la visualización de alertas":::

### <a name="collecting-event-and-performance-data"></a>Recopilación de datos de eventos y rendimiento

Log Analytics puede recopilar eventos de los registros de eventos de Windows, así como de los contadores de rendimiento que especifique para los informes y análisis a largo plazo, y actuar cuando se detecte una condición determinada. Siga estos pasos para configurar la colección de eventos desde el Registro de eventos de Windows, así como desde varios contadores de rendimiento comunes, para empezar.  

1. En Azure Portal, haga clic en **Más servicios**, en la esquina inferior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
2. Seleccione **Configuración avanzada**.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Configuración avanzada de Log Analytics":::
3. Seleccione **Datos** y, a continuación, **Registros de eventos de Windows**.  
4. Aquí, para agregar el canal de eventos del Servicio de mantenimiento, escriba el nombre que aparece a continuación y haga clic en el signo más **+** .  
   ```
   Event Channel: Microsoft-Windows-Health/Operational
   ```
5. En la tabla, compruebe los niveles de gravedad **Error** y **Advertencia**.   
6. Haga clic en **Guardar** en la parte superior de la página para guardar la configuración.
7. Seleccione **Windows Performance Counters** (Contadores de rendimiento de Windows) para habilitar la recopilación de contadores de rendimiento en un equipo Windows. 
8. La primera vez que se configuran los contadores de rendimiento Windows para un área de trabajo de Log Analytics nueva, se ofrece la opción de crear rápidamente varios contadores comunes. Se muestran todos con una casilla junto a cada uno.
    :::image type="content" source="media/monitor/windows-perfcounters-default.png" alt-text="Contadores de rendimiento predeterminados de Windows seleccionados":::
    Haga clic en **Agregar los contadores de rendimiento seleccionados**.  Se agregan con el valor preestablecido de un intervalo de ejemplo de recopilación de diez segundos.  
9. Haga clic en **Guardar** en la parte superior de la página para guardar la configuración.

## <a name="create-queries-and-alerts-based-on-log-data"></a>Creación de consultas y alertas basadas en los datos de registro

Si ha llegado hasta este paso, el clúster debería enviar los registros y contadores de rendimiento a Log Analytics. El siguiente paso es crear reglas de alertas que ejecutan automáticamente búsquedas de registros a intervalos regulares. Si los resultados de la búsqueda de registros coinciden con determinados criterios, se desencadena una alerta que le envía una notificación de correo electrónico o de texto. Vamos a explorar esto a continuación.

### <a name="create-a-query"></a>Creación de una consulta

En primer lugar, abra el portal de **búsqueda de registros**.

1. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Monitor**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Monitor**.
2. En el menú de navegación de **Monitor**, seleccione **Log Analytics** y, a continuación, seleccione un área de trabajo.

La forma más rápida de recuperar algunos datos con los que trabajar es con una consulta simple que devuelve todos los registros en una tabla. Escriba las consultas siguientes en el cuadro de búsqueda y haga clic en el botón de búsqueda.  

```
Event
```

Se devuelven los datos en la vista de lista predeterminada y puede ver el número total de registros que se han devuelto.

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-01.png" alt-text="Captura de pantalla de una consulta simple":::

En el lado izquierdo de la pantalla está el panel de filtros que le permite agregar filtros a la consulta sin necesidad de modificarla directamente.  Se muestran varias propiedades de registro para ese tipo de registro y puede seleccionar uno o más valores de propiedad para restringir los resultados de búsqueda.

Active la casilla situada junto a **Error** en **EVENTLEVELNAME** o escriba lo siguiente para limitar los resultados a los eventos de error.

```
Event | where (EventLevelName == "Error")
```

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-02.png" alt-text="Captura de pantalla de filtros":::

Una vez que haya realizado las consultas adecuadas para los eventos que le interesan, guárdelas para el paso siguiente.

### <a name="create-alerts"></a>Creación de alertas
Ahora, vamos a examinar un ejemplo de creación de una alerta.

1. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
2. En el panel izquierdo, seleccione **Alertas** y, a continuación, haga clic en **Nueva regla de alertas** en la parte superior de la página para crear una nueva alerta.
    :::image type="content" source="media/monitor/alert-rule-02.png" alt-text="Captura de pantalla de creación de una nueva regla de alertas":::
3. Como primer paso, en la sección **Crear alerta**, seleccione el área de trabajo de Log Analytics como el recurso, ya que se trata de una señal de alerta basada en el registro.  Filtre los resultados seleccionando la **Suscripción** específica en la lista desplegable si tiene más de una, la cual contiene el área de trabajo de Log Analytics creada anteriormente.  Filtre el **Tipo de recurso** seleccionando **Log Analytics** en la lista desplegable.  Por último, seleccione el **recurso** **DefaultLAWorkspace** y haga clic en **Listo**.
    :::image type="content" source="media/monitor/alert-rule-03.png" alt-text="Captura de pantalla del paso 1 de creación de una nueva regla de alertas":::
4. En la sección **Criterios de alerta**, haga clic en **Agregar criterios** para seleccionar la consulta guardada y, a continuación, especifique la lógica que sigue la regla de alertas.
5. Configure la alerta con la siguiente información:  
   a. En la lista desplegable **Basado en**, seleccione **Unidades métricas**.  Una medida de métricas creará una alerta por cada objeto de la consulta con un valor que supere el umbral especificado.  
   b. Para **Condición**, seleccione **Mayor que** y especifique un umbral.  
   c. A continuación, defina cuándo desencadenar la alerta. Por ejemplo, puede seleccionar **Infracciones consecutivas** y, en la lista desplegable, seleccionar **Mayor que** y escribir un valor de 3.  
   d. En la sección Evaluation based on (Evaluación basada en), modifique el valor de **Período** a **30** minutos y el de **Frecuencia** a 5. La regla se ejecutará cada cinco minutos y devolverá los registros que se crearon dentro de los últimos 30 minutos desde la hora actual.  El hecho de establecer el período de tiempo en una ventana más amplia justifica la posibilidad de latencia en los datos y garantiza que la consulta devuelve datos para evitar un falso negativo en aquellos casos en los que la alerta nunca se activa.  
6. Haga clic en **Listo** para finalizar la regla de alertas.
    :::image type="content" source="media/monitor/alert-signal-logic-02.png" alt-text="Captura de pantalla de la configuración de la señal de alerta":::
7. Ahora, en el segundo paso, proporcione un nombre para la alerta en el campo **Nombre de la regla de alertas**, como **Alerta para todos los eventos de error**.  Especifique una **Descripción** con los detalles específicos de la alerta y seleccione **Crítico (Gravedad 0)** para el valor **Gravedad** de las opciones proporcionadas.
8. Para activar inmediatamente la regla de alertas en la creación, acepte el valor predeterminado de **Habilitar regla tras la creación**.
9. En el paso tercero y último, especifique un **Grupo de acciones**, lo que garantiza que se realizan las mismas acciones cada vez que se desencadena una alerta y se puede utilizar para cada regla que se define. Configure un nuevo grupo de acciones con la información siguiente:  
   a. Seleccione **Nuevo grupo de acciones** y aparecerá el panel **Agregar grupo de acciones**.  
   b. En **Nombre del grupo de acciones**, especifique un nombre, como **Notificar las operaciones de TI -** y un **Nombre corto**, como **itops-n**.  
   c. Compruebe que los valores predeterminados de **Suscripción** y **Grupo de recursos** son correctos. Si no es así, seleccione los valores correctos en la lista desplegable.
   d. En la sección Acciones, especifique un nombre para la acción, como **Enviar correo electrónico** y en **Tipo de acción**, seleccione **Correo electrónico/SMS/Push/Voz** en la lista desplegable. El panel de propiedades **Correo electrónico/SMS/Push/Voz** se abrirá a la derecha para proporcionar información adicional.  
   e. En el panel **Correo electrónico/SMS/Push/Voz**, seleccione y configure sus preferencias. Por ejemplo, habilite **Correo electrónico** y proporcione una dirección SMTP válida de correo electrónico a la que entregar el mensaje.  
   f. Haga clic en **Aceptar** para guardar los cambios.<br><br> 

    :::image type="content" source="media/monitor/action-group-properties-01.png" alt-text="Captura de pantalla de creación de un grupo de acciones":::

10. Seleccione **Aceptar** para crear el grupo de acciones. 
11. Haga clic en **Crear regla de alertas** para finalizar la regla de alertas. Se iniciará la ejecución de inmediato.
    :::image type="content" source="media/monitor/alert-rule-01.png" alt-text="Captura de pantalla de creación de una nueva regla de alertas":::

### <a name="example-alert"></a>Alerta de ejemplo

Como referencia, este es el aspecto de una alerta de ejemplo en Azure.

:::image type="content" source="media/monitor/alert.gif" alt-text="Captura de pantalla de alerta de Azure":::

A continuación se muestra un ejemplo del correo electrónico que enviará Azure Monitor:

:::image type="content" source="media/monitor/warning.png" alt-text="Captura de pantalla de un ejemplo de correo electrónico de alerta":::

## <a name="create-custom-kusto-queries-in-log-analytics"></a>Creación de consultas personalizadas de Kusto en Log Analytics

También puede [escribir consultas de registro personalizadas](/azure/azure-monitor/log-query/get-started-queries) en Azure Monitor mediante el lenguaje de consulta de Kusto para recopilar datos de una o varias máquinas virtuales.

## <a name="get-a-consolidated-view-across-multiple-servers"></a>Obtención de una vista consolidada en varios servidores

Si incorpora varios servidores a una sola área de trabajo de Log Analytics dentro de Azure Monitor, puede obtener una vista consolidada de todos estos servidores desde la solución [Virtual Machines Insights](/azure/azure-monitor/insights/vminsights-overview) de Azure Monitor. (Tenga en cuenta que solo las pestañas "Rendimiento" y "Asignado a" de Virtual Machines Insights para Azure Monitor funcionarán con servidores locales: la pestaña Mantenimiento solo funciona con máquinas virtuales de Azure). Para verlo en Azure Portal, vaya a **Azure Monitor > Virtual Machines** (en Información detallada) y vaya a las pestañas **Rendimiento** o **Asignado a**.

## <a name="visualize-connected-services"></a>Visualización de servicios conectados

Cuando Windows Admin Center incorpora un servidor en la solución Virtual Machine Insights dentro de Azure Monitor, también se activa una funcionalidad llamada [Service Map](/azure/azure-monitor/insights/service-map). Esta funcionalidad detecta automáticamente los componentes de la aplicación y asigna la comunicación entre los servicios para que pueda visualizar fácilmente las conexiones entre los servidores con gran detalle desde Azure Portal. Para encontrarla, vaya a Azure Portal y seleccione **Azure Monitor > Virtual Machines** (en Información detallada) y vaya a la pestaña **Asignado a**.

> [!NOTE]
> Actualmente, las visualizaciones de Virtual Machine Insights para Azure Monitor se ofrecen en seis regiones públicas.  Para obtener la información más reciente, consulte la [documentación de Azure Monitor para VM](/azure/azure-monitor/insights/vminsights-onboard#log-analytics). Debe implementar el área de trabajo de Log Analytics en una de las regiones admitidas para obtener las ventajas adicionales que proporciona la solución Virtual Machine Insights que se ha descrito anteriormente.

## <a name="disabling-monitoring"></a>Deshabilitar la supervisión

Para desconectar completamente el servidor del área de trabajo de Log Analytics, desinstale Microsoft Monitoring Agent. Esto significa que este servidor ya no enviará datos al área de trabajo y que todas las soluciones instaladas en esa área de trabajo dejarán de recopilar y procesar los datos de ese servidor. Sin embargo, esto no afecta al área de trabajo en sí; todos los recursos que envían notificaciones a esa área de trabajo seguirán haciéndolo. Para desinstalar el agente MMA en WAC, vaya a **Aplicaciones y características**, busque **Microsoft Monitoring Agent** y haga clic en **Desinstalar**.

Si desea desactivar una solución específica dentro de un área de trabajo, tendrá que [quitar la solución de supervisión de Azure Portal](/azure/azure-monitor/insights/solutions#remove-a-management-solution). La eliminación de una solución de supervisión significa que la información creada por esa solución ya no se generará para _todos_ los servidores que envían notificaciones a esa área de trabajo. Por ejemplo, si desinstala la solución Azure Monitor para VM, ya no verá más la información detallada sobre el rendimiento de las máquinas virtuales o servidores de ninguna de las máquinas conectadas al área de trabajo.

## <a name="next-steps"></a>Pasos siguientes

Para ver otros temas relacionados, consulte también:

- [Más información sobre la integración de Azure con Windows Admin Center](/windows-server/manage/windows-admin-center/azure/)
- [Uso de Azure Monitor para enviar correos electrónicos sobre errores del Servicio de mantenimiento](/windows-server/storage/storage-spaces/configure-azure-monitor)
