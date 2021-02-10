---
title: Notas de la versión 1910 de Azure Stack Hub
description: Notas de la versión 1910 para los sistemas integrados de Azure Stack Hub, incluidas las actualizaciones y correcciones de errores.
author: sethmanheim
ms.topic: article
ms.date: 01/25/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: ced055a2983fb4aabb1c31f314ccb4587f3801cc
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248770"
---
# <a name="azure-stack-hub-1910-release-notes"></a>Notas de la versión 1910 de Azure Stack Hub

En este artículo se describe el contenido de los paquetes de actualización de Azure Stack Hub. La actualización incluye mejoras y correcciones de la versión más reciente de Azure Stack Hub.

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack Hub. No lo aplique al Kit de desarrollo de Azure Stack (ASDK).

> [!IMPORTANT]  
> Si a su instancia le faltan más de dos actualizaciones de Azure Stack Hub, se considera que no cumple los requisitos. [Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.](../azure-stack-servicing-policy.md#keep-your-system-under-support)

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Lista de comprobación de las actividades antes y después de aplicar la actualización](../release-notes-checklist.md)
- [Problemas conocidos](../known-issues.md)
- [Revisiones](#hotfixes)
- [Actualizaciones de seguridad](../release-notes-security-updates.md)

Para obtener ayuda con la solución de problemas de actualizaciones y el proceso de actualización, consulte [Solución de problemas de actualizaciones y revisiones para Azure Stack Hub](../azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Descarga de la actualización

Se puede descargar el paquete de actualizaciones de Azure Stack Hub con la [herramienta de descarga de actualizaciones de Azure Stack Hub](https://aka.ms/azurestackupdatedownload).

## <a name="1910-build-reference"></a>Referencia de la compilación 1910

El número de compilación de la actualización 1910 de Azure Stack Hub es **1.1910.0.58**.

### <a name="update-type"></a>Tipo de actualización

A partir de la versión 1908, el sistema operativo subyacente en el que se ejecuta Azure Stack Hub se actualizó a Windows Server 2019. Esta actualización permite conseguir mejoras fundamentales básicas y disponer de la capacidad de incorporar funciones adicionales a Azure Stack Hub.

El tipo de compilación de la actualización 1910 de Azure Stack Hub es **Rápido**.

El tamaño del paquete de la actualización 1910 es mayor en comparación con actualizaciones anteriores, lo cual conlleva tiempos de descarga superiores. La actualización permanecerá en el estado **Preparando** durante bastante tiempo; los operadores pueden prever que este proceso tardará más que en las actualizaciones anteriores. El tiempo esperado que tarda en completarse la actualización 1910 es de aproximadamente 10 horas, independientemente del número de nodos físicos del entorno de Azure Stack Hub. Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen las cargas de trabajo de inquilino en el sistema, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema. Los tiempos de ejecución superiores al valor esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack Hub, a menos que se produzca un error de actualización. Esta aproximación del tiempo de ejecución es específica para la actualización 1910 y no se debe comparar con otras actualizaciones de Azure Stack Hub.

Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack Hub](../azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- El portal del administrador muestra ahora las direcciones IP de punto de conexión con privilegios en el menú de propiedades de la región, para facilitar la detección. Además, muestra el servidor horario y los reenviadores DNS configurados actuales. Para más información, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](../azure-stack-privileged-endpoint.md).

- El sistema de mantenimiento y supervisión de Azure Stack Hub ahora puede generar alertas para diversos componentes de hardware si se produce un error. Estas alertas requieren una configuración adicional. Para más información, consulte [Supervisión de componentes de hardware de Azure Stack Hub](../azure-stack-hardware-monitoring.md).

- [Compatibilidad con cloud-init para Azure Stack Hub](/azure/virtual-machines/linux/using-cloud-init): cloud-init es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Dado que se llama a cloud-init durante el proceso de arranque inicial, no se necesitan pasos adicionales ni agentes para aplicar la configuración. Las imágenes de Ubuntu en Marketplace se han actualizado para admitir cloud-init para el aprovisionamiento.

- Azure Stack Hub ahora admite todas las versiones del agente de Linux para Windows Azure como Azure.

- Hay disponible una nueva versión de los módulos de administración de PowerShell para Azure Stack Hub. <!-- For more information, see -->

- El 15 de abril de 2020 se lanzaron nuevos módulos de inquilino de Azure PowerShell para Azure Stack Hub. Los módulos de Azure Resource Manager no dejarán de funcionar, pero dejarán de actualizarse después de la versión 2002.

- Se ha agregado el cmdlet **Set-AzSDefenderManualUpdate** en el punto de conexión con privilegios (PEP) para configurar la actualización manual de las definiciones de Windows Defender en la infraestructura de Azure Stack Hub. Para más información, consulte [Actualización de Antivirus de Windows Defender en Azure Stack Hub](../azure-stack-security-av.md).

- Se ha agregado el cmdlet **Set-AzSDnsForwarder** en el punto de conexión con privilegios (PEP) para cambiar la configuración de reenviador de los servidores DNS en Azure Stack Hub. Para más información acerca de la configuración de DNS, consulte [Integración de DNS del centro de datos de Azure Stack Hub](../azure-stack-integrate-dns.md).

- Se ha agregado compatibilidad con la administración de **clústeres de Kubernetes** mediante el [motor de AKS](../../user/azure-stack-kubernetes-aks-engine-overview.md). A partir de esta actualización, los clientes pueden implementar clústeres de Kubernetes de producción. El motor de AKS permite a los usuarios:
  - Administrar el ciclo de vida de los clústeres de Kubernetes. Pueden crear, actualizar y escalar clústeres.
  - Mantener los clústeres mediante imágenes administradas generadas por AKS y los equipos de Azure Stack Hub.
  - Aprovechar las ventajas de un proveedor de nube de Kubernetes integrado en Azure Resource Manager que compila clústeres mediante recursos nativos de Azure.
  - Implementar y administrar los clústeres en sellos de Azure Stack Hub conectados o desconectados.
  - Usar características híbridas de Azure:
    - Integración con Azure Arc
    - Integración con Azure Monitor para Containers.
  - Usar contenedores de Windows con el motor de AKS.
  - Recibir soporte técnico y de ingeniería de Soporte técnico de Microsoft para sus implementaciones.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- Azure Stack Hub ha mejorado la capacidad de corregir de forma automática algunos problemas de revisión y actualización que antes causaban errores de actualización o impedían que los operadores iniciaran una actualización de Azure Stack Hub. Como resultado, se incluyen menos pruebas en el grupo **Test-AzureStack -UpdateReadiness**. Para más información, consulte [Validación del estado del sistema de Azure Stack Hub](../azure-stack-diagnostic-test.md#groups). Las tres pruebas siguientes aún se mantienen en el grupo **UpdateReadiness**:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Se ha agregado una regla de auditoría para notificar cuando se monta un dispositivo externo (por ejemplo, una llave USB) en un nodo de la infraestructura de Azure Stack Hub. El registro de auditoría se emite a través de Syslog y aparece como **Microsoft-Windows-Security-Auditing: 6416|Plug and Play Events** (Eventos Plug and Play). Para más información sobre cómo configurar el cliente de Syslog, consulte [Reenvío de Syslog](../azure-stack-integrate-security.md).

- Azure Stack Hub cambia a las claves RSA de 4096 bits para los certificados internos. La ejecución de la rotación interna de secretos reemplazará los antiguos certificados de 2048 bits por certificados largos de 4096 bits. Para más información sobre la rotación de secretos en Azure Stack Hub, consulte [Cambio de secretos en Azure Stack Hub](../azure-stack-rotate-secrets.md).

- Actualizaciones en la complejidad de los algoritmos criptográficos y la seguridad de clave de varios componentes internos para cumplir con la directiva 15 del Comité de Sistemas Nacionales de Seguridad estadounidense (CNSSP-15), que proporciona procedimientos recomendados para el uso de estándares públicos sobre el uso compartido de la información de forma segura. Entre las mejoras, se incluye AES256 para la autenticación Kerberos y SHA384 para el cifrado VPN. Para más información sobre CNSSP-15, consulte la [página de directivas del Comité de Sistemas Nacionales de Seguridad](http://www.cnss.gov/CNSS/issuances/Policies.cfm) estadounidense.

- Debido a la actualización anterior, Azure Stack Hub ahora tiene nuevos valores predeterminados para las configuraciones de IPsec/IKEv2. Los nuevos valores predeterminados que se usan en Azure Stack Hub son los siguientes:

   **Parámetros de la fase 1 de IKE (modo principal)**

   | Propiedad              | Value|
   |-|-|
   | Versión de IKE           | IKEv2 |
   |Grupo Diffie-Hellman   | ECP384 |
   | Método de autenticación | Clave precompartida |
   |Algoritmos de cifrado y hash | AES256, SHA384 |
   |Vigencia de SA (tiempo)     | 28.800 segundos|

   **Parámetros de la fase 2 de IKE (modo rápido)**

   | Propiedad| Value|
   |-|-|
   |Versión de IKE |IKEv2 |
   |Cifrados y algoritmos hash (cifrado)     | GCMAES256|
   |Cifrados y algoritmos hash (autenticación) | GCMAES256|
   |Vigencia de SA (tiempo)  | 27 000 segundos  |
   |Vigencia de SA (Kilobytes) | 33 553 408     |
   |Confidencialidad directa perfecta (PFS) | ECP384 |
   |Detección de cuellos del mismo nivel | Compatible|

   Estos cambios también se reflejan en la documentación de la [propuesta predeterminada de IPsec/IKE](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters).

- El servicio de copia de seguridad de la infraestructura mejora la lógica que calcula el espacio libre deseado para las copias de seguridad, en lugar de basarse en un umbral fijo. El servicio utilizará el tamaño de una copia de seguridad, la directiva de retención, la reserva y el uso actual de la ubicación de almacenamiento externo para determinar si es necesario enviar una advertencia al operador.

### <a name="changes"></a>Cambios

- Al descargar elementos de Marketplace de Azure en Azure Stack Hub, hay una nueva interfaz de usuario que permite especificar una versión del elemento, en caso de que existan varias versiones. La nueva interfaz de usuario está disponible tanto en escenarios conectados como desconectados. Para más información, consulte [Descarga de elementos de Marketplace existentes desde Azure en Azure Stack Hub](../azure-stack-download-azure-marketplace-item.md).  

- A partir de la versión 1910, el sistema de Azure Stack Hub **requiere** un espacio de direcciones IP internas privadas adicional /20. Para más información, consulte [Planeación de la integración de red para Azure Stack](../azure-stack-network.md).
  
- El servicio de copia de seguridad de la infraestructura elimina los datos de copias de seguridad cargadas parcialmente si la ubicación de almacenamiento externo se queda sin capacidad durante el procedimiento de carga.  

- En las implementaciones de AAD, el servicio de copia de seguridad de la infraestructura agrega el servicio de identidad a la carga útil de copia de seguridad.  

- El módulo de PowerShell para Azure Stack Hub se ha actualizado a la versión 1.8.0 para la versión 1910.<br>Los cambios incluyen:
   - **Nuevo módulo de administración DRP**: Deployment Resource Provider (DRP) permite las implementaciones organizadas de proveedores de recursos en Azure Stack Hub. Estos comandos interactúan con el nivel de Azure Resource Manager para interactuar con DRP.
   - **BRP**: <br />
           - Compatibilidad con la restauración de rol único para la copia de seguridad de infraestructura de Azure Stack. <br />
           - Incorporación del parámetro `RoleName` al cmdlet `Restore-AzsBackup`.
   - **FRP**: Cambios importantes en los recursos **Unidad** y **Volumen** con la versión de API `2019-05-01`. Las características son compatibles con Azure Stack Hub 1910 y versiones posteriores: <br />
            - Se han cambiado los valores de `ID`, `Name`, `HealthStatus` y `OperationalStatus`. <br />
            - Nuevas propiedades compatibles `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer` y `StoragePool` para recursos de **Unidad**. <br />
            - Las propiedades `CanPool` y `CannotPoolReason` de los recursos de **Unidad** están en desuso; en su lugar, use `OperationalStatus`.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- Se corrigió un problema que impedía la aplicación de la directiva TLS 1.2 en entornos implementados antes de la versión 1904 de Azure Stack Hub.
- Se ha corregido un problema por el que una máquina virtual de Ubuntu 18.04 creada con la autorización de SSH habilitada no permitía usar las claves SSH para iniciar sesión.
- Se quitó la opción **Restablecer contraseña** de la interfaz de usuario del conjunto de escalado de máquinas virtuales.
- Se corrigió un problema por el que la eliminación del equilibrador de carga del portal no eliminaba el objeto en el nivel de infraestructura.
- Se corrigió un problema que mostraba un porcentaje inexacto de la alerta de uso del grupo de servidores de puerta de enlace en el portal de administración.
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack Hub, consulte [Actualizaciones de seguridad de Azure Stack Hub](../release-notes-security-updates.md).

El informe de vulnerabilidad de Qualys para esta versión se puede descargar del [sitio web de Qualys](https://www.qualys.com/azure-stack/).

## <a name="hotfixes"></a>Revisiones

Azure Stack Hub publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack Hub para la versión 1908 antes de actualizar Azure Stack Hub a la versión 1910.

> [!NOTE]
> Las revisiones de Azure Stack Hub son acumulativas; solo tiene que instalar la revisión más reciente para obtener todas las correcciones incluidas en las revisiones anteriores de esa versión.

Las revisiones de Azure Stack Hub solo son aplicables a los sistemas integrados de Azure Stack Hub; no intente instalar revisiones en el ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Requisitos previos: Antes de aplicar la actualización 1910

La versión 1910 de Azure Stack Hub debe aplicarse en la versión 1908 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Revisión 1.1908.51.133 de Azure Stack Hub](https://support.microsoft.com/help/4574734)

### <a name="after-successfully-applying-the-1910-update"></a>Después de aplicar correctamente la actualización 1910

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Revisión 1.1910.63.186 de Azure Stack Hub](https://support.microsoft.com/help/4574735)
