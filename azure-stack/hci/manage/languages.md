---
title: Cambio de idioma en Azure Stack HCl
description: En este tema se proporcionan instrucciones sobre cómo cambiar los idiomas del sistema operativo de Azure Stack HCI, Windows 10, Windows Admin Center y Microsoft Edge.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 09245e4498e87896b95df993e23693ae1ed23eaf
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947340"
---
# <a name="change-languages-in-azure-stack-hci"></a>Cambio de idioma en Azure Stack HCl

>Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Puede cambiar su idioma preferido en un equipo de administración, Windows Admin Center, Microsoft Edge y el sistema operativo de Azure Stack HCI en los servidores que administra. Windows Admin Center permanece en el lenguaje que elija, independientemente de los cambios de idioma que se realicen en estos otros recursos.

En este artículo se explica cómo cambiar los idiomas en:
- Windows 10
- Windows Admin Center
- Microsoft Edge
- Server Core en el sistema operativo de Azure Stack HCI

## <a name="change-the-language-in-the-management-pc"></a>Elección del idioma en el equipo de administración
Si usa Windows 10 en el equipo de administración, puede instalar un paquete de idioma para usarlo en Windows 10, las aplicaciones que usa y los sitios web que visita. También puede cambiar el idioma del teclado y establecer el idioma de entrada en un orden de preferencia de idioma para sitios web y aplicaciones.

Cambiar el idioma que usa en Windows 10 o del teclado no afecta al idioma para mostrar de Windows Admin Center.

   >[!IMPORTANT]
   > Después de cambiar el idioma en Windows 10, le recomendamos que establezca su idioma de teclado preferido como predeterminado antes de cerrar la sesión para evitar posibles problemas de entrada de teclado.

Para obtener más información, consulte [Administración de la configuración del idioma de entrada y para mostrar en Windows 10](https://support.microsoft.com/help/4496404/windows-10-manage-the-input-and-display-language).

## <a name="change-the-language-in-windows-admin-center"></a>Cambio del idioma en Windows Admin Center
Puede cambiar el idioma en Windows Admin Center y el formato de región según sea necesario en función de su ubicación. Cambiar cualquiera de estas opciones en Windows Admin Center no tiene ningún efecto en la configuración de idioma del equipo de administración que ejecuta Windows 10.

Para cambiar el idioma en Windows Admin Center:
1. En el panel **Todas las conexiones**, seleccione el icono de engranaje **Configuración** y, a continuación, en **Configuración**, seleccione **Idioma o región**.
1. Expanda la lista desplegable **Idioma** para seleccionar el idioma que quiera y, a continuación, expanda la lista desplegable **Formato regional** para seleccionar otra región, si es necesario.
1. Seleccione **Save and reload** (Guardar y volver a cargar).

    :::image type="content" source="media/languages/language-region.png" alt-text="Página Idioma o región en Windows Admin Center":::

Para obtener más información, consulte [Configuración de Windows Admin Center](/windows-server/manage/windows-admin-center/configure/settings).

## <a name="change-the-language-in-microsoft-edge"></a>Cambio del idioma en Microsoft Edge
Puede agregar idiomas admitidos en Microsoft Edge y cambiar el orden de las preferencias de idioma en el explorador. También puede agregar una extensión del traductor de idiomas extranjeros al explorador para obtener traducciones.

Para obtener más información, consulte [Compatibilidad de idiomas con Microsoft Edge](/deployedge/microsoft-edge-supported-languages).

## <a name="change-the-language-in-server-core"></a>Cambio del idioma en Server Core
Si necesita cambiar el idioma en Server Core del sistema operativo de Azure Stack HCI, le recomendamos que l haga después de agrupar los servidores en clústeres. Puede agregar los paquetes de idioma compatibles a Server Core y, a continuación, cambiar la distribución del teclado y el idioma por la que quiera usar. También puede usar un comando de Windows PowerShell para reemplazar el método de entrada de teclado y el idioma actual.

Cada paquete de idioma se instala en el directorio *%SystemRoot%\System32\\%Language-ID%* . Por ejemplo, *C:\Windows\System32\es-ES* es la ubicación del paquete de idioma español. Cada paquete de idioma ocupa aproximadamente 50 MB. Si quiere instalar los 38 paquetes de idioma, el tamaño de la imagen necesaria que creará será de aproximadamente 2 GB.

Para obtener más información, consulte [Idiomas disponibles para Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

Para obtener y agregar manualmente paquetes de idioma al sistema operativo:

1. Agregue un paquete de idioma a Server Core con la herramienta **DISM/Add-WindowsPackage**. El comando de PowerShell `Add-WindowsPackage` es el equivalente del ejecutable de DISM.

    Para obtener más información, consulte [Agregar idiomas a imágenes de Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows)

1. Le recomendamos que agregue características a petición (FOD) de lenguaje para habilitar la funcionalidad adicional en el lenguaje agregado, como en el ejemplo siguiente:

    ```DOS
    dism /online /add-capability /capabilityname:Language.Basic~~~de-de~0.0.1.0
    ```

    Para obtener más información, consulte [Características a petición (FOD) de idioma y región](/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

1. Puede usar el cmdlet **Set-WinUILanguageOverride** de PowerShell para cambiar el idioma de la interfaz de usuario de Windows en el sistema operativo de la cuenta de usuario actual. En el ejemplo siguiente, `de-DE` especifica el alemán como la configuración de idioma actual en el sistema operativo:

    ```PowerShell
    Set-WinUILanguageOverride de-DE
    ```

    Para obtener más información, consulte [Set-WinUILanguageOverride](/powershell/module/international/set-winuilanguageoverride?view=win10-ps).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, vea también:

- [Adición o eliminación de servidores para un clúster de Azure Stack HCI](./add-cluster.md)
