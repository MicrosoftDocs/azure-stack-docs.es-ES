---
title: Implementación de aplicaciones en Azure y Azure Stack
description: Aprenda a implementar aplicaciones en Azure y Azure Stack con una canalización de CI/CD híbrida.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/07/2019
ms.topic: conceptual
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: c821f35928df5da4c34455a0b541699b0a84d490
ms.sourcegitcommit: 5eae057cb815f151e6b8af07e3ccaca4d8e4490e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2019
ms.locfileid: "72310662"
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Implementación de aplicaciones en Azure y Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Esta solución muestra cómo implementar aplicaciones en Azure y Azure Stack mediante la integración continua y entrega continua (CI/CD) híbrida de Azure Pipelines.

Después de configurar Azure Stack, Azure DevOps y las aplicaciones web conforme a los [Requisitos previos](#prerequisites), puede:

> [!div class="checklist"]
> - Registrar la aplicación web y configurar el acceso a Azure Pipelines en Azure Active Directory (Azure AD). 
> - Crear puntos de conexión de Azure Pipelines para Azure AD y Servicios de federación de Active Directory (AD FS). 
> - Instalar el agente de compilación de Azure Pipelines en el servidor de compilación de Azure Stack. 
> - Configurar la implementación de aplicaciones autocontenida para Azure y Azure Stack.
> - Crear una canalización de compilación que ejecute compilaciones automáticas en función de las confirmaciones de código en el proyecto.
> - Crear una canalización de versión que implemente automáticamente las compilaciones tanto en Azure AD como en Azure Stack. 
> - Crear e implementar versiones de forma manual y ver resúmenes y registros de versiones. 

Para obtener más información sobre CI/CD, vea los siguientes artículos:

* [What is Continuous Integration?](https://www.visualstudio.com/learn/what-is-continuous-integration/) (¿Qué es la integración continua?)
* [What is Continuous Delivery?](https://www.visualstudio.com/learn/what-is-continuous-delivery/) (¿Qué es la entrega continua?)

## <a name="azure-stack-and-hybrid-cicd"></a>Azure Stack y CI/CD híbrida

Microsoft Azure Stack es una extensión de Azure que incorpora a los entornos locales la agilidad y la innovación de la informática en la nube. Es la única nube híbrida que permite compilar e implementar aplicaciones híbridas en entornos locales y de nube pública. Puede compilar una aplicación en Azure Stack y luego implementarla en Azure Stack, en Azure o en la nube híbrida de Azure. 

La continuidad, la seguridad y la confiabilidad de la implementación de aplicaciones son elementos fundamentales para la organización y el equipo de desarrollo. El modelo de entrega de CI/CD híbrida de Azure Pipelines permite consolidar las canalizaciones de compilación en el entorno local y la nube pública y cambiar las ubicaciones de implementación sin cambiar la aplicación. Otras ventajas derivadas del uso del enfoque híbrido son:

- Puede mantener un conjunto coherente de herramientas de desarrollo en el entorno de Azure Stack local y la nube pública de Azure.  Un conjunto de herramientas común facilita la implementación de patrones y prácticas de CI/CD.
- Las aplicaciones y los servicios implementados en Azure y Azure Stack son intercambiables y el mismo código se puede ejecutar en cualquiera de los dos. Puede aprovechar las ventajas de las características y funcionalidades del entorno local y la nube pública.

> [!TIP]
> ![hybrid-pillars.png](./media/azure-stack-solution-pipeline/hybrid-pillars.png)  
> En el artículo [Modelos de diseño de nube híbrida para Azure Stack](azure-stack-edge-pattern-overview.md) se revisan los fundamentos de calidad de software para diseñar, implementar y manipular aplicaciones híbridas. Los criterios de calidad incluyen la ubicación, la escalabilidad, la disponibilidad, la resistencia, la manejabilidad y la seguridad. Estas consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y minimizan los retos de los entornos de producción.

## <a name="prerequisites"></a>Requisitos previos

- Conocimientos básicos de Azure y Azure Stack. Para obtener más información antes de implementar esta solución, lea los siguientes artículos:
  
  - [Introducción a Azure](https://azure.microsoft.com/overview/what-is-azure/)
  - [Azure Stack overview](../operator/azure-stack-overview.md) (Introducción a Azure Stack)
  
- Una suscripción de Azure. Si no tiene una, [cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
  
- Una aplicación web creada en Azure. Use una [plantilla de Azure Resource Manager](https://azure.microsoft.com/resources/templates/) para crear una aplicación web que pueda implementar tanto en un entorno local como en la nube pública. Anote el URI de la aplicación para usarlo más adelante. 
  
- Visual Studio 2019 [instalado](/visualstudio/install/install-visual-studio).
  
- Acceso de administrador a una organización de [Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) que pueda crear canalizaciones y un [proyecto](/azure/devops/organizations/projects/create-project) o [área de trabajo](/azure/devops/repos/tfvc/create-work-workspaces) de DevOps. 
  
- Una imagen de Windows Server 2016 con .NET 3.5 para la máquina virtual (VM) privada del agente de compilación que Azure Pipelines compila en Azure Stack.
  
- Un sistema integrado de Azure Stack o el Kit de desarrollo de Azure Stack (ASDK) implementado y configurado según las instrucciones siguientes. 
  
  
  El Kit de desarrollo de Azure Stack (ASDK) es una implementación de un único nodo de Azure Stack que puede descargar y usar de forma gratuita. El ASDK permite evaluar Azure Stack y usar las API y las herramientas de Azure en un entorno que no sea de producción.
  
  Cualquier usuario con credenciales de administrador de Azure AD o Servicios de federación de Active Directory (AD FS) puede implementar el ASDK. Un asociado de OEM o hardware de Azure puede implementar una instancia de Azure Stack de producción. Debe ser operador de Azure Stack para realizar las siguientes tareas de configuración de Azure Stack: 
  
  - Implementar Azure App Service
  - Crear planes y ofertas
  - Crear una suscripción de inquilino
  - Aplicar una imagen de Windows Server 2016
  
  > [!Note]
  > La instalación del ASDK tarda aproximadamente siete horas, así que planee en consecuencia.
    
  Para implementar y configurar el ASDK:
  
  1. Siga las instrucciones de implementación detalladas de [Implementación del Kit de desarrollo de Azure Stack: Instalador](../asdk/asdk-install.md).
     
  1. Use el script de PowerShell [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) para automatizar los pasos posteriores a la implementación de ASDK.
     
  1. Implemente servicios PaaS de [Azure App Service](../operator/azure-stack-app-service-deploy.md) en Azure Stack.
     
  1. Cree [un plan y una oferta](../operator/service-plan-offer-subscription-overview.md) en Azure Stack.
     
  1. Cree una [suscripción de inquilino](../operator/azure-stack-subscribe-plan-provision-vm.md) a la oferta de Azure Stack. 
     
  1. Cree una aplicación web en la suscripción de inquilino. Anote la nueva dirección URL de aplicación web, ya que la usará más adelante.
     
  1. Implemente una máquina virtual de Windows Server 2016 con .NET 3.5 en la suscripción de inquilino para que sea el servidor de compilación que ejecuta Azure Pipelines.
  
  > [!Note]
  > El entorno de Azure Stack necesita las imágenes correctas para ejecutar Windows Server y SQL Server. También debe tener implementado App Service.

## <a name="register-your-web-app-and-give-it-access-to-resources"></a>Registrar la aplicación web y concederle acceso a los recursos 

En Azure Active Directory (Azure AD), Azure Pipelines se autentica en Azure Resource Manager mediante una *entidad de servicio*. Para aprovisionar recursos para Azure Pipelines, la entidad de servicio debe tener el rol **Colaborador** en la suscripción de Azure. 

Puede usar Azure Portal para configurar la autenticación en la aplicación. 

1. Registre la aplicación para crear una entidad de servicio.
1. Use *control de acceso basado en rol (RBAC)* para conceder el rol **Colaborador** al nombre de entidad de seguridad de servicio (SPN).
1. Copie y guarde los valores Id. de aplicación e Id. de inquilino que necesita para crear puntos de conexión para Azure Pipelines. 
1. Cree y guarde un valor de clave secreta de aplicación.

También puede [usar un script de PowerShell](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) para crear una entidad de servicio y puntos de conexión. En el artículo [Crear una conexión de servicio de Azure Resource Manager con una entidad de servicio existente](/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) se explica este proceso.

 > [!Note]  
 > Si usa el script de PowerShell para crear un punto de conexión de Azure Resource Manager de Azure Stack, tiene que pasar los parámetros **-azureStackManagementURL** y **-environmentName**. Por ejemplo:  
 > `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="register-your-app-in-azure-ad"></a>Registrar la aplicación en Azure AD 

1. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory** y luego **Registros de aplicaciones** en el panel de navegación izquierdo.
   
1. Seleccione **Nuevo registro**.
   
1. En la página **Registrar una aplicación**
   1. Escriba el nombre de la aplicación web.
   1. Seleccione un tipo de cuenta admitido. 
   1. En **URI de redirección**, seleccione **Web** para el tipo de aplicación que quiere crear y escriba el URI de la aplicación web. 
   1. Seleccione **Registrar**.
      
      ![Registrar su aplicación](./media/azure-stack-solution-pipeline/create-app.png) 

### <a name="assign-the-app-to-a-role"></a>Asignar la aplicación a un rol

Debe asignar la aplicación a un rol para que acceda a los recursos de la suscripción. Azure RBAC permite controlar el nivel de acceso que los usuarios necesitan para realizar sus tareas. Para obtener más información sobre RBAC, vea [Administración del acceso a los recursos de Azure](/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json). Para obtener más información sobre los roles disponibles, vea [RBAC: roles integrados](/azure/role-based-access-control/built-in-roles).

Azure Pipelines debe tener el rol **Colaborador** para poder aprovisionar recursos en una suscripción de Azure Stack. 

Puede establecer el ámbito del rol en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, el agregar una aplicación al rol **Lector** de un grupo de recursos significa que esta puede leer el grupo de recursos y cualquiera de sus recursos.

Para asignar la aplicación al rol **Colaborador**:

1. En Azure Portal, vaya al nivel de ámbito que quiera. Por ejemplo, para asignar un rol en el ámbito de suscripción, seleccione **Todos los servicios** y **Suscripciones**.
   
   ![Asignación de un rol en el ámbito de suscripción](./media/azure-stack-solution-pipeline/select-subscription.png)
   
1. Seleccione la suscripción que se asignará a la aplicación.
   
1. En el panel de navegación izquierdo, seleccione **Control de acceso (IAM)** .
   
1. Seleccione **Agregar una asignación de roles**.
   
1. En el cuadro de diálogo **Agregar asignación de roles**, seleccione el rol **Colaborador**. De manera predeterminada, las aplicaciones de Azure AD no se muestran en las opciones disponibles. Para encontrar la aplicación, busque el nombre y selecciónelo.
   
   ![Selección del rol y la aplicación](./media/azure-stack-solution-pipeline/select-role.png)
   
1. Haga clic en **Guardar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

La entidad de servicio está configurada. En la sección siguiente se muestra cómo obtener los valores que Azure Pipelines necesita para iniciar sesión mediante programación.

### <a name="get-values-for-signing-in"></a>Obtención de valores para iniciar sesión

Al crear puntos de conexión para Azure Pipelines, debe escribir el Id. de aplicación y el Id. de inquilino. Para obtener estos valores:

1. En Azure Portal, seleccione **Azure Active Directory**.
   
1. En el panel de navegación izquierdo, seleccione **Registros de aplicaciones** y luego la aplicación.
   
1. Copie y guarde el **Id. de directorio (inquilino)** y el **Id. de aplicación (cliente)** que se van a usar para crear puntos de conexión.
   
   ![Copia del identificador de directorio (inquilino) y del identificador de aplicación (cliente)](./media/azure-stack-solution-pipeline/copy-app-id.png)

### <a name="create-a-new-application-secret"></a>Creación de un secreto de aplicación

Al crear puntos de conexión para Azure Pipelines, la aplicación debe autenticarse. Puede [usar un certificado](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) o un secreto de aplicación. Si ha implementado Azure Stack con AD FS como proveedor de identidades, debe usar un certificado.

Siga los pasos de [Certificados y secretos](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) para crear y cargar un nuevo certificado. 

O bien, para crear un nuevo secreto de aplicación:

1. En Azure Portal, seleccione **Azure Active Directory**.
   
1. En el panel de navegación izquierdo, seleccione **Registros de aplicaciones** y luego la aplicación.
   
1. En el panel de navegación izquierdo, seleccione **Certificados y secretos**.
   
1. En **Secretos de cliente**, seleccione **Nuevo secreto de cliente**.
   
1. En **Agregar un secreto de cliente**, escriba una descripción, seleccione una expiración y luego **Agregar**.
   
1. Copie el **VALOR** del nuevo secreto. Debe proporcionar el valor para iniciar sesión como aplicación. Es importante guardar este valor ahora, ya que no se vuelve a mostrar después de salir de esta página.
   
   ![Copia del valor del secreto porque no se puede recuperar más adelante](./media/azure-stack-solution-pipeline/copy-secret.png)

## <a name="create-endpoints"></a>Creación de puntos de conexión

Mediante la creación de puntos de conexión, una compilación de Azure Pipelines puede implementar aplicaciones de Azure AD en Azure Stack. Azure Pipelines se conecta con el agente de compilación, que, a su vez, se conecta con Azure Stack.

Después de establecer los permisos de creación de puntos de conexión, puede crear puntos de conexión para Azure AD o AD FS. 

- Si ha implementado Azure Stack con Azure AD como proveedor de identidades, puede usar un certificado o un secreto de aplicación para crear una conexión de servicio de Azure Resource Manager para implementaciones de Azure. 
  
- Si ha usado Servicios de federación de Active Directory (AD FS) como proveedor de identidades de Azure Stack, debe crear la conexión de servicio mediante un certificado en lugar de un secreto de cliente para la autenticación. 

### <a name="set-endpoint-creation-permissions"></a>Establecer permisos de creación de puntos de conexión

1. En la organización y el proyecto de Azure DevOps, seleccione **Configuración del proyecto**. 
   
1. Seleccione **Seguridad** y, en **Grupos de Azure DevOps**, seleccione **Administradores de puntos de conexión**.
   
1. En la pestaña **Miembros**, seleccione **Agregar**.
   
1. En **Agregar usuarios y grupos**, seleccione los nombres de usuario de la lista, incluido usted mismo, y luego seleccione **Guardar cambios**.
   
   ![Agregar un miembro](./media/azure-stack-solution-pipeline/endpoint-permissions.png)
   
1. En la lista **Grupos de Azure DevOps**, seleccione **Creadores de puntos de conexión** y repita los pasos anteriores para agregar usuarios al grupo **Creadores de puntos de conexión**. 

### <a name="create-an-endpoint-for-azure-ad-or-ad-fs-deployments"></a>Crear un punto de conexión para implementaciones de Azure AD o AD FS

Siga las instrucciones de [Crear una conexión de servicio de Azure Resource Manager con una entidad de servicio existente](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) para crear el punto de conexión de servicio.  

Use los siguientes valores para rellenar el formulario: 

- **Nombre de la conexión**: escriba un nombre descriptivo para usarlo al referirse a esta conexión de servicio.
  
- **Entorno**: seleccione el nombre del entorno, como **AzureCloud** o **AzureStack**. Si no ve AzureStack en la lista desplegable, vea [Conexión a Azure Stack](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#connect-to-azure-stack).
  
- **URL de entorno**: si no ha seleccionado **AzureCloud**, escriba la dirección URL del entorno, como *https:\//management.local.azurestack.external*.
  
- **Nivel de ámbito**: seleccione el nivel de ámbito que necesita, como **Suscripción**. 
  
- **Id. de suscripción**: escriba el identificador de la suscripción.
  
- **Nombre de la suscripción**: escriba el nombre de usuario de Azure Stack.
  
- **Identificador de cliente de la entidad de servicio**: escriba el **Id. de aplicación (cliente)** que ha guardado anteriormente. 
  
- **Clave de la entidad de servicio** o **Certificado**: seleccione una u otra opción. 
  
  > [!NOTE]
  > Para crear un punto de conexión de AD FS, debe usar un certificado para la autenticación. 
  
  - En **Clave de la entidad de servicio**, escriba el valor del secreto de cliente que ha guardado anteriormente.
  - Si selecciona **Certificado**, escriba el contenido de las secciones de certificado y de clave privada del archivo de certificado *.pem*. 
    
    > [!NOTE]
    > Para convertir un *.pfx* en un archivo de certificado *.pem*, ejecute `openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>`.
  
- **Id. de inquilino**: escriba el **Id. de directorio (inquilino)** que ha guardado anteriormente.
  
- **Conexión: Sin comprobar**: seleccione **Comprobar conexión** para validar la configuración de conexión a la entidad de servicio.
  
  > [!NOTE]
  > Si el punto de conexión de Azure Resource Manager no está expuesto en Internet, se produce un error en la validación de la conexión. Esto es normal; puede validar la conexión si crea una canalización de versión con una tarea simple.

## <a name="install-and-configure-the-build-agent"></a>Instalación y configuración del agente de compilación 

El uso de un agente de compilación hospedado en Azure Pipelines es una opción adecuada para compilar e implementar aplicaciones web. Azure realiza automáticamente el mantenimiento y las actualizaciones del agente, lo que permite un ciclo de desarrollo ininterrumpido y continuo.

En Azure DevOps, cree un token de acceso personal (PAT) para usarlo en Azure Stack. Luego use el PAT para implementar y configurar el agente de compilación en la máquina virtual del servidor de compilación de Azure Stack. 
   
### <a name="create-a-personal-access-token"></a>Crear un token de acceso personal

1. Inicie sesión en Azure DevOps y seleccione **Mi perfil** en la esquina superior derecha. 
   
1. En la página de perfil, expanda la lista desplegable situada junto al nombre de la organización de Azure Stack y seleccione **Administrar seguridad**. 
   
   ![Administrar seguridad](media/azure-stack-solution-pipeline/managesecurity.png)
   
1. En la página **Tokens de acceso personal**, seleccione **Nuevo token**.
   
   ![Tokens de acceso personal](media/azure-stack-solution-pipeline/pats.png)
   
1. En la página **Crear un token de acceso personal**, rellene la información del token y seleccione **Crear**. 
   
   ![Creación de un PAT](media/azure-stack-solution-pipeline/createpat.png)
   
1. Copie y guarde el token. No se vuelve a mostrar después de salir de la página web.
   
   ![Advertencia de PAT](media/azure-stack-solution-pipeline/patwarning.png)
   
### <a name="install-and-configure-the-agent-on-the-build-server"></a>Instalar y configurar el agente en el servidor de compilación

1. Conéctese a la máquina virtual del servidor de compilación que ha implementado en el host de Azure Stack.
   
1. Descargue la imagen del agente de compilación. 
   
1. En un símbolo del sistema de administrador, implemente el agente como un servicio con el PAT y ejecútelo.
   
1. Vaya a la carpeta del agente de compilación extraído y ejecute el archivo *config.cmd*. *config.cmd* actualiza la carpeta del agente de compilación con archivos adicionales.
   
   ![Instalación y configuración del agente de compilación](media/azure-stack-solution-pipeline/buildagent.png)

Ahora que ha creado el punto de conexión y ha instalado el agente de compilación de Azure Pipelines en el servidor de compilación, la conexión de Azure Pipelines a Azure Stack está lista para usarse. El agente de compilación de Azure Stack obtiene instrucciones de Azure Pipelines y, a continuación, el agente transmite la información del punto de conexión para la comunicación con Azure Stack.

En lugar de administrar cada agente de forma individual, puede organizar los agentes en *grupos de agentes*. Un grupo de agentes define el límite de uso compartido de todos los agentes de ese grupo. Los grupos de agentes están limitados a la organización de Azure DevOps, lo que significa que puede compartir un grupo de agentes entre proyectos. Para obtener más información sobre los grupos de agentes, vea [Creación de grupos de agentes y colas](/azure/devops/pipelines/agents/pools-queues).

## <a name="create-build-and-release-pipelines"></a>Creación de las canalizaciones de compilación y versión 

Azure Pipelines proporciona una canalización con una gran capacidad de configuración y administración para versiones de varios entornos, como desarrollo, ensayo, control de calidad (QA) y producción. El proceso de versión puede incluir la necesidad de aprobaciones en determinadas fases del ciclo de vida de la aplicación.

En esta parte de la solución se hace lo siguiente:

- Clonar, conectarse y agregar código al proyecto de Azure DevOps en Visual Studio.
- Crear una implementación de aplicación web autocontenida.
- Configurar las canalizaciones de versión y de compilación de CI/CD.

La canalización de CI/CD híbrida se puede aplicar al código de aplicación y al código de infraestructura. Puede usar [plantillas híbridas de Azure Resource Manager](https://azure.microsoft.com/resources/templates/) para implementar el código de aplicación web de Azure en el entorno local y en nubes públicas.

### <a name="clone-your-project"></a>Clonar el proyecto

1. En **Team Explorer** de Visual Studio, seleccione el icono **Conectar** e inicie sesión en la organización de Azure DevOps. 
   
1. Seleccione **Administrar conexiones** > **Conectar a un proyecto**. 
   
   ![Conexión a un proyecto desde Team Explorer](media/azure-stack-solution-pipeline/connecttoprojectteamexp.png)

1. En el cuadro de diálogo **Conectar a un proyecto**, seleccione el proyecto de aplicación web, establezca una ruta de acceso local y luego seleccione **Clonar** para clonar el repositorio de forma local.
   
   ![Clonación del repositorio en Conectar a un proyecto](media/azure-stack-solution-pipeline/cloneproject.png)

### <a name="create-a-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Crear una implementación de aplicación web autocontenida para App Services en ambas nubes

1. En el **Explorador de soluciones** de Visual Studio, abra el archivo *WebApplication.csproj* y agregue `<RuntimeIdentifier>win10-x64</RuntimeIdentifier>`. Para obtener más información sobre este paso, vea [Implementaciones autocontenidas](/dotnet/core/deploying/#self-contained-deployments-scd).
   
   ![Configuración de RuntimeIdentifier](media/azure-stack-solution-pipeline/runtimeidentifier.png)
   
1. Guarde el trabajo y use **Team Explorer** para comprobar el código del proyecto.

### <a name="create-a-build-pipeline-and-run-a-build"></a>Crear una canalización de compilación y ejecutar una compilación

1. En el explorador web, abra la organización y el proyecto de Azure DevOps.
   
1. Seleccione **Canalizaciones** > **Compilaciones** en el panel de navegación izquierdo y luego **Nueva canalización**. 

1. Seleccione el repositorio de código. Azure Pipelines analiza e identifica el proyecto como ASP.NET Core y abre la plantilla de compilación predeterminada *azure-pipelines.yml*  de ASP.NET Core. 
   
   ![Archivo azure-pipelines.yml de ASP.NET Core](media/azure-stack-solution-pipeline/buildargument.png)
   
1. Puede editar el código de canalización directamente o seleccionar **Mostrar el asistente** para abrir un panel de **tareas** que le ayude a agregar tareas y pasos. 
   
   Si selecciona **Mostrar el asistente**, seleccione **.NET Core** en el panel **Tareas**. En el formulario de **.NET Core**:
   - En **Comando**, seleccione **publicar**. 
   - En **Argumentos**, escriba *-r win10-x64*.
   - Asegúrese de que **Publicar proyectos web** está seleccionado.
   - Seleccione **Agregar**.
   
   En lugar de usar el asistente, puede editar y agregar el código siguiente directamente al archivo *azure-pipelines.yml*:
   
   - En `pool`, cambie `vmImage` de `ubuntu-latest` a `vs2017-win2016`.
     
   - En `steps`, agregue la tarea [DotNetCoreCLI](/azure/devops/pipelines/tasks/build/dotnet-core-cli), el comando y los argumentos: 
     
     ```yaml
     - task: DotNetCoreCLI@2
       inputs:
         command: 'publish'
         publishWebProjects: true
         arguments: '-r win10-x64'
     ```
   El archivo *azure-pipelines.yml* debería tener ahora el siguiente código: 
   
   ```yaml
   # ASP.NET Core
   # Build and test ASP.NET Core projects targeting .NET Core.
   # Add steps that run tests, create a NuGet package, deploy, and more:
   # https://docs.microsoft.com/azure/devops/pipelines/languages/dotnet-core
   
   trigger:
   - master
   
   pool:
     vmImage: 'vs2017-win2016'
   
   variables:
     buildConfiguration: 'Release'

   steps:
   - script: dotnet build --configuration $(buildConfiguration)
     displayName: 'dotnet build $(buildConfiguration)'
   
   - task: DotNetCoreCLI@2
     inputs:
       command: 'publish'
       publishWebProjects: true
       arguments: '-r win10-x64'
   ```
1. Seleccione **Guardar y ejecutar**, agregue un mensaje de confirmación y una descripción opcional, y seleccione **Guardar y ejecutar** de nuevo. 
   
La [compilación de implementación autocontenida](/dotnet/core/deploying/#self-contained-deployments-scd) publica los artefactos que se pueden ejecutar en Azure y Azure Stack.

### <a name="create-a-release-pipeline"></a>Creación de una canalización de versión

La creación de una canalización de versión es el último paso del proceso de configuración de CI/CD híbrida. La canalización de versión se usa para crear una versión e implementar la compilación.

1. En el proyecto de Azure DevOps, seleccione **Canalizaciones** > **Versiones** en el panel de navegación izquierdo y luego **Nueva canalización**. 
   
1. En la página **Seleccionar una plantilla**, seleccione **Implementación de Azure App Service** y luego **Aplicar**.
   
   ![Selección de la plantilla de versión](media/azure-stack-solution-pipeline/releasetemplate.png)
   
1. En la pestaña **Canalización**, seleccione **Agregar un artefacto** en el panel izquierdo. En el panel derecho, seleccione la compilación de la aplicación web que acaba de crear en el menú desplegable **Origen (canalización de compilación)** y luego **Agregar**.
   
   ![Adición de un artefacto de compilación](media/azure-stack-solution-pipeline/addartifact.png)
   
1. En la pestaña **Canalización**, en **Fases**, seleccione el hipervínculo de **Fase 1** para **Ver tareas de la fase**.
   
   ![Visualización de tareas de la fase](media/azure-stack-solution-pipeline/viewstagetasks.png)
   
1. En la pestaña **Tareas**, escriba *Azure* como **Nombre de fase**. 
   
1. En **Parámetros**, seleccione la suscripción en la lista desplegable **Suscripción de Azure** y escriba el **Nombre de App Service**.
   
   ![Selección de la suscripción y especificación del nombre de App Service](media/azure-stack-solution-pipeline/stage1.png)
   
1. En el panel izquierdo, seleccione **Ejecutar en el agente**. En el panel derecho, seleccione **Azure Pipelines** en la lista desplegable **Grupo de agentes** y después seleccione **vs2017-win2016** de la lista desplegable **Agent Specification** (Especificación de agente).
   
   ![Selección del agente hospedado](media/azure-stack-solution-pipeline/agentjob.png)
   
1. En el panel izquierdo, seleccione **Deploy Azure App Service** (Implementar Azure App Service). En el panel derecho, desplácese hacia abajo y seleccione el botón de puntos suspensivos **...** junto a **Paquete o carpeta**.
   
   ![Selección del paquete o la carpeta](media/azure-stack-solution-pipeline/packageorfolder.png)
   
1. En el cuadro de diálogo **Seleccione un archivo o carpeta**, vaya a la ubicación de la compilación de la aplicación web de Azure y, después, seleccione **Aceptar**.
   
1. En la página **Nueva canalización de versión**, seleccione **Guardar** en la parte superior derecha. 
   
1. En la pestaña **Canalización**, seleccione **Agregar un artefacto**. Seleccione el proyecto y después la compilación de Azure Stack en el menú desplegable **Origen (canalización de compilación)** . Seleccione **Agregar**. 
   
1. En **Fases**, mantenga el puntero sobre la fase de **Azure** hasta que aparezca **+** y, después, seleccione **Agregar**.
   
1. En **Plantilla**, seleccione **Fase vacía**. 
   
1. En el cuadro de diálogo **Fase**, escriba *Azure Stack* como nombre de la fase. 
   
   ![Visualización de nueva fase](media/azure-stack-solution-pipeline/newstage.png)
   
1. En **Parámetros**, seleccione el punto de conexión de Azure Stack y escriba el nombre de la aplicación web de Azure Stack como **Nombre de App Service**.
   
1. En **Ejecutar en el agente**, seleccione el agente del servidor de compilación de Azure Stack en la lista desplegable **Grupo de agentes**.
   
1. En **Implementación de Azure App Service**, seleccione el **Paquete o carpeta** para la compilación de Azure Stack y luego **Aceptar** en el cuadro de diálogo **Seleccione un archivo o carpeta**.
   
1. En la pestaña **Variables**, busque la variable llamada **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Establezca el valor de la variable en **true** y establezca su ámbito en **Azure Stack**.
   
   ![Configuración de variable](media/azure-stack-solution-pipeline/setvariable.png)
   
1. En la pestaña **Canalización**, seleccione el icono **Desencadenador de implementación continua** de cada artefacto y establézcalo en **Habilitado**.  
   
   ![Establecimiento del desencadenador de implementación continua](media/azure-stack-solution-pipeline/contindeploymentenabled.png)
   
1. Seleccione el icono **Condiciones anteriores a la implementación** en la fase de Azure Stack y establezca el desencadenador en **Después de la versión**.
   
   ![Establecimiento del desencadenador de las condiciones anteriores a la implementación](media/azure-stack-solution-pipeline/predeployafterrelease.png)
   
1. Seleccione **Guardar** en la parte superior derecha de la página **Nueva canalización de versión** para guardar la canalización de versión.

> [!Note]
> Algunos valores de configuración de las tareas de la versión se pueden haber definido automáticamente como [variables personalizadas](/azure/devops/pipelines/release/variables?view=vsts#custom-variables) al crear la canalización de versión a partir de la plantilla. Estos valores no pueden modificarse en la configuración de la tarea, pero se pueden editar en los elementos de la fase principal.

## <a name="release-the-app"></a>Lanzamiento de la aplicación

Ahora que tiene una canalización de versión, puede usarla para crear una versión e implementar la aplicación. 

Dado que el desencadenador de implementación continua se establece en la canalización de versión, al modificar el código fuente se inicia una nueva compilación y se crea una nueva versión automáticamente. Sin embargo, esta vez va a crear y ejecutar esta nueva versión de forma manual.

Para crear e implementar una versión:

1. En la página Nueva canalización de versión, seleccione **Crear versión** en la parte superior derecha. 
   
   ![Creación de una versión ](media/azure-stack-solution-pipeline/createreleaseicon.png)
   
1. En la página **Crear una nueva versión**:
   1. En **Canalización**, seleccione la fase **Azure** para cambiar su desencadenador de automatizado a manual. 
   1. En **Artefactos**, asegúrese de que están seleccionados los artefactos correctos.
   1. Escriba una **Descripción de la versión** y seleccione **Crear**. 
   
   Un mensaje emergente indica que se ha creado la nueva versión. Puede seleccionar el vínculo del nombre de la versión para ver una página de resumen de la versión que muestra detalles sobre esta, como el estado de implementación.
   
1. Para implementar la versión manual, seleccione la fase **Azure**, **Implementar** y luego **Implementar** en el cuadro de diálogo de fase. 
   
1. Cuando la implementación se complete correctamente, abra la aplicación implementada en el explorador. Por ejemplo, para el sitio web de Azure App Services, abra la dirección URL `https://<your-app-name>.azurewebsites.net`.

### <a name="monitor-and-track-releases"></a>Supervisar y seguir versiones

Puede seleccionar el estado hipervinculado de una fase de versión para ver más información sobre la implementación. 

![Página de resumen de la versión](media/azure-stack-solution-pipeline/releasesummary.png)

Para un administrador es fácil seguir el progreso general de las versiones y ver qué versiones están pendientes de aprobación.

![Página de resumen de la versión que muestra que la aprobación está pendiente](media/azure-stack-solution-pipeline/releasepending.png)

Puede ver los registros de versión de todas las implementaciones: 

1. En el proyecto de Azure DevOps, seleccione **Canalizaciones** > **Versiones** a la izquierda y luego seleccione una versión. 
   
1. En la página de resumen de la versión, mantenga el puntero sobre una fase o selecciónela y luego seleccione **Registros**. 
   
   En el registro de versión, el panel izquierdo muestra el estado de cada operación de cada fase. Durante una implementación, el panel derecho muestra el registro en directo del agente. Una vez finalizada la implementación, el archivo de registro completo aparece en el panel derecho. 
   
1. Seleccione cualquier paso del panel izquierdo para ver el archivo de registro de ese paso, como **Aprobaciones anteriores a la implementación**. 
   
1. Para ver las aprobaciones, seleccione **Ver aprobación** en el panel derecho para ver quién ha aprobado o rechazado la versión y otros detalles. 
   
La posibilidad de ver registros de los pasos individuales facilita el seguimiento y la depuración de partes de la implementación general. También puede **Descargar todos los registros** como archivo *.zip*.
   
![Registro de versión](media/azure-stack-solution-pipeline/releaselog.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](/azure/architecture/patterns).
