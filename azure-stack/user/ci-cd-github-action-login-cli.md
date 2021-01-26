---
title: Uso de la acción de inicio de sesión de Azure con la CLI de Azure y PowerShell en Azure Stack Hub
description: Use la acción de inicio de sesión de Azure con la CLI de Azure y PowerShell para crear un flujo de trabajo de integración e implementación continuas (CI/CD) en Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 1/11/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 1/11/2021
ms.openlocfilehash: 4413070dc3d55a7a879b5c4589d9f453a617e0e0
ms.sourcegitcommit: 51ce5ba6cf0a377378d25dac63f6f2925339c23d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224794"
---
# <a name="use-the-azure-login-action-with-azure-cli-and-powershell-on-azure-stack-hub"></a>Uso de la acción de inicio de sesión de Azure con la CLI de Azure y PowerShell en Azure Stack Hub

Puede configurar Acciones de GitHub para iniciar sesión en la instancia de Azure Stack Hub, ejecutar PowerShell y, a continuación, ejecutar un script de la CLI de Azure. Puede usarlo como base de un flujo de trabajo de integración e implementación continua (CI/CD) para su solución con Azure Stack Hub. Con este flujo de trabajo, puede automatizar la compilación, la prueba y la implementación de la solución para que pueda centrarse en escribir código. Por ejemplo, mediante la adición de algunas otras acciones podría usar este flujo de trabajo junto con una plantilla de Azure Resource Manager para aprovisionar una máquina virtual, validar un repositorio de aplicación y, a continuación, implementar una aplicación en esa máquina virtual cada vez que realice una fusión mediante combinación en una rama específica en GitHub. Por ahora, este artículo le ayudará a orientarse con las Acciones de GitHub y Azure Stack Hub.

Las Acciones de GitHub son flujos de trabajo que se componen de acciones que permiten la automatización directamente dentro del repositorio de código. Puede desencadenar los flujos de trabajo con eventos en el proceso de desarrollo de GitHub. Puede realizar tareas comunes de automatización de DevOps, como las pruebas, la implementación y la integración continua.

Para usar las Acciones de GitHub con Azure Stack Hub, debe usar una entidad de servicio (SPN) con requisitos específicos. En este artículo, creará una *ejecutor autohospedado*. GitHub le permite usar cualquier máquina a la que GitHub pueda acceder en las Acciones de GitHub. Puede crear una máquina virtual que actúe como su ejecutor en Azure, en Azure Stack Hub o en otro lugar.

Este flujo de trabajo de ejemplo incluye:
- Instrucciones para crear y validar el SPN.
- Configuración de una máquina con Windows Server 2016 Core como un ejecutor autohospedado de Acciones de GitHub para trabajar con Azure Stack Hub.
- Un flujo de trabajo que usa:
    - La acción Azure Login
    - La acción de script de PowerShell

### <a name="azure-stack-hub-github-actions"></a>Acciones de GitHub de Azure Stack Hub

En el diagrama siguiente se muestran los distintos entornos y sus relaciones.

![Acción de Github de Azure Stack Hub](.\media\ci-cd-github-action-login-cli\ash-github-actions-v1d1.svg) Partes del uso del ejecutor autohospedado:

- Acciones de GitHub hospedadas en GitHub
- Ejecutor autohospedado hospedado en Azure
- Azure Stack Hub

Una limitación del uso de las Acciones de GitHub con Azure Stack Hub es que el proceso requiere el uso de un entorno de Azure Stack Hub conectado a la web. El flujo de trabajo se desencadena en un repositorio de GitHub. Puede usar Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS) como proveedor de identidades.

Aunque esto está fuera del ámbito de este artículo, el ejecutor autohospedado también puede usar una red privada virtual para conectarse al entorno de Azure Stack Hub detrás de un firewall.

## <a name="get-service-principal"></a>Obtención de una entidad de servicio

Un SPN proporciona credenciales basadas en roles para que los procesos fuera de Azure puedan conectarse a los recursos e interactuar con ellos. Necesitará un SPN con acceso de colaborador y los atributos especificados en estas instrucciones para usarlo con las Acciones de GitHub.

