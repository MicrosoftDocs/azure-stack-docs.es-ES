---
title: Uso de Azure Backup para hacer una copia de seguridad de las instancias de Windows Server
description: En este artículo se proporcionan instrucciones sobre cómo usar Azure Backup a través de Windows Admin Center para realizar copias de seguridad de las instancias de Windows Server.
author: thomasmaurer
ms.author: thmaure
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: a4668e4d78a2ea3fb9f94913863d3471313034e5
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947510"
---
# <a name="use-azure-backup-to-back-up-windows-servers"></a>Uso de Azure Backup para hacer una copia de seguridad de las instancias de Windows Server

>Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Windows Admin Center simplifica la copia de seguridad de las instancias de Windows Server en Microsoft Azure. El proceso también le protege de eliminaciones accidentales o malintencionadas, daños e incluso ransomware. Para automatizar la configuración, debe conectar Windows Admin Center a Azure para que use el servicio Azure Backup.

En este artículo se muestra cómo configurar Azure Backup y crear una directiva de copia de seguridad para volúmenes de servidor y el estado del sistema de Windows desde Windows Admin Center. La guía está destinada a realizar copias de seguridad de las cargas de trabajo que se ejecutan en máquinas virtuales (VM) en el sistema operativo de Azure Stack HCI en Azure.

