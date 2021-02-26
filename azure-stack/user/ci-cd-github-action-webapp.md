---
title: Uso de la acción de implementación de aplicaciones web de Azure con Azure Stack Hub
description: Use la acción de implementación de aplicaciones web de Azure para crear un flujo de trabajo de integración e implementación continuas (CI/CD) en Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 2/16/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 2/16/2021
ms.openlocfilehash: fec9acb7a3e156a8646aef88c53e681eed9e53da
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655279"
---
# <a name="use-the-azure-web-app-deploy-action-with-azure-stack-hub"></a>Uso de la acción de implementación de aplicaciones web de Azure con Azure Stack Hub

Puede configurar acciones de GitHub para implementar una aplicación web en la instancia de Azure Stack Hub. Esto le permite configurar la integración y la implementación continuas para la aplicación. Este artículo le ayudará a empezar a trabajar con la implementación automatizada mediante Acciones de GitHub y Azure Stack Hub. Va a crear una aplicación web y usará el perfil de publicación para crear la aplicación web para hospedar la aplicación.

Las Acciones de GitHub son flujos de trabajo que se componen de acciones que permiten la automatización directamente en el repositorio de código. Puede desencadenar los flujos de trabajo con eventos en el proceso de desarrollo de GitHub. Puede establecer tareas comunes de automatización de DevOps, como pruebas o implementación e integración continuas.

Este flujo de trabajo de ejemplo incluye:
- Instrucciones sobre la creación y validación de la entidad de servicio (SPN).
- Instrucciones sobre la creación del perfil de publicación de la aplicación web
- Incorporación de un flujo de trabajo específico del entorno de ejecución
- Incorporación de un flujo de trabajo que coincida con la implementación de la aplicación web
## <a name="get-service-principal"></a>Obtención de una entidad de servicio

Un SPN proporciona credenciales basadas en roles para que los procesos fuera de Azure puedan conectarse a los recursos e interactuar con ellos. Necesitará un SPN con acceso de colaborador y los atributos especificados en estas instrucciones para usarlo con las Acciones de GitHub.