Como usuario de Azure Stack Hub, no tiene permisos para crear el SPN. Deberá solicitar la creación de esta entidad a su operador en la nube. Aquí se proporcionan instrucciones para que pueda crear el SPN si es un operador de la nube, o para que pueda validar el SPN si es un desarrollador que usa un SPN en el flujo de trabajo proporcionado por un operador de la nube.

El operador de la nube tendrá que crear el SPN mediante la CLI de Azure.

Los fragmentos de código siguientes se han escrito para una máquina Windows mediante el símbolo del sistema de PowerShell con la CLI de Azure. Si usa la CLI en una máquina con Linux y bash, elimine la extensión de línea o reemplácela por el carácter `\`.

1. Prepare los valores de los siguientes parámetros utilizados para crear el SPN:

    | Parámetro | Ejemplo | Descripción |
    | --- | --- | --- |
    endpoint-resource-manager | "https://management.orlando.azurestack.corp.microsoft.com"  | Punto de conexión de administración de recursos. |
    suffix-storage-endpoint | "orlando.azurestack.corp.microsoft.com"  | Sufijo del punto de conexión para las cuentas de almacenamiento. |
    suffix-keyvault-dns | ".vault.orlando.azurestack.corp.microsoft.com"  | Sufijo DNS del servicio Key Vault. |
    endpoint-active-directory-graph-resource-id | "https://graph.windows.net/"  | El identificador de recurso de Active Directory. |
    endpoint-sql-management | https://notsupported  | Punto de conexión de administración de SQL Server. Establézcalo en `https://notsupported` |
    perfil | 2019-03-01-hybrid | Perfil que se va a usar para esta nube. |

2. Abra la herramienta de la línea de comandos, como Windows PowerShell o Bash, e inicie sesión. Use el comando siguiente:

    ```azurecli  
    az login
    ```

