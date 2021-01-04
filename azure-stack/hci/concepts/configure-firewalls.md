---
title: Configuración de firewalls para Azure Stack HCI
description: En este tema se proporcionan instrucciones sobre cómo configurar firewalls para el sistema operativo de Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 12/10/2020
ms.openlocfilehash: 374041b5472b20781f6c8648287d665925a8f227
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97623069"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>Configuración de firewalls para Azure Stack HCI

>Se aplica a: Azure Stack HCI, versión 20H2

En este tema se proporcionan instrucciones sobre cómo configurar firewalls para el sistema operativo de Azure Stack HCI. Se incluyen los requisitos de conectividad y se explica cómo las etiquetas de servicio agrupan las direcciones IP de Azure a las que el sistema operativo necesita acceder. En el tema también se proporcionan los pasos para actualizar Firewall de Microsoft Defender.

## <a name="connectivity-requirements"></a>Requisitos de conectividad
Azure Stack HCI necesita conectarse periódicamente a Azure. El acceso está limitado únicamente a:
- Direcciones IP conocidas de Azure
- Dirección de salida
- Puerto 443 (HTTPS)

Para obtener más información, consulte la sección "Conectividad de Azure Stack HCI" en [Preguntas más frecuentes sobre Azure Stack HCI](../faq.md).

En este tema se describe cómo se puede usar una configuración de firewall bloqueada para impedir todo el tráfico a todos los destinos, excepto los incluidos en la lista de permitidos.

   >[!IMPORTANT]
   > Si la conectividad saliente está restringida por el firewall corporativo externo o el servidor proxy, asegúrese de que las direcciones URL que aparecen en la tabla siguiente no estén bloqueadas. Para obtener información relacionada, consulte la sección "Configuración de red" de [Información general del agente de servidores habilitados para Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/agent-overview#networking-configuration).


Como se muestra a continuación, es posible que Azure Stack HCI acceda a Azure con más de un firewall.

:::image type="content" source="./media/configure-firewalls/firewalls-diagram.png" alt-text="En el diagrama se muestra cómo Azure Stack HCI accede a los puntos de conexión de las etiquetas de servicio a través del puerto 443 (HTTPS) de los firewalls." lightbox="./media/configure-firewalls/firewalls-diagram.png":::

## <a name="working-with-service-tags"></a>Trabajo con etiquetas de servicio
Una *etiqueta de servicio* representa un grupo de direcciones IP de un servicio de Azure determinado. Microsoft administra las direcciones IP incluidas en la etiqueta de servicio y actualiza la etiqueta automáticamente a medida que cambian las direcciones IP para reducir las actualizaciones al mínimo. Para más información, consulte [Etiquetas de servicio de red virtual](https://docs.microsoft.com/azure/virtual-network/service-tags-overview).

## <a name="required-endpoint-daily-access-after-azure-registration"></a>Acceso diario a puntos de conexión necesarios (después del registro de Azure)
Azure mantiene las direcciones IP conocidas de los servicios de Azure que se organizan mediante etiquetas de servicio. Azure publica un archivo JSON semanal de todas las direcciones IP para cada servicio. Aunque las direcciones IP no cambian con frecuencia, sí lo hacen varias veces al año. En la tabla siguiente se muestran los puntos de conexión de etiqueta de servicio a los que el sistema operativo necesita acceder.

| Descripción                   | Etiqueta de servicio para el intervalo IP  | URL                                                                                 |
| :-----------------------------| :-----------------------  | :---------------------------------------------------------------------------------- |
| Azure Active Directory        | AzureActiveDirectory      | `https://login.microsoftonline.com`<br> `https://graph.microsoft.com`               |
| Azure Resource Manager        | AzureResourceManager      | `https://management.azure.com`                        |
| Servicio en la nube de Azure Stack HCI | AzureFrontDoor.Frontend   | Depende de la región en la que se haya registrado:<br> Este de EE. UU.: `https://eus-azurestackhci-usage.azurewebsites.net`<br> Oeste de Europa: `https://weu-azurestackhci-usage.azurewebsites.net` |
| Azure Arc                     | AzureArcInfrastructure<br> AzureTrafficManager | Depende de la funcionalidad que desee usar:<br> Servicio de identidad híbrida: `*.his.arc.azure.com`<br> Configuración de invitado: `*.guestconfiguration.azure.com`<br> **Nota:** Se esperan más direcciones URL a medida que se habiliten más funciones. |

## <a name="update-microsoft-defender-firewall"></a>Actualización de Firewall de Microsoft Defender
En esta sección se muestra cómo configurar Firewall de Microsoft Defender para permitir que las direcciones IP asociadas a una etiqueta de servicio se conecten con el sistema operativo:

1. Descargue el archivo JSON del recurso siguiente en el equipo de destino que ejecuta el sistema operativo: [Rangos de direcciones IP y etiquetas de servicio de Azure: nube pública](https://www.microsoft.com/download/details.aspx?id=56519).

1. Use el siguiente comando de PowerShell para abrir el archivo JSON:

    ```powershell
    $json = Get-Content -Path .\ServiceTags_Public_20201012.json | ConvertFrom-Json
    ```

1. Obtenga la lista de intervalos de direcciones IP para una etiqueta de servicio determinada, como la etiqueta de servicio "AzureResourceManager":

    ```powershell
    $IpList = ($json.values | where Name -Eq "AzureResourceManager").properties.addressPrefixes
    ```

1. Importe la lista de direcciones IP al firewall corporativo externo, si está usando una lista de permitidos con él.

1. Cree una regla de firewall para cada servidor del clúster donde se permita el tráfico saliente de 443 (HTTPS) a la lista de intervalos de direcciones IP:

    ```powershell
    New-NetFirewallRule -DisplayName "Allow Azure Resource Manager" -RemoteAddress $IpList -Direction Outbound -LocalPort 443 -Protocol TCP -Action Allow -Profile Any -Enabled True
    ```

## <a name="additional-endpoint-for-one-time-azure-registration"></a>Punto de conexión adicional para el registro único de Azure
Durante el proceso de registro de Azure, cuando ejecuta `Register-AzStackHCI` o usa Windows Admin Center, el cmdlet intenta contactar con la Galería de PowerShell para comprobar que tiene la versión más reciente de los módulos de PowerShell necesarios, como Az y AzureAD. Aunque la Galería de PowerShell se hospeda en Azure, actualmente no dispone de etiqueta de servicio. Si no puede ejecutar el cmdlet `Register-AzStackHCI` desde un nodo de servidor debido a que no tiene acceso a Internet, se recomienda descargar los módulos en el equipo de administración y, después, transferirlos manualmente al nodo de servidor donde desea ejecutar el cmdlet.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, vea también:
- La sección de conectividad de [Preguntas más frecuentes sobre Azure Stack HCI](../faq.md)
