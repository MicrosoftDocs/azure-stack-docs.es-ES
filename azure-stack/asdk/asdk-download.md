---
title: Descargar y extraer el ASDK
description: Obtenga información sobre cómo descargar y extraer el Kit de desarrollo de Azure Stack (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 05/06/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 08/10/2019
ms.openlocfilehash: 3f8a0615f18ddf9ba4a98c2af2b947c49cfdd550
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873272"
---
# <a name="download-and-extract-the-asdk"></a>Descargar y extraer el ASDK
Después de asegurarse de que el equipo host del kit de desarrollo cumple los requisitos básicos para instalar el Kit de desarrollo de Azure Stack (ASDK), el siguiente paso es descargar y extraer el paquete de implementación de ASDK para obtener Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Descarga del ASDK
1. Antes de iniciar la descarga, asegúrese de que el equipo cumple con los requisitos previos siguientes:

   - El equipo debe tener al menos 60 GB de espacio en disco disponible en cuatro unidades de disco duro lógicas idénticas e independientes, además del disco de sistema operativo.
   - [.NET framework 4.6 (o una versión posterior)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) debe estar instalado.

2. [Vaya a la página de introducción](https://azure.microsoft.com/overview/azure-stack/try/?v=try) donde puede descargar el ASDK, proporcione sus datos y, a continuación, haga clic en **Submit** (Enviar).
3. Descargue y ejecute el [comprobador de implementaciones de ASDK](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409), que es el script de comprobación que constituye un requisito previo. Este script independiente revisa las comprobaciones de requisitos previos que realiza la configuración de ASDK. Ofrece una manera de confirmar que se están cumpliendo los requisitos de hardware y software, antes de descargar el paquete más grande de ASDK.
4. En **Descargar el software**, haga clic en **Kit de desarrollo de Azure Stack**.

   > [!NOTE]
   > La descarga de ASDK (AzureStackDevelopmentKit.exe) ocupa aproximadamente 10 GB.

## <a name="extract-the-asdk"></a>Extracción del ASDK
1. Una vez finalizada la descarga, haga clic en **Ejecutar** para iniciar el autoextractor de ASDK (AzureStackDevelopmentKit.exe).
2. Revise y acepte el contrato de licencia que aparece en la página **Contrato de licencia** del asistente del autoextractor y, a continuación, haga clic en **Siguiente**.
3. Revise la información sobre la declaración de privacidad que aparece en la página **Important Notice** (Aviso importante) del asistente del autoextractor y, a continuación, haga clic en **Next** (Siguiente).
4. Seleccione la ubicación de los archivos de instalación de Azure Stack que se van a extraer en la página **Seleccionar ubicación de destino** del asistente del autoextractor y, a continuación, haga clic en **Siguiente**. La ubicación predeterminada es *carpeta actual*\Azure Stack Development Kit. 
5. Revise el resumen de información de la ubicación de destino en la página **Ready to Extract** (Preparado para extraer) del asistente para extracción automática y, a continuación, haga clic en **Extraer** para extraer los archivos CloudBuilder.vhdx (de unos 28 GB) y ThirdPartyLicenses.rtf. Este proceso tarda un tiempo en completarse.
6. Copie o mueva el archivo CloudBuilder.vhdx a la raíz de la unidad C:\ (`C:\CloudBuilder.vhdx`) del equipo host de ASDK.

> [!NOTE]
> Después de extraer los archivos, puede eliminar los archivos EXE y .BIN para recuperar espacio en el disco duro. O bien, puede hacer una copia de seguridad de estos archivos, con el fin de no tener que volver a descargarlos si necesita implementar ASDK.


## <a name="next-steps"></a>Pasos siguientes
[Preparación del equipo host del Kit de desarrollo de Azure Stack](asdk-prepare-host.md)