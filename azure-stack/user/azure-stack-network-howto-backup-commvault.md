---
title: Copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault
description: Aprenda a hacer una copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: 390c6fdb3268dee90b0928b5a280d60c08c1e7fa
ms.sourcegitcommit: 278aaeca069213a98b90751253f6b15423634849
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742495"
---
# <a name="back-up-your-vm-on-azure-stack-hub-with-commvault"></a>Copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault

## <a name="overview-of-backing-up-a-vm-with-commvault"></a>Introducción a la copia de seguridad de una máquina virtual con Commvault

Este artículo le guía por la configuración de Commvault Live Sync para actualizar una máquina virtual de recuperación situada en una unidad de escalado de Azure Stack Hub independiente. En este artículo se detalla cómo configurar una solución de asociado común para proteger y recuperar los datos y el estado del sistema de las máquinas virtuales implementadas en Azure Stack Hub.

En el diagrama siguiente se muestra la solución general al usar Commvault para hacer copias de seguridad de las máquinas virtuales.

![](./media/azure-stack-network-howto-backup-commvault/bcdr-commvault-overall-arc.png)

En este artículo aprenderá a:

1. Crear una máquina virtual que ejecute el software Commvault en una instancia de Azure Stack Hub de origen.

2. Crear una cuenta de almacenamiento en una ubicación secundaria. En el artículo se supone que va a crear un contenedor de blobs en una cuenta de almacenamiento de una instancia de Azure Stack Hub independiente (el destino) y que desde la instancia de Azure Stack Hub de origen se puede acceder a la instancia de Azure Stack Hub de destino.

3. Configure Commvault en la instancia de Azure Stack Hub de origen y agregue las máquinas virtuales de esta instancia al grupo de máquinas virtuales.

4. Configurar LifeSync de Commvault.

También puede descargar y ofrecer imágenes de máquina virtual de asociados compatibles para proteger las máquinas virtuales de Azure Stack Hub en una nube de Azure o en otra instancia de Azure Stack Hub. En este artículo se describe la protección de máquinas virtuales con Commvault Live Sync.

La topología de este enfoque tendrá el siguiente aspecto:

![](./media/azure-stack-network-howto-backup-commvault/backup-vm-commvault-diagram.svg)

## <a name="create-the-commvault-vm-form-the-commvault-marketplace-item"></a>Creación del formato de máquina virtual Commvault en el elemento Commvault de Marketplace

1. Abra el portal de usuarios de Azure Stack Hub.

2. Seleccione **Create a resource** > **Compute** > **Commvault** (Crear un recurso > Proceso > Commvault).

    > [!Note]  
    > Si Commvault no está disponible, póngase en contacto con su operador de nube.

    ![Creación de una máquina virtual](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-01.png)

3. Configure las opciones básicas en **Create virtual machine, 1 Basics** (Crear máquina virtual, 1 Conceptos básicos):

    a. Escriba un **nombre**.

    b. Seleccione **Standard HHD** (HHD estándar).
    
    c. Especifique un **nombre de usuario**.
    
    d. Escriba una **contraseña**.
    
    e. Confirme la contraseña.
    
    f. Seleccione una **suscripción** para la copia de seguridad.
    
    g. Seleccione un **grupo de recursos**.
    
    h. Seleccione la **ubicación** de Azure Stack Hub. Si usa un ASDK, seleccione **local**.
    
    i. Seleccione **Aceptar**.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-02.png)

4. Elija el tamaño de la máquina virtual de Commvault. El tamaño de la máquina virtual para la copia de seguridad debe ser de al menos 10 GB de RAM y 100 GB de almacenamiento.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-03.png).

5. Elija la configuración de la máquina virtual de Commvault.

    a. Establezca la disponibilidad en **None** (Ninguno).
    
    b. Seleccione **Yes** (Sí) para usar discos administrados.
    
    c. Seleccione la red virtual predeterminada para la **red virtual**.
    
    d. Seleccione la **subred** predeterminada.
    
    e. Seleccione la **dirección IP pública** predeterminada.
    
    f. Deje la máquina virtual en el grupo de seguridad de red **Basic** (Básico).
    
    g. Abra los puertos HTTP (80), HTTPS (443), SSH (22) y RDP (3389).
    
    h. Seleccione **No extensions** (Sin extensiones).
    
    i. Seleccione **Enabled** (Habilitado) para **Boot diagnostics** (Diagnósticos de arranque).
    
    j. Deje **Guest OS diagnostics** (Diagnósticos del SO invitado) establecido en **Disabled** (Deshabilitado).
    
    k. Deje el valor predeterminado de **Diagnostics storage account** (Cuenta de almacenamiento de diagnóstico).
    
    l. Seleccione **Aceptar**.

