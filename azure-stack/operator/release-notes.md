---
title: Notas de la versión de Azure Stack Hub
description: Notas de la versión para los sistemas integrados de Azure Stack Hub, incluidas las actualizaciones y correcciones de errores.
author: sethmanheim
ms.topic: article
ms.date: 11/11/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: 74b1be3736d21d968fa45135034637d4ca3cd5eb
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546062"
---
# <a name="azure-stack-hub-release-notes"></a>Notas de la versión de Azure Stack Hub

En este artículo se describe el contenido de los paquetes de actualización de Azure Stack Hub. La actualización incluye mejoras y correcciones de la versión más reciente de Azure Stack Hub.

Para tener acceso a las notas de la versión de una versión diferente, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda.

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack Hub. No lo aplique al Kit de desarrollo de Azure Stack (ASDK).
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> Si a su instancia le faltan más de dos actualizaciones de Azure Stack Hub, se considera que no cumple los requisitos. [Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.](azure-stack-servicing-policy.md#keep-your-system-under-support)
::: moniker-end

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

Para obtener ayuda con la solución de problemas de actualizaciones y el proceso de actualización, consulte [Solución de problemas de actualizaciones y revisiones para Azure Stack Hub](azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Descarga de la actualización

Se puede descargar el paquete de actualizaciones de Azure Stack Hub con la [herramienta de descarga de actualizaciones de Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2008"
## <a name="2008-build-reference"></a>Referencia de la compilación 2008

El número de compilación de la actualización 2008 de Azure Stack Hub es **1.2008.13.88**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 2008 de Azure Stack Hub es **Completo**.

El tamaño del paquete de la actualización 2008 es mayor en comparación con el de actualizaciones anteriores. Esto supone que el tiempo de descarga es también mayor. La actualización permanecerá en el estado **Preparando** durante bastante tiempo; los operadores pueden prever que este proceso tardará más que en las actualizaciones anteriores. La actualización 2008 ha tenido los siguientes tiempos de ejecución previstos en nuestras pruebas internas. 4 nodos: 13-20 horas, 8 nodos: 16-26 horas, 12 nodos: 19-32 horas, 16 nodos: 22-38 horas. Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen las cargas de trabajo de inquilino en el sistema, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema. Unos tiempos de ejecución inferiores o superiores al valor esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack Hub, a menos que se produzca un error de actualización. Esta aproximación del tiempo de ejecución es específica de la actualización 2008 y no se debe comparar con otras actualizaciones de Azure Stack Hub.

Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->
- Azure Stack Hub admite ahora el emparejamiento de VNET, que ofrece la posibilidad de conectar redes virtuales sin una aplicación virtual de red (NVA). Para más información, consulte la [nueva documentación del emparejamiento de VNET](../user/virtual-network-peering.md).
- El almacenamiento de blobs de Azure Stack Hub permite ahora a los usuarios usar un blob inmutable. Mediante el establecimiento de directivas inmutables en un contenedor, puede almacenar objetos de datos críticos para la empresa en un estado WORM (escribir una vez, leer muchas). En esta versión, las directivas inmutables solo se pueden establecer a través de la API de REST o los SDK de cliente. Escribir blobs en anexos tampoco es posible en esta versión. Para más información sobre los blobs inmutables, vea [Almacenamiento de datos críticos para la empresa con almacenamiento inmutable](/azure/storage/blobs/storage-blob-immutable-storage).
- Azure Stack Hub Storage admite ahora las API de servicios de Azure Storage, versión 2019-07-07. En el caso de las bibliotecas cliente de Azure que son compatibles con la nueva versión de API de REST, consulte [Herramientas de desarrollo de almacenamiento de Azure Stack Hub](../user/azure-stack-storage-dev.md#azure-client-libraries).
- El proceso de Azure Stack Hub es ahora compatible con las API de Azure Compute versión 2020-06-01, con un subconjunto de las características totales disponibles.
- Versión preliminar de Windows Admin Center que ahora puede conectarse a Azure Stack Hub para proporcionar información detallada sobre la infraestructura durante las operaciones de soporte técnico (se requiere romper el cristal).
- Capacidad de agregar un banner de inicio de sesión al punto de conexión con privilegios (PEP) en el momento de la implementación.
- Se han publicado más banners de **operaciones exclusivas**, que mejoran la visibilidad de las operaciones que se están produciendo actualmente en el sistema y que deshabilitan el inicio de los usuarios (y posterior el error) de cualquier otra operación exclusiva.
- Se han introducido dos nuevos banners en cada página de producto del elemento de Marketplace de Azure Stack Hub. Si se produce un error de descarga de Marketplace, los operadores pueden ver los detalles del error y probar los pasos recomendados para resolver el problema.
- Se ha publicado una herramienta de clasificación para que los clientes proporcionen comentarios. Esto permitirá que Azure Stack Hub mida y optimice la experiencia del cliente.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->
- Se ha implementado la supervisión interna del controlador de red y los agentes de host SLB, por lo que los servicios se corrigen automáticamente si entran en estado detenido.
- Los Servicios de federación de Active Directory (AD FS) recuperan ahora el nuevo certificado de firma de tokens después de que el cliente lo haya cambiado en su propio servidor de AD FS. A fin de aprovechar esta nueva funcionalidad para los sistemas ya configurados, se debe volver a configurar la integración de AD FS. Para más información, consulte [Integración de la identidad de AD FS con el centro de datos de Azure Stack Hub](azure-stack-integrate-identity.md).
- Cambios en el proceso de inicio y apagado en las instancias de rol de la infraestructura y sus dependencias en nodos de unidad de escalado. Con ello se aumenta la confiabilidad del inicio y apagado de Azure Stack Hub.
- El conjunto **AzSScenarios** de la herramienta de validación **Test-AzureStack** se ha actualizado para que los proveedores de servicios en la nube puedan ejecutar este conjunto correctamente con la autenticación multifactor aplicada en todas las cuentas de cliente.
- Mejora de la confiabilidad de las alertas mediante la adición de lógica de supresión para 29 alertas orientadas al cliente durante las operaciones del ciclo de vida.

### <a name="changes"></a>Cambios

- Se ha habilitado la propiedad **supportHttpsTrafficOnly** de tipo de recurso de cuenta de almacenamiento en SRP API versión **2016-01-01** y **2016-05-01**, pero esta propiedad no se admite en Azure Stack Hub.
- Se ha elevado el umbral de alerta de uso de capacidad de volumen del 80 % (advertencia) y el 90 % (crítico) al 90 % (advertencia) y el 95 % (crítico). Para más información, consulte [Alertas de espacio de almacenamiento](azure-stack-manage-storage-shares.md#storage-space-alerts).
- Los pasos de configuración de AD Graph cambian con esta versión. Para más información, consulte [Integración de la identidad de AD FS con el centro de datos de Azure Stack Hub](azure-stack-integrate-identity.md).
- Para estar en línea con los procedimientos recomendados actuales definidos para Windows Server 2019, se han efectuado cambios en Azure Stack Hub para que use una clase o prioridad de tráfico adicional para separar aún más la comunicación entre servidores y mejorar la comunicación del control de los clústeres de conmutación por error. El resultado de estos cambios proporciona una mayor resistencia para la comunicación del clúster de conmutación por error. Esta configuración de clase de tráfico y reserva de ancho de banda se realiza mediante un cambio en los conmutadores de la parte superior del rack (ToR) de la solución de Azure Stack Hub y en el host o los servidores de Azure Stack Hub.

  Tenga en cuenta que estos cambios se agregan en el nivel de host de un sistema de Azure Stack Hub. Póngase en contacto con su OEM para organizar la realización de los cambios necesarios en los conmutadores de red Top-of-rack (ToR). Este cambio de ToR se puede realizar antes o después de actualizar a la versión 2008. Para más información, consulte la [documentación de migración de red](azure-stack-network.md).

  - Los tamaños de máquina virtual compatibles con GPU **NCas_v4 (NVIDIA T4)** se han reemplazado en esta compilación por los tamaños de máquina virtual **NCasT4_v3**, para ser coherentes con Azure. Tenga en cuenta que todavía no están visibles en el portal y solo se pueden usar a través de plantillas de Azure Resource Manager.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Se ha corregido un problema por el que se producía un error al eliminar un NSG de una instancia de NIC que no estaba conectada a una máquina virtual en ejecución.
- Se ha corregido un problema por el que la modificación del valor **IdleTimeoutInMinutes** para una dirección IP pública asociada a un equilibrador de carga ponía la dirección IP pública en estado de error.
- Se ha corregido el cmdlet **Get-AzsDisk** para que devuelva el estado **Conectado** correcto, en lugar de **OnlineMigration**, para los discos administrados conectados.

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack Hub, consulte [Actualizaciones de seguridad de Azure Stack Hub](release-notes-security-updates.md).

## <a name="hotfixes"></a>Revisiones

Azure Stack Hub publica revisiones de forma periódica. A partir de la versión 2005, cuando se actualiza a una nueva versión principal (por ejemplo, 1.2002.x a 1.2005 x), se instalan automáticamente en ella las revisiones más recientes (si existen). A partir de ese momento, si se publica una revisión para una compilación, se debe instalar.

> [!NOTE]
> Las revisiones de Azure Stack Hub son acumulativas; solo tiene que instalar la revisión más reciente para obtener todas las correcciones incluidas en las revisiones anteriores de esa versión.

Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

Las revisiones de Azure Stack Hub solo son aplicables a los sistemas integrados de Azure Stack Hub; no intente instalar revisiones en el ASDK.

### <a name="prerequisites-before-applying-the-2008-update"></a>Requisitos previos: Antes de aplicar la actualización 2008

Cuando se actualiza a una nueva versión principal (por ejemplo, de 1.2005.x a 1.2008.x), se instalan automáticamente las revisiones más recientes (si existen) en la nueva versión principal. A partir de ese momento, si se publica una revisión para una compilación, se debe instalar.

### <a name="after-successfully-applying-the-2008-update"></a>Después de aplicar correctamente la actualización 2008

Después de la instalación de 2008, si se publica posteriormente alguna revisión de la versión 2008, se debe instalar:

- No hay ninguna revisión de Azure Stack Hub disponible para la versión 2008.
::: moniker-end

::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>Referencia de la compilación 2005

El número de compilación de la actualización 2005 de Azure Stack Hub es **1.2005.6.53**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 2005 de Azure Stack Hub es **Completo**.

El tamaño del paquete de la actualización 2005 es mayor en comparación con el de actualizaciones anteriores. Esto supone que el tiempo de descarga es también mayor. La actualización permanecerá en el estado **Preparando** durante bastante tiempo; los operadores pueden prever que este proceso tardará más que en las actualizaciones anteriores. La actualización 2005 ha tenido los siguientes tiempos de ejecución previstos en nuestras pruebas internas. 4 nodos: 13-20 horas, 8 nodos: 16-26 horas, 12 nodos: 19-32 horas, 16 nodos: 22-38 horas. Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen las cargas de trabajo de inquilino en el sistema, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema. Unos tiempos de ejecución inferiores o superiores al valor esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack Hub, a menos que se produzca un error de actualización. Esta aproximación del tiempo de ejecución es específica de la actualización 2005 y no se debe comparar con otras actualizaciones de Azure Stack Hub.

Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->
- Esta compilación ofrece compatibilidad con 3 nuevos tipos de máquinas virtuales de GPU: Tamaños de máquina virtual NCv3 (NVIDIA V100), NVv4 (AMD MI25) y NCas_v4 (NVIDIA T4). Las implementaciones de máquinas virtuales se realizarán correctamente para aquellos que tengan el hardware adecuado y se incorporen al programa de versión preliminar de GPU de Azure Stack Hub. Si está interesado, regístrese en el programa de versión preliminar de GPU en https://aka.ms/azurestackhubgpupreview. Para más información, consulte [aquí](../user/gpu-vms-about.md).
- Esta versión ofrece una nueva característica que habilita una funcionalidad de recuperación autónoma, que detecta los errores, evalúa el efecto y mitiga de forma segura los problemas del sistema. Con esta característica, estamos trabajando para aumentar la disponibilidad del sistema sin intervención manual. Con la versión 2005 y versiones posteriores, los clientes experimentarán una reducción en el número de alertas. Cualquier error en esta canalización no requerirá ninguna acción por parte de los operadores de Azure Stack Hub, a menos que se notifique lo contrario.
- Existe una nueva opción en el portal de administración de Azure Stack Hub para clientes de Azure Stack Hub desconectados o protegidos con aislamiento físico, que permite guardar los registros de forma local. Se pueden almacenar los registros en un recurso compartido de SMB local cuando Azure Stack Hub está desconectado de Azure.
- Ahora, el portal de administración de Azure Stack Hub bloquea determinadas operaciones si una operación del sistema ya está en curso. Por ejemplo, si hay una actualización en curso, no es posible agregar un nuevo nodo de unidad de escalado.
- Esta versión proporciona una mayor coherencia del tejido con Azure en las máquinas virtuales creadas antes de la versión 1910. En la versión 1910, Microsoft anunció que todas las máquinas virtuales recién creadas usarían el protocolo WireServer. Este protocolo permite a los clientes usar el mismo agente de WALA y el mismo agente invitado de Windows que Azure, lo que facilita el uso de imágenes de Azure en Azure Stack Hub. Con esta versión, todas las máquinas virtuales creadas antes de la versión 1910 se migran automáticamente para usar el protocolo WireServer. En este contexto, también se crean máquinas virtuales e implementaciones de extensiones más confiables, y se proporcionan mejoras en el tiempo de actividad de estado estable.
- Azure Stack Hub Storage admite ahora las API de servicios de Azure Storage, versión 2019-02-02. En el caso de las bibliotecas cliente de Azure, estas API son compatibles con la nueva versión de la API REST. Para más información, consulte [Herramientas de desarrollo de Azure Stack Hub Storage](../user/azure-stack-storage-dev.md#azure-client-libraries).
- Azure Stack Hub admite ahora la versión más reciente de [CreateUiDefinition (versión 2)](/azure/azure-resource-manager/managed-applications/create-uidefinition-overview).
- Nueva guía para implementaciones por lotes de máquinas virtuales. Para más información, [consulte este artículo](../operator/azure-stack-capacity-planning-compute.md).
- El elemento Container Linux de CoreOS del Marketplace de Azure Stack Hub [se aproxima a su fecha de vencimiento](https://azure.microsoft.com/updates/flatcar-in-azure/). Para más información, consulte [Migración desde Container Linux de CoreOS](https://docs.flatcar-linux.org/os/migrate-from-container-linux/).

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- Mejoras en los registros y eventos del servicio de clúster de infraestructura de almacenamiento. Los registros y eventos del servicio de clúster de infraestructura de almacenamiento se conservarán durante 14 días, con el fin de mejorar los diagnósticos y la solución de problemas.
- Mejoras que aumentan la confiabilidad del inicio y la detención de Azure Stack Hub.
- Mejoras que reducen el tiempo de ejecución de la actualización mediante la descentralización y la eliminación de dependencias. En comparación con la actualización 2002, el tiempo de actualización del stamp de 4 nodos se reduce de 15-42 horas a 13-20 horas. El de 8 nodos se reduce de 20-50 horas a 16-26 horas. El de 12 nodos se reduce de 20-60 horas a 19-32 horas. El de 16 nodos se reduce de 25-70 horas a 22-38 horas. Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen las cargas de trabajo de inquilino en el sistema, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema.
- Ahora, la actualización produce un error al principio si hay algunos errores que son irrecuperables.
- Mejora de la resistencia del paquete de actualizaciones durante la descarga desde Internet.
- Mejora de la resistencia al detener (desasignar) una máquina virtual.
- Mejora de la resistencia del agente de host de la controladora de red.
- Se han agregado campos adicionales a la carga de CEF de los mensajes de syslog para notificar la dirección IP de origen y la cuenta usada para conectarse al punto de conexión con privilegios y al punto de conexión de recuperación. Para más información, consulte [Integración de Azure Stack Hub con soluciones de supervisión mediante el reenvío de syslog](azure-stack-integrate-security.md).
- Se han agregado eventos de Windows Defender (con los identificadores de evento 5001, 5010, 5012) a la lista de eventos emitidos a través del cliente de syslog.
- Se han agregado alertas en el portal de administración de Azure Stack para eventos relacionados con Windows Defender, con el fin de notificar las incoherencias de las versiones de plataformas y firmas de Defender y los errores al realizar acciones en el malware detectado.
- Se ha agregado compatibilidad con cuatro dispositivos de borde al integrar Azure Stack Hub en el centro de datos.

### <a name="changes"></a>Cambios

- Se han quitado las acciones para detener, apagar y reiniciar una instancia de rol de infraestructura desde el portal de administración. También se han quitado las API correspondientes en el proveedor de recursos del tejido. Los siguientes cmdlets de PowerShell del módulo RM de administrador y de la versión preliminar de AZ para Azure Stack Hub ya no funcionan: **Stop-AzsInfrastructureRoleInstance**, **Disable-InfrastructureRoleInstance** y **Restart-InfrastructureRoleInstance**. Estos cmdlets se quitarán de la siguiente versión del módulo AZ de administrador para Azure Stack Hub.
- Azure Stack Hub 2005 ahora solo admite [App Service en Azure Stack Hub 2020 (versiones 87 .x)](app-service-release-notes-2020-Q2.md).
- La configuración de cifrado del usuario necesaria para la supervisión de hardware se cambió de DES a AES para aumentar la seguridad. Póngase en contacto con su asociado de hardware para más información sobre cómo cambiar la configuración en el controlador de administración de la placa base (BMC). Una vez realizado el cambio en el BMC, puede requerir que ejecute el comando **Set-BmcCredential** de nuevo mediante el punto de conexión con privilegios. Consulte [Cambio de secretos en Azure Stack Hub](azure-stack-rotate-secrets.md) para más información.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Se ha corregido un problema que podía hacer que se produjera un error en un nodo de la unidad de escalado de reparación porque no se encontraba la ruta de acceso a la imagen base del sistema operativo.
- Se ha corregido un problema con la escalabilidad horizontal y vertical del rol de infraestructura de soporte técnico que tiene un efecto en cascada sobre la reparación de los nodos de la unidad de escalado.
- Se ha corregido un problema que hacía que la extensión .VHD (en lugar de .vhd) no se permitiera cuando los operadores agregaban sus propias imágenes al portal de administración de Azure Stack Hub en **All services > Compute > VM Images > Add** (Todos los servicios > Proceso > Imágenes de VM > Agregar).
- Se ha corregido un problema que hacía que una operación anterior de reinicio de la máquina virtual provocara un reinicio posterior inesperado después de cualquier otra operación de actualización de la máquina virtual (agregar discos, etiquetas, etc.).
- Se ha corregido un problema que provocaba que el portal dejara de responder al crear una zona DNS duplicada. Ahora debería mostrarse un error adecuado.
- Se ha corregido un problema que hacía que **Get-AzureStackLogs** no recopilara los registros necesarios para solucionar las incidencias de red. 
- Se ha corregido un problema que hacía que el portal permitiera asociar menos NIC de las realmente permitidas. 
- Se ha corregido la directiva de integridad de código para no emitir eventos de infracción para determinado software interno. De esta forma, se reduce el ruido en los eventos de infracción de la integridad del código emitidos a través del cliente de syslog.
- Se ha corregido el cmdlet **Set-TLSPolicy** para aplicar una nueva directiva sin necesidad de reiniciar el servicio HTTPS o de volver a arrancar el host.
- Se ha corregido un problema que hacía que al usar un servidor NTP de Linux se generaran alertas por error en el portal de administración.  
- Se ha corregido un problema que hacía que la conmutación por error de la instancia del servicio de controladora de copia de seguridad deshabilitara las copias de seguridad automáticas.
- Se ha corregido un problema que hace que la rotación interna de un secreto produzca un error cuando los servicios de infraestructura no tienen conectividad a Internet.
- Se ha corregido un problema que impedía a los usuarios ver los permisos de suscripción mediante los portales de Azure Stack Hub.

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack Hub, consulte [Actualizaciones de seguridad de Azure Stack Hub](release-notes-security-updates.md).

## <a name="hotfixes"></a>Revisiones

Azure Stack Hub publica revisiones de forma periódica. A partir de la versión 2005, cuando se actualiza a una nueva versión principal (por ejemplo, 1.2002.x a 1.2005 x), se instalan automáticamente en ella las revisiones más recientes (si existen). A partir de ese momento, si se publica una revisión para una compilación, se debe instalar.

> [!NOTE]
> Las revisiones de Azure Stack Hub son acumulativas; solo tiene que instalar la revisión más reciente para obtener todas las correcciones incluidas en las revisiones anteriores de esa versión.

Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

Las revisiones de Azure Stack Hub solo son aplicables a los sistemas integrados de Azure Stack Hub; no intente instalar revisiones en el ASDK.

### <a name="prerequisites-before-applying-the-2005-update"></a>Requisitos previos: Antes de aplicar la actualización 2005

La versión 2005 de Azure Stack Hub debe aplicarse sobre la versión 2002 con las revisiones siguientes:

- [Revisión 1.2002.61.163 de Azure Stack Hub](https://support.microsoft.com/help/4592241)

### <a name="after-successfully-applying-the-2005-update"></a>Después de aplicar correctamente la actualización 2005

A partir de la versión 2005, cuando se actualiza a una nueva versión principal (por ejemplo, 1.2002.x a 1.2005 x), se instalan automáticamente en ella las revisiones más recientes (si existen).

Después de la instalación de 2005, si se publican posteriormente algunas revisiones de la versión 2005, se deben instalar:

- [Revisión 1.2005.20.82 de Azure Stack Hub](https://support.microsoft.com/help/4592228)
::: moniker-end

::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>Referencia de la compilación 2002

El número de compilación de la actualización 2002 de Azure Stack Hub es **1.2002.0.35**.

> [!IMPORTANT]  
> Con la actualización de Azure Stack Hub 2002, Microsoft amplía temporalmente las [instrucciones de directiva de soporte técnico de Azure Stack Hub](azure-stack-servicing-policy.md).  Estamos trabajando con clientes de todo el mundo que tienen que hacer frente a la COVID-19 y que deben tomar decisiones importantes sobre sus sistemas de Azure Stack Hub, cómo se actualizan y administran, para garantizar que las operaciones empresariales del centro de datos continúan funcionando con normalidad. Para dar soporte técnico a nuestros clientes, Microsoft ofrece una extensión de cambio de directiva de soporte técnico temporal que incluye tres versiones de actualización anteriores.  Como resultado, se admitirán la actualización 2002 recién publicada y cualquiera de las tres versiones de actualización anteriores (por ejemplo, 1910, 1908 y 1907).

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 2002 de Azure Stack Hub es **Completo**.

El tamaño del paquete de la actualización 2002 es mayor en comparación con actualizaciones anteriores. Esto supone que el tiempo de descarga es también mayor. La actualización permanecerá en el estado **Preparando** durante bastante tiempo; los operadores pueden prever que este proceso tardará más que en las actualizaciones anteriores. La actualización 2002 tuvo los siguientes tiempos de ejecución previstos en nuestras pruebas internas: 4 nodos: 15-42 horas, 8 nodos: 20-50 horas, 12 nodos: 20-60 horas, 16 nodos: 25-70 horas. Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen las cargas de trabajo de inquilino en el sistema, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema. Unos tiempos de ejecución inferiores o superiores al valor esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack Hub, a menos que se produzca un error de actualización. Esta aproximación del tiempo de ejecución es específica para la actualización 2002 y no se debe comparar con otras actualizaciones de Azure Stack Hub.

Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack Hub](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- Hay disponible una nueva versión (1.8.1) de los módulos de administración de PowerShell para Azure Stack Hub en AzureRM.
- Hay disponible una nueva versión de la API REST de administración de Azure Stack Hub. Encontrará información detallada sobre los puntos de conexión y los cambios importantes en la [referencia de la API](/rest/api/azure-stack/).
- El 15 de abril de 2020 se lanzarán nuevos módulos de inquilino de Azure PowerShell para Azure Stack Hub. Los módulos de Azure Resource Manager no dejarán de funcionar, pero dejarán de actualizarse después de la versión 2002.
- Se ha agregado una nueva alerta de advertencia en el portal de administración de Azure Stack Hub para notificar problemas de conectividad con el servidor syslog configurado. El título de la alerta es **The Syslog client encountered a networking issue while sending a Syslog message** (El cliente de Syslog ha encontrado un problema de red al enviar un mensaje de Syslog).
- Se ha agregado una nueva alerta de advertencia en el portal de administración de Azure Stack Hub para notificar problemas de conectividad con el protocolo de tiempo de red (NTP). El título de la alerta es **Invalid Time Source on [node name]** (Origen de hora no válido en [nombre del nodo]).
- El [SDK de Java](https://azure.microsoft.com/develop/java/) ha publicado nuevos paquetes debido a un cambio importante en la actualización 2002 relacionado con las restricciones de TLS. Debe instalar la nueva dependencia del SDK de Java. Puede encontrar las instrucciones en los [perfiles de versión de Java y API](../user/azure-stack-version-profiles-java.md?view=azs-2002#java-and-api-version-profiles).
- Hay una nueva versión (1.0.5.10) del módulo de administración de System Center Operations Manager para Azure Stack Hub disponible y obligatoria para todos los sistemas que ejecutan la actualización 2002 debido a cambios importantes en la API. Los cambios de la API afectan a los paneles de rendimiento de copia de seguridad y almacenamiento, y se recomienda que actualice primero todos los sistemas con la actualización 2002 antes de actualizar el módulo de administración.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- Esta actualización contiene cambios en el proceso de actualización que mejoran considerablemente el rendimiento de futuras actualizaciones completas. Estos cambios surtirán efecto a partir de la siguiente actualización completa después de la versión 2002 y estarán diseñados, específicamente, para mejorar el rendimiento de una fase de actualización completa en la que se actualizan los sistemas operativos host. La mejora del rendimiento de las actualizaciones del sistema operativo host reduce significativamente el intervalo de tiempo en el que las cargas de trabajo de un inquilino se ven afectadas durante las actualizaciones completas.
- La herramienta Azure Stack Hub Readiness Checker valida ahora la integración de AD Graph mediante todos los puertos TCP IP asignados a AD Graph.
- La herramienta de redifusión sin conexión se ha actualizado con mejoras de confiabilidad. La herramienta ya no está disponible en GitHub y se ha [trasladado a la galería de PowerShell](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/). Para más información, consulte [Descarga de elementos de Marketplace existentes desde Azure en Azure Stack Hub](azure-stack-download-azure-marketplace-item.md).
- Se está incorporando una nueva funcionalidad de supervisión. La plataforma corregirá automáticamente la alerta de espacio en disco insuficiente para los hosts físicos y las máquinas virtuales de infraestructura, y solo si se produce un error en esta acción, la alerta será visible en el portal de administrador de Azure Stack Hub para que el operador tome medidas.
- Mejoras en la [recopilación de registros de diagnóstico](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002). La nueva experiencia mejora y simplifica la recopilación de registros de diagnóstico mediante la eliminación de la necesidad de configurar una cuenta de almacenamiento de blobs de antemano. El entorno de almacenamiento está preconfigurado para que pueda enviar registros antes de abrir una incidencia de soporte técnico y gaste menos tiempo en una llamada de soporte técnico.
- El tiempo que se emplea en la [recopilación proactiva de registros y la recopilación de registros a petición](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002) se ha reducido en un 80 %. El tiempo de recopilación de registros puede tardar más de lo esperado, pero no será necesaria ninguna acción por parte de los operadores de Azure Stack Hub a menos que se produzca un error en la recopilación de registros.
- El progreso de la descarga de un paquete de actualizaciones de Azure Stack Hub es ahora visible en la hoja de actualización una vez que se inicia una actualización. Esto solo se aplica a los sistemas conectados de Azure Stack Hub que eligen [preparar los paquetes de actualizaciones mediante la descarga automática](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages).
- Se han realizado mejoras de confiabilidad en el agente del host de Controladora de red.
- Se ha presentado un nuevo microservicio denominado DNS Orchestrator que mejora la lógica de resistencia de los servicios DNS internos durante los procesos de revisión y actualización.
- Se ha agregado una nueva validación de solicitudes para detectar errores de URI de blobs no válidos para el parámetro de la cuenta de almacenamiento del diagnóstico de arranque al crear máquinas virtuales.
- Se han agregado mejoras de registro y corrección automática para RdAgent y el agente del host: dos servicios en el host que facilitan las operaciones CRUD de máquinas virtuales.
- Se ha agregado una nueva característica a la administración de Marketplace que permite a Microsoft agregar atributos para impedir que los administradores descarguen productos de Marketplace que no son compatibles con sus instancias de Azure Stack, mediante diversas propiedades como la versión de Azure Stack o el modelo de facturación. Solo Microsoft puede agregar estos atributos. Para más información, consulte [Uso del portal para descargar elementos de Marketplace](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items).

### <a name="changes"></a>Cambios

- El portal de administración indica ahora si una operación está en curso, con un icono junto a la región de Azure Stack. Si mantiene el puntero sobre el icono, aparecerá el nombre de la operación. Esto le permite identificar las operaciones en segundo plano del sistema en ejecución; por ejemplo, un trabajo de copia de seguridad o una expansión de almacenamiento que puede estar en ejecución durante varias horas.

- Las siguientes API de administrador han quedado en desuso:

  | Proveedor de recursos       | Resource              | Versión            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft.Storage.Admin | granjas                 | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/acquisitions    | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/shares          | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/storageaccounts | 2015-12-01-preview |

- Se han sustituido las siguientes API de administrador por una versión más reciente (2018-09-01):

  | Proveedor de recursos      | Resource              | Versión    |
  |------------------------|-----------------------|------------|
  | Microsoft.Backup.Admin | backupLocation         | 2016-05-01 |
  | Microsoft.Backup.Admin | backups                | 2016-05-01 |
  | Microsoft.Backup.Admin | operaciones             | 2016-05-01 |

- Al crear una máquina virtual Windows mediante PowerShell, asegúrese de agregar la marca `provisionvmagent` si desea que la máquina virtual implemente extensiones. Sin esta marca, la máquina virtual se crea sin el agente invitado, lo que elimina la capacidad de implementar extensiones de máquina virtual:

   ```powershell
   $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName "MainComputer" `
     -Credential $Credential -ProvisionVMAgent
  ```

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Se corrigió un problema por el que la incorporación de más de una dirección IP pública en la misma NIC de una máquina virtual provocaba problemas de conectividad a Internet. Ahora, una NIC con dos direcciones IP públicas funciona según lo previsto.
- Se ha corregido un problema que hacía que el sistema generase una alerta que indicaba que se debía configurar el directorio principal de Azure AD.
- Se ha corregido un problema que hacía que una alerta no se cerrara automáticamente. La alerta indicaba que se debía configurar el directorio principal de Azure AD, pero no se cerraba incluso después de haberse corregido el problema.
- Se ha corregido un problema que provocaba que se produjera un error durante la fase de preparación de las actualizaciones como resultado de errores internos del proveedor de recursos de actualización.
- Se ha corregido un problema que hacía que se produjera un error en las operaciones del proveedor de recursos de complementos después de realizar la rotación de secretos de Azure Stack Hub.
- Se ha corregido un problema que provocaba errores frecuentes de actualización de Azure Stack Hub debido a la presión de memoria en el rol ERCS.
- Se ha corregido un error en la hoja de actualización por el que el estado de actualización aparecía como **Instalación en curso** en lugar de **Se está preparando** durante la fase de preparación de una actualización de Azure Stack Hub.
- Se ha corregido un problema por el que la característica RSC en los conmutadores virtuales creaba incoherencias e interrumpía el flujo de tráfico que pasaba a través de un equilibrador de carga. La característica RSC está ahora deshabilitada de forma predeterminada.
- Se corrigió un problema que hacía que varias configuraciones de IP de una NIC provocaran que el tráfico se enrutará de forma incorrecta, lo que impedía que hubiera conectividad de salida. 
- Se ha corregido un problema en el que la dirección MAC de una NIC se almacenaba en la memoria caché y la asignación de esa dirección a otro recurso causaba errores de implementación de la máquina virtual.
- Se ha corregido un problema por el que AVMA no podía activar las licencias de las imágenes de máquinas virtuales Windows desde el canal comercial RETAIL.
- Se ha corregido un problema por el que las máquinas virtuales no se podían crear si el número de núcleos virtuales solicitados por la máquina virtual era igual a los núcleos físicos del nodo. Ahora se permite que las máquinas virtuales tengan un número de núcleos virtuales que sea igual o inferior al de los núcleos físicos del nodo.
- Se ha corregido un problema por el que no permitíamos que el tipo de licencia se estableciera en "null" para cambiar las imágenes de pago por uso a BYOL.
- Se ha corregido un problema que impedía que las extensiones se agregaran a un conjunto de escalado de máquinas virtuales.

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack Hub, consulte [Actualizaciones de seguridad de Azure Stack Hub](release-notes-security-updates.md).

## <a name="hotfixes"></a>Revisiones

Azure Stack Hub publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack Hub para la versión 1910 antes de actualizar Azure Stack Hub a la versión 2002.

> [!NOTE]
> Las revisiones de Azure Stack Hub son acumulativas; solo tiene que instalar la revisión más reciente para obtener todas las correcciones incluidas en las revisiones anteriores de esa versión.

Las revisiones de Azure Stack Hub solo son aplicables a los sistemas integrados de Azure Stack Hub; no intente instalar revisiones en el ASDK.

Para más información acerca de las revisiones, consulte [Directiva de servicio de Azure Stack Hub](azure-stack-servicing-policy.md#hotfixes).

### <a name="prerequisites-before-applying-the-2002-update"></a>Requisitos previos: Antes de aplicar la actualización 2002

La versión 2002 de Azure Stack Hub debe aplicarse en la versión 1910 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Revisión 1.1910.84.230 de Azure Stack Hub](https://support.microsoft.com/help/4592243)

### <a name="after-successfully-applying-the-2002-update"></a>Después de aplicar correctamente la actualización 2002

Después de instalar esta actualización, instale todas las revisiones aplicables.

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Revisión 1.2002.61.163 de Azure Stack Hub](https://support.microsoft.com/help/4592241)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1910
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1908
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1907
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1906
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1905
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1904
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1903
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1902
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1901
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1811
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1809
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1808
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1807
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1805
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1804
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1803
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1802
::: moniker-end

::: moniker range="<azs-2002"
Puede consultar las [notas de las versiones anteriores de Azure Stack Hub en la Galería de TechNet](https://aka.ms/azsarchivedrelnotes). Estos documentos archivados se proporcionan únicamente con fines de referencia y no implican que estas versiones sean compatibles. Para obtener información sobre el soporte técnico de Azure Stack Hub, consulte [Directiva de mantenimiento de Azure Stack Hub](azure-stack-servicing-policy.md). Para obtener más ayuda, póngase en contacto con los servicios de asistencia al cliente de Microsoft.
::: moniker-end
