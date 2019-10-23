---
title: Integrar una solución de supervisión externa con Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo integrar Azure Stack con una solución de supervisión externa en el centro de datos.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 06/05/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: aa9b20b9ee80cfdb17dba3020c03718085d8b625
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277178"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrar una solución de supervisión externa con Azure Stack

Para la supervisión externa de la infraestructura de Azure Stack, deberá supervisar el software Azure Stack, los equipos físicos y los modificadores de red físicos. Cada una de estas áreas ofrece un método para recuperar la información sobre el estado de mantenimiento y las alertas:

- El software Azure Stack ofrece una API basada en REST para recuperar el estado de mantenimiento y las alertas. El uso de tecnologías definidas por software, como Espacios de almacenamiento directo, las alertas y el estado de mantenimiento del almacenamiento forma parte de la supervisión de software.
- Los equipos físicos pueden hacer que la información acerca del estado de mantenimiento y las alertas esté disponible a través de los controladores de administración de placa base (BMC).
- Los dispositivos de red físicos pueden hacer que la información acerca del estado de mantenimiento y las alertas esté disponible a través del protocolo SNMP.

Cada solución de Azure Stack se suministra con un host de ciclo de vida de hardware. Este host ejecuta el software de supervisión del fabricante del hardware OEM para los dispositivos de red y servidores físicos. Compruebe con el proveedor de OEM si sus soluciones de supervisión se pueden integrar con las soluciones de supervisión existente de su centro de datos.

> [!IMPORTANT]
> La solución de supervisión externa que use debe ser sin agente. No puede instalar agentes de terceros dentro de componentes de Azure Stack.

En el siguiente diagrama se muestra el flujo de tráfico entre un sistema integrado de Azure Stack, el host de ciclo de vida de hardware, una solución de supervisión externa y un sistema externo de recopilación de datos y vales.

![Diagrama que muestra el tráfico entre Azure Stack y las soluciones de vales y de supervisión.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> No se permite la integración de supervisión externa directamente con servidores físicos y la bloquean activamente las listas de control de acceso (ACL). Se admite la integración de supervisión externa directamente con dispositivos de red físicos. Consulte con su proveedor de OEM cómo habilitar esta característica.

En este artículo se explica cómo integrar Azure Stack con soluciones de supervisión externas, como System Center Operations Manager y Nagios. También se incluye cómo trabajar con alertas mediante programación con PowerShell o a través de llamadas a la API de REST.

## <a name="integrate-with-operations-manager"></a>Integración con Operations Manager

Puede usar Operations Manager para realizar una supervisión externa de Azure Stack. El Módulo de administración de System Center para Microsoft Azure Stack le permite supervisar varias implementaciones de Azure Stack con una única instancia de Operations Manager. El módulo de administración usa las API REST del proveedor de recursos de mantenimiento y actualización para comunicarse con Azure Stack. Si tiene pensado omitir el software de supervisión de OEM que se ejecuta en el host de ciclo de vida de hardware, puede instalar los módulos de administración del proveedor para supervisar los servidores físicos. También puede utilizar la detección de dispositivos de red de Operations Manager para supervisar los modificadores de red.

El módulo de administración de Azure Stack proporciona las siguientes funcionalidades:

- Puede administrar varias implementaciones de Azure Stack.
- Es compatible con Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS).
- Puede recuperar y cerrar alertas.
- Dispone de un panel de estado de mantenimiento y de un panel de capacidad.
- Incluye la detección del modo de mantenimiento automático para cuando hay revisiones y actualizaciones en curso.
- Incluye tareas de Forzar actualización para implementaciones y regiones.
- Puede agregar información personalizada a una región.
- Admite notificaciones e informes.

