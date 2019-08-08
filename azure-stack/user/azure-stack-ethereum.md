---
title: Implementación de una red de la cadena de bloques de Ethereum en Azure Stack | Microsoft Docs
description: Tutorial en el que se usan plantillas de soluciones personalizadas para implementar y configurar una red de la cadena de bloques de Ethereum del consorcio en Azure Stack.
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 06/03/2019
ms.topic: tutorial
ms.service: azure-stack
ms.reviewer: seyadava
ms.custom: mvc
manager: femila
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: b68a6df35b5345d3e1f00be126cdae24e87d3d0b
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842975"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack"></a>Implementación de una red de la cadena de bloques Ethereum en Azure Stack

La plantilla de la solución Ethereum está diseñada para facilitar y agilizar la implementación y configuración de una red de cadena de bloques del consorcio Ethereum con unos conocimientos mínimos de Azure y Ethereum.

Con un conjunto de entradas de usuario y una implementación con un solo clic a través del portal de inquilino Azure Stack, cada miembro puede aprovisionar su consumo de red. La superficie de la red de cada miembro consta de tres cosas:

1. Un conjunto de nodos de transacción de carga equilibrada con los que una aplicación o un usuario pueden interactuar para enviar transacciones.
2. Un conjunto de nodos de minería para registrar transacciones.
3. Una aplicación virtual de red (NVA).

Un paso posterior de la conexión conecta las aplicaciones virtuales de red para crear una red de cadena de bloques con varios miembros totalmente configurada.

Para realizar la configuración:

- Elija una arquitectura de implementación.
- Implemente una red independiente de miembros del consorcio o de coordinadores del consorcio.

## <a name="prerequisites"></a>Requisitos previos

Descargue los últimos elementos de [Marketplace](../operator/azure-stack-download-azure-marketplace-item.md):

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- Script personalizado para Linux 2.0
- Extensión de la secuencia de comandos personalizada para Windows

Para más información acerca de los escenarios de la cadena de bloques, consulte [Plantilla de solución del consorcio de prueba de autoridad de Ethereum](/azure/blockchain/templates/ethereum-poa-deployment).

## <a name="deployment-architecture"></a>Arquitectura de implementación

Esta plantilla de solución puede implementar una red del consorcio Ethereum con uno o varios miembros. La red virtual se conecta en una topología de cadena mediante una aplicación virtual de red y los recurso de la conexión.

## <a name="deployment-use-cases"></a>Casos de uso de la implementación

La plantilla puede implementar el consorcio Ethereum para coordinador y miembro de varias formas. Estos son los que hemos probado:

- En una instancia de Azure Stack de varios nodos, con Azure AD o AD FS, implemente los clientes potenciales y los miembros mediante la misma suscripción o con suscripciones diferentes.
- En una instancia de Azure Stack de un solo nodo (con Azure AD), implemente los clientes potenciales y los miembros mediante la misma suscripción.

### <a name="standalone-and-consortium-leader-deployment"></a>Implementación de clientes potenciales independientes y del consorcio

La plantilla de cliente potencial del consorcio configura la huella del primer miembro en la red. 

1. Descargue la [plantilla de coordinador de GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json).
2. En el portal de inquilinos de Azure Stack, seleccione **+ Crear un recurso > Template Deployment** (Implementación de plantillas) para realizar la implementación desde una plantilla personalizada.
3. Seleccione **Editar plantilla** para editar la nueva plantilla personalizada.
4. En el panel de edición de la derecha, copie y pegue el JSON de la plantilla de cliente potencial que descargó anteriormente.
    
    ![Editar plantilla de cliente potencial](./media/azure-stack-ethereum/edit-leader-template.png)

