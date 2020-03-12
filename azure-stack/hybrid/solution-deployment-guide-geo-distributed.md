---
title: Tráfico directo con una solución de aplicación distribuida geográficamente con Azure y Azure Stack Hub
description: Aprenda a crear una solución de aplicación distribuida geográficamente con Azure y Azure Stack Hub, que dirige el tráfico a puntos de conexión específicos.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: cc17789485bc4c1d0676d54751f4d5bc820c3a5e
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366286"
---
# <a name="create-a-geo-distributed-app-solution-to-direct-traffic-with-azure-and-azure-stack-hub"></a>Crear una solución de aplicación distribuida geográficamente para dirigir el tráfico con Azure y Azure Stack Hub

Aprenda a dirigir el tráfico a puntos de conexión específicos en función de varias métricas con el patrón de aplicaciones distribuidas geográficamente. La creación de un perfil de Traffic Manager con la configuración de puntos de conexión y enrutamiento geográfico garantiza que la información se enruta a puntos de conexión en función de los requisitos regionales, la legislación internacional y corporativa, y su necesidad en cuanto a los datos.

En esta solución, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Crear una aplicación distribuida geográficamente.
> - Usar Traffic Manager para orientar la aplicación.

## <a name="use-the-geo-distributed-apps-pattern"></a>Uso del patrón de aplicaciones distribuidas geográficamente

Con el patrón de distribución geográfica, la aplicación puede abarcar regiones. Puede usar de forma predeterminada la nube pública, pero algunos usuarios pueden requerir que sus datos permanezcan en su región. Puede dirigirlos a la nube más adecuada en función de sus requisitos.

### <a name="issues-and-considerations"></a>Problemas y consideraciones

#### <a name="scalability-considerations"></a>Consideraciones sobre escalabilidad

La solución que va a crear con este artículo no admite la escalabilidad. Sin embargo, si se utiliza en combinación con otras soluciones de Azure y locales, puede dar cabida a los requisitos de escalabilidad. Para obtener información acerca de cómo crear una solución híbrida con ajuste de escala automático a través de Traffic Manager, consulte [Creación de soluciones de escalado en toda la nube con Azure](solution-deployment-guide-cross-cloud-scaling.md).

#### <a name="availability-considerations"></a>Consideraciones sobre disponibilidad

Como sucede con las consideraciones sobre escalabilidad, esta solución no aborda directamente la disponibilidad. Sin embargo, las soluciones locales y de Azure se pueden implementar dentro de esta solución para garantizar una alta disponibilidad para todos los componentes implicados.

### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón

- Su organización oficinas internacionales que requieren una seguridad regional personalizada y directivas de distribución.

- Cada una de las oficinas de su organización extrae datos de los empleados, el negocio y las instalaciones, que requieren informes de actividad para cada normativa local y zona horaria.

- Para cumplir los requisitos de gran escala, es necesario escalar horizontalmente las aplicaciones mediante varias implementaciones de la aplicación en una única región, así como entre regiones, para afrontar los requisitos extremos de carga.

### <a name="planning-the-topology"></a>Planeación de la topología

Antes de crear el entorno de una aplicación distribuida, resulta útil conocer lo siguiente:

-   **Dominio personalizado para la aplicación:** ¿cuál es el nombre de dominio personalizado que los clientes usarán para acceder a la aplicación? En el caso de la aplicación de ejemplo, el nombre de dominio personalizado es *www\.scalableasedemo.com*.