6. Revise el resumen de la máquina virtual de Commvault después de que haya superado la validación. Seleccione **Aceptar**.

## <a name="get-your-service-principal"></a>Obtención de la entidad de servicio

Tendrá que saber si el administrador de identidades es Azure AD o AD DFS. En la tabla siguiente se muestra la información necesaria para configurar Commvault en una instancia de Azure Stack Hub.

| Elemento | Descripción | Source |
|--------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Dirección URL de Azure Resource Manager | Punto de conexión de Resource Manager para Azure Stack Hub. | https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1908#the-azure-stack-hub-resource-manager-endpoint |
| Nombre de la aplicación |  |  |
| Identificador de aplicación | Id. de la aplicación de la entidad de servicio que guardó al crear dicha entidad de servicio en la sección anterior de este artículo. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |
| Id. de suscripción | El identificador de suscripción se usa para acceder a las ofertas de Azure Stack Hub. | https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-1908#subscriptions |
| Id. de inquilino (Id. de directorio) | El identificador de inquilino de su instancia de Azure Stack Hub. | https://docs.microsoft.com/azure-stack/operator/azure-stack-identity-overview?view=azs-1908 |
| Contraseña de aplicación | Secreto de aplicación de la entidad de servicio que guardó al crear dicha entidad de servicio. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |

## <a name="configure-backup-using-the-commvault-console"></a>Configuración de la copia de seguridad mediante la consola de Commvault

1. Abra el cliente de RDP y conéctese a la máquina virtual de Commvault de Azure Stack Hub. Escriba sus credenciales.

2. Instale PowerShell en Azure Stack Hub y las herramientas de Azure Stack Hub en la máquina virtual de Commvault.

    a. Para obtener instrucciones sobre cómo instalar PowerShell en Azure Stack Hub, consulte [Instalación de PowerShell para Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).  
    b. Para obtener instrucciones sobre cómo instalar las herramientas de Azure Stack Hub, consulte [Descarga de herramientas de Azure Stack Hub desde GitHub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json%3Fview%3Dazs-1908&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json%3Fview%3Dazs-1908&view=azs-1908).

3. Después de que Commvault se instala en su máquina virtual, abra Commcell Console. En Inicio, seleccione **Commvault** > **Commvault Commcell Console**.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-console.png)

4. Configure los repositorios de copia de seguridad para usar el almacenamiento externo en Azure Stack Hub en Commcell Console de Commvault. En CommCell Browser, seleccione Storage Resources > Storage Pools (Recursos de almacenamiento > Grupos de almacenamiento). Haga clic con el botón derecho y seleccione **Add Storage Pool** (Agregar grupo de almacenamiento). Seleccione **Nube**.

5. Agregue el nombre del grupo de almacenamiento. Seleccione **Next** (Siguiente).

6. Seleccione **Create** > **Cloud Storage** (Crear > Almacenamiento en la nube).

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-storage-add-storage-device.png)

7. Seleccione el proveedor de servicios en la nube. En este procedimiento, vamos a usar una segunda instancia de Azure Stack Hub en otra ubicación. Seleccione Microsoft Azure Storage.

8. Seleccione la máquina virtual de Commvault como MediaAgent.

9. Escriba la información de acceso de la cuenta de almacenamiento. Puede encontrar aquí instrucciones sobre la configuración de una cuenta de Azure Storage. Información de acceso:

    -  **Host de servicio**: obtenga el nombre de la dirección URL de las propiedades del contenedor de blobs en el recurso. Por ejemplo, mi dirección URL era https:\//backuptest.blob.westus.stackpoc.com/mybackups y usé blob.westus.stackpoc.com en el host de servicio.
    
    -   **Nombre de cuenta**: use el nombre de la cuenta de almacenamiento. Puede encontrarlo en la hoja Access Keys (Claves de acceso) del recurso de almacenamiento.
    
    -   **Clave de acceso**: obtenga la clave de acceso en la hoja Access Keys (Claves de acceso) del recurso de almacenamiento.
    
    -   **Contenedor**: nombre del contenedor. En este caso, mybackups.
    
    -   **Storage Class** (Clase de almacenamiento): deje este campo como la clase de almacenamiento predeterminada del contenedor de usuario.

