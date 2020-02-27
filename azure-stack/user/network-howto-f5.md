---
title: Implementación de F5 en dos instancias de Azure Stack Hub
description: Aprenda a implementar de F5 en dos instancias de Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: f3b564bc667376361e1bfc57df0ec50e32522401
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636325"
---
# <a name="how-to-deploy-f5-across-two-azure-stack-hub-instances"></a>Implementación de F5 en dos instancias de Azure Stack Hub

Este artículo le guiará a través de la configuración de un equilibrador de carga externo en dos entornos de Azure Stack Hub. Puede usar esta configuración para administrar diferentes cargas de trabajo. En este artículo se implementará F5 como una solución de equilibrio de carga global entre dos instancias independientes de Azure Stack Hub. También implementará una aplicación web de carga equilibrada que se ejecutará en un servidor NGINX en las dos instancias. Se ejecutarán detrás de un par de aplicaciones virtuales F5 con conmutación por error y alta disponibilidad.

Puede encontrar las plantillas de Azure Resource Manager en el repositorio de GitHub [f5-azurestack-gslb](https://github.com/Mikej81/f5-azurestack-gslb).

## <a name="overview-of-load-balancing-with-f5"></a>Introducción al equilibrio de carga con F5

El hardware de F5, el equilibrador de carga, puede estar fuera de Azure Stack Hub y dentro del centro de datos que hospeda Azure Stack Hub. Azure Stack Hub no tiene ninguna funcionalidad nativa para equilibrar las cargas de trabajo en dos implementaciones de Azure Stack Hub independientes. BIG-IP Virtual Edition (VE) de F5 se ejecuta en ambas plataformas. Esta configuración admite la paridad entre las arquitecturas de Azure y de Azure Stack Hub mediante la replicación de los servicios de aplicaciones auxiliares. Puede desarrollar una aplicación en un entorno y trasladarla a otro. También puede reflejar toda la instancia de Azure Stack Hub lista para producción, incluidas las mismas configuraciones de BIG-IP, directivas y servicios de aplicación. Este enfoque elimina la necesidad de innumerables horas de refactorización y pruebas de la aplicación, y le permite empezar a escribir código.

La protección de las aplicaciones y sus datos ser un problema para los desarrolladores que trasladan aplicaciones a la nube pública. Esto no debería ser así. Puede compilar una aplicación en el entorno de Azure Stack Hub mientras un arquitecto de seguridad configura los valores necesarios en el firewall de aplicaciones web de F5. Toda la pila se puede replicar en Azure Stack Hub con la seguridad de que la aplicación estará protegida por el mismo firewall de aplicaciones web líder del sector. Al utilizar directivas y conjuntos de normas idénticos no se produce ningún tipo de vulnerabilidades de seguridad que, de otro modo, se podrían generar por el uso de diferentes firewall de aplicaciones web.

Azure Stack Hub tiene un Marketplace independiente de Azure. Solo se agregan determinados elementos. En este caso, si desea crear un grupo de recursos en cada una de las instancias de Azure Stack Hub e implementar la aplicación virtual F5 que ya está disponible. Allí verá que se necesita una dirección **IP pública** para permitir la conectividad de red entre ambas instancias de Azure Stack Hub. Básicamente, son islas y la dirección **IP pública** permitirá la comunicación entre ellas.

## <a name="prerequisites-for-big-ip-ve"></a>Requisitos previos para BIG-IP VE

-  Descargue **F5 BIG-IP VE - ALL (BYOL, dos ubicaciones de arranque)** en cada Marketplace de Azure Stack Hub. Si no están disponibles en el portal, póngase en contacto con el operador de la nube.

-  Puede encontrar la plantilla de Azure Resource Manager en el siguiente repositorio de GitHub: https://github.com/Mikej81/f5-azurestack-gslb.

## <a name="deploy-f5-big-ip-ve-on-each-instance"></a>Implementación de F5 BIG-IP VE en cada instancia

Impleméntelo en la instancias A y B de Azure Stack Hub.

1. Inicie sesión en el portal de usuarios de Azure Stack Hub.

2. Seleccione **+ Crear un recurso**.

3. Para buscar el Marketplace, escriba `F5`.

4. Seleccione **F5 BIG-IP VE - ALL (BYOL, 2 ubicaciones de arranque)** .

    ![](./media/network-howto-f5/image1.png)

5. En la parte inferior de la página siguiente, seleccione **Crear**.

    ![](./media/network-howto-f5/image2.png)

6. Cree un nuevo grupo de recursos denominado **F5-GSLB**.

7. Use los valores siguientes como ejemplo para completar la implementación:

    ![](./media/network-howto-f5/image3.png)

8. Compruebe que la implementación finaliza correctamente.

    ![](./media/network-howto-f5/image4.png)

    > [!Note]  
    > Cada implementación de BIG-IP suele tardar unos 20 minutos.

## <a name="configure-big-ip-appliances"></a>Configuración de dispositivos BIG-IP

Siga estos pasos para las instancias A y B de Azure Stack Hub.

1. Inicie sesión en el portal de usuarios de Azure Stack Hub, en la instancia A de Azure Stack Hub, para revisar los recursos que se crearon a partir de la implementación de la plantilla BIG-IP.

    ![](./media/network-howto-f5/image18.png)

2. Siga las instrucciones que aparecen en F5 sobre los [elementos de configuración de BIG-IP](https://clouddocs.f5.com/training/community/dns/html/class1/class1.html). 

3. Configure la amplia lista de direcciones IP de BIG-IP para escuchar los dos dispositivos implementados en las instancias A y B de Azure Stack Hub. Para más instrucciones, consulte [Configuración de GTM para BIG-IP](https://techdocs.f5.com/kb/en-us/products/big-ip_gtm/manuals/product/gtm-concepts-11-5-0/4.html).


4. Valide la conmutación por error de los dispositivos de BIG-IP. En un sistema de prueba, configure los servidores DNS para que usen lo siguiente:
    - Instancia A de Azure Stack Hub = Dirección IP pública `f5stack1-ext`
    - Instancia B de Azure Stack Hub = Dirección IP pública `f5stack1-ext`

5. Vaya a `www.contoso.com` y el explorador cargará la página predeterminada de NGINX.

## <a name="create-a-dns-sync-group"></a>Creación de un grupo de sincronización de DNS

1. Habilite la cuenta raíz para establecer la confianza. Siga las instrucciones que se encuentran en [Cambio de contraseñas de la cuenta de mantenimiento del sistema (11.x a 15.x)](https://support.f5.com/csp/article/K13121). Después de establecer la confianza (intercambio de certificados), deshabilite la cuenta raíz.

1. Inicie sesión en BIG-IP y cree un grupo de sincronización de DNS. Para obtener instrucciones, consulte [Creación de un grupo de sincronización de DNS de BIG-IP](https://f5-dns-automation-demo-12-1-x.readthedocs.io/en/latest/lab2/sync-group.html).

    > [!Note]  
    > Puede encontrar la dirección IP local del dispositivo de BIG-IP en el grupo de recursos **F5-GSLB**. La interfaz de red es "f5stack1-ext" y desea conectarse a una dirección IP pública o privada (en función del acceso).

    ![](./media/network-howto-f5/image5.png)
          
    ![](./media/network-howto-f5/image6.png)

1. Seleccione el nuevo grupo de recursos **F5-GSLB**, seleccione la máquina virtual **f5stack1** y, en **Configuración**, seleccione **Redes**.

## <a name="post-install-configurations"></a>Configuraciones posteriores a la instalación

Después de la instalación, tendrá que configurar los grupos de seguridad de red de Azure Stack Hub y bloquear las direcciones IP de origen.

1. Deshabilite el puerto 22 una vez establecida la confianza.

2. Cuando el sistema esté en línea, bloquee los grupos de seguridad de red de origen. El grupo de seguridad de red de administración debe estar bloqueado en el origen de administración. El grupo de seguridad de red externo (4353/TCP) debe estar bloqueado en la otra instancia para la sincronización. 443 también debe bloquearse hasta que se implementen las aplicaciones con servidores virtuales.

3. La regla GTM_DNS se ha establecido para permitir el tráfico de entrada del puerto 53 (DNS) y la resolución de BIG-IP comenzará a funcionar inmediatamente. Se crean clientes de escucha.

    ![](./media/network-howto-f5/image7.png)

4. Implemente una carga de trabajo de aplicación web básica en el entorno de Azure Stack Hub para equilibrar la carga que hay detrás de BIG-IP. Puede encontrar un ejemplo de uso del servidor NGINX en [Implementación de NGINX y NGINX Plus en Docker](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/).

    > [!Note]  
    > Implemente una instancia de NGINX en las instancias A y B de Azure Stack Hub.

5. Después de implementar NGINX en un contenedor de Docker de una máquina virtual Ubuntu dentro de cada una de las instancias de Azure Stack Hub, compruebe que puede acceder a la página web predeterminada en los servidores.

    ![](./media/network-howto-f5/image8.png)

6. Inicie sesión en la interfaz de administración del dispositivo de BIG-IP. En este ejemplo, use la dirección IP pública **f5-stack1-ext**.

    ![](./media/network-howto-f5/image9.png)

7. Publique el acceso a NGINX mediante BIG-IP.
    
    -  En esta tarea, va a configurar BIG-IP con un servidor virtual y un grupo para permitir el acceso entrante de Internet a la aplicación WordPress. En primer lugar, debe identificar la dirección IP privada de la instancia de NGINX.

8. Inicie sesión en el portal de usuarios de Azure Stack Hub. 

9. Seleccione la interfaz de red de NGINX.

    ![](./media/network-howto-f5/image10.png)

10. En la consola de BIG-IP, vaya a **Local traffic > Pools > Pool List** (Tráfico local > Grupos > Lista de grupos) y seleccione **+** . Configure el grupo con los valores de la tabla. Deje todos los demás campos con sus valores predeterminados.

    ![](./media/network-howto-f5/image11.png)
    
    | Clave | Value |
    | --- | --- |
    | Nombre | NGINX_Pool |
    | Health Monitor | HTTPS |
    | Nombre del nodo | NGINX |
    | Dirección | \<la dirección IP privada de NGINX> |
    | Puerto de servicio | 443 |

11. Seleccione **Finished** (Finalizado). Si se configura correctamente, el estado del grupo es verde.

    ![](./media/network-howto-f5/image12.png)

    Ahora debe configurar el servidor virtual. Para ello, primero debe buscar la dirección IP privada de F5 BIG-IP.

12. En la consola de BIG-IP, vaya a **Network > Self IPs** (Red > IP propias) y anote la dirección IP.

    ![](./media/network-howto-f5/image13.png)

13. Para crear un servidor virtual, vaya a **Local Traffic** > **Virtual Servers** > **Virtual Server List** (Tráfico local > Servidores virtuales > Lista de servidores virtuales) y seleccione **+** . Configure el grupo con los valores de la tabla. Deje todos los demás campos con sus valores predeterminados.

    | Clave | Value |
    | --- | --- |
    |Nombre | NGINX |
    |Dirección de destino | \<dirección IP propia de BIG-IP> |
    |Puerto de servicio | 443 |
    |Perfil SSL (cliente) | clientssl |
    |Traducción de direcciones de origen | Asignación automática |
        
    ![](./media/network-howto-f5/image14.png)

    ![](./media/network-howto-f5/image15.png)

14. Ahora ya ha completado la configuración de BIG-IP para la aplicación NGINX. Para comprobar que funciona correctamente, examine el sitio y compruebe las estadísticas de F5.

15. Abra un explorador, vaya a `https://<F5-public-VIP-IP>` y asegúrese de que muestra la página predeterminada de NGINX.

    ![](./media/network-howto-f5/image16.png)

16. Ahora, compruebe las estadísticas del servidor virtual para comprobar el flujo de tráfico. Para ello, vaya a **Statistics > Module Statistics > Local Traffic** (Estadísticas > Estadísticas del módulo > Tráfico local).

17. En **Statistics Type** (Tipo de estadísticas), seleccione **Virtual Servers** (Servidores virtuales).

    ![](./media/network-howto-f5/image17.png)


## <a name="for-more-information"></a>Para obtener más información

Estos son algunos artículos de referencia sobre el uso de F5:

- [Servicios de disponibilidad del centro de datos mediante el DNS de BIG-IP](https://clouddocs.f5.com/training/community/dns/html/class3/class3.html)
- [Implementación del sistema BIG-IP con aplicaciones HTTP](https://www.f5.com/content/dam/f5/corp/global/pdf/deployment-guides/iapp-http-dg.pdf)
- [Creación de una dirección IP amplia para GSLB](https://clouddocs.f5.com/training/community/big-iq-cloud-edition/html/class10/module2/lab1.html)

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md) 
