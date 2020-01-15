---
title: Patrón para compilar una aplicación que escale entre nubes mediante datos locales, en Azure y Azure Stack Hub.
description: Aprenda a usar Azure y Azure Stack Hub para compilar una aplicación entre nubes escalable que use datos locales.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: fbf25d49539ebe244b44681ca3207badf744b99b
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2020
ms.locfileid: "75718290"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>Patrón de escalado entre nubes (datos locales)

Aprenda a compilar una aplicación híbrida que abarque Azure y Azure Stack Hub. Este patrón también muestra cómo usar un solo origen de datos local para el cumplimiento.

## <a name="context-and-problem"></a>Contexto y problema

Muchas organizaciones recopilan y almacenan enormes cantidades de datos confidenciales de los clientes. No se les suele permitir almacenar datos confidenciales en la nube pública, ya sea debido a regulaciones corporativas o a directivas gubernamentales. Estas organizaciones también desean aprovechar la escalabilidad de la nube pública. La nube pública puede controlar los picos estacionales de tráfico, lo que permite a los clientes pagar exactamente por el hardware que necesitan cuando lo necesitan.

## <a name="solution"></a>Solución

La solución aprovecha las ventajas de cumplimiento de la nube privada, combinándolas con la escalabilidad de la nube pública. La nube híbrida de Azure y Azure Stack Hub ofrece una experiencia coherente a los desarrolladores. Esta coherencia les permite aplicar sus aptitudes tanto a la nube pública como a los entornos locales.

La guía de implementación de soluciones permite implementar una aplicación web idéntica en una nube pública y privada. También puede acceder a una red enrutable sin conexión a Internet hospedada en la nube privada. Las aplicaciones web se supervisan a fin de cargarse. Tras aumentar el tráfico de forma significativa, un programa manipula los registros DNS para redireccionar el tráfico a la nube pública. Si el tráfico deja de ser significativo, los registros DNS se actualizarán para dirigir el tráfico de vuelta a la nube privada.