5. Seleccione **Guardar**.
6. Seleccione **Editar parámetros** y complete los parámetros de la plantilla de su implementación.
    
    ![Editar los parámetros de la plantilla de cliente potencial](./media/azure-stack-ethereum/edit-leader-parameters.png)

    Nombre de parámetro | DESCRIPCIÓN | Valores permitidos | Valor de ejemplo
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Cadena que se usa como base para asignar nombres a los recursos implementados. | Caracteres alfanuméricos con una longitud de 1 a 6. | eth
    AUTHTYPE. | El método de autenticación en la máquina virtual. | Contraseña o clave pública SSH. | Contraseña
    ADMINUSERNAME | Nombre de usuario del administrador de cada una de las máquinas virtuales que implementadas. | Entre 1 y 64 caracteres. | gethadmin
    ADMINPASSWORD (tipo de autenticación = contraseña)| La contraseña de la cuenta de administrador de cada una de las máquinas virtuales implementadas. La contraseña debe cumplir 3 de los siguientes requisitos: 1 letra mayúscula, 1 letra minúscula, 1 número y 1 carácter especial. <br />Aunque todas las máquinas virtuales tienen inicialmente la misma contraseña, puede cambiarla después del aprovisionamiento.|Entre 12 y 72 caracteres. |
    ADMINSSHKEY (tipo de autenticación = sshPublicKey) | La clave de shell seguro que se usa para el inicio de sesión remoto. | |
    GENESISBLOCK | Cadena JSON que representa el bloque génesis personalizado.  La especificación de un valor para este parámetro es opcional. | |
    ETHEREUMACCOUNTPSSWD | La contraseña de administrador que se usa para proteger la cuenta de Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | La frase de contraseña que se usa para generar la clave privada asociada con la cuenta de Ethereum. | |
    ETHEREUMNETWORKID | El identificador de red del consorcio. | Use cualquier valor entre 5 y 999 999 999. | 72
    CONSORTIUMMEMBERID | Identificador asociado con cada miembro de la red del consorcio.   | Este identificador debe ser único en la red. | 0
    NUMMININGNODES | Número de nodos de minería. | Entre 2 y 15. | 2
    MNNODEVMSIZE | Tamaño de máquina virtual de los nodos de minería. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Rendimiento del almacenamiento de los nodos de minería. | | Standard_LRS
    NUMTXNODES | Número de nodos de transacciones. | Entre 1 y 5. | 1
    TXNODEVMSIZE | Tamaño de máquina virtual de los nodos de la transacción. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Rendimiento del almacenamiento de los nodos de transacción. | | Standard_LRS
    BASEURL | Dirección URL base de la que se obtienen las plantillas de implementación. | Use el valor predeterminado, salvo que desee personalizar las plantillas de implementación. | 