3. Use el comando `register` para un nuevo entorno o el comando `update` si usa un entorno existente. Use el comando siguiente.

    ```azurecli  
    az cloud register `
        -n "AzureStackUser" `
        --endpoint-resource-manager "https://management.<local>.<FQDN>" `
        --suffix-storage-endpoint ".<local>.<FQDN>" `
        --suffix-keyvault-dns ".vault.<local>.<FQDN>" `
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" `
        --endpoint-sql-management https://notsupported  `
        --profile 2019-03-01-hybrid
    ```

4. Obtenga el identificador de suscripción y el grupo de recursos que desea usar para el SPN.

5. Cree el SPN mediante el siguiente comando con el identificador de suscripción y el grupo de recursos:

    ```azurecli  
    az ad sp create-for-rbac --name "myApp" --role contributor `
        --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} `
        --sdk-auth
    ```

6. Compruebe el objeto JSON resultante. Usará el objeto JSON para crear el secreto en el repositorio de GitHub que contiene la acción. El objeto JSON debe tener los siguientes atributos:

    ```json
    {
      "clientId": <Application ID for the SPN>,
      "clientSecret": <Client secret for the SPN>,
      "subscriptionId": <Subscription ID for the SPN>,
      "tenantId": <Tenant ID for the SPN>,
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com/",
      "resourceManagerEndpointUrl": "https://management.<FQDN>",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://notsupported",
      "galleryEndpointUrl": "https://providers.<FQDN>:30016/",
      "managementEndpointUrl": "https://management.<FQDN>"
    }
    ```

## <a name="add-service-principal-to-repository"></a>Adición de la entidad de servicio al repositorio

Puede usar secretos de GitHub para cifrar la información confidencial que se usará en las acciones. Creará un secreto que contendrá el SPN para que la acción pueda iniciar sesión en la instancia de Azure Stack Hub.

> [!WARNING]  
> GitHub recomienda que no use ejecutores autohospedados con bifurcaciones de repositorios públicos de su repositorio público, ya que podría ejecutar código peligroso en la máquina del ejecutor autohospedado mediante la creación de una solicitud de incorporación de cambios que ejecute el código en un flujo de trabajo. Para más información, consulte [Seguridad del ejecutor autohospedado con repositorios públicos](https://docs.github.com/en/free-pro-team@latest/github/automating-your-workflow-with-github-actions/about-self-hosted-runners#self-hosted-runner-security-with-public-repositories).

1. Abra o cree un repositorio de GitHub. Si necesita instrucciones sobre cómo crear un repositorio en GitHub, puede encontrar [instrucciones en la documentación de GitHub](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo).
1. Establezca el repositorio en privado.
    1. Seleccione **Settings** > **Change repository visibility** (Configuración > Cambiar la visibilidad del repositorio).
    1. Seleccione **Make private** (Convertir en privado).
    1. Escriba el nombre del repositorio.
    1. Seleccione **I understand, change the repository visibility** (Entiendo, cambiar la visibilidad del repositorio).
1. Haga clic en **Configuración**.
1. Seleccione **Secrets** (Secretos).
1. Seleccione **New repository secret** (Nuevo secreto del repositorio).
    ![Adición del secreto de Acciones de GitHub](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Asigne el nombre `AZURE_CREDENTIALS` al secreto.
1. Pegue el objeto JSON que representa el SPN.
1. Seleccione **Add secret** (Agregar secreto).

## <a name="create-your-vm-and-install-prerequisites"></a>Creación de la máquina virtual y requisitos previos de la instalación

1. Cree el ejecutor autohospedado. 

    Estas instrucciones crean un ejecutor como una máquina virtual Windows en Azure. Si necesita conectarse a la instancia de Azure Stack Hub hospedada en un centro de datos, es posible que necesite una conexión VPN. Puede encontrar instrucciones sobre cómo habilitar la conexión en la sección [Instalación de las herramientas de Azure Stack Hub en el ejecutor autohospedado](#optional-install-azure-stack-hub-tools-on-your-self-hosted-runner) que puede requerir una conexión VPN.
    - Para obtener instrucciones sobre cómo crear una máquina virtual Windows en Azure, consulte [Inicio rápido: crear una máquina virtual de Windows en Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal). Cuando siga estas instrucciones, instale Windows Server 2016 Core.
    - Para obtener instrucciones sobre cómo crear una máquina virtual Windows en Azure Stack Hub, consulte [Inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal). Cuando siga estas instrucciones, instale Windows Server 2016 Core.
1. Use una conexión remota para conectarse al servidor Windows Server 2016 con la dirección IP del servidor, el nombre de usuario y la contraseña que definió al crear la máquina.
1. Instale Chocolatey. Chocolatey es un administrador de paquetes para Windows que puede usar para instalar y administrar dependencias desde la línea de comandos. En un símbolo del sistema de PowerShell con privilegios elevados, escriba:
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
1. Instale PowerShell Core. En un símbolo del sistema de PowerShell con privilegios elevados, escriba:
    ```powershell  
    choco install powershell-core
    ```
1. Instale la CLI de Azure. En un símbolo del sistema de PowerShell con privilegios elevados, escriba:
    ```powershell  
    choco install azure-cli
    ```
1. Instale PowerShell de Azure Stack Hub. En un símbolo del sistema de PowerShell con privilegios elevados, escriba:
    ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
    ```
    Para más información sobre el uso de los módulo Az de Azure Stack Hub, consulte [Instalación del módulo Az de PowerShell para Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/powershell-install-az-module).
7. Reinicie el equipo. En un símbolo del sistema de PowerShell con privilegios elevados, escriba:
    ```powershell  
    shutdown /r
    ```
8. Agregue la máquina como un ejecutor autohospedado al repositorio de GitHub. Puede encontrar instrucciones sobre cómo agregar un ejecutor autohospedado en la documentación de GitHub. Para más información, consulte [Adición de ejecutores autohospedados](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/adding-self-hosted-runners).

    ![El ejecutor está a la escucha.](.\media\ci-cd-github-action-login-cli\github-action-runner-listen.png)

