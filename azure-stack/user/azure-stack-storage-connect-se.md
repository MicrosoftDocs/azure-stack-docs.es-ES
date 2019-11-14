---
title: Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento | Microsoft Docs
description: Aprenda a conectar el Explorador de Storage a una suscripción de Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 2fa9998a584bd76c8f9c875d3a2ac082c9ca812b
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955622"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo, aprenderá a conectarse a sus suscripciones y cuentas de almacenamiento de Azure Stack mediante el [Explorador de Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer). El Explorador de Storage es una aplicación independiente que permite trabajar fácilmente con datos de almacenamiento de Azure Stack en Windows, macOS y Linux.

> [!NOTE]  
> Hay varias herramientas disponibles para mover datos hacia el almacenamiento de Azure Stack y desde este. Para más información, consulte [Herramientas de transferencia de datos de Azure Stack Storage](azure-stack-storage-transfer.md).

Si no está instalado, [descargue el Explorador de Storage](https://www.storageexplorer.com/) e instálelo.

Después de conectarse a una suscripción de Azure Stack o a una cuenta de almacenamiento, puede usar los [artículos acerca del Explorador de Azure Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para trabajar con los datos de Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Prepárese para conectarse a Azure Stack

Necesita acceso directo a Azure Stack o una conexión VPN para que el Explorador de Storage pueda acceder a la suscripción de Azure Stack. Para más información sobre cómo configurar una conexión VPN a Azure Stack, consulte [Conexión a Azure Stack con VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

> [!Note]  
> En el caso de ASDK, si se va a conectar ASDK mediante VPN, no utilice el certificado raíz (CA.cer) que se creó en el proceso de configuración de la VPN.  Este certificado tiene codificación DER y no permitirá que el Explorador de Storage recupere las suscripciones de Azure Stack. Siga estos pasos para exportar un certificado codificado en base 64 para usarlo con el Explorador de Storage.

En el caso de los sistemas integrados que están desconectados y para ASDK, se recomienda usar una entidad de certificación empresarial interna para exportar el certificado raíz en un formato de base 64 y, a continuación, importarlo en el Explorador de Azure Storage.  

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportación y posterior importación del certificado de Azure Stack

Exporte y, después, importe el certificado de Azure Stack para los sistemas integrados desconectados y para ASDK. En el caso de los sistemas integrados, el certificado está firmado públicamente y este paso no es necesario.

1. Abra `mmc.exe` en una máquina host de Azure Stack o una máquina local con una conexión VPN a Azure Stack. 

2. En **Archivo**, seleccione **Agregar o quitar complemento**. Seleccione **Certificados** en Complementos disponibles. 

3. Seleccione la **cuenta de equipo** y haga clic en **Siguiente**. Seleccione **Equipo local** y luego haga clic en **Finalizar**.

4.  En **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** busque **AzureStackSelfSignedRootCert**.

    ![Carga del certificado raíz de Azure Stack a través de mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Haga clic con el botón derecho en el certificado, seleccione **Todas las tareas** > **Exportar** y luego siga las instrucciones para exportar el certificado con **X.509 (.CER) codificado en base 64**.

    Se usará el certificado exportado en el paso siguiente.

6. Inicie el Explorador de Storage. Si ve el cuadro de diálogo **Conectar a Azure Storage** , cancélelo.

7. En el menú **Editar**, seleccione **Certificados SSL** y, después, **Importar certificados**. Use el cuadro de diálogo del selector de archivos para buscar y abrir el certificado que exportó en el paso anterior.

    Después de la importación del certificado, se le pedirá que reinicie el Explorador de Storage.

    ![Importación del certificado en el Explorador de Storage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Después de que se reinicie el Explorador de Storage, seleccione el menú **Edit** (Editar) y compruebe si está seleccionada la opción **Target Azure Stack APIs** (API de Azure Stack de destino). Si no lo está, seleccione **Target Azure Stack** (Azure Stack de destino) y reinicie el Explorador de Storage para que el cambio surta efecto. Esta configuración es necesaria para obtener compatibilidad con el entorno de Azure Stack.

    ![Comprobación de que Azure Stack de destino está activada](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Conexión a una suscripción de Azure Stack con Azure AD

Siga estos pasos para conectar el Explorador de Storage a una suscripción de Azure Stack que pertenezca a una cuenta de Azure Active Directory (Azure AD).

1. En el panel izquierdo del Explorador de Storage, seleccione **Administrar cuentas**.  
    Se muestran todas las suscripciones de Microsoft en las que inició sesión.

2. Para conectarse a la suscripción de Azure Stack, seleccione **Agregar una cuenta**.

    ![Adición de una cuenta de Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. En el cuadro de diálogo Conectar a Azure Storage, en **Entorno de Azure**, seleccione **Azure**, **Azure China 21Vianet**, **Azure Alemania**, **Azure Gobierno de EE. UU.** o **Agregar nuevo entorno** en función de la cuenta de Azure Stack que vaya a usar. Seleccione **Iniciar sesión** para iniciar sesión con la cuenta de Azure Stack que está asociada a una suscripción de Azure Stack activa por lo menos.

    ![Conectar a Azure Storage](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Después de iniciar sesión correctamente con una cuenta de Azure Stack, el panel izquierdo se llena con las suscripciones de Azure Stack asociadas a dicha cuenta. Seleccione las suscripciones de Azure Stack con la que quiere trabajar y luego seleccione **Aplicar**. (Al activar o desactivar la casilla **Todas las suscripciones**, se alterna entre seleccionar todas o ninguna de las suscripciones de Azure Stack que aparecen).

    ![Selección de las suscripciones de Azure Stack después de rellenar el cuadro de diálogo Custom Cloud Environment (Entorno personalizado en la nube)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure Stack seleccionadas.

    ![Lista de cuentas de almacenamiento, incluidas las cuentas de suscripción de Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Conexión a una suscripción de Azure Stack con una cuenta de AD FS

> [!Note]  
> La experiencia de inicio de sesión de los Servicios de federación de Active Directory (AD FS) es compatible con el Explorador de Storage 1.2.0 o versiones más recientes con Azure Stack 1804 u otras actualizaciones más recientes.
Siga estos pasos para conectar el Explorador de Storage a una suscripción de Azure Stack que pertenece a una cuenta de AD FS.

1. Seleccione **Administrar cuentas**. El explorador enumera las suscripciones de Microsoft en las que ha iniciado sesión.
2. Para conectarse a la suscripción de Azure Stack, seleccione **Agregar una cuenta**.

    ![Agregar una cuenta (Explorador de Storage)](media/azure-stack-storage-connect-se/add-an-account.png)

3. Seleccione **Next** (Siguiente). En el cuadro de diálogo Conectar a Azure Storage, en **Entorno de Azure**, seleccione **Use Custom Environment** (Usar entorno personalizado) y luego haga clic en **Siguiente**.

    ![Conectar a Azure Storage](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Escriba la información necesaria del entorno personalizado de Azure Stack. 

    | Campo | Notas |
    | ---   | ---   |
    | Nombre del entorno | El usuario puede personalizar este campo. |
    | Punto de conexión de Azure Resource Manager | Los ejemplos de los puntos de conexión de recursos de Azure Resource Manager del Kit de desarrollo de Azure Stack.<br>Para operadores: https://adminmanagement.local.azurestack.external <br> Para usuarios: https://management.local.azurestack.external |

    Si trabaja en un sistema integrado de Azure Stack y no conoce el punto de conexión de administración, póngase en contacto con el operador.

    ![Agregar una cuenta (Entornos personalizados)](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Seleccione **Iniciar sesión** para conectarse a la cuenta de Azure Stack que está asociada, como mínimo, a una suscripción de Azure Stack.



6. Seleccione las suscripciones de Azure Stack con las que desea trabajar y luego seleccione **Aplicar**.

    ![Administración de cuentas](./media/azure-stack-storage-connect-se/account-management.png)

    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure Stack seleccionadas.

    ![Lista de las suscripciones asociadas](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Conexión a una cuenta de almacenamiento de Azure Stack

También puede conectarse a una cuenta de almacenamiento de Azure Stack mediante el par de claves y el nombre de la cuenta de almacenamiento.

1. En el panel izquierdo del Explorador de Storage, seleccione Administrar cuentas. Se muestran todas las cuentas de Microsoft en las que inició sesión.

    ![Agregar una cuenta (Explorador de Storage)](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Para conectarse a la suscripción de Azure Stack, seleccione **Agregar una cuenta**.

    ![Agregar una cuenta (Conectar a Azure Storage)](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. En el cuadro de diálogo Conectar a Azure Storage, seleccione **Use a storage account name and key** (Usar un nombre y una clave de cuenta de almacenamiento).

4. Escriba el nombre de la cuenta en **Nombre de cuenta** y pegue la clave de la cuenta en el cuadro de texto **Clave de cuenta**. Luego, seleccione **Other (enter below)** [Otro (escribir siguiente)]en **Dominio de puntos de conexión de Storage** y escriba el punto de conexión de Azure Stack.

    Un punto de conexión de Azure Stack incluye dos partes: el nombre de una región y el dominio de Azure Stack. En el Kit de desarrollo de Azure Stack, el punto de conexión predeterminado es **local.azurestack.external**. Si no está seguro de cuál es su punto de conexión, póngase en contacto con el administrador de la nube.

    ![Asociar nombre y clave](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Seleccione **Conectar**.
6. Cuando la cuenta de almacenamiento se haya asociado correctamente, se muestra con (**External, Other**) (Externa, Otros) anexado a su nombre.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Pasos siguientes

* [Introducción al Explorador de Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack Storage: Diferencias y consideraciones](azure-stack-acs-differences.md)
* Para más información sobre Azure Storage, consulte [Introducción a Microsoft Azure Storage](/azure/storage/common/storage-introduction).