7. Seleccione **Aceptar**.
8. En **Implementación personalizada**, especifique **Suscripción**, **Grupo de recursos** y **Ubicación del grupo de recursos**.
    
    ![Parámetros de implementación de cliente potencial](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nombre de parámetro | DESCRIPCIÓN | Valores permitidos | Valor de ejemplo
    ---------------|-------------|----------------|-------------
    Subscription | La suscripción en la que se implementa la red del consorcio. | | Suscripción de consumo
    Grupo de recursos | El grupo de recursos en el que se va a implementar la red del consorcio. | | EthereumResources
    Location | La región de Azure para el grupo de recursos. | | local

8. Seleccione **Crear**.

Una implementación puede tardar 20 minutos, o más, en completarse.

Después de que se complete la implementación, examine el resumen de la implementación de **Microsoft.Template** en la sección de implementación del grupo de recursos. El resumen contiene valores de salida que se usan para combinar los miembros del consorcio.

Para comprobar la implementación del coordinador, vaya al sitio de administración del mismo. La dirección del sitio de administración se encuentra en la sección de salida de la implementación de **Microsoft.Template**.  

![Resumen de implementación de cliente potencial](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Combinación de la implementación de los miembros del consorcio

1. Descargue el [plantilla de miembro del consorcio de GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json).
2. En el portal de inquilinos de Azure Stack, seleccione **+ Crear un recurso > Template Deployment** (Implementación de plantillas) para realizar la implementación desde una plantilla personalizada.
3. Seleccione **Editar plantilla** para editar la nueva plantilla personalizada.
4. En el panel de edición de la derecha, copie y pegue el JSON de la plantilla del cliente potencial que descargó anteriormente.
5. Seleccione **Guardar**.
6. Seleccione **Editar parámetros** y complete los parámetros de la plantilla de su implementación.

    Nombre de parámetro | DESCRIPCIÓN | Valores permitidos | Valor de ejemplo
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Cadena que se usa como base para asignar nombres a los recursos implementados. | Caracteres alfanuméricos con una longitud de 1 a 6. | eth
    AUTHTYPE | El método de autenticación en la máquina virtual. | Contraseña o clave pública SSH. | Contraseña
    ADMINUSERNAME | Nombre de usuario del administrador de cada una de las máquinas virtuales que implementadas. | Entre 1 y 64 caracteres. | gethadmin
    ADMINPASSWORD (tipo de autenticación = contraseña)| La contraseña de la cuenta de administrador de cada una de las máquinas virtuales implementadas. La contraseña debe cumplir 3 de los siguientes requisitos: 1 letra mayúscula, 1 letra minúscula, 1 número y 1 carácter especial. <br />Aunque todas las máquinas virtuales tienen inicialmente la misma contraseña, puede cambiarla después del aprovisionamiento.|Entre 12 y 72 caracteres. |
    ADMINSSHKEY (tipo de autenticación = sshPublicKey) | La clave de shell seguro que se usa para el inicio de sesión remoto. | |
    CONSORTIUMMEMBERID | Identificador asociado con cada miembro de la red del consorcio.   | Este identificador debe ser único en la red. | 0
    NUMMININGNODES | Número de nodos de minería. | Entre 2 y 15. | 2
    MNNODEVMSIZE | Tamaño de máquina virtual de los nodos de minería. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Rendimiento del almacenamiento de los nodos de minería. | | Standard_LRS
    NUMTXNODES | Número de nodos de transacciones. | Entre 1 y 5. | 1
    TXNODEVMSIZE | Tamaño de máquina virtual de los nodos de la transacción. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Rendimiento del almacenamiento de los nodos de transacción. | | Standard_LRS
    CONSORTIUMDATA | La dirección URL que apunta a los datos de configuración relevantes del consorcio proporcionados por la implementación de otro miembro. Este valor se puede encontrar en la salida de la implementación del cliente potencial. | |
    REMOTEMEMBERVNETADDRESSSPACE | La dirección IP NVA del cliente potencial. Este valor se puede encontrar en la salida de la implementación del cliente potencial. | | 
    REMOTEMEMBERNVAPUBLICIP | La dirección IP NVA del cliente potencial. Este valor se puede encontrar en la salida de la implementación del cliente potencial. | | 
    CONNECTIONSHAREDKEY | Un secreto establecido previamente entre los miembros de la red del consorcio que establecen una conexión. | |
    BASEURL | Dirección URL base de la plantilla. | Use el valor predeterminado, salvo que desee personalizar las plantillas de implementación. | 

7. Seleccione **Aceptar**.
8. En **Implementación personalizada**, especifique **Suscripción**, **Grupo de recursos** y **Ubicación del grupo de recursos**.

    Nombre de parámetro | DESCRIPCIÓN | Valores permitidos | Valor de ejemplo
    ---------------|-------------|----------------|-------------
    Subscription | La suscripción en la que se implementa la red del consorcio. | | Suscripción de consumo
    Grupo de recursos | El grupo de recursos en el que se va a implementar la red del consorcio. | | MemberResources
    Location | La región de Azure para el grupo de recursos. | | local

8. Seleccione **Crear**.

Una implementación puede tardar 20 minutos, o más, en completarse.

Después de que se complete la implementación, examine el resumen de la implementación de **Microsoft.Template** en la sección de implementación del grupo de recursos. El resumen contiene valores de salida que se usan para conectar a los miembros del consorcio.

Para comprobar la implementación de un miembro, examine su sitio de administración. La dirección del sitio de administración se puede encontrar en la sección de salida de la implementación de **Microsoft.Template**.

![Resumen de la implementación de un miembro](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Como se muestra en la imagen, el estado de los nodos del miembro es **Not running** (No está en ejecución). Este estado se debe a que no se ha establecido la conexión entre el miembro y el coordinador. La conexión entre los miembros y los clientes potenciales es bidireccional. Cuando se implementa un miembro, la plantilla crea automáticamente la conexión del miembro al cliente potencial. Para crear la conexión inversa, vaya al paso siguiente.

### <a name="connect-member-and-leader"></a>Conexión de un miembro y un cliente potencial

Esta plantilla crea una conexión desde el cliente potencial a un miembro remoto. 

1. Descargue la [plantilla de coordinador y miembro de la conexión de GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json).
2. En el portal de inquilinos de Azure Stack, seleccione **+ Crear un recurso > Template Deployment** (Implementación de plantillas) para realizar la implementación desde una plantilla personalizada.
3. Seleccione **Editar plantilla** para editar la nueva plantilla personalizada.
4. En el panel de edición de la derecha, copie y pegue el JSON de la plantilla del cliente potencial que descargó anteriormente.
    
    ![Editar plantilla de conexión](./media/azure-stack-ethereum/edit-connect-template.png)

5. Seleccione **Guardar**.
6. Seleccione **Editar parámetros** y complete los parámetros de la plantilla de su implementación.
    
    ![Editar parámetros de la plantilla de conexión](./media/azure-stack-ethereum/edit-connect-parameters.png)

    Nombre de parámetro | DESCRIPCIÓN | Valores permitidos | Valor de ejemplo
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Prefijo del nombre del cliente potencial. Este valor se puede encontrar en la salida de la implementación del cliente potencial.  | Caracteres alfanuméricos con una longitud de 1 a 6. | |
    MEMBERROUTETABLENAME | Nombre de tabla de rutas del cliente potencial. Este valor se puede encontrar en la salida de la implementación del cliente potencial. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Espacio de direcciones del miembro. Este valor se puede encontrar en la salida de la implementación del miembro. | |
    CONNECTIONSHAREDKEY | Un secreto establecido previamente entre los miembros de la red del consorcio que establecen una conexión.  | |
    REMOTEMEMBERNVAPUBLICIP | La dirección IP NVA del miembro. Este valor se puede encontrar en la salida de la implementación del miembro. | |
    MEMBERNVAPRIVATEIP | Dirección IP NVA privada del cliente potencial. Este valor se puede encontrar en la salida de la implementación del cliente potencial. | |
    LOCATION | Ubicación del entorno de Azure Stack. | | local
    BASEURL | Dirección URL base de la plantilla. | Use el valor predeterminado, salvo que desee personalizar las plantillas de implementación. | 

7. Seleccione **Aceptar**.
8. En **Implementación personalizada**, especifique **Suscripción**, **Grupo de recursos** y **Ubicación del grupo de recursos**.
    
    ![Parámetros de implementación de conexión](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nombre de parámetro | DESCRIPCIÓN | Valores permitidos | Valor de ejemplo
    ---------------|-------------|----------------|-------------
    Subscription | Suscripción del cliente potencial. | | Suscripción de consumo
    Grupo de recursos | Grupo de recursos del cliente potencial. | | EthereumResources
    Location | La región de Azure para el grupo de recursos. | | local

8. Seleccione **Crear**.

Una vez completada la implementación, la comunicación entre el cliente potencial y el miembro tarda pocos minutos en iniciarse. Para comprobar la implementación, actualice el sitio de administración del miembro. Los nodos del miembro deben estar en ejecución.

![Comprobar la implementación](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Ethereum y Azure, consulte [Tecnología y aplicaciones de Blockchain](https://azure.microsoft.com/solutions/blockchain/).
