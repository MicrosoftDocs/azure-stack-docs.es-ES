---
title: Notas de la versión de Azure Stack | Microsoft Docs
description: Obtenga información sobre las actualizaciones de los sistemas integrados de Azure Stack, como novedades y dónde se puede descargar dicha actualización.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: 8c124f44ed938108a4b23cea44642b7e275eb04d
ms.sourcegitcommit: c0fe1ac8c38d0475621ad8022db96983f9e5ae79
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2019
ms.locfileid: "75188385"
---
# <a name="azure-stack-updates-release-notes"></a>Actualizaciones de Azure Stack: notas de la versión

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido de los paquetes de actualización de Azure Stack. La actualización incluye novedades, mejoras y correcciones para esta versión de Azure Stack.

Para tener acceso a las notas de la versión de una versión diferente, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> Si a su instancia le faltan más de dos actualizaciones de Azure Stack, se considera que no cumple los requisitos. [Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.](azure-stack-servicing-policy.md#keep-your-system-under-support)
::: moniker-end

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

Para obtener ayuda con la solución de problemas de actualizaciones y el proceso de actualización, vea [Solución de problemas de actualizaciones y revisiones para Azure Stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>Referencia de la compilación 1910

El número de compilación de la actualización 1910 de Azure Stack es **1.1910.0.58**.

### <a name="update-type"></a>Tipo de actualización

A partir de la versión 1908, el sistema operativo subyacente en el que se ejecuta Azure Stack se actualizó a Windows Server 2019. Esto permite conseguir mejoras fundamentales básicas y disponer de la capacidad de incorporar funciones adicionales a Azure Stack en un futuro próximo.

El tipo de compilación de la actualización 1910 de Azure Stack es **Rápido**.

El tamaño del paquete de la actualización 1910 es mayor en comparación con actualizaciones anteriores. Esto supone que el tiempo de descarga es también mayor. La actualización permanecerá en el estado **Preparando** durante bastante tiempo; los operadores pueden prever que este proceso tardará más que en las actualizaciones anteriores. El tiempo esperado que tarda en completarse la actualización 1910 es de aproximadamente 10 horas, independientemente del número de nodos físicos del entorno de Azure Stack. Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen las cargas de trabajo de inquilino en el sistema, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema. Los tiempos de ejecución superiores al valor esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización. Esta aproximación del tiempo de ejecución es específica para la actualización 1910 y no se debe comparar con otras actualizaciones de Azure Stack.

Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- El portal del administrador muestra ahora las direcciones IP de punto de conexión con privilegios en el menú de propiedades de la región, para facilitar la detección. Además, muestra el servidor horario y los reenviadores DNS configurados actuales.

- El sistema de mantenimiento y supervisión de Azure Stack ahora puede generar alertas para diversos componentes de hardware si se produce un error. Esta opción requiere configuración adicional. Para más información, consulte [Supervisión de componentes de hardware de Azure Stack](azure-stack-hardware-monitoring.md).

- [Compatibilidad con cloud-init para Azure Stack](/azure/virtual-machines/linux/using-cloud-init): cloud-init es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Dado que se llama a cloud-init durante el proceso de arranque inicial, no se necesitan pasos adicionales ni agentes para aplicar la configuración. Las imágenes de Ubuntu en Marketplace se han actualizado para admitir cloud-init para el aprovisionamiento.

- Azure Stack ahora admite todas las versiones del agente de Linux para Windows Azure como Azure.

- Hay disponible una nueva versión de los módulos de PowerShell de administración de Azure Stack. <!-- For more information, see -->

- Se ha agregado el cmdlet **Set-AzSDefenderManualUpdate** en el punto de conexión con privilegios (PEP) para configurar la actualización manual de las definiciones de Windows Defender en la infraestructura de Azure Stack. Para más información, consulte [Actualización de Antivirus de Windows Defender en Azure Stack](azure-stack-security-av.md).

- Se ha agregado el cmdlet **Get-AzSDefenderManualUpdate** en el punto de conexión con privilegios (PEP) para recuperar la configuración de la actualización manual de las definiciones de Windows Defender en la infraestructura de Azure Stack. Para más información, consulte [Actualización de Antivirus de Windows Defender en Azure Stack](azure-stack-security-av.md).

- Se ha agregado el cmdlet **Set-AzSDnsForwarder** en el punto de conexión con privilegios (PEP) para cambiar la configuración de reenviador de los servidores DNS en Azure Stack. Para más información acerca de la configuración de DNS, consulte [Integración de DNS del centro de datos de Azure Stack](azure-stack-integrate-dns.md).

- Se ha agregado el cmdlet **Get-AzSDnsForwarder** en el punto de conexión con privilegios (PEP) para recuperar la configuración de reenviador de los servidores DNS en Azure Stack. Para más información acerca de la configuración de DNS, consulte [Integración de DNS del centro de datos de Azure Stack](azure-stack-integrate-dns.md).

- Se ha agregado compatibilidad con la administración de **clústeres de Kubernetes** mediante el [motor de AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview). A partir de esta actualización, los clientes pueden implementar clústeres de Kubernetes de producción. El motor de AKS permite a los usuarios:
   - Administrar el ciclo de vida de los clústeres de Kubernetes. Pueden crear, actualizar y escalar clústeres.
   - Mantener los clústeres mediante imágenes administradas generadas por AKS y los equipos de Azure Stack.
   - Aprovechar las ventajas de un proveedor de nube de Kubernetes integrado en Azure Resource Manager que compila clústeres mediante recursos nativos de Azure.
   - Implementar y administrar los clústeres en sellos de Azure Stack conectados o desconectados.
   - Usar características híbridas de Azure:
      * Integración con Azure Arc (versión preliminar privada próximamente)
      * Integración con Azure Monitor para contenedores (en versión preliminar pública)
   - Usar contenedores de Windows con el motor de AKS (en versión preliminar privada).
   - Recibir soporte técnico de CSS y PG para las implementaciones.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- Azure Stack ha mejorado la capacidad de corregir de forma automática algunos problemas de revisión y actualización que antes causaban errores de actualización o impedían que los operadores iniciaran una actualización de Azure Stack. Como resultado, se incluyen menos pruebas en el grupo **Test-AzureStack -UpdateReadiness**. Para más información, consulte [Validación del estado del sistema de Azure Stack](azure-stack-diagnostic-test.md#groups). Las tres pruebas siguientes aún se mantienen en el grupo **UpdateReadiness**:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Se ha agregado una regla de auditoría para notificar cuando se monta un dispositivo externo (por ejemplo, una llave USB) en un nodo de la infraestructura de Azure Stack. El registro de auditoría se emite a través de Syslog y aparece como **Microsoft-Windows-Security-Auditing: 6416|Plug and Play Events** (Eventos Plug and Play). Para más información sobre cómo configurar el cliente de Syslog, consulte [Reenvío de Syslog](azure-stack-integrate-security.md).

- Azure Stack cambia a las claves RSA de 4096 bits para los certificados internos. La ejecución de la rotación interna de secretos reemplazará los antiguos certificados de 2048 bits por certificados largos de 4096 bits. Para más información sobre la rotación de secretos en Azure Stack, consulte [Cambio de secretos en Azure Stack](azure-stack-rotate-secrets.md).

- Actualizaciones en la complejidad de los algoritmos criptográficos y la seguridad de clave de varios componentes internos para cumplir con la directiva 15 del Comité de Sistemas Nacionales de Seguridad estadounidense (CNSSP-15), que proporciona procedimientos recomendados para el uso de estándares públicos sobre el uso compartido de la información de forma segura. Entre las mejoras, se incluye AES256 para la autenticación Kerberos y SHA384 para el cifrado VPN. Para más información sobre CNSSP-15, consulte la [página de directivas del Comité de Sistemas Nacionales de Seguridad](http://www.cnss.gov/CNSS/issuances/Policies.cfm) estadounidense.

- Como resultado de la actualización anterior, Azure Stack ahora tiene nuevos valores predeterminados para las configuraciones de IPsec/IKEv2. Los nuevos valores predeterminados que se usan en Azure Stack son los siguientes:

   **Parámetros de la fase 1 de IKE (modo principal)**

   | Propiedad              | Valor|
   |-|-|
   | Versión de IKE           | IKEv2 |
   |Grupo Diffie-Hellman   | ECP384 |
   | Método de autenticación | Clave precompartida |
   |Algoritmos de cifrado y hash | AES256, SHA384 |
   |Vigencia de SA (tiempo)     | 28.800 segundos|

   **Parámetros de la fase 2 de IKE (modo rápido)**

   | Propiedad| Valor|
   |-|-|
   |Versión de IKE |IKEv2 |
   |Cifrados y algoritmos hash (cifrado)     | GCMAES256|
   |Cifrados y algoritmos hash (autenticación) | GCMAES256|
   |Vigencia de SA (tiempo)  | 27 000 segundos  |
   |Vigencia de SA (Kilobytes) | 33 553 408     |
   |Confidencialidad directa perfecta (PFS) | ECP384 |
   |Detección de cuellos del mismo nivel | Compatible|

   Estos cambios también se reflejan en la documentación de la [propuesta predeterminada de IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters).

- El servicio de copia de seguridad de la infraestructura mejora la lógica que calcula el espacio libre deseado para las copias de seguridad, en lugar de basarse en un umbral fijo. El servicio utilizará el tamaño de una copia de seguridad, la directiva de retención, la reserva y el uso actual de la ubicación de almacenamiento externo para determinar si es necesario enviar una advertencia al operador. 

### <a name="changes"></a>Cambios

- Al descargar elementos de Marketplace de Azure en Azure Stack, hay una nueva interfaz de usuario que permite especificar una versión del elemento, en caso de que existan varias versiones. La nueva interfaz de usuario está disponible tanto en escenarios conectados como desconectados. Para más información, consulte [Descarga de elementos de Marketplace existentes desde Azure en Azure Stack](azure-stack-download-azure-marketplace-item.md).  

- A partir de la versión 1910, el sistema Azure Stack **requiere** un espacio de direcciones IP internas privadas adicional /20. Esta red es privada para el sistema Azure Stack y se puede reutilizar en varios sistemas Azure Stack de su centro de datos. Aunque la red es privada de Azure Stack, no debe superponerse con un red del centro de datos. El espacio IP privado /20 se divide en varias redes que permiten ejecutar la infraestructura de Azure Stack en contenedores (como se mencionó anteriormente en las [notas de la versión 1905](release-notes.md?view=azs-1905)). Al ejecutar la infraestructura de Azure Stack en contenedores se pretende optimizar el uso y mejorar el rendimiento. Además, el espacio IP privado /20 también se usa para permitir esfuerzos continuos que reducirán el espacio IP enrutable necesario antes de la implementación.

  - Tenga en cuenta que la entrada /20 actuará como requisito previo en la actualización de Azure Stack posterior a la 1910. Cuando se publique la siguiente actualización de Azure Stack después de la versión 1910 y se intente realizar su instalación, se producirá un error en el proceso si no se ha completado la entrada /20 tal como se describe a continuación en los pasos de corrección. Se mostrará una alerta en el portal de administración hasta que se hayan completado los pasos de corrección indicados. Consulte el artículo [Integración de la red del centro de datos](azure-stack-network.md#private-network) para comprender cómo se consumirá este nuevo espacio privado. 

  - Pasos de corrección: Para llevar a cabo la corrección, siga las instrucciones para [abrir una sesión de PEP](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Prepare un [intervalo de direcciones IP internas privadas](azure-stack-network.md#logical-networks) de tamaño /20 y ejecute el siguiente cmdlet (solo disponible a partir de la versión 1910) en la sesión PEP; para ello, use el siguiente ejemplo: `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Si la operación se realiza correctamente, recibirá el mensaje **Azs Internal Network range added to the config** (Intervalo de red interno de Azs agregado a la configuración). Además, la alerta se cerrará en el portal de administración. El sistema Azure Stack ya se podrá actualizar a la versión siguiente.
  
- El servicio de copia de seguridad de la infraestructura elimina los datos de copias de seguridad cargadas parcialmente si la ubicación de almacenamiento externo se queda sin capacidad durante el procedimiento de carga.  

- En las implementaciones de AAD, el servicio de copia de seguridad de la infraestructura agrega el servicio de identidad a la carga útil de copia de seguridad.  

- El módulo AzureStack de PowerShell se ha actualizado a la versión 1.8.0 para la versión 1910.<br>Los cambios incluyen:
   - **Nuevo módulo de administración DRP**: Deployment Resource Provider (DRP) permite las implementaciones organizadas de proveedores de recursos en Azure Stack. Estos comandos interactúan con el nivel de Azure Resource Manager para interactuar con DRP.
   - **BRP**: <br />
           - Compatibilidad con la restauración de rol único para la copia de seguridad de infraestructura de Azure Stack. <br />
           - Incorporación del parámetro `RoleName` al cmdlet `Restore-AzsBackup`.
   - **FRP**: Cambios importantes en los recursos **Unidad** y **Volumen** con la versión de API `2019-05-01`. Las características son compatibles con Azure Stack 1910 y versiones posteriores: <br />
            - Se han cambiado los valores de `ID`, `Name`, `HealthStatus` y `OperationalStatus`. <br />
            - Nuevas propiedades compatibles `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer` y `StoragePool` para recursos de **Unidad**. <br />
            - Las propiedades `CanPool` y `CannotPoolReason` de los recursos de **Unidad** están en desuso; en su lugar, use `OperationalStatus`.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Se corrigió un problema que impedía la aplicación de la directiva TLS 1.2 en entornos implementados antes de la versión 1904 de Azure Stack.
- Se ha corregido un problema por el que una máquina virtual de Ubuntu 18.04 creada con la autorización de SSH habilitada no permitía usar las claves SSH para iniciar sesión. 
- Se quitó la opción **Restablecer contraseña** de la interfaz de usuario del conjunto de escalado de máquinas virtuales.
- Se corrigió un problema por el que la eliminación del equilibrador de carga del portal no eliminaba el objeto en el nivel de infraestructura.
- Se corrigió un problema que mostraba un porcentaje inexacto de la alerta de uso del grupo de servidores de puerta de enlace en el portal de administración.
- Se corrigió un problema por el que la incorporación de más de una dirección IP pública en la misma NIC de una máquina virtual provocaba problemas de conectividad a Internet. Ahora, una NIC con dos direcciones IP públicas debe funcionar según lo previsto.

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1910 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1908 antes de actualizar Azure Stack a la versión 1910.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Requisitos previos: Antes de aplicar la actualización 1910

La versión 1910 de Azure Stack debe aplicarse en la versión 1908 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1908.11.47](https://support.microsoft.com/help/4535000)

### <a name="after-successfully-applying-the-1910-update"></a>Después de aplicar correctamente la actualización 1910

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1910.8.76](https://support.microsoft.com/help/4534999)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>Referencia de la compilación 1908

El número de compilación de la actualización 1908 de Azure Stack es **1.1908.4.33**.

### <a name="update-type"></a>Tipo de actualización

En la versión 1908, el sistema operativo subyacente en el que se ejecuta Azure Stack se ha actualizado a Windows Server 2019. Esto permite conseguir mejoras fundamentales básicas y disponer de la capacidad de incorporar funciones adicionales a Azure Stack en un futuro próximo.

El tipo de compilación de la actualización 1908 de Azure Stack es **Completa**. Como resultado, la actualización 1908 tiene un tiempo de ejecución más largo que las actualizaciones rápidas, como 1906 y 1907. Los tiempos de ejecución exactos para las actualizaciones completas suelen depender del número de nodos que contiene la instancia de Azure Stack, de la capacidad del sistema que usan las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de la configuración del hardware del sistema. La actualización 1908 tuvo los siguientes tiempos de ejecución previstos en nuestras pruebas internas: 4 nodos: 42 horas, 8 nodos: 50 horas, 12 nodos: 60 horas, 16 nodos: 70 horas. Los tiempos de ejecución de la actualización superiores a estos valores previstos son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.

Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack](azure-stack-updates.md).

- Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las configuración de hardware del sistema.
- Los tiempos de ejecución superiores a lo esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.
- Esta aproximación del tiempo de ejecución es específica para la actualización 1908 y no se debe comparar con otras actualizaciones de Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- Tenga en cuenta que en la versión 1908 el sistema operativo subyacente en el que se ejecuta Azure Stack se ha actualizado a Windows Server 2019. Esto permite conseguir mejoras fundamentales básicas y disponer de la capacidad de incorporar funciones adicionales a Azure Stack en un futuro próximo.
- Ahora todos los componentes de la infraestructura de Azure Stack funcionan en modo FIPS 140-2.
- Los operadores de Azure Stack pueden quitar ahora los datos de usuario del portal. Para más información, consulte [Eliminación de los datos de usuario del portal de Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->
- Se han producido mejoras en el cifrado de datos en reposo de Azure Stack para conservar los secretos en el Módulo de plataforma segura (TPM) de hardware de los nodos físicos.

### <a name="changes"></a>Cambios

- Los proveedores de hardware lanzarán el paquete de extensión de OEM de la versión 2.1 o posterior al mismo tiempo que la versión 1908 de Azure Stack. Este paquete será un requisito previo para la versión 1908 de Azure Stack. Para obtener más información acerca de cómo descargar el paquete de extensión de OEM de la versión 2.1 o posterior, póngase en contacto con el proveedor de hardware del sistema y consulte el artículo sobre [actualizaciones de OEM](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes"></a>Correcciones

- Se ha corregido un problema de compatibilidad con futuras actualizaciones de OEM de Azure Stack y un problema con la implementación de máquinas virtuales mediante imágenes de usuario de cliente. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44).  
- Se ha corregido un problema con la actualización de firmware de OEM y se ha corregido un error de diagnóstico en la prueba de Azure Stack para el estado del anillo de Fabric. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35).
- Se corrigió un problema con el proceso de actualización de firmware de OEM. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37).

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1908 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1907 antes de actualizar Azure Stack a la versión 1908.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Requisitos previos: Antes de aplicar la actualización 1908

La versión 1908 de Azure Stack debe aplicarse en la versión 1907 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1907.18.56](https://support.microsoft.com/help/4528552)

La actualización Azure Stack 1908 requiere la **versión de OEM 2.1 o posterior para Azure Stack** del proveedor de hardware del sistema. Las actualizaciones de OEM incluyen actualizaciones de controladores y firmware en el hardware del sistema Azure Stack. Para más información sobre la aplicación de actualizaciones de OEM, consulte [Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack](azure-stack-update-oem.md).

### <a name="after-successfully-applying-the-1908-update"></a>Después de aplicar correctamente la actualización 1908

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1908.11.47](https://support.microsoft.com/help/4535000)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>Referencia de la compilación 1907

El número de compilación de la actualización 1907 de Azure Stack es **1.1907.0.20**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 1907 de Azure Stack es **Rápida**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](azure-stack-updates.md). Según las pruebas internas, el tiempo esperado que tarda en completarse la actualización 1907 es de unas 13 horas.

- Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las configuración de hardware del sistema.
- Los tiempos de ejecución superiores a lo esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.
- Esta aproximación del tiempo de ejecución es específica para la actualización 1907 y no se debe comparar con otras actualizaciones de Azure Stack.

## <a name="whats-in-this-update"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- Versión de disponibilidad general del servicio de recopilación de registros de diagnóstico de Azure Stack para facilitar y mejorar la colección de registros de diagnóstico. El servicio de recopilación de registros de diagnóstico de Azure Stack proporciona una manera simplificada de recopilar y compartir registros de diagnóstico con los servicios de asistencia al cliente (CSS) de Microsoft. Este servicio de recopilación de registros de diagnóstico proporciona una experiencia de usuario nueva en el portal de administración de Azure Stack que permite que los operadores configuren la carga automática de los registros de diagnóstico a un blob de almacenamiento cuando surgen ciertas alertas críticas o que realicen la misma operación a petición. Para más información, consulte el artículo [Recopilación de registros de diagnóstico](azure-stack-diagnostic-log-collection-overview.md).

- Versión de disponibilidad general de la validación de la infraestructura de red de Azure Stack como parte de la herramienta de validación de Azure Stack **Test-AzureStack**. La infraestructura de red de Azure Stack será parte de **Test-AzureStack**, para identificar si se produce un error en la infraestructura de red de Azure Stack. La prueba revisa la conectividad de la infraestructura de red al omitir la red definida por software de Azure Stack. Muestra la conectividad de una VIP pública a los reenviadores DNS configurados, los servidores NTP y los puntos de conexión de identidad. Además, comprueba la conectividad con Azure cuando se usa Azure AD como el proveedor de identidades o el servidor federado cuando se usa ADFS. Para más información, consulte el artículo sobre la [herramienta de validación de Azure Stack](azure-stack-diagnostic-test.md).

- Se agregó un procedimiento de cambio de secretos internos para cambiar certificados TLS de SQL internos según sea necesario durante una actualización del sistema.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- La hoja de actualización de Azure Stack ahora muestra la hora del **último paso completado** para las actualizaciones activas. Para ver esto, vaya a la hoja de actualización y haga clic en una actualización en ejecución. El **último paso completado** está disponible entonces en la sección **Detalles de ejecución de la actualización**.

- Mejoras en las acciones de operador **Start-AzureStack** y **Stop-AzureStack**. El tiempo para iniciar Azure Stack se redujo en un promedio de 50 %. El tiempo para apagar Azure Stack se redujo en un promedio de 30 %. Los tiempos promedio de inicio y apagado siguen iguales, porque el número de nodos aumenta en una unidad de escalado.

- Se mejoró el control de errores de la herramienta de Marketplace desconectada. Si se produce un error en la descarga o se realiza correctamente de manera parcial cuando se usa **Export-AzSOfflineMarketplaceItem**, aparece un mensaje de error con más detalles sobre el error y los pasos de mitigación, si los hay.

- Se mejoró el rendimiento de la creación de los discos administrados a partir de una instantánea o de un blob en páginas de gran tamaño. Anteriormente, desencadenada un tiempo de expiración agotado cuando se creaba un disco de gran tamaño.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Se mejoró la comprobación de estado de un disco virtual antes de apagar un nodo para evitar que el disco virtual se desasocie de manera inesperada.

- Se mejoró el almacenamiento de los registros internos de las operaciones de administrador. Esto genera un mejor rendimiento y confiabilidad durante las operaciones de administrador al minimizar el consumo de memoria y almacenamiento de los procesos de registro internos. También puede observar mejores tiempos de carga de página de la hoja de actualización en el portal del administrador. Como parte de esta mejora, los registros de actualizaciones con más de 6 meses ya no estarán disponibles en el sistema. Si necesita registros para estas actualizaciones, asegúrese de [descargar el resumen](azure-stack-apply-updates.md) de todas las ejecuciones de actualizaciones anteriores a 6 meses antes de realizar la actualización 1907.

### <a name="changes"></a>Cambios

- La versión 1907 de Azure Stack contiene una alerta de advertencia que indica a los operadores que se aseguren de actualizar el paquete de OEM de su sistema a la versión 2.1 o posterior antes de actualizar a la versión 1908. Para más información sobre la aplicación de actualizaciones de OEM en Azure Stack, consulte [Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack](azure-stack-update-oem.md).

- Se agregó una regla de salida (HTTPS) nueva para permitir la comunicación del servicio de recopilación de registros de diagnóstico de Azure Stack. Para más información, consulte [Integración del centro de datos de Azure Stack: publicar puntos de conexión](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- El servicio de copia de seguridad de la infraestructura ahora elimina de manera parcial las copias de seguridad cargadas si la ubicación de almacenamiento externo se queda sin capacidad.

- Las copias de seguridad de la infraestructura ya no incluyen una copia de seguridad de los datos de servicios de dominio. Esto solo se aplica a los sistemas que usan Azure Active Directory como el proveedor de identidades.

- Ahora se valida que la imagen que se está ingiriendo a **Proceso -> Imágenes de VM** es del tipo de blob en páginas.

- El comando de punto de conexión con privilegios **Set-BmcCredential** ahora actualiza la credencial en el controlador de administración de placa base.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Se corrigió un problema en el que se consideraba que el publicador, la oferta y la SKU distinguía entre mayúsculas y minúsculas en una plantilla de Resource Manager: la imagen no se capturó para la implementación, a menos que los parámetros de la imagen hubiesen tenido las mismas mayúsculas y minúsculas que el publicador, la oferta y la SKU.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Se corrigió un problema con las copias de seguridad que presentan el mensaje de error **PartialSucceeded**, debido a los tiempos de expiración durante la copia de seguridad de los metadatos del servicio de almacenamiento.  

- Se corrigió un problema en el que la eliminación de las suscripciones de usuario generó recursos huérfanos.

- Se corrigió un problema en el que el campo de descripción no se guardó al crear una oferta.

- Se corrigió un problema en el que un usuario con permisos de **solo lectura** podía crear, editar y eliminar recursos. Ahora el usuario solo puede crear recursos cuando se asigna el permiso de **colaborador**. 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Se corrigió un problema en el que la actualización presenta errores debido a un archivo DLL bloqueado por el host de proveedor de WMI.

- Se corrigió un problema en el servicio de actualización que impedía que las actualizaciones disponibles se mostrarán en el proveedor de recursos o en el icono de actualización. Este problema se encontró en 1906 y se corrigió en la revisión [KB4511282](https://support.microsoft.com/help/4511282/).

- Se corrigió un problema que podía provocar errores en las actualizaciones debido a que el plano de administración era incorrecto a causa de una configuración errónea. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794/).

- Se corrigió un problema que impedía que los usuarios completaran la implementación de imágenes de terceros desde Marketplace. Este problema se encontró en 1906 y se corrigió en la revisión [KB4511259](https://support.microsoft.com/help/4511259/).

- Se corrigió un problema que podía hacer que la creación de VM desde imágenes administradas presentaran un error debido al bloqueo del servicio del administrador de imágenes del usuario. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794/).

- Se corrigió un problema en el que las operaciones CRUD de VM podían presentar un error debido a que la caché de la puerta de enlace de una aplicación no se actualizada según lo esperado. Este problema se encontró en 1906 y se corrigió en la revisión [KB4513119](https://support.microsoft.com/en-us/help/4513119/).

- Se corrigió un problema en el proveedor de recursos de estado que afectaba la disponibilidad de las hojas de alerta y región en el portal del administrador. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794).

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1907 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1906 antes de actualizar Azure Stack a la versión 1907.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1907-update"></a>Antes de aplicar la actualización 1907

La versión 1907 de Azure Stack debe aplicarse en la versión 1906 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Después de aplicar correctamente la actualización 1907

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1907.18.56](https://support.microsoft.com/help/4528552)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>Referencia de la compilación 1906

El número de compilación de la actualización 1906 de Azure Stack es **1.1906.0.30**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 1906 de Azure Stack es **Rápida**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](azure-stack-updates.md). El tiempo esperado que tarda en completarse la actualización 1906 es de aproximadamente 10 horas, independientemente del número de nodos físicos de su entorno de Azure Stack. Por lo general, los tiempos de ejecución de actualización exactos dependerán de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema. Los tiempos de ejecución superiores al valor esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización. Esta aproximación del runtime es específica para la actualización 1906 y no se debe comparar con otras actualizaciones de Azure Stack.

## <a name="whats-in-this-update"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Se ha agregado un cmdlet **Set-TLSPolicy** en el punto de conexión con privilegios (PEP) para aplicar TLS 1.2 en todos los puntos de conexión. Para más información, consulte el artículo sobre los [controles de seguridad de Azure Stack](azure-stack-security-configuration.md).

- Se ha agregado un cmdlet **Get-TLSPolicy** en el punto de conexión con privilegios (PEP) para recuperar la directiva TLS aplicada. Para más información, consulte el artículo sobre los [controles de seguridad de Azure Stack](azure-stack-security-configuration.md).

- Se ha agregado un procedimiento de cambio de secretos internos para cambiar certificados TLS internos según sea necesario durante una actualización del sistema.

- Se ha agregado una medida de seguridad para evitar la expiración de secretos internos. Para ello, se aplica el cambio de secretos internos en caso de omisión de una alerta crítica sobre secretos a punto de expirar. No se debería confiar en esta medida como un procedimiento de funcionamiento normal. La rotación de secretos debería planearse durante una ventana de mantenimiento. Para más información, consulte [Cambio de secretos en Azure Stack](azure-stack-rotate-secrets.md).

- Visual Studio Code ahora es compatible con la implementación de Azure Stack mediante AD FS.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- El cmdlet **Get-GraphApplication** del punto de conexión con privilegios ahora muestra la huella digital del certificado usado actualmente. Esto mejora la administración de certificados para las entidades de servicio cuando se implementa Azure Stack con AD FS.

- Se han agregado nuevas reglas de seguimiento de estado para validar la disponibilidad de AD Graph y AD FS, incluida la capacidad de generar alertas.

- Mejoras en la confiabilidad del proveedor de recursos de copia de seguridad cuando el servicio de copia de seguridad de infraestructura se mueve a otra instancia.

- Optimización del rendimiento del procedimiento de cambio de secretos externos para proporcionar un tiempo de ejecución uniforme a fin de facilitar la programación de la ventana de mantenimiento.

- El cmdlet **Test-AzureStack** ahora informa sobre los secretos internos que van a expirar (alertas críticas).

- Existe un nuevo parámetro disponible para el cmdlet **Register-CustomAdfs** en el punto de conexión con privilegios, que permite omitir la comprobación de la lista de revocación de certificados al configurar la confianza de federación de AD FS.

- La versión 1906 presenta una mayor visibilidad del progreso de actualización, lo que garantiza que no se produzcan pausas en las actualizaciones. Esto provoca un aumento en el número total de pasos de actualización que se muestran a los operadores en la hoja **Actualización**. Es posible que también observe más pasos de actualización en paralelo que en las actualizaciones anteriores.

#### <a name="networking-updates"></a>Actualizaciones de redes

- Se ha actualizado el tiempo de concesión establecido en el respondedor DHCP para que sea coherente con Azure.

- Se han mejorado las frecuencias de reintento del proveedor de recursos en el caso de error de implementación de los recursos.

- Se ha quitado la opción de SKU **Estándar** del equilibrador de carga y la dirección IP pública, ya que no se admite actualmente.

### <a name="changes"></a>Cambios

- La creación de una experiencia de cuenta de almacenamiento ahora es coherente con Azure.

- Se han cambiado los desencadenadores de alertas para la expiración de secretos internos:
  - Las alertas de advertencia se generan ahora 90 días antes de la expiración de los secretos.
  - Las alertas críticas se generan ahora 30 días antes de la expiración de los secretos.

- Se han actualizado las cadenas del proveedor de recursos de copia de seguridad de infraestructura para que la terminología sea coherente.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Se ha corregido un problema que provocaba un **error de funcionamiento interno** al cambiar el tamaño de una VM de disco administrado.

- Se ha corregido un problema en que un error de creación de usuario daba lugar a un estado incorrecto del servicio de administración de imágenes, que bloqueaba la eliminación de la imagen errónea y la creación de nuevas imágenes. Esto también se ha corregido en la revisión 1905.

- Las alertas activas sobre los secretos internos a punto de expirar ahora se cierran automáticamente después de la ejecución correcta del cambio de secretos internos.

- Se ha corregido un problema en que la duración de la actualización en la pestaña del historial de actualizaciones recortaba el primer dígito si la actualización se ejecutaba durante más de 99 horas.

- La hoja **Actualización** ahora incluye una opción **Reanudar** para las actualizaciones que no se realizan correctamente.

- En los portales de administradores y usuarios, se ha corregido un problema del Marketplace en que la búsqueda devolvía por error la extensión de Docker, pero no se podía realizar ninguna otra acción, porque no está disponible en Azure Stack.

- Se ha corregido un problema de la interfaz de usuario de implementación de plantillas en que los parámetros no se rellenaban si el nombre de la plantilla empezada con el carácter de subrayado “_”.

- Se corrigió un problema donde la experiencia de creación de conjuntos de escalado de máquinas virtuales proporciona la versión 7.2 basada en CentOS como una opción para la implementación. CentOS 7.2 no está disponible en Azure Stack. Ahora se proporciona Centos 7.5 como la opción de implementación.

- Ahora puede quitar un conjunto de escalado desde la hoja **Conjuntos de escalado de máquinas virtuales**.

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1906 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1905 antes de actualizar Azure Stack a la versión 1906. Después de la actualización, instale las [revisiones disponibles para 1906](#after-successfully-applying-the-1906-update).

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1906-update"></a>Antes de aplicar la actualización 1906

La versión 1906 de Azure Stack debe aplicarse en la versión 1905 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Después de aplicar correctamente la actualización 1906

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1906.15.60](https://support.microsoft.com/help/4524559)
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="automatic-update-notifications"></a>Notificaciones de actualización automáticas

Los sistemas que pueden acceder a Internet desde la red de infraestructura verán el mensaje **Actualización disponible** en el portal del operador. Los sistemas sin acceso a Internet pueden descargar e importar el archivo ZIP con el archivo .xml correspondiente.

> [!TIP]  
> Suscríbase a las siguientes fuentes *RRS* o *Atom* para mantenerse al día con las revisiones de Azure Stack:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archivar

Para acceder a las notas de la versión archivadas de una versión anterior, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda y seleccione la versión que quiere ver.

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1906"
Puede consultar las [notas de las versiones anteriores de Azure Stack en la Galería de TechNet](https://aka.ms/azsarchivedrelnotes). Estos documentos archivados se proporcionan únicamente con fines de referencia y no implican que estas versiones sean compatibles. Para obtener información sobre la compatibilidad con Azure Stack, consulte [Directiva de mantenimiento de Azure Stack](azure-stack-servicing-policy.md). Para obtener más ayuda, póngase en contacto con los servicios de asistencia al cliente de Microsoft.
::: moniker-end


