---
title: Implementación de una aplicación web de C# ASP.Net en una máquina virtual en Azure Stack | Microsoft Docs
description: Implemente una aplicación web de C# ASP.Net en una máquina virtual en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 3f925d7c6a7f08257dbcb054044403e1488d38cb
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482401"
---
# <a name="how-to-deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>Implementación de una aplicación web de C# ASP.Net en una máquina virtual en Azure Stack

Puede crear una máquina virtual para hospedar la aplicación web de C# (ASP.NET) en Azure Stack. En este artículo se examinan los pasos que debería seguir para ajustar los parámetros del servidor, configurarlo para hospedar su aplicación web de C# (ASP.NET) y, a continuación, implementar la aplicación directamente desde Visual Studio.

C# es un lenguaje de programación multiparadigma destinado a fines genéricos que abarca disciplinas de programación de tipado fuerte, de ámbito léxico, imperativas, declarativas, funcionales, genéricas y orientadas a objetos y componentes. Para conocer el lenguaje de programación de C# y consultar recursos adicionales para C#, consulte [la guía de C#](https://docs.microsoft.com/dotnet/csharp/).

En este artículo se usará una aplicación C# 6.0 que usa ASP.NET Core 2.2 y se ejecuta en un servidor Windows Server 2016.

## <a name="create-a-vm"></a>Crear una VM

1. Cree una [máquina virtual de Windows Server](azure-stack-quick-windows-portal.md).

2. Ejecute el siguiente script para instalar los componentes en la máquina virtual. El script:
      - Instale IIS (con la consola de administración).
      - Instale ASP.NET Core 4.6.

        ```PowerShell  
        # Install IIS (with Management Console)
        Install-WindowsFeature -name Web-Server -IncludeManagementTools
        
        # Install ASP.NET 4.6
        Install-WindowsFeature Web-Asp-Net45
        
        # Install Web Management Service
        Install-WindowsFeature -Name Web-Mgmt-Service
        ```