-   **Dominio de Traffic Manager:** se elige nombre de dominio al crear un [perfil de Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Este nombre se combina con el sufijo *trafficmanager.net* para registrar una entrada de dominio que administra Traffic Manager. Para la aplicación de ejemplo, el nombre elegido es *scalable-ase-demo*. Como resultado, el nombre de dominio completo administrado por Traffic Manager es *scalable-ase-demo.trafficmanager.net*.

-   **Estrategia para escalar la superficie de la aplicación:** Decida si la superficie de la aplicación se va a distribuir en varias instancias de App Service Environment de una sola región, de varias regiones o de una opción combinada. La decisión debería basarse en las expectativas de dónde se vaya a originar el tráfico del cliente y de la escalabilidad del resto de la infraestructura de back-end complementaria de una aplicación. Por ejemplo, con una aplicación totalmente sin estado, se puede escalar una aplicación de forma masiva mediante una combinación de varias instancias de App Service Environment por región de Azure, multiplicadas por más instancias de App Service Environment implementadas en varias regiones de Azure. Con más de 15 regiones de Azure globales entre las que elegir, los clientes pueden realmente crear una superficie de aplicación de gran escala en todo el mundo. Para la aplicación de ejemplo que se usa aquí, se crearon tres instancias de App Service Environment en una sola región de Azure (Centro-sur de EE. UU.).

-   **Convención de nomenclatura para las instancias de App Service Environment:** cada instancia de App Service Environment requiere un nombre único. Si existen más de uno o dos entornos de App Service Environment, resulta útil disponer de una convención de nomenclatura que ayude a identificarlos. Para la aplicación de ejemplo que se usa aquí, se usó una convención de nomenclatura sencilla. Los nombres de las tres instancias de App Service Environment son *fe1ase*, *fe2ase* y *fe3ase*.

-   **Convención de nomenclatura para las aplicaciones:** dado que se van a implementar varias instancias de la aplicación, se necesita un nombre para cada instancia de la aplicación implementada. Con las instancias de App Service Environment, se puede usar el mismo nombre de aplicación en varios entornos. Dado que cada uno tiene un sufijo de dominio único, los desarrolladores pueden reutilizar el mismo nombre de aplicación en cada entorno. Por ejemplo, un desarrollador podría asignar los siguientes nombres a las aplicaciones:*miapp.foo1.p.azurewebsites.net*, *miapp.foo2.p.azurewebsites.net*, *miapp.foo3.p.azurewebsites.net, etc*. Para la aplicación que se usa aquí, cada instancia de la aplicación tiene un nombre único. Los nombres de las instancias de aplicación usados son *webfrontend1*, *webfrontend2* y *webfrontend3*.

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub es una extensión de Azure. Azure Stack Hub aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En el artículo [Consideraciones de diseño para aplicaciones híbridas](overview-app-design-considerations.md) se examinan los pilares de la calidad de software (selección de ubicación, escalabilidad, disponibilidad, resistencia, manejabilidad y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.

## <a name="part-1-create-a-geo-distributed-app"></a>Parte 1: Crear una aplicación distribuida geográficamente

En esta parte, creará una aplicación web.

> [!div class="checklist"]
> - Crear y publicar aplicaciones web.
> - Agregar código a Azure Repos.
> - Dirigir la compilación de la aplicación a varios destinos en la nube.
> - Administración y configuración del proceso de CD.

### <a name="prerequisites"></a>Prerrequisitos

Se requieren una suscripción de Azure y la instalación de Azure Stack Hub.

### <a name="geo-distributed-app-steps"></a>Pasos para una aplicación distribuida geográficamente

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Obtención de un dominio personalizado y configuración de DNS

Actualice el archivo de zona DNS para el dominio. Azure AD puede entonces comprobar la propiedad del nombre de dominio personalizado. Use [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para los registros DNS de Azure/Office 365/external dentro de Azure, o bien agregue la entrada DNS a [un registrador DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Registre un dominio personalizado con un registrador público.

2. Inicie sesión en el registrador de nombres de dominio para el dominio. Puede que se requiera un administrador autorizado para realizar las actualizaciones de DNS.

3. Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que Azure AD proporcione. La entrada DNS no cambiará los comportamientos como el enrutamiento de correo o el hospedaje web.

### <a name="create-web-apps-and-publish"></a>Creación y publicación de aplicaciones web

Configure la canalización de integración y entrega continuas (CI/CD) híbrida para implementar la aplicación web en Azure y Azure Stack Hub e insertar automáticamente los cambios en ambas nubes.

> [!Note]  
> Se requiere Azure Stack Hub con las imágenes adecuadas sindicadas para ejecutarse (Windows Server y SQL) y la implementación de App Service. Para obtener más información, consulte [Antes de empezar a trabajar con App Service en Azure Stack Hub](../operator/azure-stack-app-service-before-you-get-started.md) en la documentación del operador de Azure Stack Hub.

#### <a name="add-code-to-azure-repos"></a>Adición de código a Azure Repos

1. Inicie sesión en Visual Studio con **una cuenta que tenga derechos de creación de proyectos** en Azure Repos.

    La CI/CD se puede aplicar al código de aplicación y al código de infraestructura. Use [plantillas de Azure Resource Manager](https://azure.microsoft.com/resources/templates/) tanto para el desarrollo en la nube hospedado como para el privado.

    ![Conectarse a un proyecto en Visual Studio](media/solution-deployment-guide-geo-distributed/image1.JPG)

2. **Clone el repositorio**; para ello, cree y abra la aplicación web predeterminada.

    ![Clonar el repositorio en Visual Studio](media/solution-deployment-guide-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Creación de una implementación de aplicaciones web en ambas nubes

1.  Edite el archivo **WebApplication.csproj**: Seleccione `Runtimeidentifier` y agregue `win10-x64`. (Consulte la documentación de [Implementaciones autocontenidas](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf)).

    ![Editar el archivo de proyecto de aplicación web en Visual Studio](media/solution-deployment-guide-geo-distributed/image3.png)

1.  **Inserte el código en Azure Repos** mediante Team Explorer.

2.  Confirme que el **código de la aplicación** se ha insertado en Azure Repos.

### <a name="create-the-build-definition"></a>Creación de la definición de compilación

1. **Inicie sesión en Azure Pipelines** para confirmar la posibilidad de crear definiciones de compilación.

2. Agregue el código `-r win10-x64`. Esta adición es necesaria para activar una implementación autocontenida con .Net Core.

    ![Agregar código para la definición de compilación](media/solution-deployment-guide-geo-distributed/image4.png)

3. **Ejecute la compilación**. El proceso de [compilación de implementación autocontenida](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf) publicará los artefactos que se pueden ejecutar en Azure y Azure Stack Hub.

**Uso de un agente hospedado de Azure**

El uso de un agente hospedado en Azure Pipelines es una opción adecuada para compilar e implementar aplicaciones web. Microsoft Azure realiza automáticamente el mantenimiento y las actualizaciones, lo que permite el desarrollo, la prueba y la implementación de forma continua e ininterrumpida.

### <a name="manage-and-configure-the-cd-process"></a>Administración y configuración del proceso de CD

Azure DevOps Services proporcionan una canalización con una gran capacidad de configuración y administración para versiones de varios entornos, como desarrollo, ensayo, control de calidad (QA) y producción, lo que incluye las aprobaciones necesarias en etapas específicas.

## <a name="create-release-definition"></a>Creación de la definición de versión

1.  Seleccione el botón **más** para agregar una nueva versión en la pestaña **Versiones** de la sección **Compilación y versión** de VSO.

    ![Creación de una definición de versión](media/solution-deployment-guide-geo-distributed/image5.png)

2. Aplique la plantilla Implementación de Azure App Service.

   ![Aplicar la plantilla Implementación de Azure App Service](meDia/solution-deployment-guide-geo-distributed/image6.png)

3. En **Agregar artefacto**, agregue el artefacto para la aplicación de compilación de nube de Azure.

   ![Agregar el artefacto a la compilación en la nube de Azure](media/solution-deployment-guide-geo-distributed/image7.png)

4. En la pestaña Canalización, seleccione el vínculo **Fase, Tarea** del entorno y establezca los valores del entorno de nube de Azure.

   ![Definir los valores de entorno de nube de Azure](media/solution-deployment-guide-geo-distributed/image8.png)

5. Establezca el **nombre del entorno** y seleccione la **suscripción de Azure** como el punto de conexión de la nube de Azure.

      ![Seleccione la suscripción de Azure para el punto de conexión en la nube de Azure](media/solution-deployment-guide-geo-distributed/image9.png)

6. En **Nombre de App Service**, establezca el nombre del servicio de aplicación de Azure necesario.

      ![Definir el nombre del servicio de App Service](media/solution-deployment-guide-geo-distributed/image10.png)

7. Escriba "Hospedado VS2017" en **Cola de agentes** para el entorno hospedado en la nube de Azure.

      ![Defina la Cola de agentes para el entorno hospedado de la nube de Azure.](media/solution-deployment-guide-geo-distributed/image11.png)

8. En el menú de implementación de Azure App Service, seleccione el **paquete o carpeta** válidos para el entorno. Seleccione **Aceptar** para la **ubicación de carpeta**.
  
      ![Seleccionar el paquete o la carpeta del entorno de Azure App Service](media/solution-deployment-guide-geo-distributed/image12.png)

      ![Seleccionar el paquete o la carpeta del entorno de Azure App Service](media/solution-deployment-guide-geo-distributed/image13.png)

9. Guarde todos los cambios y vuelva a la **canalización de versión**.

    ![Guardar los cambios en la canalización de versión](media/solution-deployment-guide-geo-distributed/image14.png)

10. Agregue un nuevo artefacto mediante la selección de la compilación para la aplicación de Azure Stack Hub.
    
    ![Agregar nuevo artefacto para la aplicación de Azure Stack Hub](media/solution-deployment-guide-geo-distributed/image15.png)


11. Agregue un entorno más; para ello, aplique Implementación de Azure App Service.
    
    ![Agregar entorno en implementación de Azure App Service](media/solution-deployment-guide-geo-distributed/image16.png)

12. Asigne al nuevo entorno el nombre Azure Stack Hub.
    
    ![Nombrar entorno en implementación de Azure App Service](media/solution-deployment-guide-geo-distributed/image17.png)

13. Busque el entorno de Azure Stack Hub en la pestaña **Tarea**.
    
    ![Entorno de Azure Stack Hub](media/solution-deployment-guide-geo-distributed/image18.png)

14. Seleccione la suscripción para el punto de conexión de Azure Stack Hub.
    
    ![Seleccionar la suscripción para el punto de conexión de Azure Stack Hub](media/solution-deployment-guide-geo-distributed/image19.png)

15. Establezca el nombre de la aplicación web de Azure Stack Hub como el nombre del servicio de aplicación.

    ![Definir el nombre de la aplicación web de Azure Stack Hub](media/solution-deployment-guide-geo-distributed/image20.png)

16. Seleccione el agente de Azure Stack Hub.
    
    ![Seleccionar el agente de Azure Stack Hub](media/solution-deployment-guide-geo-distributed/image21.png)

17. En la sección de implementación de Azure App Service, seleccione el **paquete o carpeta** válidos para el entorno. Seleccione **Aceptar** para la ubicación de carpeta.

    ![Seleccionar carpeta para la implementación de Azure App Service](media/solution-deployment-guide-geo-distributed/image22.png)

    ![Seleccionar carpeta para la implementación de Azure App Service](media/solution-deployment-guide-geo-distributed/image23.png)

18. En la pestaña Variable, agregue una variable denominada `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, establezca su valor como **true** y defina el ámbito en Azure Stack Hub.
    
    ![Agregar variable a la implementación de App de Azure](media/solution-deployment-guide-geo-distributed/image24.png)

19. Seleccione el icono del desencadenador de implementación **Continuo** en ambos artefactos y habilite el desencadenador de implementación **Continua**.
    
    ![Seleccionar desencadenador de implementación continua](media/solution-deployment-guide-geo-distributed/image25.png)

20. Seleccione el icono de condiciones **previas a la implementación** en el entorno de Azure Stack Hub y establezca el desencadenador en **Tras el lanzamiento**.
    
    ![Seleccionar condiciones anteriores a la implementación](media/solution-deployment-guide-geo-distributed/image26.png)

21. Guarde todos los cambios.

> [!Note]  
> Algunos valores de configuración de las tareas se han definido automáticamente como [variables de entorno](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) cuando se creó una definición de versión desde una plantilla. Estos valores de configuración no se pueden modificar en la configuración de tareas; para hacerlo, debe seleccionar el elemento del entorno principal.

## <a name="part-2-update-web-app-options"></a>Parte 2: Actualizar las opciones de la aplicación web

[Azure App Service](https://docs.microsoft.com/azure/app-service/overview) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático. 

![Azure App Service](media/solution-deployment-guide-geo-distributed/image27.png)

> [!div class="checklist"]
> - Asigne un nombre DNS personalizado a Azure Web Apps.
> - Use un **registro CNAME** o un **registro A** para asignar un nombre DNS personalizado a App Service.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Asignar un nombre DNS personalizado a Azure Web Apps

> [!Note]  
>  Se recomienda usar un CNAME para todos los nombres DNS personalizados, excepto un dominio raíz (por ejemplo, northwind.com).

Para migrar un sitio en vivo y su nombre de dominio DNS a App Service, consulte [Migración de un nombre DNS activo a Azure App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).

### <a name="prerequisites"></a>Prerrequisitos

Para realizar esta solución:

-   [Cree una aplicación de App Service](https://docs.microsoft.com/azure/app-service/) o use alguna aplicación que se haya creado para otra solución.

-   Compre un nombre de dominio y asegure el acceso al registro DNS para el proveedor de dominio.

Actualice el archivo de zona DNS para el dominio. Azure AD comprobará la propiedad del nombre de dominio personalizado. Use [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para los registros DNS de Azure/Office 365/external dentro de Azure, o bien agregue la entrada DNS a [un registrador DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Registre un dominio personalizado con un registrador público.

-   Inicie sesión en el registrador de nombres de dominio para el dominio. (Puede que se requiera un administrador autorizado para realizar las actualizaciones de DNS).

-   Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que Azure AD proporcione.

Por ejemplo, para agregar entradas DNS a northwindcloud.com y www\.northwindcloud.com, configure los valores de DNS del dominio raíz northwindcloud.com.

> [!Note]  
>  Un nombre de dominio puede adquirirse mediante [Azure Portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain). Para asignar un nombre DNS personalizado a una aplicación web, el [plan de App Service](https://azure.microsoft.com/pricing/details/app-service/) de dicha aplicación debe ser un nivel de pago (**Compartido**, **Básico**, **Estándar** o **Premium**).



### <a name="create-and-map-cname-and-a-records"></a>Creación y asignación de los registros D y CNAME

#### <a name="access-dns-records-with-domain-provider"></a>Acceso a los registros DNS con el proveedor de dominios

> [!Note]  
>  Puede utilizar Azure DNS para configurar un nombre DNS personalizado para Azure Web Apps. Para más información, consulte [Usar Azure DNS para proporcionar la configuración de un dominio personalizado para un servicio de Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain).

1.  Inicie sesión en el sitio web de su proveedor principal.

2.  Busque la página de administración de registros DNS. Cada proveedor de dominio tiene su propia interfaz de registros DNS. Busque áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**.

La página de registros DNS puede verse en **Mis dominios**. Busque el vínculo denominado **Archivo de zona**, **Registros DNS** o **Configuración avanzada**.

La captura de pantalla siguiente es un ejemplo de página de registros DNS:

![Página de registros DNS de ejemplo](media/solution-deployment-guide-geo-distributed/image28.png)

1. En el registrador de nombres de dominio, seleccione **Agregar o crear** para crear un registro. Algunos proveedores tienen diferentes vínculos para agregar diferentes tipos de registros. Consulte la documentación del proveedor.

2. Agregue un registro CNAME para asignar un subdominio al nombre de host predeterminado de la aplicación.

   Para el ejemplo del dominio www\.northwindcloud.com, agregue un registro CNAME que asigne el nombre a <nombre\_Aplicación>.azurewebsites.net.

Después de agregar el registro CNAME, la página de registros DNS es como la del ejemplo siguiente:

![Navegación en el portal a la aplicación de Azure](media/solution-deployment-guide-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Habilitación de la asignación de registros CNAME en Azure

1. En otra pestaña, inicie sesión en Azure Portal.

2. Vaya a App Services.

3. Seleccione la aplicación web.

4. En el panel de navegación izquierdo de la página de la aplicación en Azure Portal, seleccione **Dominios personalizados**.

5. Seleccione el icono **+** situado junto a **Agregar nombre de host**.

6. Escriba el nombre de dominio completo, por ejemplo `www.northwindcloud.com`.

7. Seleccione **Validar**.

8. Si se indica, agregue más registros de otros tipos (`A` o `TXT`) a los registros DNS de los registradores de nombres de dominio. Azure proporcionará los valores y los tipos de estos registros:

   a.  Un registro **D** que se asigna a la dirección IP de la aplicación.

   b.  Un registro **TXT** que se asigna al nombre de host predeterminado de la aplicación <app_name>.azurewebsites.net. App Service usa este registro solo durante la configuración para confirmar la propiedad del dominio personalizado. Después de la confirmación, elimine el registro TXT.

9. Complete esta tarea en la pestaña del registrador de dominio y vuelva a validar hasta que el botón **Agregar nombre de host** se active.

10. Asegúrese de que en **Tipo de registro de nombre de host** está seleccionado **CNAME** (www.example.com o cualquier subdominio).

11. Seleccione **Agregar nombre de host**.

12. Escriba el nombre de dominio completo, por ejemplo `northwindcloud.com`.

13. Seleccione **Validar**. Se activa **Agregar**.

14. Asegúrese de que el **Tipo de registro de nombre de host** esté establecido en **Registro A** (example.com).

15. **Agregue un nombre de host**.

    El nuevo nombre de host puede tardar algo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.
  
    ![Dominios personalizados](media/solution-deployment-guide-geo-distributed/image31.png) 
  
    Si se produce un error, aparecerá una notificación de error de comprobación en la parte inferior de la página. ![Error de comprobación](media/solution-deployment-guide-geo-distributed/image32.png)

> [!Note]  
>  Se pueden repetir los pasos anteriores para asignar un dominio con comodín (\*.northwindcloud.com). Esto permite la adición de subdominios adicionales a este servicio de aplicaciones sin tener que crear un registro CNAME independiente para cada uno. Siga las instrucciones del registrador para establecer esta configuración.

#### <a name="test-in-a-browser"></a>Prueba en un explorador

Vaya a los nombres DNS que configuró anteriormente (por ejemplo, `northwindcloud.com` o www.northwindcloud.com).

## <a name="part-3-bind-a-custom-ssl-cert"></a>Parte 3: Enlazar un certificado SSL personalizado

En esta parte, seguiremos estos pasos:

> [!div class="checklist"]
> - Se enlazará el certificado SSL personalizado con App Service.
> - Se aplicará HTTPS para la aplicación.
> - Automatizar el enlace de certificado SSL con scripts.

> [!Note]  
> Si es necesario, se obtendrá un certificado SSL de cliente en Azure Portal y se enlazará a la aplicación web. Para obtener más información, consulte el [tutorial sobre App Service Certificates](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Prerrequisitos

Para realizar esta solución:

-   [Cree una aplicación de App Service](https://docs.microsoft.com/azure/app-service/).
-   [Asigne un nombre DNS personalizado a la aplicación web.](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Adquiera un certificado SSL de una entidad de certificación de confianza y use la clave para firmar la solicitud.

### <a name="requirements-for-your-ssl-certificate"></a>Requisitos para el certificado SSL

Para usar un certificado en App Service, el certificado debe cumplir los siguientes requisitos:

-   Estar firmado por una entidad de certificación de confianza.

-   Haberse exportado como archivo PFX protegido por contraseña.

-   Contener una clave privada con una longitud de al menos 2048 bits.

-   Contener todos los certificados intermedios de la cadena de certificados.

> [!Note]  
>  Los **certificados de criptografía de curva elíptica (ECC)** funcionan con App Service, pero están fuera del ámbito de esta guía. Consulte una entidad de certificación para obtener ayuda en la creación de certificados ECC. 

#### <a name="prepare-the-web-app"></a>Preparación de la aplicación web

Para enlazar un certificado SSL personalizado a la aplicación web, su [plan de App Service](https://azure.microsoft.com/pricing/details/app-service/) debe estar en el nivel **Básico**, **Estándar** o **Premium**.

#### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

1.  Abra [Azure Portal](https://portal.azure.com/) y vaya a la aplicación web.

2.  En el menú izquierdo, seleccione **App Services** y, después, el nombre de la aplicación web.

![Seleccionar la aplicación web](media/solution-deployment-guide-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>Comprobar el plan de tarifa

1.  En el panel de navegación izquierdo de la página de la aplicación web, desplácese a la sección **Configuración** y seleccione **Escalar verticalmente (plan de App Service)** .

    ![Menú Escalar verticalmente](media/solution-deployment-guide-geo-distributed/image34.png)

1.  Asegúrese de que la aplicación web no está en el nivel **Gratis** ni **Compartido**. El nivel actual de la aplicación web aparece resaltado en un cuadro azul oscuro.

    ![Comprobar plan de tarifa](media/solution-deployment-guide-geo-distributed/image35.png)

El SSL personalizado no es compatible con los niveles **Gratis** y **Compartido**. Para escalar, siga los pasos de la sección siguiente o, en la página **Elija su plan de tarifa**, vaya directamente a las secciones sobre cómo [cargar y enlazar el certificado SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Escalar verticalmente el plan de App Service

1.  Seleccione uno de los niveles, **Básico**, **Estándar** o **Premium**.

2.  Elija **Seleccionar**.

![Elegir plan de tarifa](media/solution-deployment-guide-geo-distributed/image36.png)

La operación de escalado está completa cuando se muestra la notificación.

![Notificación de escalado vertical](media/solution-deployment-guide-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Enlace del certificado SSL y combinación de certificados intermedios

Combine varios certificados en la cadena.

1. **Abra cada certificado** que ha recibido en un editor de texto.

2. Cree un archivo para el certificado combinado, denominado *mergedcertificate.crt*. En un editor de texto, copie el contenido de cada certificado en este archivo. Los certificados deben seguir el orden de la cadena de certificados, comenzando por el certificado y terminando por el certificado raíz. Debe ser similar al ejemplo siguiente:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Exportar el certificado a PFX

Exporte el certificado SSL combinado con la clave privada generada por el certificado.

Se crea un archivo de clave privada a través de OpenSSL. Para exportar el certificado a PFX, ejecute el comando siguiente y reemplace los marcadores de posición *\<private-key-file* y *\<merged-certificate-file>* por las rutas de acceso de la clave privada y el archivo de certificado combinado:

```powershell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Cuando se le pida, defina una contraseña de exportación para cargar el certificado SSL a Azure App Service más adelante.

Cuando se usan IIS o **Certreq.exe** para generar la solicitud de certificado, instale el certificado en una máquina local y luego [exporte el certificado a PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Carga del certificado SSL

1. Seleccione **Configuración de SSL** en la navegación del lado izquierdo de la aplicación web.

2. Seleccione **Cargar certificado**.

3. En **Archivo de certificado PFX**, seleccione el archivo PFX.

4. En **Contraseña del certificado**, escriba la contraseña que se creó al exportar el archivo PFX.

5. Seleccione **Cargar**.

![Carga del certificado](media/solution-deployment-guide-geo-distributed/image38.png)

Cuando App Service termina de cargar el certificado, este aparece en la página **Configuración de SSL**.

![Configuración de SSL](media/solution-deployment-guide-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Enlazar el certificado SSL

1.  En la sección **Enlaces SSL**, seleccione **Agregar enlaces**.

    > [!Note]  
    >  Si se ha cargado el certificado, pero no aparece en los nombres de dominio en la lista desplegable **Nombre de host**, pruebe a actualizar la página del explorador.

1.  En la página **Agregar enlace SSL**, use las listas desplegables para seleccionar el nombre de dominio que se va a proteger, así como el certificado que pretende utilizar.

1.  En **Tipo de SSL**, seleccione si se va a usar [**Indicación de nombre de servidor (SNI)** ](https://en.wikipedia.org/wiki/Server_Name_Indication) o SSL basada en IP.

    - **SSL basada en SNI**: Se pueden agregar varios enlaces SSL basados en SNI. Esta opción permite que varios certificados SSL protejan varios dominios en una misma dirección IP. Los exploradores más modernos (como Internet Explorer, Chrome, Firefox y Opera) admiten SNI (encontrará información de compatibilidad con exploradores más completa en [Indicación de nombre de servidor](https://wikipedia.org/wiki/Server_Name_Indication)).

    - **SSL basada en IP**: solo puede agregarse un enlaces SSL basado en IP. Esta opción solo permite que un único certificado SSL proteja una dirección IP dedicada. Para proteger varios dominios, debe usar el mismo certificado SSL. La SSL basada en IP es la opción tradicional para enlaces SSL.

1. Haga clic en **Agregar enlace**.

    ![Agregar enlace SSL](media/solution-deployment-guide-geo-distributed/image40.png)

Cuando App Service termina de cargar el certificado, este aparece en la sección **Enlaces SSL**.

![Enlaces SSL](media/solution-deployment-guide-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Reasignación del registro D en SSL de IP

Si la SSL basada en IP no se usa en la aplicación web, vaya a [Probar HTTPS para el dominio personalizado](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

De manera predeterminada, la aplicación web usa una dirección IP pública compartida. Cuando se enlaza un certificado con SSL basada en IP, App Service crea otra dirección IP dedicada para la aplicación web.

Cuando un registro D se asigna a la aplicación web, el registro de dominio debe actualizarse con la dirección IP dedicada.

La página **Dominio personalizado** se actualiza con la nueva dirección IP dedicada. Copie esta [dirección IP](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) y luego reasigne el [registro D](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) a esta nueva dirección IP.

#### <a name="test-https"></a>Probar HTTPS

En varios exploradores, vaya a https://<your.custom.domain> para garantizar que la aplicación web funciona.

![Navegación a la aplicación web](media/solution-deployment-guide-geo-distributed/image42.png)

> [!Note]  
> Si se producen errores de validación de certificado, la causa podría ser que hayan quedado un certificado autofirmado o certificados intermedios al exportar al archivo PFX.

#### <a name="enforce-https"></a>Aplicación de HTTPS

De forma predeterminada, cualquier usuario puede acceder a la aplicación web mediante HTTP. Todas las solicitudes HTTP al puerto HTTPS se pueden redirigir.

En la página de la aplicación web, seleccione **Configuración de SL**. A continuación, en **Solo HTTPS**, seleccione **On**.

![Aplicación de HTTPS](media/solution-deployment-guide-geo-distributed/image43.png)

Una vez completada la operación, vaya a cualquiera de las direcciones URL HTTP que apuntan a la aplicación. Por ejemplo:

-   https://<app_name>.azurewebsites.net
-   https://northwindcloud.com
-   <https://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Aplicación de TLS 1.1 y 1.2

La aplicación permite [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 de forma predeterminada que, según los estándares del sector, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard), ya no se considera seguro. Para aplicar las versiones posteriores de TLS, siga estos pasos:

1.  En la página de la aplicación web, en el panel de navegación izquierdo, seleccione **Configuración de SSL**.

2.  En **Versión de TLS**, seleccione la versión mínima de TLS que desee.

![Exigir aplicación de TLS 1.1 o 1.2](media/solution-deployment-guide-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Crear un perfil de Traffic Manager

1.  Seleccione **Crear un recurso** > **Redes** > **Perfil de Traffic Manager** > **Crear**.

2.  En **Crear perfil de Traffic Manager**, complete los campos de la manera siguiente:

    1.  En **Nombre**, proporcione un nombre para el perfil. Este nombre debe ser único en la zona trafficmanager.net y generará el nombre DNS trafficmanager.net, que se usa para acceder al perfil de Traffic Manager.

    2.  En **Método de enrutamiento**, seleccione el **Método de enrutamiento geográfico**.

    3.  En **Suscripción**, seleccione la suscripción en la que desea crear este perfil.

    4.  En **Grupo de recursos**, cree un grupo de recursos nuevo en el cual colocar este perfil.

    5.  En **Ubicación del grupo de recursos**, seleccione la ubicación del grupo de recursos. Esta configuración hace referencia a la ubicación del grupo de recursos y no tiene efecto alguno sobre el perfil de Traffic Manager que se implementa globalmente.

    6.  Seleccione **Crear**.

    7.  Una vez que se complete la implementación global del perfil de Traffic Manager, aparecerá en el grupo de recursos respectivo como uno de los recursos.

    ![Grupos de recursos para crear un perfil de Traffic Manager](media/solution-deployment-guide-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Incorporación de puntos de conexión de Traffic Manager

1. En la barra de búsqueda del portal, busque el **nombre del perfil de Traffic Manager** que creó en la sección anterior y seleccione el perfil en los resultados que aparecen.

2. En **Perfil de Traffic Manager**, en la sección **Configuración**, seleccione **Puntos de conexión**.

3. Seleccione **Agregar**.

4. Incorporación del punto de conexión de Azure Stack Hub.

5. En **Tipo**, seleccione **Punto de conexión externo**.

6. Proporcione un **nombre** para este punto de conexión: la mejor opción es el nombre de Azure Stack Hub.

7. En el nombre de dominio completo (**FQDN**), use la dirección URL externa de la aplicación web de Azure Stack Hub.

8. En la asignación geográfica, seleccione la región o continente donde se encuentra el recurso. Por ejemplo, **Europa.**

9. En la lista desplegable de países y regiones que aparece, seleccione el país que se aplica a este punto de conexión Por ejemplo, **Alemania**.

10. No active la opción **Agregar como deshabilitado**.

11. Seleccione **Aceptar**.

12. Incorporación del punto de conexión de Azure:

    1.  En **Tipo**, seleccione **Punto de conexión de Azure**.

    2.  Proporcione un **Nombre** para el punto de conexión.

    3.  En **Tipo de recurso de destino**, seleccione **App Service**.

    4.  En **Recurso de destino**, seleccione **Elegir un servicio de aplicaciones** para mostrar la lista de Web Apps en la misma suscripción. En **Recurso**, elija el servicio de aplicación que se usa como primer punto de conexión.

13. En la asignación geográfica, seleccione la región o continente donde se encuentra el recurso. Por ejemplo, **Norteamérica/Centroamérica/Caribe.**

14. En la lista desplegable de países o regiones que aparece, deje este espacio en blanco para seleccionar toda la agrupación regional anterior.

15. No active la opción **Agregar como deshabilitado**.

16. Seleccione **Aceptar**.

    > [!Note]  
    >  Cree al menos un punto de conexión con el ámbito geográfico Todos (mundo) para que actúe como punto de conexión predeterminado para el recurso.

1. Cuando termine de agregar ambos puntos de conexión, aparecerán en **Perfil de Traffic Manager** junto con el estado de supervisión como **En línea**.

    ![Estado de punto de conexión de perfil de Traffic Manager](media/solution-deployment-guide-geo-distributed/image46.png)

**La empresa global se basa en las capacidades de la distribución geográfica de Azure**

Dirigir el tráfico de datos a través de Azure Traffic Manager y puntos de conexión específicos de la geografía permite a las empresas globales cumplir la legislación regional y mantener los datos conformes a la vez que seguros, lo que resulta crucial para el éxito del negocio tanto en el entorno local como en ubicaciones remotas.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
