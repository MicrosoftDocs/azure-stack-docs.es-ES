---
title: Implementación de una red de la cadena de bloques Ethereum en Azure Stack Hub
description: Tutorial en el que se usan plantillas de soluciones personalizadas para implementar y configurar una red de la cadena de bloques de Ethereum del consorcio en Azure Stack Hub.
author: PatAltimore
ms.author: patricka
ms.date: 10/07/2020
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 10/07/2020
ms.openlocfilehash: f409a2629243c0eb1cfe15ad857aa3e229832bb5
ms.sourcegitcommit: 7b189e5317b8fe5f8ad825565da3607a39a1b899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94493710"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack-hub"></a>Implementación de una red de la cadena de bloques Ethereum en Azure Stack Hub

La plantilla de la solución Ethereum está diseñada para facilitar y agilizar la implementación y configuración de una red de cadena de bloques del consorcio Ethereum con unos conocimientos mínimos de Azure y Ethereum.

Con un conjunto de entradas de usuario y una implementación con un solo clic a través del portal de inquilino Azure Stack Hub, cada miembro puede aprovisionar su consumo de red. La superficie de la red de cada miembro consta de tres cosas:

1. Un conjunto de nodos de transacción de carga equilibrada con los que una aplicación o un usuario pueden interactuar para enviar transacciones.
2. Un conjunto de nodos de minería para registrar transacciones.
3. Una aplicación virtual de red (NVA).

Un paso posterior de la conexión conecta las aplicaciones virtuales de red para crear una red de cadena de bloques con varios miembros totalmente configurada.

Para realizar la configuración:

- Elija una arquitectura de implementación.
- Implemente una red independiente de miembros del consorcio o de coordinadores del consorcio.

## <a name="prerequisites"></a>Prerrequisitos

Descargue los últimos elementos de [Marketplace](../operator/azure-stack-download-azure-marketplace-item.md):

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- Script personalizado para Linux 2.0
- Extensión de la secuencia de comandos personalizada para Windows

Para más información acerca de los escenarios de la cadena de bloques, consulte [Plantilla de solución del consorcio de prueba de autoridad de Ethereum](/azure/blockchain/templates/ethereum-poa-deployment).

## <a name="deployment-architecture"></a>Arquitectura de implementación

Esta plantilla de solución puede implementar una red del consorcio Ethereum con uno o varios miembros. La red virtual se conecta en una topología de cadena mediante una aplicación virtual de red y los recurso de la conexión.

La plantilla puede implementar el consorcio Ethereum para coordinador y miembro de varias formas. Estos son los que hemos probado:

- En una instancia de Azure Stack Hub de varios nodos, con Azure AD o AD FS, implemente los clientes potenciales y los miembros mediante la misma suscripción o con suscripciones diferentes.
- En una instancia de Azure Stack Hub de un solo nodo (con Azure AD), implemente los clientes potenciales y los miembros mediante la misma suscripción.

## <a name="standalone-and-consortium-leader-deployment"></a>Implementación de clientes potenciales independientes y del consorcio

La plantilla de cliente potencial del consorcio configura la huella del primer miembro en la red. 