Para descargar el módulo de administración de System Center y la guía de usuario asociada, consulte [Descarga del módulo de administración de System Center para Microsoft Azure Stack](https://www.microsoft.com/en-us/download/details.aspx?id=55184). También puede descargarlo directamente desde Operations Manager.

Para obtener una solución de vales, puede integrar Operations Manager con System Center Service Manager. El conector de producto integrado habilita la comunicación bidireccional que permite cerrar una alerta de Azure Stack y Operations Manager después de resolver una solicitud de servicio en Service Manager.

En el diagrama siguiente se muestra la integración de Azure Stack con una implementación de System Center existente. Puede automatizar Service Manager aún más con System Center Orchestrator o Service Management Automation (SMA) para ejecutar las operaciones en Azure Stack.

![Diagrama que muestra la integración con OM, Service Manager y SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integración con Nagios

Puede instalar y configurar el complemento Nagios para Microsoft Azure Stack.

Se ha desarrollado un complemento de supervisión de Nagios junto con las soluciones del asociado Cloudbase que está disponible bajo la permisiva licencia de software gratuito, MIT (Instituto Tecnológico de Massachusetts).

Este complemento se escribe en Python y aprovecha la API de REST del proveedor de recursos de mantenimiento. Ofrece la funcionalidad básica para recuperar y cerrar alertas de Azure Stack. Al igual que el Módulo de administración de System Center, le permite agregar varias implementaciones de Azure Stack y enviar notificaciones.

Con la versión 1.2, el complemento Nagios para Azure Stack aprovecha la biblioteca Microsoft ADAL y admite la autenticación mediante una entidad de servicio con un secreto o certificado. Además, la configuración se ha simplificado mediante el uso de un único archivo de configuración con parámetros nuevos. Ahora admite implementaciones de Azure Stack que usan Azure AD y AD FS como sistema de identidad.

El complemento funciona con Nagios 4 x y XI. Para descargar el complemento, consulte [Supervisión de alertas de Azure Stack ](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). El sitio de descarga también incluye detalles de instalación y configuración.

### <a name="requirements-for-nagios"></a>Requisitos de Nagios

1.  La versión mínima de Nagios es la 4.x

2.  Biblioteca Python de Microsoft Azure Active Directory. Se puede instalar esta biblioteca mediante Python PIP.

    ```bash  
    sudo pip install adal pyyaml six
    ```

### <a name="install-plugin"></a>Instalación del complemento

En esta sección se describe cómo instalar el complemento de Azure Stack, suponiendo que la instalación de Nagios es la predeterminada.

El paquete del complemento contiene los archivos siguientes:

```
azurestack_plugin.py
azurestack_handler.sh
samples/etc/azurestack.cfg
samples/etc/azurestack_commands.cfg
samples/etc/azurestack_contacts.cfg
samples/etc/azurestack_hosts.cfg
samples/etc/azurestack_services.cfg
```

1.  Copie el complemento `azurestack_plugin.py` en el directorio siguiente: `/usr/local/nagios/libexec`.

2.  Copie el controlador `azurestack_handler.sh` en el directorio siguiente: `/usr/local/nagios/libexec/eventhandlers`.

3.  Asegúrese de que se haya establecido que el archivo del complemento sea ejecutable:

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Configuración del complemento

Los parámetros siguientes están disponibles para configurarse en el archivo azurestack.cfg. Los parámetros en negrita deben configurarse de forma independiente del modelo de autenticación que elija.

Para más información sobre cómo crear un nombre de entidad de seguridad de servicio, consulte [Uso de una identidad de aplicación para acceder a recursos](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals).

| Parámetro | DESCRIPCIÓN | Authentication |
| --- | --- | --- |
| **External_domain_fqdn ** | Nombre de dominio completo externo |    |
| **region: ** | Nombre de región |    |
| **tenant_id: ** | Id. de inquilino\* |    |
| client_id: | Id. de cliente | SPN con secreto |
| client_secret: | Contraseña de cliente | SPN con secreto |
| client_cert\*\*: | Ruta de acceso al certificado | SPN con certificado |
| client_cert_thumbprint\*\*: | Huella digital de certificado | SPN con certificado |

\*El identificador de inquilino no es necesario para las implementaciones de Azure Stack con AD FS.

El \*\* secreto de cliente y el certificado de cliente son mutuamente excluyentes.

Los otros archivos de configuración contienen valores de configuración opcionales, ya que se pueden configurar en Nagios también.

> [!Note]  
> Compruebe el destino de la ubicación en azurestack_hosts.cfg y azurestack_services.cfg.

| Configuración | DESCRIPCIÓN |
| --- | --- |
| azurestack_commands.cfg | No se requieren cambios en la configuración del |
| azurestack_contacts.cfg | Configuración de notificación |
| azurestack_hosts.cfg | Nomenclatura de la implementación de Azure Stack |
| azurestack_services.cfg | Configuración del servicio |

### <a name="setup-steps"></a>Pasos de configuración

1.  Modifique el archivo de configuración.

2.  Copie los archivos de configuración modificados en la carpeta `/usr/local/nagios/etc/objects`.

### <a name="update-nagios-configuration"></a>Actualización de la configuración de Nagios

La configuración de Nagios debe actualizarse para asegurarse de que el complemento Nagios para Azure Stack está cargado.

1.  Abra el siguiente archivo:

```bash  
/usr/local/nagios/etc/nagios.cfg
```

2.  Agregue la siguiente entrada:

```bash  
# Load the Azure Stack Plugin Configuration
cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
```

3.  Vuelva a cargar Nagios.

```bash  
sudo service nagios reload
```

### <a name="manually-close-active-alerts"></a>Cierre manual de las alertas activas

Las alertas activas se pueden cerrar dentro de Nagios mediante la funcionalidad de notificación personalizada. La notificación personalizada debe ser:

```
/close-alert <ALERT_GUID>
```

También se puede cerrar una alerta desde un terminal con el comando siguiente:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>solución de problemas

La solución de problemas del complemento se puede realizar llamando al complemento manualmente en un terminal. Use el siguiente método:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Usar PowerShell para supervisar el estado de mantenimiento y las alertas

Si no está utilizando una solución basada en Nagios, Nagios o Operations Manager, puede usar PowerShell para habilitar una amplia gama de soluciones de supervisión que pueden integrarse con Azure Stack.

1. Para usar PowerShell, asegúrese de tener [PowerShell instalado y configurado](azure-stack-powershell-install.md) para un entorno de operador de Azure Stack. Instale PowerShell en un equipo local que alcance el punto de conexión de Resource Manager (administrador) (https://adminmanagement.[region].[External_FQDN]).

2. Ejecute los comandos siguientes para conectarse con el entorno de Azure Stack como un operador de Azure Stack:

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Utilice comandos como los que se indican en los ejemplos siguientes para trabajar con alertas:
   ```powershell
    # Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    # Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    # Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    # Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Más información

Para obtener información acerca de la supervisión de estado de mantenimiento integrada, consulte [Supervisar el estado y las alertas en Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Pasos siguientes

[Integración de seguridad](azure-stack-integrate-security.md)
