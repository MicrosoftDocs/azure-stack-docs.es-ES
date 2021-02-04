---
title: Aplicación Windows de n niveles en Azure Stack Hub con SQL Server
description: Obtenga información sobre cómo ejecutar una aplicación Windows de n niveles en Azure Stack Hub con SQL Server.
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 5af32be701617f7ff357d4776557b2edbbdee729
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247496"
---
# <a name="windows-n-tier-application-on-azure-stack-hub-with-sql-server"></a>Aplicación Windows de n niveles en Azure Stack Hub con SQL Server

Esta arquitectura de referencia muestra cómo implementar máquinas virtuales (VM) y una red virtual configurada para una aplicación de [n niveles](/azure/architecture/guide/architecture-styles/n-tier), con SQL Server en Windows para la capa de datos. 

## <a name="architecture"></a>Architecture

La arquitectura tiene los siguientes componentes.

![En el diagrama se muestra una red virtual compuesta por seis subredes: Application Gateway, administración, capa web, capa de negocio, capa de datos y Active Directory. La subred de capa de datos usa Testigo en la nube. Hay tres equilibradores de carga.](./media/iaas-architecture-windows-sql-n-tier/image1.png)

## <a name="general"></a>General

-   **Grupo de recursos**. Los [grupos de recursos](/azure/azure-resource-manager/resource-group-overview) se utilizan para agrupar los recursos de Azure con el fin de que puedan administrarse según su duración, su propietario u otros criterios.

-   **Conjunto de disponibilidad.** Un conjunto de disponibilidad es otra configuración de centro de datos para proporcionar redundancia y disponibilidad de máquina virtual. Esta configuración dentro de una marca de Azure Stack Hub garantiza que, durante un evento de mantenimiento planeado o no planeado, hay al menos una máquina virtual disponible. Las máquinas virtuales se colocan en un conjunto de disponibilidad que las distribuye entre varios dominios de error (hosts de Azure Stack Hub).

## <a name="networking-and-load-balancing"></a>Redes y equilibrio de carga

-   **Red virtual y subredes**. Todas las máquinas virtuales se implementan en una red virtual que se puede dividir en subredes. Cree una subred independiente para cada nivel.

