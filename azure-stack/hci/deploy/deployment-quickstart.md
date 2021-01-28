---
title: Inicio rápido para crear un clúster de Azure Stack HCI y registrarlo con Azure
description: Aprenda a implementar Azure Stack HCI, crear un clúster mediante Windows Admin Center y registrarlo con Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/22/2021
ms.openlocfilehash: 6527623ba5b42f40eb24cd6269f5d45bbccb0d25
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772243"
---
# <a name="quickstart-create-an-azure-stack-hci-cluster-and-register-it-with-azure"></a>Inicio rápido: Creación de un clúster de Azure Stack HCI y registro con Azure

> Se aplica a: Azure Stack HCI, versión 20H2

En este inicio rápido, aprenderá a implementar un clúster de Azure Stack HCI de dos servidores y un único sitio, y a registrarlo con Azure. Para implementaciones multisitio, consulte [Información general sobre clústeres extendidos](../concepts/stretched-clusters.md).

## <a name="before-you-start"></a>Antes de empezar

Antes de crear un clúster, haga lo siguiente:

* Compre dos servidores en el [Catálogo de Azure Stack HCI](https://hcicatalog.azurewebsites.net), a través del asociado de hardware de Microsoft que prefiera, con el sistema operativo Azure Stack HCI preinstalado. Revise los [requisitos del sistema](../concepts/system-requirements.md) para asegurarse de que el hardware que seleccione admitirá las cargas de trabajo que planea ejecutar en el clúster. Para una configuración sencilla, se recomienda usar un sistema con adaptadores de red de alta velocidad que usen iWARP.
* Cree una cuenta de usuario que sea miembro del grupo de administradores local en cada servidor.
* [Suscríbase a Azure](https://azure.microsoft.com/), si todavía no lo está.
* [Instale Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) en un equipo de administración y [regístrelo con Azure](../manage/register-windows-admin-center.md). Tenga en cuenta que el equipo de administración debe estar unido al mismo dominio de Active Directory en el que va a crear el clúster o a un dominio de plena confianza.
* Tome nota de los nombres de servidor, los nombres de dominio, las direcciones IP y el identificador de VLAN de la implementación.

## <a name="create-the-cluster"></a>Creación del clúster

Siga estos pasos para crear un clúster simple de dos nodos y un solo sitio. Para más información o para crear un clúster extendido, consulte [Creación de un clúster de Azure Stack HCI mediante Windows Admin Center](create-cluster.md).

1. En Windows Admin Center, en **All connections** (Todas las conexiones), haga clic en **Add** (Agregar).
1. En el panel **Add resources** (Agregar recursos), en **Clúster de Windows Server** (Windows Server cluster), seleccione **Create new** (Crear nuevo).
1. En **Choose cluster type** (Seleccionar tipo de clúster), seleccione **Azure Stack HCI**.
1. En **Select server locations** (Seleccionar ubicaciones de servidor), seleccione **All servers in one site** (Todos los servidores de un sitio).
1. Haga clic en **Crear**. Ahora aparecerá el Asistente para crear clúster. Si aparece el mensaje emergente **Credential Security Service Provider (CredSSP)** (Proveedor del servicio de seguridad de credenciales [CredSSP]), seleccione **Yes** (Sí) para habilitarlo de forma temporal. 

El Asistente para crear clúster tiene cinco secciones, cada una con varios pasos.

1. **Primeros pasos.** En esta sección, comprobará los requisitos previos, agregará servidores, se unirá a un dominio, instalará las características y actualizaciones necesarias y reiniciará los servidores. 
2. **Redes.** Esta sección del asistente permite comprobar que los adaptadores de red correctos están habilitados y deshabilita los que no se estén usando. Seleccionará adaptadores de administración, modificará una configuración de conmutador virtual y definirá la red proporcionando direcciones IP.
3. **Agrupación en clústeres.** Esta sección permite validar que los servidores tienen una configuración coherente y que son adecuados para la agrupación en clústeres, y crear el clúster real.
4. **Almacenamiento**. A continuación, limpiará y comprobará las unidades, validará el almacenamiento y habilitará Espacios de almacenamiento directo.
5. **SDN.** Puede omitir la sección 5 porque no vamos a usar redes definidas por software (SDN) para este clúster.

Si ha habilitado el protocolo CredSSP en el asistente, puede que desee deshabilitarlo en cada servidor por motivos de seguridad.

1. En Windows Admin Center, en **All connections** (Todas las conexiones), seleccione el clúster que acaba de crear.
1. En **Herramientas**, seleccione **Servidores**.
1. En el panel derecho, seleccione el primer servidor del clúster.
1. En **Información general**, seleccione **Disable CredSSP** (Deshabilitar CredSSP). Verá que el banner **CredSSP ENABLED** (CredSSP HABILITADO) de la parte superior desaparece.
1. Repita los pasos 3 y 4 para el segundo servidor del clúster.

## <a name="set-up-a-cluster-witness"></a>Configuración de un testigo del clúster

La configuración de un recurso testigo es necesaria para que si uno de los servidores del clúster se queda sin conexión, no haga que el otro nodo deje de estar disponible. En este inicio rápido, vamos a usar un recurso compartido de archivos SMB ubicado en otro servidor como testigo. Es posible que prefiera usar un testigo en la nube de Azure siempre que todos los nodos de servidor del clúster tengan una conexión a Internet confiable. Para más información sobre las opciones del testigo, consulte [Configuración de un testigo del clúster](../manage/witness.md).

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Conexiones de clúster**, seleccione el clúster.
1. En **Herramientas**, seleccione **Configuración**.
1. En el panel derecho, seleccione **Testigo**.
1. En **Tipo de testigo**, seleccione **Testigo del recurso compartido de archivos**.
1. Especifique una ruta de acceso del recurso compartido de archivos, como **\\servername.domain.com\Witness$** y proporcione las credenciales si es necesario.
1. Haga clic en **Save**(Guardar).

## <a name="register-with-azure"></a>Registrarse en Azure

Azure Stack HCI requiere una conexión a Azure y necesitará permisos de Azure Active Directory para completar el registro. Si aún no los tiene, pida a su administrador de Azure AD que le conceda los permisos o se los delegue. Para más información, consulte [Conexión de Azure Stack HCI a Azure](register-with-azure.md). Una vez registrado, el clúster se conecta automáticamente en segundo plano.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado un clúster de Azure Stack HCI y lo ha registrado con Azure. Ahora ya está listo para [crear volúmenes](../manage/create-volumes.md) y, posteriormente, [máquinas virtuales](../manage/vm.md).