1. Descargue la [plantilla de coordinador de GitHub](https://aka.ms/aa6z619).
1. En el portal de inquilinos de Azure Stack Hub, seleccione **+ Crear un recurso > Implementación de plantillas** para realizar la implementación desde una plantilla personalizada.
1. Seleccione **Cree su propia plantilla en el editor** para editar la nueva plantilla personalizada.
1. En el panel de edición de la derecha, copie y pegue el JSON de la plantilla de cliente potencial que descargó anteriormente.
    
    [![Página Editar plantilla con la plantilla de cliente potencial pegada](./media/azure-stack-ethereum/edit-leader-template.png)](./media/azure-stack-ethereum/edit-leader-template.png#lightbox)

1. Seleccione **Guardar**.
1. En la pestaña **Datos básicos**, rellene los siguientes valores.

    Nombre de parámetro | Descripción | Valor de ejemplo
    ---------------|-------------|-------------
    Subscription | La suscripción en la que se implementa la red del consorcio. | Suscripción de consumo
    Grupo de recursos | El grupo de recursos en el que se va a implementar la red del consorcio. | EthereumResources
    Region | La región de Azure para los recursos. | local
    Prefijo de nombre | Cadena que se usa como base para asignar nombres a los recursos implementados. Use 6 caracteres alfanuméricos como máximo. | eth
    Auth type | El método de autenticación en la máquina virtual. Los valores permitidos son contraseña o clave pública SSH. | Contraseña
    Nombre de usuario administrador | Nombre de usuario del administrador de cada una de las máquinas virtuales que implementadas. Use entre 1 y 64 caracteres. | gethadmin
    Contraseña de administrador (Tipo de autenticación = Contraseña)| La contraseña de la cuenta de administrador de cada una de las máquinas virtuales implementadas. La contraseña debe cumplir 3 de los siguientes requisitos: 1 letra mayúscula, 1 letra minúscula, 1 número y 1 carácter especial. <br />Aunque todas las máquinas virtuales tienen inicialmente la misma contraseña, puede cambiarla después del aprovisionamiento. Use entre 12 y 72 caracteres. |
    Clave SSH de administrador (tipo de autenticación = sshPublicKey) | Cadena de clave pública RSA de Secure Shell que se usa para el inicio de sesión remoto. |
    Bloque génesis | Cadena JSON que representa el bloque génesis personalizado.  La especificación de un valor para este parámetro es opcional. |
    Contraseña de la cuenta de Ethereum | La contraseña de administrador que se usa para proteger la cuenta de Ethereum. |
    Frase de contraseña de la cuenta de Ethereum | La frase de contraseña que se usa para generar la clave privada asociada con la cuenta de Ethereum. Considere una contraseña con la aleatoriedad suficiente para garantizar una clave privada segura. |
    Identificador de red de Ethereum | El identificador de red del consorcio. Use cualquier valor entre 5 y 999 999 999. | 72
    Identificador de miembro del consorcio | Identificador asociado con cada miembro de la red del consorcio. Este identificador debe ser único en la red. | 0
    Número de nodos de minería | Número de nodos de minería de datos para cada miembro del consorcio. Use un valor entre 2 y 15. | 2
    Tamaño de la máquina virtual del nodo de minería de datos | Tamaño de máquina virtual de los nodos de minería. | Standard_A1
    Tipo de cuenta de almacenamiento de minería de datos | Rendimiento del almacenamiento de los nodos de minería. | Standard_LRS
    Número de nodos TX | Número de nodos de transacción con equilibrio de carga. Use un valor entre 1 y 5. | 1
    Tamaño de la máquina virtual del nodo TX | Tamaño de máquina virtual de los nodos de la transacción. | Standard_A1
    Tipo de cuenta de almacenamiento TX | Rendimiento del almacenamiento de los nodos de transacción. | Standard_LRS
    URL base | Dirección URL base de la que se obtienen las plantillas de implementación. Use el valor predeterminado, salvo que desee personalizar las plantillas de implementación. |

1. Seleccione **Revisar + crear**. Tras una validación correcta, seleccione **Crear**.

Una implementación puede tardar 20 minutos, o más, en completarse.

Después de que se complete la implementación, examine el resumen de la implementación de **Microsoft.Template** en la sección de implementación del grupo de recursos. El resumen contiene valores de salida que se usan para combinar los miembros del consorcio.

Para comprobar la implementación del coordinador, vaya al sitio de administración del mismo. La dirección del sitio de administración se encuentra en la sección de salida de la implementación de **Microsoft.Template**.  

![Resumen de implementación de cliente potencial](./media/azure-stack-ethereum/ethereum-node-status.png)

## <a name="joining-consortium-member-deployment"></a>Combinación de la implementación de los miembros del consorcio

1. Descargue el [plantilla de miembro del consorcio de GitHub](https://aka.ms/aa6zkua).
1. En el portal de inquilinos de Azure Stack Hub, seleccione **+ Crear un recurso > Implementación de plantillas** para realizar la implementación desde una plantilla personalizada.
1. Seleccione **Cree su propia plantilla en el editor** para editar la nueva plantilla personalizada.
1. En el panel de edición de la derecha, copie y pegue el JSON de la plantilla de miembro del consorcio que descargó anteriormente.
1. Seleccione **Guardar**.
1. En la pestaña **Datos básicos**, rellene los siguientes valores.

    Nombre de parámetro | Descripción | Valor de ejemplo
    ---------------|-------------|-------------
    Subscription | La suscripción en la que se implementa la red del consorcio. | Suscripción de consumo
    Grupo de recursos | El grupo de recursos en el que se va a implementar la red del consorcio. | EthereumResources
    Region | La región de Azure para los recursos. | local
    Prefijo de nombre | Cadena que se usa como base para asignar nombres a los recursos implementados. Use 6 caracteres alfanuméricos como máximo. | eth
    Auth type | El método de autenticación en la máquina virtual. Los valores permitidos son contraseña o clave pública SSH. | Contraseña
    Nombre de usuario administrador | Nombre de usuario del administrador de cada una de las máquinas virtuales que implementadas. Use entre 1 y 64 caracteres. | gethadmin
    Contraseña de administrador (Tipo de autenticación = Contraseña)| La contraseña de la cuenta de administrador de cada una de las máquinas virtuales implementadas. La contraseña debe cumplir 3 de los siguientes requisitos: 1 letra mayúscula, 1 letra minúscula, 1 número y 1 carácter especial. <br />Aunque todas las máquinas virtuales tienen inicialmente la misma contraseña, puede cambiarla después del aprovisionamiento. Use entre 12 y 72 caracteres. |
    Clave SSH de administrador (tipo de autenticación = sshPublicKey) | Cadena de clave pública RSA de Secure Shell que se usa para el inicio de sesión remoto. |
    Bloque génesis | Cadena JSON que representa el bloque génesis personalizado. La especificación de un valor para este parámetro es opcional. |
    Contraseña de la cuenta de Ethereum | La contraseña de administrador que se usa para proteger la cuenta de Ethereum. |
    Frase de contraseña de la cuenta de Ethereum | La frase de contraseña que se usa para generar la clave privada asociada con la cuenta de Ethereum. Considere una contraseña con la aleatoriedad suficiente para garantizar una clave privada segura. |
    Identificador de miembro del consorcio | Identificador asociado con cada miembro de la red del consorcio. Este identificador debe ser único en la red. | 0
    Número de nodos de minería | Número de nodos de minería de datos para cada miembro del consorcio. Use un valor entre 2 y 15. | 2
    Tamaño de la máquina virtual del nodo de minería de datos | Tamaño de máquina virtual de los nodos de minería. | Standard_A1
    Tipo de cuenta de almacenamiento de minería de datos | Rendimiento del almacenamiento de los nodos de minería. | Standard_LRS
    Número de nodos TX | Número de nodos de transacción con equilibrio de carga. Use un valor entre 1 y 5. | 1
    Tamaño de la máquina virtual del nodo TX | Tamaño de máquina virtual de los nodos de la transacción. | Standard_A1
    Tipo de cuenta de almacenamiento TX | Rendimiento del almacenamiento de los nodos de transacción. | Standard_LRS
    Datos del consorcio | La dirección URL que apunta a los datos de configuración relevantes del consorcio proporcionados por la implementación de otro miembro. Este valor se puede encontrar en la salida de la implementación del cliente potencial. |
    Espacio de direcciones de la red virtual del miembro remoto | El espacio de direcciones de la red virtual del cliente potencial. Este valor se puede encontrar en la salida de la implementación del cliente potencial. |
    Dirección IP pública de la aplicación virtual de red del miembro remoto | La dirección IP NVA del cliente potencial. Este valor se puede encontrar en la salida de la implementación del cliente potencial. |
    Clave compartida de conexión | Un secreto establecido previamente entre los miembros de la red del consorcio que establecen una conexión de puerta de enlace. |
    URL base | Dirección URL base de la que se obtienen las plantillas de implementación. Use el valor predeterminado, salvo que desee personalizar las plantillas de implementación. |
1. Seleccione **Revisar + crear**. Tras una validación correcta, seleccione **Crear**.

Una implementación puede tardar 20 minutos, o más, en completarse.

Después de que se complete la implementación, examine el resumen de la implementación de **Microsoft.Template** en la sección de implementación del grupo de recursos. El resumen contiene valores de salida que se usan para conectar a los miembros del consorcio.

Para comprobar la implementación de un miembro, examine su sitio de administración. La dirección del sitio de administración se puede encontrar en la sección de salida de la implementación de **Microsoft.Template**.

![Resumen de la implementación de un miembro](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Como se muestra en la imagen, el estado de los nodos del miembro es **Not running** (No está en ejecución). Este estado se debe a que no se ha establecido la conexión entre el miembro y el coordinador. La conexión entre los miembros y los clientes potenciales es bidireccional. Cuando se implementa un miembro, la plantilla crea automáticamente la conexión del miembro al cliente potencial. Para crear la conexión inversa, vaya al paso siguiente.

## <a name="connect-member-and-leader"></a>Conexión de un miembro y un cliente potencial

Esta plantilla crea una conexión desde el cliente potencial a un miembro remoto. 

1. Descargue la [plantilla de coordinador y miembro de la conexión de GitHub](https://aka.ms/aa6zdyt).
1. En el portal de inquilinos de Azure Stack Hub, seleccione **+ Crear un recurso > Implementación de plantillas** para realizar la implementación desde una plantilla personalizada.
1. Seleccione **Cree su propia plantilla en el editor** para editar la nueva plantilla personalizada.
1. En el panel de edición de la derecha, copie y pegue el JSON de la plantilla de miembro del consorcio que descargó anteriormente.
1. Seleccione **Guardar**.
1. Complete la siguiente configuración.

    Nombre de parámetro | Descripción | Valor de ejemplo
    ---------------|-------------|-------------
    Subscription | La suscripción en la que se implementa la red del consorcio. | Suscripción de consumo
    Grupo de recursos | El grupo de recursos en el que se va a implementar la red del consorcio. | EthereumResources
    Region | La región de Azure para los recursos. | local
    Prefijo del nombre del miembro | Cadena que se usa como base para asignar nombres a los recursos implementados. Use 6 caracteres alfanuméricos como máximo. | eth
    Nombre de la tabla de rutas del miembro | Nombre de tabla de rutas del cliente potencial. Este valor se puede encontrar en la salida de la implementación del cliente potencial. |
    Espacio de direcciones de la red virtual del miembro remoto | Espacio de direcciones del miembro. Este valor se puede encontrar en la salida de la implementación del miembro. |
    Dirección IP pública de la aplicación virtual de red del miembro remoto | Dirección IP de la aplicación virtual de red a la que se conectará. Este valor se puede encontrar en la salida de la implementación del miembro. |
    Clave compartida de conexión | Un secreto establecido previamente entre los miembros de la red del consorcio que establecen una conexión.  |
    Dirección IP privada de la aplicación virtual de red del miembro | La dirección IP NVA del miembro. Este valor se puede encontrar en la salida de la implementación del miembro. |
    URL base | Dirección URL base de la que se obtienen las plantillas de implementación. Use el valor predeterminado, salvo que desee personalizar las plantillas de implementación. |
1. Seleccione **Revisar + crear**. Tras una validación correcta, seleccione **Crear**.

Una vez completada la implementación, la comunicación entre el cliente potencial y el miembro tarda pocos minutos en iniciarse. Para comprobar la implementación, actualice el sitio de administración del miembro. Los nodos del miembro deben estar en ejecución.

![Comprobar la implementación](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Ethereum y Azure, consulte [Tecnología y aplicaciones de Blockchain](https://azure.microsoft.com/solutions/blockchain/).