[![Escalado entre nubes con el patrón de datos local](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>Componentes

Esta solución usa los siguientes componentes:

| Nivel | Componente | Descripción |
|----------|-----------|-------------|
| Azure | Azure App Service | [Azure App Service](/azure/app-service/) permite compilar y hospedar aplicaciones web, aplicaciones de API de RESTful y Azure Functions. Todo en el lenguaje de programación de su elección, sin administrar infraestructura alguna. |
| | Azure Virtual Network| [Azure Virtual Network (VNet)](/azure/virtual-network/virtual-networks-overview) es el bloque de creación fundamental de redes privadas en Azure. VNet permite varios tipos de recursos de Azure, como Virtual Machines (VM), para comunicarse de forma segura entre usuarios, con Internet y con las redes locales. La solución también muestra el uso de componentes de red adicionales:<br>- subredes de puerta de enlace y aplicación<br>- una puerta de enlace de red local<br>- una puerta de enlace de red virtual, que actúa como conexión de puerta de enlace de VPN de sitio a sitio<br>- una dirección IP pública<br>- una conexión VPN de punto a sitio<br>- Azure DNS para hospedar dominios DNS y proporcionar la resolución de nombres |
| | Administrador de tráfico de Azure | [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) es un equilibrador de carga de tráfico basado en DNS. Permite controlar la distribución del tráfico de los usuarios para puntos de conexión de servicio en distintos centros de datos. |
| | Azure Application Insights | [Application Insights](/azure/azure-monitor/app/app-insights-overview) es un servicio de Application Performance Management extensible para desarrolladores web que compilan y administran aplicaciones en varias plataformas.|
| | Azure Functions | [Azure Functions](/azure/azure-functions/) permite ejecutar el código en un entorno sin servidor sin necesidad de crear antes una máquina virtual o publicar una aplicación web. |
| | Escalado automático de Azure | El [escalado automático](/azure/azure-monitor/platform/autoscale-overview) es una característica que está integrada en Cloud Services, Virtual Machines y Web Apps. La característica permite a las aplicaciones obtener sus mejores resultados al cambiar la demanda. Las aplicaciones se ajustarán para los picos de tráfico, comunicándole cuándo cambian las métricas, y el escalado según sea necesario. |
| Azure Stack Hub | Proceso de IaaS | Azure Stack Hub permite usar el mismo modelo de aplicación, el portal de autoservicio y API que habilita Azure. IaaS de Azure Stack Hub permite una amplia gama de tecnologías de código abierto para implementaciones de nube híbrida coherentes. En el ejemplo de la solución se usa una máquina virtual de Windows Server para SQL Server, por ejemplo.|
| | Azure App Service | Del mismo modo que la aplicación web de Azure, la solución usa [Azure App Service en Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-overview) para hospedar la aplicación web. |
| | Redes | La red virtual de Azure Stack Hub funciona exactamente como Azure Virtual Network. Usa muchos de los mismos componentes de red, incluidos nombres de host personalizados. 
| Azure DevOps Services | Registro | Configure rápidamente la integración continua para compilación, prueba e implementación. Para obtener más información, consulte [Registrarse e iniciar sesión en Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops). |
| | Azure Pipelines | Use [Azure Pipelines](/azure/devops/pipelines/agents/agents?view=azure-devops) para integración y entrega continuas. Azure Pipelines permite administrar definiciones y agentes de versión y compilación hospedados. |
| | Repositorio de código | Aproveche varios repositorios de código para optimizar su canalización de desarrollo. Use repositorios de código existentes en GitHub, Bitbucket, Dropbox, OneDrive y Azure Repos. |

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar esta solución:

### <a name="scalability"></a>Escalabilidad 

Azure y Azure Stack Hub solo son adecuados para respaldar las necesidades de la empresa distribuida globalmente actual.

**Nube híbrida sin los inconvenientes**

Microsoft ofrece una integración incomparable de los recursos locales con Azure Stack Hub y Azure en una solución unificada. Esta integración acaba con los inconvenientes de administrar varias soluciones específicas y una combinación de los proveedores de nube. Con el escalado entre nubes, la eficacia de Azure se consigue con solo unos clics. Solo tiene que conectar su instancia de Azure Stack Hub a Azure con la ampliación en la nube y sus datos y aplicaciones estarán disponibles en Azure si es necesario.

- Elimine la necesidad de crear y mantener un sitio de recuperación ante desastres secundario.
- Ahorre tiempo y dinero mediante la eliminación de la copia de seguridad en cinta y hospede hasta 99 años de datos de copia de seguridad en Azure.
- Migre fácilmente cargas de trabajo de Hyper-V, físicas (en versión preliminar) y VMware (en versión preliminar) en ejecución a Azure para aprovechar la economía y elasticidad de la nube.
- Ejecute análisis o informes intensivos de proceso en una copia replicada de su recurso local en Azure, sin que ello afecte a las cargas de trabajo de producción.
- Irrumpa en la nube y ejecute cargas de trabajo locales en Azure, con plantillas de proceso más grandes si es necesario. Lo híbrido le ofrece la eficacia que necesita cuando la necesita.
- Cree entornos de desarrollo de varios niveles en Azure con unos pocos clics (incluso puede replicar datos de producción activos en su entorno desarrollo/pruebas para mantenerlos sincronizados casi en tiempo real).

**Economía del escalado entre nubes con Azure Stack Hub**

La ventaja clave de la irrupción en la nube es el ahorro económico. Solo paga por los recursos adicionales si hay una demanda de dichos recursos. No hay más gastos en capacidad adicional innecesaria ni al intentar predecir los picos y fluctuaciones de la demanda.

**Reducción de cargas de alta demanda en la nube**

El escalado entre nubes se puede usar para respaldar las cargas de procesamiento. La carga se distribuye moviendo aplicaciones básicas a la nube pública, liberando recursos locales para aplicaciones críticas para la empresa. Puede utilizarse una aplicación en la nube privada y luego ampliarla a la nube pública solo cuando sea necesario cumplir con las demandas.


### <a name="availability"></a>Disponibilidad

La implementación global tiene sus propios desafíos, como la conectividad de variable y la diferencia de las regulaciones gubernamentales por región. Los desarrolladores pueden desarrollar solo una aplicación y, a continuación, implementarla por diferentes motivos con diversos requisitos.  Implemente su aplicación en la nube pública de Azure y, a continuación, implemente instancias o componentes adicionales localmente. Puede administrar el tráfico entre todas las instancias mediante Azure.

### <a name="manageability"></a>Facilidad de uso

**Un enfoque de desarrollo único y coherente**

Azure y Azure Stack Hub permiten usar un conjunto coherente de herramientas de desarrollo en toda la organización. Esta coherencia facilita la implementación de una práctica de integración continua y desarrollo continuo (CI/CD). Muchas aplicaciones y servicios implementados en Azure y Azure Stack Hub son intercambiables y se pueden ejecutar en cualquier ubicación sin problemas.

Una canalización de CI/CD híbrida puede ayudarle a:
- Iniciar una nueva compilación basada en confirmaciones de código en su repositorio de código.
- Implementar automáticamente el código recién compilado en Azure para llevar a cabo las pruebas de aceptación del usuario.
- Una vez que el código haya pasado las pruebas, se implementan automáticamente en Azure Stack Hub.

**Una solución de administración de identidades única y coherente**

Azure Stack Hub funciona con Azure Active Directory y Servicios de federación de Active Directory (AD FS). Azure Stack Hub funciona con Azure Active Directory en escenarios conectados. En los entornos sin conectividad, puede usar ADFS como solución desconectada. Las entidades de servicio se usan para conceder acceso a las aplicaciones, permitiéndoles implementar o configurar recursos a través de Azure Resource Manager. 

### <a name="security"></a>Seguridad

**Garantía de cumplimiento y soberanía de los datos**

Azure Stack Hub permite ejecutar el mismo servicio en varios países como si usara una nube pública. La implementación de la misma aplicación en centros de datos de cada país permite el cumplimiento de los requisitos de soberanía de datos. Esta funcionalidad garantiza el mantenimiento de los datos personales dentro de las fronteras de cada país.

**Azure Stack Hub: posición de seguridad**

No hay ninguna posición de seguridad sin un proceso de mantenimiento sólido y continuo. Por esta razón, Microsoft invirtió en un motor de orquestación que aplica revisiones y actualizaciones sin problemas en toda la infraestructura.

Gracias a las asociaciones con los asociados de OEM de Azure Stack Hub, Microsoft amplía la misma posición de seguridad a componentes específicos de OEM. Como, por ejemplo, el host de ciclo de vida de hardware y el software que se ejecuta sobre él. Esta asociación garantiza que Azure Stack Hub tenga una posición de seguridad uniforme y sólida en toda la infraestructura. A su vez, los clientes pueden crear y proteger sus cargas de trabajo de aplicaciones.

**Uso de entidades de servicio a través de PowerShell, la CLI y Azure Portal**

Para proporcionar acceso a los recursos a un script o una aplicación, configure una identidad para la aplicación y autentíquela con sus propias credenciales. Esta identidad se conoce como entidad de servicio y le permite:

- Asignar permisos a la identidad de aplicación que sean diferentes a los suyos propios y se restrinjan, precisamente, a las necesidades de la aplicación.
- Usar un certificado para la autenticación al ejecutar un script desatendido. 

Para obtener más información sobre la creación de la entidad de servicio y el uso de un certificado para las credenciales, consulte [Uso de una identidad de aplicación para acceder a recursos](/azure-stack/operator/azure-stack-create-service-principals).

## <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón 

- Mi organización usa un enfoque DevOps, o tiene uno planeado en un futuro cercano.
- Quiero implementar prácticas de CI/CD en mi implementación de Azure Stack Hub y la nube pública.
- Quiero consolidar la canalización de CI/CD en entornos locales y de nube.
- Quiero la posibilidad de desarrollar aplicaciones sin problemas mediante servicios locales y en la nube.
- Quiero aprovechar habilidades de desarrollo coherentes en aplicaciones locales y en la nube.
- Uso Azure, pero tengo desarrolladores que trabajan en una nube de Azure Stack Hub local.
- Mis aplicaciones locales experimentan picos de demanda durante fluctuaciones estacionales, cíclicas o imprevisibles.
- Tengo componentes locales y deseo usar la nube para escalarlos sin problemas.
- Deseo escalabilidad a la nube, pero quiero que mi aplicación se ejecute localmente tanto como sea posible.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas presentados en este artículo:
- Vea [Dynamically scale apps between data centers and public cloud](https://www.youtube.com/watch?v=2lw8zOpJTn0) para obtener información general de cómo se usa este patrón.
- Consulte [Consideraciones sobre el diseño de aplicaciones híbridas](overview-app-design-considerations.md) para más información sobre los procedimientos recomendados y responder a preguntas adicionales.
- Este patrón usa la familia de productos de Azure Stack, incluido Azure Stack Hub. Consulte la información relativa a la [familia de productos y soluciones de Azure Stack](/azure-stack) para más información sobre toda la gama de productos y soluciones.

Cuando esté listo para probar la solución de ejemplo, continúe con la [guía de implementación de soluciones de escalado entre nubes (datos locales)](solution-deployment-guide-cross-cloud-scaling-onprem-data.md). La guía de implementación proporciona instrucciones paso a paso para implementar y probar sus componentes.