10. Cree un cliente de Microsoft Azure Stack Hub siguiendo las instrucciones de [Creación de un cliente de Microsoft Azure Stack Hub](https://documentation.commvault.com/commvault/v11_sp13/article?p=86495.htm)

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-ceate-client.png)

11. Seleccione las máquinas virtuales o los grupos de recursos para proteger y adjuntar una directiva de copia de seguridad.

12. Configure la programación de copia de seguridad para que coincida con los requisitos de RPO de recuperación.

13. Realice la primera copia de seguridad completa.

## <a name="configure-commvault-live-sync"></a>Configuración de Commvault Live Sync 

Hay dos opciones disponibles. Puede optar por replicar los cambios de la copia principal de las copias de seguridad o replicar los cambios de una copia secundaria en la máquina virtual de recuperación. La replicación desde un conjunto de copia de seguridad elimina el impacto de E/S de lectura en la máquina de origen.

1. Durante la configuración de Live Sync, tendrá que proporcionar los detalles tanto de la instancia de Azure Stack Hub de origen (agente de servidor virtual) como de la de destino.

2. Para conocer los pasos necesarios para configurar Commvault Live Sync, consulte [Replicación de Live Sync para Microsoft Azure Stack Hub](https://documentation.commvault.com/commvault/v11_sp13/article?p=94386.htm).

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-1.png)
 
3. Durante la configuración de Live Sync, tendrá que especificar los detalles de la instancia de Azure Stack Hub de destino y del agente de servidor virtual.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-2.png)

4. Continúe con la configuración y agregue la cuenta de almacenamiento de destino donde se hospedarán los discos de réplica, los grupos de recursos donde se colocarán las máquinas virtuales de réplica y el nombre que desea adjuntar a las máquinas virtuales de réplica.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-3.png)

5. También puede cambiar el tamaño de la máquina virtual y configurar las opciones de red. Para ello, seleccione **Configure** (Configurar) junto a cada máquina virtual.

6. Establezca la frecuencia de replicación en la instancia de Azure Stack Hub de destino.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-5.png)

7. Revise los ajustes para guardar la configuración. Se creará el entorno de recuperación y se iniciará la replicación en el intervalo elegido.


## <a name="set-up-failover-behavior-using-live-sync"></a>Configuración del comportamiento de la conmutación por error mediante Live Sync

Commvault Live Sync permite realizar conmutaciones por error de máquinas de una instancia de Azure Stack Hub a otra, así como realizar conmutaciones por recuperación para reanudar las operaciones en la instancia de Azure Stack Hub original. El flujo de trabajo se automatiza y se registra.

![](./media/azure-stack-network-howto-backup-commvault/back-up-live-sync-panel.png)

Seleccione las máquinas virtuales en las que desea realizar la conmutación por error en la instancia de Azure Stack Hub de recuperación y elija una conmutación por error planeada o no planeada. Es adecuada una conmutación por error planeada cuando hay tiempo para cerrar correctamente el entorno de producción antes de reanudar las operaciones en el sitio de recuperación. La conmutación por error planeada apaga las máquinas virtuales de producción, replica los cambios finales en el sitio de recuperación y conecta las máquinas virtuales de recuperación con los datos más recientes; además, aplica el tamaño de máquina virtual y la configuración de red especificados durante la configuración de Live Sync. Una conmutación por error no planeada intentará apagar las máquinas virtuales de producción, pero continuará si el entorno de producción no está disponible y simplemente conecta las máquinas virtuales de recuperación con el último conjunto de datos de replicación recibido aplicado a la máquina virtual y al tamaño y la configuración de la red elegida previamente. En las imágenes siguientes se muestra una conmutación por error no planeada en la que Commvault Live Sync ha conectado las máquinas virtuales de recuperación.

![](./media/azure-stack-network-howto-backup-commvault/unplanned-failover.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-2.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-3.png)

## <a name="next-steps"></a>Pasos siguientes

[Diferencias y consideraciones para las redes de Azure Stack Hub](azure-stack-network-differences.md)  