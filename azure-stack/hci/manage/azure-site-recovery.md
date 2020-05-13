---
title: Protección de máquinas virtuales de Azure Stack HCI mediante Azure Site Recovery
description: Utilice Windows Admin Center para proteger las máquinas virtuales de Azure Stack HCI con Azure Site Recovery.
ms.topic: article
author: davannaw-msft
ms.author: dawhite
ms.date: 04/30/2020
ms.localizationpriority: low
ms.openlocfilehash: 01b6f16b3812b5f11f95d9d11f6563a1631fd690
ms.sourcegitcommit: 21cdab346fc242b8848a04a124bc16c382ebc6f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82784001"
---
# <a name="protect-azure-stack-hci-vms-using-azure-site-recovery"></a>Protección de máquinas virtuales de Azure Stack HCI mediante Azure Site Recovery

>Se aplica a: Windows Server 2019

Windows Admin Center simplifica el proceso de replicación de máquinas virtuales en los servidores o clústeres, lo que le permite aprovechar Azure desde su propio centro de datos. Para automatizar la configuración, conecte Windows Admin Center a Azure.

En este artículo se muestra cómo usar Azure Site Recovery para configurar las opciones de replicación y crear un plan de recuperación en Azure Portal. La finalización de estas tareas permite a Windows Admin Center iniciar la replicación para la protección de las máquinas virtuales. Azure Site Recovery también protege los servidores físicos y los nodos de clúster.

Para más información, consulte [Connecting Windows Server to Azure hybrid services](/windows-server/manage/windows-admin-center/azure/) (Conexión de Windows Server a los servicios híbridos de Azure).