3. Descargue el [MSI para la implementación web 3.6](https://www.microsoft.com/download/details.aspx?id=43717). Instale desde el MSI y, a continuación, habilítelo en todas las características.

4. Instale el lote de hospedaje de .NET Core para 2.2 en el servidor. Para conocer los pasos, consulte [.NET Core Installer](https://dotnet.microsoft.com/download/dotnet-core/2.2) (Instalador de .NET Core). Asegúrese de que está utilizando la misma versión de .NET Core en el equipo de desarrollo y el servidor de destino.

5. Vuelva al portal de Azure Stack y abra los puertos en la configuración de red para la máquina virtual.

    1. Abra el portal de Azure Stack de su inquilino.
    2. Busque su máquina virtual. Es posible que haya anclado la máquina virtual en el panel; si no, puede buscarla en el cuadro **Buscar recursos**.
    3. Seleccionar **Redes**.
    4. Seleccione **Agregar regla de puerto de entrada** en la máquina virtual.
    1. Agregue una regla de seguridad de entrada para los puertos siguientes:

    | Port | Protocolo | DESCRIPCIÓN |
    | --- | --- | --- |
    | 80 | HTTP | El protocolo de transferencia de hipertexto (HTTP) es un protocolo de aplicación para los sistemas de información de hipermedia distribuidos y colaborativos. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 443 | HTTPS | El protocolo de transferencia de hipertexto con cifrado de Capa de sockets seguros (HTPPS) es una extensión del Protocolo de transferencia de hipertexto (HTTP). Se usa para establecer una comunicación segura a través de la red de un equipo. Los clientes se conectarán a la aplicación web con la dirección IP pública o el nombre DNS de la máquina virtual. |
    | 22 | SSH | Secure Shell (SSH) es un protocolo de red criptográfico que permite usar servicios de red de forma segura a través de una red no segura. Usará esta conexión con un cliente SSH para configurar la máquina virtual e implementar la aplicación. |
    | 3389 | RDP | Opcional. El Protocolo de escritorio remoto permite que una conexión de escritorio remoto utilice una interfaz gráfica de usuario de su equipo.   |

    Para cada puerto:

    1. Seleccione **Cualquiera** para Origen.
    1. Escriba `*` para el intervalo de puertos de origen.
    1. Seleccione **Cualquiera** para **Destino**.
    1. Agregue el puerto que desea abrir al **intervalo de puertos de destino**.
    1. Seleccione **Cualquiera** para **Protocolo**.
    1. En **Acción**, seleccione **Permitir**.
    1. Deje el valor predeterminado para **Prioridad**.
    1. Ponga un **nombre** y añada una **descripción** para anotar por qué abrió el puerto.
    1. Seleccione Agregar.

5.  En la configuración de **Redes** para su máquina virtual en Azure Stack, cree un nombre DNS para el servidor. Los usuarios pueden conectarse a su sitio web utilizando la dirección URL.

    1. Abra el portal de Azure Stack de su inquilino.
    1. Busque su máquina virtual. Es posible que haya anclado la máquina virtual en el panel; si no, puede buscarla en el cuadro **Buscar recursos**.
    1. Seleccione **Información general**.
    1. Seleccione **Configurar** en la VM.
    1. Seleccione **Dinámica** para **Asignación**.
    1. Escriba la etiqueta de nombre DNS como `mywebapp` para que su dirección URL completa tenga un aspecto similar a: `mywebapp.local.cloudapp.azurestack.external`.

## <a name="create-an-app"></a>Creación de una aplicación 

Puede usar su propia aplicación web o bien el ejemplo de [Publicar una aplicación de ASP.NET Core en Azure con Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
).

En este artículo se describe cómo crear y publicar una aplicación web ASP.NET en una máquina virtual de Azure mediante la característica de publicación Microsoft Azure Virtual Machines en Visual Studio 2017. Una vez que haya instalado su aplicación y se haya asegurado de que se ejecuta localmente, actualizará su destino de publicación a la máquina virtual de Windows en su instancia de Azure Stack.

## <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación

Cree un destino de publicación a la máquina virtual en Azure Stack.

1. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y seleccione **Publicar**.

    ![Implementación de una aplicación web ASP.NET en Azure Stack y publicación](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

2.  En la ventana Publicar, haga clic en Nuevo perfil.
3. Seleccione IIS, FTP, etc.
4. Seleccione Publicar.

5.  Seleccione **Web Deploy** como **método de publicación**.
6.  Para **Servidor** escriba el nombre DNS que definió anteriormente, como `w21902.local.cloudapp.azurestack.external`
7.  Para **Nombre del sitio**, escriba `Default Web Site`.
8.  Para **Nombre de usuario**, escriba el nombre de usuario para la máquina.
9.  Para **Contraseña**, escriba la contraseña de la máquina.
10. Para **Dirección URL de destino**, escriba la dirección URL del sitio, como `mywebapp.local.cloudapp.azurestack.external`.

    ![Implementación de la aplicación web de ASP.NET: configuración de Web Deploy](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

9. Seleccione **Validar conexión** para validar la configuración de Web Deploy. Y luego haga clic en **Siguiente**.
10. Establezca su **Configuración** como **Versión**.
11. Establezca el **Marco de destino** como **netcoreapp2.2**.
12. Establezca el **Tiempo de ejecución de destino** como **Portátil**.
13. Seleccione **Guardar**.
14. Seleccione **Publicar**.
15. Vaya a su nuevo servidor. Debería ver la aplicación web en ejecución.

```HTTP  
    mywebapp.local.cloudapp.azurestack.external
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de cómo [desarrollar para Azure Stack](azure-stack-dev-start.md)
- Obtenga información sobre las [implementaciones comunes para Azure Stack como IaaS](azure-stack-dev-start-deploy-app.md).