Para obtener más información sobre la integración de Azure con Windows Admin Center, consulte [Conexión de Windows Server a servicios híbridos de Azure](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-backup-works-with-windows-admin-center"></a>Funcionamiento de Azure Backup con Windows Admin Center
**Azure Backup** es un servicio en la nube que se puede usar para realizar copias de seguridad, proteger y restaurar datos en Azure. Reemplaza sus soluciones de copia de seguridad local o remota existente por una solución confiable, segura y rentable basada en la nube.

Para obtener más información, consulte [¿Qué es el servicio Azure Backup?](/azure/backup/backup-overview)

Azure Backup ofrece varios componentes que se descargan e implementan en el equipo o servidor adecuados, o en la nube. El componente, o agente, que se implemente depende de lo que quiera proteger. Todos los componentes de Azure Backup se pueden usar para realizar una copia de seguridad de datos en un almacén de Recovery Services en Azure, sin importar si va a proteger los datos de forma local o en Azure.

La integración de Azure Backup con Windows Admin Center es ideal para realizar copias de seguridad de volúmenes y el estado del sistema de Windows para servidores virtuales e instancias locales de Windows Server. El proceso completo realiza copias de seguridad de servidores de archivos, controladores de dominio y servidores web de IIS.

Puede acceder a Azure Backup en Windows Admin Center a través de la herramienta **Backup**. Las funcionalidades de configuración, administración y supervisión de la herramienta **Backup** permiten iniciar rápidamente copias de seguridad de servidores, realizar operaciones de copia de seguridad y restauración, y supervisar el estado de copia de seguridad general de las instancias de Windows Server.

## <a name="prerequisites"></a>Requisitos previos
Los siguientes requisitos previos son indispensables para usar Azure Backup:
- Una cuenta de Azure con al menos una suscripción activa.
- Acceso a Internet en Azure para las instancias de Windows Server de destino
- Una conexión de la puerta de enlace de Windows Admin Center a Azure.

    Para más información, consulte [Configuración de la integración con Azure](/windows-server/manage/windows-admin-center/azure/azure-integration).

## <a name="getting-started-with-azure-backup"></a>Introducción a Azure Backup
Cuando seleccione por primera vez la herramienta **Backup** en Windows Admin Center para establecer una conexión de servidor con Azure, se mostrará la página **Le damos la bienvenida a Azure Backup**. Seleccione **Set up Azure Backup** (Configurar Azure Backup) para iniciar el asistente para la instalación de Azure Backup. En las secciones siguientes se detallan los pasos del asistente.

Si Azure Backup ya está configurado para una conexión de servidor, al seleccionar la herramienta **Backup**, se abre el **panel de Backup**. Consulte la sección [Administración y supervisión](#management-and-monitoring) para obtener información acerca de las operaciones y las tareas que puede realizar desde el panel.

### <a name="step-1-log-on-to-the-microsoft-azure-portal"></a>Paso 1: inicio sesión en Microsoft Azure Portal
Inicie sesión en la cuenta de Azure.

> [!NOTE]
> Si ya ha conectado la puerta de enlace de Windows Admin Center a Azure, la sesión debería iniciarse automáticamente en el portal. Seleccione **Cerrar sesión** para iniciarla con otro usuario.

### <a name="step-2-set-up-azure-backup"></a>Paso 2: configuración de Azure Backup
Seleccione la siguiente configuración para Azure Backup:
- **Id. de suscripción:** la suscripción a Azure que quiera usar para la copia de seguridad de Windows Server en Azure. Todos los recursos de Azure, como el grupo de recursos de Azure y el almacén de Recovery Services, se crearán y asociarán a la suscripción seleccionada.
- **Almacén:** el almacén de Recovery Services es la ubicación donde se almacenarán las copias de seguridad de los servidores. Puede usar un almacén existente o Windows Admin Center creará uno nuevo.  
- **Grupos de recursos:** El grupo de recursos de Azure es un contenedor para una colección de recursos. El almacén de Recovery Services se crea o incluye en el grupo de recursos especificado. Puede usar un grupo de recursos existente o Windows Admin Center creará uno nuevo.
- **Ubicación:** región de Azure en la que se creará el almacén de Recovery Services. Le recomendamos que seleccione la región de Azure más cercana a Windows Server de la que realiza la copia de seguridad.

### <a name="step-3-select-backup-items-and-schedule"></a>Paso 3: selección de la programación y los elementos de la copia de seguridad
1. Seleccione los elementos de los que quiere realizar una copia de seguridad desde el servidor. Windows Admin Center le permite elegir una combinación de volúmenes y el estado del sistema de Windows, y proporciona un tamaño estimado de los datos que ha seleccionado para realizar copias de seguridad.

    > [!NOTE]
    > La primera copia de seguridad es una copia de seguridad completa de todos los datos seleccionados. Las copias de seguridad posteriores son incrementales y solo transfieren los cambios en los datos que se han realizado desde la copia de seguridad anterior.

1. Seleccione entre los distintos valores preestablecidos de **programaciones de copia de seguridad** para los volúmenes y el estado del sistema de Windows.

### <a name="step-4-enter-an-encryption-passphrase"></a>Paso 4: introducción de una frase de contraseña de cifrado
1. Escriba una **frase de contraseña de cifrado** que tenga un mínimo de 16 caracteres. Azure Backup protege los datos de copia de seguridad con una frase de contraseña de cifrado que crea y administra. La frase de contraseña de cifrado es necesaria para recuperar datos de Azure Backup.

    > [!NOTE]
    > Almacene la frase de contraseña en una ubicación segura remota, como otro servidor o [Azure Key Vault](/azure/key-vault/quick-create-portal). Microsoft no almacena la frase de contraseña y no puede recuperar ni restablecer la frase de contraseña si la pierde o se olvida de ella.

1. Revise todas las opciones de configuración de la página del asistente y, a continuación, seleccione **Aplicar**.

Después, Windows Admin Center realiza las operaciones siguientes:
1. Crea un grupo de recursos de Azure si todavía no existe.
1. Crea un nuevo almacén de Azure Recovery Services según se haya especificado.
1. Instala y registra el agente de Microsoft Azure Recovery Services en el almacén.
1. Crea la programación de retención y copia de seguridad según las opciones seleccionadas asociadas a Windows Server.

## <a name="management-and-monitoring"></a>Administración y supervisión
Después de configurar correctamente Azure Backup, verá el **panel de Backup** al abrir la herramienta **Backup** de una conexión de servidor existente. En el panel, puede realizar las tareas siguientes:
- **Acceder al almacén en Azure:** en la pestaña **Información general**, seleccione **Almacén de Recovery Services** para acceder al almacén en el que puede realizar muchas operaciones de administración. Para obtener más información, consulte [Supervisión y administración de almacenes de Recovery Services](/azure/backup/backup-azure-manage-windows-server).
- **Realizar una copia de seguridad ad hoc:** seleccione **Realizar copia de seguridad ahora** para realizar una copia de seguridad ad hoc. 
- **Supervisar trabajos y configurar notificaciones de alerta:** vaya a la pestaña **Trabajos** para supervisar los trabajos en curso o pasados y [configure notificaciones de alerta](/azure/backup/backup-azure-manage-windows-server#configuring-notifications-for-alerts) para recibir correos electrónicos sobre trabajos con errores y otras alertas de copia de seguridad.
- **Ver puntos de recuperación y recuperar datos:** Seleccione la pestaña **Puntos de recuperación** para ver los puntos de recuperación y seleccione **Recuperar datos** para obtener los pasos sobre cómo recuperar los datos de Azure.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, vea también:
- [Protección de máquinas virtuales de Azure Stack HCI mediante Azure Site Recovery](./azure-site-recovery.md)