## <a name="how-azure-site-recovery-works-with-windows-admin-center"></a>Funcionamiento de Azure Site Recovery con Windows Admin Center
*Azure Site Recovery* es un servicio de Azure que replica cargas de trabajo que se ejecutan en máquinas virtuales para que la infraestructura crítica para la empresa esté protegida ante un desastre. Para más información, consulte [Acerca de Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Azure Site Recovery consta de dos componentes: *replicación* y *conmutación por error*. La parte de replicación protege las máquinas virtuales ante desastres mediante la replicación del disco duro virtual de la máquina virtual de destino en una cuenta de almacenamiento de Azure. Después, puede conmutar por error las máquinas virtuales y ejecutarlas en Azure en caso de que se produzca un desastre. También puede realizar una conmutación por error de prueba sin que afecte a las máquinas virtuales principales para probar el proceso de recuperación en Azure.

Solo con la finalización de la configuración del componente de replicación es suficiente para proteger las máquinas virtuales de un desastre. Sin embargo, no puede iniciar las máquinas virtuales en Azure hasta que configure la etapa de la conmutación por error del proceso.

Puede configurar la conmutación por error cuando desee conmutar por error en una máquina virtual de Azure. No es necesario que lo haga durante la configuración inicial. Si el servidor host deja de funcionar, puede configurar el componente de conmutación por error en ese momento y acceder a las cargas de trabajo de la máquina virtual protegida. Sin embargo, se recomienda configurar las opciones relacionadas con la conmutación por error antes de un desastre.

## <a name="prerequisites-and-planning"></a>Requisitos previos y planeamiento
Se necesita lo siguiente para completar los pasos descritos en este artículo:

- Los servidores de destino que hospedan las máquinas virtuales que desea proteger deben tener acceso a Internet para replicarse en Azure.
- Una conexión desde Windows Admin Center a Azure. Para más información, consulte [Configuración de la integración con Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).
- Revise la herramienta de planeamiento de capacidad para evaluar los requisitos para una replicación y conmutación por error correctas. Para más información, consulte [Información sobre Azure Site Recovery Deployment Planner para la recuperación ante desastres de Hyper-V en Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-capacity).

## <a name="step-1-set-up-vm-protection-on-your-target-host"></a>Paso 1: Configuración de la protección de máquinas virtuales en el host de destino
Realice los pasos siguientes una vez por cada servidor host o clúster que contenga las máquinas virtuales a las que desea destinar la protección:
1. En Windows Admin Center, en la página **Todas las conexiones**, conéctese al servidor o clúster que hospeda las máquinas virtuales que desea proteger.
1. En **Herramientas**, seleccione **Máquinas virtuales** y, a continuación, seleccione la pestaña **Inventario**.
1. Seleccione cualquier máquina virtual (no es necesario que sea la máquina virtual que desea proteger).
1. Expanda el submenú **Más** y, después, seleccione **Configuración de la protección de máquinas virtuales**.

    :::image type="content" source="media/azure-site-recovery/set-up-vm-protection.png" alt-text="Opción del submenú Configuración de la protección de máquinas virtuales en Windows Admin Center":::.

1. Inicie sesión en la cuenta de Azure.
1. En la página Configuración del host con Azure Site Recovery, escriba la información necesaria y, a continuación, seleccione **Configurar**:

   - **Subscription** (Suscripción): Suscripción de Azure que desea utilizar para la replicación de máquinas virtuales en este host.
   - **Grupos de recursos:** Un nombre nuevo de grupo de recursos.
   - **Almacén de Recovery Services**: Un nombre para el almacén de Azure Site Recovery para las máquinas virtuales protegidas en este host.  
   - **Ubicación:** La región de Azure en la que se deben crear los recursos de Azure Site Recovery.

    :::image type="content" source="media/azure-site-recovery/set-up-host-with-asr.png" alt-text="La página Configuración del host con Azure Site Recovery en Windows Admin Center":::.

1. Espere hasta que vea la notificación: **Configuración completada de Site Recovery**.
 
Este proceso puede tardar hasta 10 minutos. Para ver el progreso, vaya a **Notificaciones** (el icono de campana en la parte superior derecha de Windows Admin Center).

>[!NOTE]
> Este proceso instala automáticamente el agente de Azure Site Recovery en el servidor o en los nodos de destino (si está configurando un clúster) y crea un **grupo de recursos** con la **cuenta de almacenamiento** y el **almacén** especificados, en la **ubicación** indicada. También registra el host de destino con el servicio Azure Site Recovery y configura una directiva de replicación predeterminada.

## <a name="step-2-select-vms-to-protect"></a>Paso 2: Selección de las máquinas virtuales que se van a proteger
Realice los pasos siguientes para proteger las máquinas virtuales:
1. En Windows Admin Center, vuelva al servidor o clúster que configuró en la tarea anterior.
1. En **Herramientas**, seleccione **Máquinas virtuales** y, a continuación, seleccione la pestaña **Inventario**.
1. Seleccione la máquina virtual que desea proteger, expanda el submenú **Más** y, a continuación, seleccione **Proteger VM**.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting.png" alt-text="La opción de configuración Proteger VM en Windows Admin Center":::.

1. Revise los requisitos de capacidad para proteger la máquina virtual. Para más información, consulte [Planeamiento de la capacidad para la recuperación ante desastres de máquinas virtuales de Hyper-V](https://docs.microsoft.com/azure/site-recovery/site-recovery-capacity-planner).

    Si desea usar una cuenta de almacenamiento premium, cree una en Azure Portal. Para más información, consulte la sección **SSD Premium** de [¿Qué tipos de disco están disponibles en Azure?](https://docs.microsoft.com/azure/storage/common/storage-premium-storage) La opción **Crear nuevo** que se proporciona en Windows Admin Center crea una cuenta de almacenamiento estándar.

1. Escriba el nombre de la **cuenta de almacenamiento** que se usará para la replicación de esta máquina virtual y, a continuación, seleccione **Proteger VM** para permitir la replicación de la máquina virtual.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting-asr.png" alt-text="Definición de la cuenta de almacenamiento de Azure Site Recovery para proteger una máquina virtual en Windows Admin Center":::.

    Azure Site Recovery inicia el proceso de replicación. La máquina virtual está protegida cuando el valor de la columna **Protegida** de la cuadrícula **Inventario de máquinas virtuales** cambia a **Sí**. Este proceso puede tardar varios minutos.  

## <a name="step-3-configure-and-run-a-test-failover-in-the-azure-portal"></a>Paso 3: Configuración y ejecución de una conmutación por error de prueba en Azure Portal
No es necesario completar este paso antes de iniciar la replicación de la máquina virtual. La máquina virtual está protegida solo con la replicación. Sin embargo, se recomienda configurar las opciones de conmutación por error cuando configure Azure Site Recovery.
 
Realice los pasos siguientes para preparar la conmutación por error en una máquina virtual de Azure:
1. Configure una red virtual de Azure a la que se pueda conectar la máquina virtual conmutada por error. Para más información, consulte [Configuración de la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-prepare-azure).

    >[!NOTE]
    > Windows Admin Center completa automáticamente los pasos de este recurso. Solo tiene que configurar la red de Azure.

1. Ejecute una conmutación por error de prueba. Para más información, consulte [Ejecución de un simulacro de recuperación ante desastres en Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-test-failover).

## <a name="step-4-create-recovery-plans"></a>Paso 4: Creación de planes de recuperación
Los planes de recuperación de Azure Site Recovery permiten la conmutación por error y la recuperación de toda la colección de máquinas virtuales de una aplicación. Es posible recuperar máquinas virtuales protegidas de forma individual. También puede agregar las máquinas virtuales de una aplicación al plan de recuperación. Después, puede conmutar por error toda la aplicación mediante el plan de recuperación. También puede usar la característica de conmutación por error de prueba de un plan de recuperación para probar la recuperación de la aplicación.

Los planes de recuperación permiten agrupar las máquinas virtuales, secuenciar el orden en que deben iniciarse durante una conmutación por error y automatizar los pasos de recuperación adicionales. Una vez que haya protegido las máquinas virtuales, puede ir al almacén de Azure Site Recovery en Azure Portal para crear planes de recuperación para ellas. Para más información, consulte [Creación y personalización de los planes de recuperación](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).

## <a name="step-5-monitor-replicated-vms-in-azure"></a>Paso 5: Supervisión de máquinas virtuales replicadas en Azure
Para comprobar que no hay errores en el proceso de registro del servidor, vaya a **Azure Portal**, seleccione **Todos los recursos**, **Almacén de Recovery Services**, **Trabajos** y, finalmente, seleccione **Trabajos de Site Recovery**. El nombre del **Almacén de Recovery Services** es el que especificó en el paso 6 de la primera tarea de este artículo.

Para supervisar la replicación de la máquina virtual, vaya al **almacén de Recovery Services** y, a continuación, a **Elementos replicados**.

Para ver todos los servidores registrados en el almacén, vaya a **Almacén de Recovery Services**, **Infraestructura de Site Recovery** y, después, a **Hosts de Hyper-V** (en la sección de sitios de Hyper-V).

## <a name="known-issue"></a>Problema conocido ##
Cuando registra Azure Site Recovery con un clúster, si un nodo no puede instalar Azure Site Recovery o no puede registrar este servicio, las máquinas virtuales no se protegerán. Para comprobar que todos los nodos del clúster están registrados en Azure Portal, vaya al **almacén de Recovery Services**, **Trabajos** y, finalmente, a **Trabajos de Site Recovery**.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Azure Site Recovery, consulte también:

- [Preguntas generales acerca de Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-faq)
