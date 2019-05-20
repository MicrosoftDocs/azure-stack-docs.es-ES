---
title: Hoja de cálculo de implementación para sistemas integrados de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo instalar y usar la herramienta de hoja de cálculo de implementación para implementar Azure Stack.
services: azure-stack
documentationcenter: ''
author: wamota
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: d75915f110b6316f4621f66b1f91b010f735d165
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172665"
---
# <a name="deployment-worksheet-for-azure-stack-integrated-systems"></a>Hoja de cálculo de implementación para sistemas integrados de Azure Stack

La hoja de cálculo de implementación de Azure Stack es una aplicación de Windows Forms que agrega todas las decisiones y toda la información de implementación necesarias en un solo lugar. Puede rellenar la hoja de cálculo de implementación durante el proceso de planeamiento y revisarla antes de iniciar la implementación.

La información necesaria para la hoja de cálculo es de redes, seguridad e identidad. Requiere decisiones importantes que pueden exigir conocimientos en muchas áreas distintas; por lo tanto, es posible que quiera ponerse en contacto con los equipos que poseen experiencia en estas áreas para rellenar la hoja de cálculo.

Al rellenar la hoja de cálculo, es posible que tenga que realizar algunos cambios en la configuración previa a la implementación en su entorno de red. Esto podría incluir la reserva de espacios de direcciones IP para la solución Azure Stack, y la configuración de enrutadores, conmutadores y firewalls para prepararlos para la conectividad con la nueva solución Azure Stack.

> [!NOTE]
> Para obtener más información sobre cómo utilizar la herramienta de implementación de la hoja de cálculo, consulte [este artículo en la documentación de Azure Stack](azure-stack-datacenter-integration.md).

[![Hoja de cálculo de implementación](media/azure-stack-deployment-worksheet/depworksheet.png "Deployment Worksheet")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>Instalación del módulo de Windows PowerShell

Para cada versión de la hoja de cálculo de implementación, debe realizar una instalación única de un módulo de Powershell para cada máquina en la que quiera usar la hoja de cálculo de implementación.

> [!NOTE]  
> El equipo debe estar conectado a Internet para que este método funcione.

1. Abra un símbolo del sistema de PowerShell con privilegios elevados.

2. En la ventana de PowerShell, instale el módulo desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/):

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

Si recibe un mensaje sobre la instalación desde un repositorio no confiable, presione **Y** para continuar con la instalación.

## <a name="use-the-deployment-worksheet-tool"></a>Uso de la herramienta de hoja de cálculo de implementación

Para iniciar y usar la hoja de cálculo de implementación en un equipo en el que ha instalado el módulo de PowerShell de la hoja de cálculo de implementación, realice los pasos siguientes:

1. Inicie Windows PowerShell (no use PowerShell ISE, ya que pueden producirse resultados inesperados). No es necesario ejecutar PowerShell como administrador.

2. Importe el módulo de PowerShell **AzS.Deployment.Worksheet**:

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. Una vez importado el módulo, inicie la hoja de cálculo de implementación:

   ```PowerShell
   Start-DeploymentWorksheet
   ```

La hoja de cálculo de implementación consta de pestañas independientes para recopilar la configuración del entorno, como **Customer Settings** (Configuración de cliente), **Network Settings** (Configuración de red) y **Scale Unit #** (N.º de unidad de escalado). Debe proporcionar todos los valores (excepto los marcados como **Opcional**) en todas las pestañas para poder generar cualquier archivo de datos de configuración. Una vez introducidos todos los valores necesarios en la herramienta, puede usar el menú **Acción** para ejecutar las opciones **Importar**, **Exportar** y **Generar**. Los archivos JSON necesarios para la implementación son los siguientes:

**Importar**: le permite importar un archivo de datos de configuración de Azure Stack (ConfigurationData.json) generado con esta herramienta, o bien los creados con cualquier versión anterior de la hoja de cálculo de implementación. Al realizar una importación se restablecen los formularios y se elimina cualquier dato generado u opción de configuración introducida previamente.

**Exportar**: valida los datos introducidos actualmente en los formularios, genera las subredes IP y las asignaciones y, a continuación, guarda el contenido como archivos de configuración con formato JSON. Posteriormente, puede utilizar estos archivos para generar la configuración de red e instalar Azure Stack.

**Generar**: valida los datos introducidos actualmente y genera el mapa de red sin necesidad de exportar los archivos JSON de implementación. Se crean dos nuevas pestañas si **Generar** se ejecuta correctamente: **Subnet Summary** (Resumen de subred) y **IP Assignments** (Asignaciones de IP). Puede analizar los datos en estas pestañas para asegurarse de que las asignaciones de red sean las previstas.

**Borrar todo**: borra todos los datos introducidos actualmente en los formularios y los devuelve a los valores predeterminados.

**Guardar o abrir el trabajo en curso**: puede guardar y abrir datos introducidos parcialmente cuando trabaje en ellos mediante los menús **Archivo -> Guardar** y **Archivo -> Abrir**. Son diferentes de las funciones **Importar** y **Exportar**, ya que estas exigen que todos los datos estén introducidos y validados. Las opciones Abrir y Guardar no realizan la validación y no exigen que todos los campos se hayan introducido para guardar el trabajo en curso.

**Registro y mensajes de advertencia**: mientras se usa el formulario, es posible que se muestren mensajes de advertencia no críticos en la ventana de PowerShell. Los errores críticos se muestran como mensajes emergentes. El registro detallado opcional, incluido un registro escrito en el disco, puede habilitarse para ayudar a solucionar problemas.

Para iniciar la herramienta con el registro detallado:

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

Puede encontrar el registro guardado en el directorio **Temp** del usuario actual; por ejemplo: **C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**.

## <a name="next-steps"></a>Pasos siguientes

* [Modelos de conexión de la implementación de Azure Stack](azure-stack-connection-models.md)