Como usuario de Azure Stack Hub, no tiene permisos para crear el SPN. Deberá solicitar la creación de esta entidad a su operador de nube. Las instrucciones se proporcionan aquí para que pueda crear el SPN si es un operador de nube. O bien, si es un desarrollador, puede validar el SPN proporcionado por un operador de nube.

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

    Si no tiene privilegios de operador de nube, también puede iniciar sesión con el SPN que le proporcione el operador de nube. Necesitará el identificador de cliente, el secreto y el identificador de inquilino. Con estos valores, puede usar los siguientes comandos de la CLI de Azure para crear el objeto JSON que puede agregar al repositorio de GitHub como un secreto.

    ```azurecli  
    az login --service-principal -u "<client-id>" -p "<secret>" --tenant "<tenant-ID>" --allow-no-subscriptions
    az account show --sdk-auth
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

## <a name="create-the-web-app-publish-profile"></a>Creación del perfil de publicación de la aplicación web

### <a name="open-the-create-web-app-blade"></a>Abrir hoja de creación de aplicación web

1. Inicie sesión en el portal de Azure Stack Hub.
1. Seleccione **Crear un recurso** > **Web y móvil** > **Aplicación web**.
    ![Crear una aplicación web en Azure Stack Hub](\media\ci-cd-github-action-webapp\create-web-app.png)
### <a name="to-create-your-web-app"></a>Creación de la aplicación web

1. Seleccione su **suscripción**.
1. Cree o seleccione un **grupo de recursos**.
1. Escriba el **nombre** de la aplicación. El nombre de la aplicación aparecerá en la dirección URL de la aplicación, por ejemplo, `yourappname.appservice.<region>.<FQDN>`
1. Seleccione la **pila del entorno en tiempo de ejecución** de la aplicación. El entorno en tiempo de ejecución debe coincidir con el flujo de trabajo que se usa en el perfil de publicación de destino.
1. Seleccione el **sistema operativo** que hospedará el entorno de ejecución y la aplicación.
1. Seleccione o escriba la **región** de la instancia de Azure Stack Hub.
1. Seleccione el plan según la instancia de Azure Stack Hub, la región y el sistema operativo de la aplicación.
1. Seleccione **Revisar + crear**.
1. Revise la aplicación web. Seleccione **Crear**.
    ![Revisar una aplicación web en Azure Stack Hub](\media\ci-cd-github-action-webapp\review-azure-stack-hub-web-app.png)
1. Haga clic en **Go to resource** (Ir al recurso).
    ![Obtención de un perfil de publicación en Azure Stack Hub](\media\ci-cd-github-action-webapp\get-azure-stack-hub-web-app-publish-profile.png)
1. Seleccione **Obtener perfil de publicación**. El perfil de publicación se descarga y se denomina `<yourappname>.PublishSettings`. El archivo contiene un XML con los valores de destino de la aplicación web.
1. Almacene su perfil de publicación para que pueda acceder a él cuando cree los secretos para el repositorio.

## <a name="add-your-secrets-to-the-repository"></a>Incorporación de secretos al repositorio

Puede usar secretos de GitHub para cifrar la información confidencial que se usará en las acciones. Creará un secreto que contendrá el SPN y otro secreto que contendrá el perfil de publicación de la aplicación web para que la acción pueda iniciar sesión en la instancia de Azure Stack Hub y crear la aplicación en el destino de la aplicación web.

1. Abra o cree un repositorio de GitHub. Si necesita instrucciones sobre cómo crear un repositorio en GitHub, puede encontrar [instrucciones en la documentación de GitHub](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo).
1. Haga clic en **Configuración**.
1. Seleccione **Secrets** (Secretos).
1. Seleccione **New repository secret** (Nuevo secreto del repositorio).
    ![Adición del secreto de Acciones de GitHub](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Asigne el nombre `AZURE_CREDENTIALS` al secreto.
1. Pegue el objeto JSON que representa el SPN.
1. Seleccione **Add secret** (Agregar secreto).
1. Seleccione **New repository secret** (Nuevo secreto del repositorio).
1. Asigne el nombre `AZURE_WEBAPP_PUBLISH_PROFILE` al secreto.
1. Abra `<yourappname>.PublishSettings` en un editor de texto y, a continuación, copie y pegue el archivo XML en el secreto del repositorio.
1. Seleccione **Add secret** (Agregar secreto).

## <a name="add-a-runtime-workflow"></a>Incorporación de un flujo de trabajo del entorno de ejecución

1. Elija una plantilla de la tabla para el entorno de ejecución de la aplicación web.

    | Tiempo de ejecución | Plantilla |
    | --- | --- |
    | DotNet | [dotnet.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/asp.net-core-webapp-on-azure.yml) |
    | NodeJS | [node.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/node.js-webapp-on-azure.yml) |
    | Java | [java_jar.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-jar-webapp-on-azure.yml) |
    | Java | [java_war.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-war-webapp-on-azure.yml) |
    | Python | [python.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/python-webapp-on-azure.yml) |
    | PHP | [php.yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/php-webapp-on-azure.yml) |
    | Docker | [docker.yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/docker-webapp-container-on-azure.yml) |

2. Coloque el directorio del flujo de trabajo de la acción de GitHub de la plantilla en el repositorio del proyecto: `.github/workflows/<runtime.yml>` El directorio del flujo de trabajo contendrá dos flujos de trabajo.

## <a name="add-the-web-app-deploy-action"></a>Incorporación de la acción de implementación de la aplicación web

Cree un segundo flujo de trabajo mediante el archivo yaml de esta sección. En este ejemplo, va a implementar una aplicación web de Python. Deberá seleccionar una acción de instalación basada en el flujo de trabajo. Puede buscar referencias para configurar acciones para varios entornos de ejecución en la tabla, [Acciones de configuración para diferentes runtimes](#setup-actions-for-different-runtimes), después de los pasos utilizados para crear la acción.

### <a name="example-github-action-workflow"></a>Ejemplo de flujo de trabajo de acción de GitHub

1. Abra el repositorio de GitHub. Si aún no ha agregado los recursos de la aplicación web, agréguelos ahora. En este ejemplo, voy a usar el ejemplo [Python Flask Hello World](https://github.com/Azure-Samples/python-docs-hello-world) y he agregado los archivos `.gitignore`, `app.py` y `requirements.txt` de Python.

    ![Ejemplo del uso de Python Flask con Azure Stack Hub del repositorio](\media\ci-cd-github-action-webapp\example-of-repo.png)
1. Seleccione **Actions** (Acciones).
1. Seleccione **New workflow** (Nuevo flujo de trabajo).
    - Si se trata de su primer flujo de trabajo, seleccione **set up a workflow yourself** (Configurar un flujo de trabajo personalmente) en **Choose a workflow template** (Elegir una plantilla de flujo de trabajo).
    - Si tiene flujos de trabajo existentes, seleccione **New workflow** > **Set up a workflow yourself** (Nuevo flujo de trabajo > Configurar un flujo de trabajo personalmente).

4. En la ruta de acceso, asigne el nombre `workflow.yml` al archivo.
5. Copie y pegue el código yml del flujo de trabajo.
    ```yaml  
    # .github/workflows/worfklow.yml
    on: push

    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout Github Action' 
          uses: actions/checkout@master
    
        - name: Setup Python 3.6
          uses: actions/setup-node@v1
          with:
            python-version: '3.6'
        - name: 'create a virtual environment and install dependencies'
          run: |
            python3 -m venv .venv
            source .venv/bin/activate
            pip install -r requirements.txt
    
        - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with:
            app-name: <YOURAPPNAME>
            publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
    ```

6. En workflow.yaml, actualice `<YOURAPPNAME>` con el nombre de la aplicación.
7. Seleccione **Start commit** (Iniciar confirmación).
8. Agregue el título de la confirmación y los detalles opcionales y, a continuación, seleccione **Commit new file** (Confirmar nuevo archivo).


### <a name="setup-actions-for-different-runtimes"></a>Acciones de configuración para diferentes entornos de ejecución

Para crear código de aplicación en un entorno específico basado en un lenguaje, use las acciones de configuración:

|  Tiempo de ejecución | Acciones de configuración |
|------------|---------|
| DotNet     | [Configuración de DotNet](https://github.com/actions/setup-dotnet) |
| NodeJS     | [Configuración de Node](https://github.com/actions/setup-node) |
| Java     | [Configuración de Java](https://github.com/actions/setup-java) |
| Python     | [Configuración de Python](https://github.com/actions/setup-python) |
| Docker | [Inicio de sesión de Docker](https://github.com/Azure/docker-login) |

Una vez que se realiza la acción de inicio de sesión, el siguiente conjunto de acciones del flujo de trabajo puede realizar tareas como crear, etiquetar e insertar contenedores. Para más información, consulte la documentación de la [acción de creación de aplicaciones web de Azure](https://github.com/marketplace/actions/azure-webapp).
## <a name="trigger-your-deployment"></a>Desencadenamiento de la implementación

Cuando se ejecute la acción, compruebe que se ha ejecutado correctamente.

1. Abra el repositorio de GitHub. Puede desencadenar el flujo de trabajo mediante un envío de cambios al repositorio.
1. Seleccione **Actions** (Acciones).
1. Seleccione el nombre de la confirmación en **All workflows** (Todos los flujos de trabajo). Ambos flujos de trabajo han registrado su estado.
    ![Revisar el estado de la acción de GitHub](\media\ci-cd-github-action-webapp\workflow-success.png)
1. Seleccione el nombre del trabajo para la implementación, `.github/workflows/workflow.yml`.
1. Expanda las secciones para revisar los valores devueltos de las acciones del flujo de trabajo. Busque la dirección URL de la aplicación web implementada.
    ![Buscar la dirección URL de la aplicación web de Azure Stack Hub](\media\ci-cd-github-action-webapp\review-cli-log-and-get-url.png)
1. Abra un explorador web y cargue la dirección URL.
## <a name="next-steps"></a>Pasos siguientes

- Encuentre más acciones en [Marketplace de GitHub](https://github.com/marketplace).
- Más información sobre [Implementaciones comunes para Azure Stack Hub](azure-stack-dev-start-deploy-app.md).  
- Más información sobre [Uso de plantillas de Azure Resource Manager en Azure Stack Hub](azure-stack-arm-templates.md).  
- Consulte el patrón de nube híbrida de DevOps en [Patrón DevOps](https://docs.microsoft.com/hybrid/app-solutions/pattern-cicd-pipeline).