-   **Equilibrador de carga de capa 7.** Como Application Gateway todavía no está disponible en Azure Stack Hub, existen alternativas disponibles en el [Marketplace de Azure Stack Hub](../operator/azure-stack-marketplace-azure-items.md) como: [KEMP LoadMaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) o [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Equilibradores de carga.** Use [Azure Load Balancer](/azure/load-balancer/load-balancer-overview) para distribuir el tráfico de red desde el nivel web al nivel de empresa y desde el nivel de empresa a SQL Server.

-   **Grupos de seguridad de red** (NSG). Use NSG para restringir el tráfico de red dentro de la red virtual. Por ejemplo, en la arquitectura de tres niveles que se muestra aquí, el nivel de base de datos no acepta el tráfico procedente del front-end web, solo el procedente del nivel de empresa y la subred de administración.

-   **DNS**. Azure Stack Hub no proporciona su propio servicio de hospedaje de DNS, por lo que debe usar el servidor DNS en su ADDS.

**Máquinas virtuales**

-   **Grupo de disponibilidad AlwaysOn de SQL Server** Proporciona alta disponibilidad en el nivel de datos, al habilitar la replicación y la conmutación por error. Usa la tecnología de clúster de conmutación por error de Windows Server (WSFC) para la conmutación por error.

-   **Servidores de Active Directory Domain Services (AD DS)** . Los objetos de equipo del clúster de conmutación por error y sus roles en clúster asociados se crean en Active Directory Domain Services (AD DS). La configuración de servidores de AD DS en máquinas virtuales en la misma red virtual es el método preferido para unir otras máquinas virtuales a AD DS. También puede unir las máquinas virtuales a una instancia de AD DS empresarial existente conectando la red virtual a la red empresarial con la conexión VPN. Con ambos enfoques, debe cambiar el DNS de la red virtual al servidor DNS de AD DS (en la red virtual o la red empresarial existente) para resolver el FQDN del dominio de AD DS.

-   **Testigo en la nube**. Un clúster de conmutación por error requiere que más de la mitad de sus nodos se estén ejecutando, lo que se conoce como tener cuórum. Si el clúster tiene solo dos nodos, una partición de la red podría provocar que cada uno de ellos creyera que es el principal. En ese caso, se necesita un *testigo* que sea quien dilucide cuál es el principal y establezca el cuórum. Un testigo es un recurso, como por ejemplo un disco compartido, que puede actuar como dilucidador para establecer el cuórum. Un testigo en la nube es un tipo de testigo que usa Azure Blob Storage. Para más información acerca del concepto de quórum, consulte [Understanding cluster and pool quorum](/windows-server/storage/storage-spaces/understand-quorum) (Descripción del cuórum de clúster y de grupo). Para más información acerca del testigo en la nube, consulte [Implementación de un testigo en la nube en un clúster de conmutación por error](/windows-server/failover-clustering/deploy-cloud-witness). En Azure Stack Hub, el punto de conexión del testigo en la nube es distinto de Azure global. 

Puede tener el aspecto siguiente:

- Para Azure global:  
  `https://mywitness.blob.core.windows.net/`

- Para Azure Stack Hub:  
  `https://mywitness.blob.<region>.<FQDN>`

-   **JumpBox**. También se denomina [host bastión](https://en.wikipedia.org/wiki/Bastion_host). Se trata de una máquina virtual segura en la red que usan los administradores para conectarse al resto de máquinas virtuales. El JumpBox tiene un NSG que solo permite el tráfico remoto que procede de direcciones IP públicas de una lista segura. El NSG debe permitir el tráfico de escritorio remoto (RDP).

## <a name="recommendations"></a>Recomendaciones

Los requisitos pueden diferir de los de la arquitectura que se describe aquí. Use estas recomendaciones como punto de partida.

### <a name="virtual-machines"></a>Máquinas virtuales

Para recomendaciones sobre cómo configurar las máquinas virtuales, consulte [Ejecución de una máquina virtual Windows en Azure Stack Hub](iaas-architecture-vm-windows.md).

### <a name="virtual-network"></a>Virtual network

Cuando cree la red virtual, determine cuántas direcciones IP requieren los recursos de cada subred. Especifique una máscara de subred y un intervalo de direcciones de la red lo suficientemente grande para la dirección IP requerida con el uso de la notación [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). Use un espacio de direcciones que se encuentre dentro de los [bloques de direcciones IP privados](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces) estándar, que son 10.0.0.0/8, 172.16.0.0/12 y 192.168.0.0/16.

Elija un intervalo de direcciones que no se superponga con la red local, en caso de que necesite configurar una puerta de enlace entre la red virtual y la red local más adelante. Una vez creada la red virtual, no se puede cambiar el intervalo de direcciones.

Diseñe subredes teniendo en cuenta los requisitos de funcionalidad y seguridad. Todas las máquinas virtuales dentro del mismo nivel o rol deben incluirse en la misma subred, lo que puede servir como un límite de seguridad. Para más información sobre cómo diseñar las redes virtuales y las subredes, consulte [Planeamiento y diseño de Azure Virtual Networks](/azure/virtual-network/virtual-network-vnet-plan-design-arm).

### <a name="load-balancers"></a>Equilibradores de carga

No exponga las máquinas virtuales directamente a Internet; en su lugar, asigne una dirección IP privada a cada una. Los clientes se conectan mediante la dirección IP pública asociada con el equilibrador de carga de capa 7.

Defina reglas del equilibrador de carga para dirigir el tráfico de red a las máquinas virtuales. Por ejemplo, para habilitar el tráfico HTTP, asigne el puerto 80 de la configuración de front-end al puerto 80 del grupo de direcciones de back-end. Cuando un cliente envía una solicitud HTTP al puerto 80, el equilibrador de carga selecciona una dirección IP de back-end mediante un [algoritmo hash](/azure/load-balancer/concepts#limitations) que incluye la dirección IP de origen. Las solicitudes del cliente se distribuyen entre todas las máquinas virtuales del grupo de direcciones de back-end.

### <a name="network-security-groups"></a>Grupos de seguridad de red

Use reglas NSG para restringir el tráfico entre los niveles. En la arquitectura de tres niveles mostrada anteriormente, el nivel web no se comunica directamente con el nivel de base de datos. Para aplicar esta regla, el nivel de base de datos debe bloquear el tráfico entrante desde la subred del nivel Web.

1.  Deniegue todo el tráfico entrante de la red virtual. (Use la etiqueta VIRTUAL_NETWORK en la regla).

2.  Permita el tráfico entrante de la subred del nivel Business.

3.  Permita el tráfico entrante de la propia subred del nivel de la base de datos. Esta regla permite la comunicación entre las máquinas virtuales de la base de datos, lo cual es necesario para la replicación y la conmutación por error de esta.

4.  Permita el tráfico RDP (puerto 3389) desde la subred de JumpBox. Esta regla permite a los administradores conectarse al nivel de base de datos desde JumpBox.

Cree las reglas 2 a 4 con una prioridad más alta que la primera regla para que puedan invalidarla.

## <a name="sql-server-always-on-availability-groups"></a>Grupos de disponibilidad AlwaysOn de SQL Server

Se recomiendan los [grupos de disponibilidad AlwaysOn](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true) para alta disponibilidad de SQL Server. Antes de Windows Server 2016, los grupos de disponibilidad AlwaysOn requerían un controlador de dominio y todos los nodos del grupo de disponibilidad debían estar en el mismo dominio de AD.

En el caso de alta disponibilidad del nivel de máquina virtual, todas las máquinas virtuales de SQL deben estar en un conjunto de disponibilidad.

Otros niveles se conectan a la base de datos a través de una [escucha de grupo de disponibilidad](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover?view=sql-server-ver15&preserve-view=true). La escucha permite a un cliente SQL conectarse sin conocer el nombre de la instancia física de SQL Server. Las máquinas virtuales que acceden a la base de datos deben estar unidas al dominio. El cliente (en este caso, otro nivel) utiliza DNS para resolver el nombre de la red virtual de la escucha en direcciones IP.

Configure el grupo de disponibilidad AlwaysOn de SQL Server como sigue:

1.  Cree un clúster de clústeres de conmutación por error de Windows Server (WSFC), un grupo de disponibilidad AlwaysOn de SQL Server y una réplica principal. Para más información, consulte [Introducción a Grupos de disponibilidad AlwaysOn](/sql/database-engine/availability-groups/windows/getting-started-with-always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true).

2.  Cree un equilibrador de carga interno con una dirección IP privada estática.

3.  Cree una escucha de grupo de disponibilidad y asigne el nombre DNS de la escucha a la dirección IP del equilibrador de carga interno.

4.  Cree una regla del equilibrador de carga para el puerto de escucha de SQL Server (puerto TCP 1433 de forma predeterminada). La regla del equilibrador de carga debe habilitar la *IP flotante*, también denominada Direct Server Return. Esto causa que la máquina virtual responda directamente al cliente, lo que permite establecer una conexión directa con la réplica principal.

> [!NOTE]
> Cuando la IP flotante está habilitada, el número de puerto de front-end debe ser el mismo que el número de puerto de back-end en la regla del equilibrador de carga.

Cuando un cliente SQL intenta conectarse, el equilibrador de carga enruta la solicitud de conexión a la réplica principal. Si se produce una conmutación por error a otra réplica, el equilibrador de carga enruta automáticamente las nuevas solicitudes a una nueva réplica principal. Para más información, consulte [Configuración de un equilibrador de carga para Grupos de disponibilidad AlwaysOn de SQL Server](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

Durante una conmutación por error, se cierran las conexiones de cliente existentes. Una vez completada la conmutación por error, las conexiones nuevas se enrutarán a la nueva réplica principal.

Si la aplicación realiza más lecturas que escrituras, puede descargar algunas de las consultas de solo lectura en una réplica secundaria. Consulte [Usar un agente de escucha para conectarse a una réplica secundaria de solo lectura (enrutamiento de solo lectura)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover?view=sql-server-ver15&preserve-view=true#ConnectToSecondary).

Para probar la implementación, [fuerce una conmutación por error manual](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-ver15&preserve-view=true) del grupo de disponibilidad.

Si necesita optimizar el rendimiento de SQL, consulte también el artículo [Procedimientos recomendados de SQL Server para optimizar el rendimiento en Azure Stack Hub](./azure-stack-sql-server-vm-considerations.md).

**JumpBox**

No permita el acceso mediante RDP desde la red pública de Internet a las máquinas virtuales que ejecutan la carga de trabajo de la aplicación. En su lugar, todo el acceso RDP a estas máquinas virtuales se debe realizar a través de JumpBox. Un administrador inicia sesión en JumpBox y, después, en la otra máquina virtual desde JumpBox. JumpBox permite el tráfico RDP desde Internet, pero solo desde direcciones IP conocidas y seguras.

JumpBox tiene unos requisitos de rendimiento mínimos, por lo que puede seleccionar un pequeño tamaño de máquina virtual. Cree una [dirección IP pública](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) para JumpBox. Coloque JumpBox en la misma red virtual que las demás máquinas virtuales, pero en una subred de administración independiente.

Para proteger JumpBox, agregue una regla de grupo de seguridad de red que permita las conexiones RDP solo desde un conjunto seguro de direcciones IP públicas. Configure el NSG para las demás subredes, a fin de permitir el tráfico RDP de la subred de administración.

## <a name="scalability-considerations"></a>Consideraciones sobre escalabilidad

### <a name="scale-sets"></a>Conjuntos de escalado

Para los niveles web y de empresa, considere la posibilidad de usar [conjuntos de escalado de máquinas virtuales](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview), en lugar de implementar máquinas virtuales independientes. Un conjunto de escalado permite implementar y administrar de manera sencilla un conjunto de máquinas virtuales idénticas. Considere la posibilidad de usar conjuntos de escalado si necesita escalar horizontalmente las máquinas virtuales de manera rápida.

Hay dos maneras básicas de configurar máquinas virtuales implementadas en un conjunto de escalado:

-   Use extensiones para configurar la máquina virtual después de implementarla. Con este método, las nuevas instancias de máquina virtual pueden tardar más en iniciarse que una máquina virtual sin extensiones.

-   Implemente un [disco administrado](./azure-stack-managed-disk-considerations.md) con una imagen de disco personalizada. Esta opción puede ser más rápida de implementar. Sin embargo, requiere que la imagen esté actualizada.

Para más información, consulte [Consideraciones de diseño para conjuntos de escalado](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview). Esta consideración de diseño se cumple principalmente para Azure Stack Hub, aunque hay algunos puntos en los que debe poner atención:

-   Los conjuntos de escalado de máquinas virtuales de Azure Stack Hub no admiten el sobreaprovisionamiento ni las actualizaciones graduales.

-   No es posible escalar automáticamente los conjuntos de escalado de máquinas virtuales en Azure Stack Hub.

-   Se recomienda usar discos administrados en Azure Stack Hub en lugar de discos no administrados para el conjunto de escalado de máquinas virtuales.

-   Actualmente, hay un límite de 700 máquinas virtuales en Azure Stack Hub, que representa todas las máquinas virtuales de la infraestructura de Azure Stack Hub, las máquinas virtuales individuales y las instancias de conjunto de escalado.

## <a name="subscription-limits"></a>Límites de suscripción

Cada suscripción de inquilino de Azure Stack Hub tiene límites predeterminados, incluido un número máximo de máquinas virtuales por región que configura el operador de Azure Stack Hub. Para más información, consulte [Introducción a los servicios, planes, ofertas y suscripciones de Azure Stack Hub](../operator/service-plan-offer-subscription-overview.md). Consulte también [Tipos de cuota en Azure Stack Hub](../operator/azure-stack-quota-types.md).

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Las redes virtuales son un límite de aislamiento del tráfico de Azure. De manera predeterminada, las máquinas virtuales de una red virtual no se pueden comunicar directamente con las máquinas virtuales de otra red virtual.

**NSG**. Use los [grupos de seguridad de red](/azure/virtual-network/virtual-networks-nsg) para restringir el tráfico desde y hacia Internet. Para más información, consulte [Servicios en la nube de Microsoft y seguridad de red](/azure/best-practices-network-security).

**DMZ**. Considere la posibilidad de agregar una aplicación virtual de red (NVA) para crear una red perimetral entre la red de Internet y la red virtual de Azure. NVA es un término genérico para una aplicación virtual que puede realizar tareas relacionadas con la red, como firewall, inspección de paquetes, auditoría y enrutamiento personalizado.

**Cifrado**. Cifre datos confidenciales en reposo y use [Key Vault en Azure Stack Hub](./azure-stack-key-vault-manage-portal.md) para administrar las claves de cifrado de la base de datos. Para más información, consulte [Configuración de la integración de Azure Key Vault para SQL Server en máquinas virtuales de Azure](/azure/azure-sql/virtual-machines/windows/azure-key-vault-integration-configure). También se recomienda almacenar los secretos de aplicación como, por ejemplo, las cadenas de conexión de base de datos, en Key Vault.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](/azure/architecture/patterns).