9. Cuando haya terminado, compruebe que el servicio está en ejecución y que escucha al servicio. Para realizar una doble comprobación, ejecute `/run.cmd` desde el directorio del ejecutor.

### <a name="optional-install-azure-stack-hub-tools-on-your-self-hosted-runner"></a>Opcional: Instalación de las herramientas de Azure Stack Hub en el ejecutor autohospedado

Las instrucciones de este artículo no requieren acceso a las [Herramientas de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?&tabs=az), pero a medida que desarrolle su propio flujo de trabajo, es posible que tenga que usar las herramientas. Las instrucciones siguientes pueden ayudarle a instalar las herramientas en el ejecutor Windows autohospedado. Para más información acerca de las herramientas de Azure Stack Hub, consulte [Descarga de herramientas de Azure Stack Hub desde GitHub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?&tabs=az). En estas instrucciones se supone que ha instalado el administrador de paquetes Chocolatey.

1. Instale Git.
    ```powershell  
    choco install git
    ```

2. En un símbolo del sistema de PowerShell con privilegios elevados, escriba:
    ```powershell
    # Change directory to the root directory.
    cd \
    
    # Download the tools archive.
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
    invoke-webrequest `
      https://github.com/Azure/AzureStack-Tools/archive/az.zip `
      -OutFile az.zip
    
    # Expand the downloaded files.
    expand-archive az.zip `
      -DestinationPath . `
      -Force
    
    # Change to the tools directory.
    cd AzureStack-Tools-az
    ```

3. Si necesita que el ejecutor se conecte a la instancia de Azure Stack Hub, puede usar PowerShell. Puede encontrar las instrucciones en el artículo [Conexión a Azure Stack Hub con PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-configure-admin?&tabs=az1%2Caz2%2Caz3).

## <a name="create-a-self-hosted-runner"></a>Creación de un ejecutor autohospedado

Puede configurar un ejecutor autohospedado según la documentación de GitHub. Un ejecutor autohospedado se puede ejecutar en cualquier máquina que pueda conectarse a GitHub. Puede optar por usar un ejecutor autohospedado si tiene una tarea de automatización en el flujo de trabajo que requiere amplias dependencias, requisitos de licencias específicos, como una llave USB para una licencia de software, u otras necesidades específicas de la máquina o del software. La máquina puede ser una máquina física, una máquina virtual o un contenedor. Puede colocar el ejecutor en el centro de datos o en la nube.

En este artículo, va a usar una máquina virtual Windows hospedada en Azure que se configurará con los requisitos específicos de PowerShell para Azure Stack Hub.

Para obtener instrucciones sobre la instalación, configuración y conexión del ejecutor autohospedado al repositorio, consulte la documentación de GitHub, "[Acerca de los ejecutores autohospedados](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners)".

![Ejecutor autohospedado conectado](.\media\ci-cd-github-action-login-cli\github-actions-self-hosted-runner.png)

Anote el nombre y las etiquetas del ejecutor autohospedado. El flujo de trabajo de este artículo lo llamará con la etiqueta `self-hosted`.

## <a name="add-the-workflow-to-your-repository"></a>Adición del flujo de trabajo al repositorio

Cree un nuevo flujo de trabajo mediante yaml en esta sección para crear el flujo de trabajo.

1. Abra el repositorio de GitHub.
2. Seleccione **Actions** (Acciones).
3. Cree un nuevo flujo de trabajo.
    - Si se trata de su primer flujo de trabajo, seleccione **set up a workflow yourself** (Configurar un flujo de trabajo personalmente) en **Choose a workflow template** (Elegir una plantilla de flujo de trabajo).
    - Si tiene flujos de trabajo existentes, seleccione **New workflow** > **Set up a workflow yourself** (Nuevo flujo de trabajo > Configurar un flujo de trabajo personalmente).

4. En la ruta de acceso, asigne el nombre `workflow.yml` al archivo.
5. Copie y pegue el código yml del flujo de trabajo.
    ```yaml  
    on: [push]
    
    env:
      ACTIONS_ALLOW_UNSECURE_COMMANDS: 'true'
     
    jobs: 
      azurestack-test:
        runs-on: self-hosted
        steps:
    
          - name: Login to AzureStack with Az Powershell
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: true
          
          - name: Run Az PowerShell Script Against AzureStack
            uses: azure/powershell@v1
            with:
              azPSVersion: '3.1.0'
              inlineScript: |
                hostname
                Get-AzContext
                Get-AzResourceGroup
          
          - name: Login to AzureStack with CLI
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: false
          
          - name: Run Azure CLI Script Against AzureStack
            run: |
              hostname
              az group list --output table
    ```
6. Seleccione **Start commit** (Iniciar confirmación).
7. Agregue el título de la confirmación y los detalles opcionales y, a continuación, seleccione **Commit new file** (Confirmar nuevo archivo).

Cuando se ejecute la acción, compruebe que se ha ejecutado correctamente.

1. Abra el repositorio de GitHub. Puede desencadenar el flujo de trabajo mediante un envío de cambios al repositorio.
1. Seleccione **Actions** (Acciones).
1. Seleccione el nombre de la confirmación en **All workflows** (Todos los flujos de trabajo).

    ![Revisión del resumen de la confirmación](.\media\ci-cd-github-action-login-cli\github-actions-review-log-summary.png)
1. Seleccione el nombre del trabajo, **azurestack-test**.

    ![Revisión de los detalles de la confirmación](.\media\ci-cd-github-action-login-cli\github-action-success-screen.png)
1. Expanda las secciones para revisar los valores devueltos de los comandos de PowerShell y de la CLI.

Notas sobre el archivo de flujo de trabajo y la acción:

- El flujo de trabajo contiene un único trabajo llamado `azurestack-test`.
- Un evento de envío de cambios desencadena el flujo de trabajo.
- La acción usa un ejecutor autohospedado que se ha configurado en el repositorio, al que se llama mediante la etiqueta del ejecutor en el flujo de trabajo con la línea: `runs on: self-hosted`.
- El flujo de trabajo contiene tres acciones.
- La primera acción llama a la acción Azure Login para iniciar sesión con PowerShell. Con Acciones de GitHub para Azure puede crear flujos de trabajo que puede configurar en el repositorio para compilar, probar, empaquetar, lanzar e implementar en Azure. Esta acción usa las credenciales del SPN de Azure Stack para conectarse y abrir una sesión en el entorno de Azure Stack Hub. Puede encontrar más información sobre el uso de la acción en GitHub, [Acción Azure Login](https://github.com/marketplace/actions/azure-login).
- La segunda acción usa Azure PowerShell. La acción usa los módulos Az de PowerShell y funciona con las nubes de Azure Government y Azure Stack Hub. Después de ejecutar este flujo de trabajo, revise el trabajo para validar que el script ha recopilado los grupos de recursos del entorno de Azure Stack Hub. Para más información, consulte [Acción Azure PowerShell](https://github.com/marketplace/actions/azure-powershell-action).
- La tercera acción usa la CLI de Azure para iniciar sesión y conectarse a Azure Stack Hub para recopilar los grupos de recursos. Para más información, consulte [Acción CLI de Azure](https://github.com/marketplace/actions/azure-cli-action).
- Para más información sobre cómo trabajar con Acciones de GitHub y el ejecutor autohospedado, consulte la documentación [Acciones de GitHub](https://github.com/features/actions).

## <a name="next-steps"></a>Pasos siguientes

- Encuentre más acciones en [Marketplace de GitHub](https://github.com/marketplace).
- Más información sobre [Implementaciones comunes para Azure Stack Hub](azure-stack-dev-start-deploy-app.md).  
- Más información sobre [Uso de plantillas de Azure Resource Manager en Azure Stack Hub](azure-stack-arm-templates.md).  
- Consulte el patrón de nube híbrida de DevOps en [Patrón DevOps](https://docs.microsoft.com/hybrid/app-solutions/pattern-cicd-pipeline).
