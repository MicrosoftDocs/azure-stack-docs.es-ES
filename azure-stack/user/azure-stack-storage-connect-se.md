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
ms.date: 03/14/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: f6e9e8836cb2bbc16dbad7484f0b90ce1af5606a
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986106"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo, aprenderá a conectarse a sus suscripciones y cuentas de almacenamiento de Azure Stack mediante el explorador de almacenamiento. El Explorador de Azure Storage es una aplicación independiente que permite trabajar fácilmente con datos de almacenamiento de Azure Stack en Windows, macOS y Linux.

> [!NOTE]  
> Hay varias herramientas disponibles para mover datos hacia el almacenamiento de Azure Stack y desde este. Para más información, consulte [Herramientas de transferencia de datos de Azure Stack Storage](azure-stack-storage-transfer.md).

Si no lo ha instalado todavía, [descargue el Explorador de Storage](https://www.storageexplorer.com/) e instálelo.

Después de conectarse a una suscripción de Azure Stack o una cuenta de almacenamiento, puede usar los [artículos sobre el Explorador de Azure Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para trabajar con los datos de Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Prepárese para conectarse a Azure Stack

Necesita acceso directo a Azure Stack o una conexión VPN para que el Explorador de Storage pueda obtener acceso a la suscripción de Azure Stack. Para más información sobre cómo configurar una conexión VPN a Azure Stack, consulte [Conexión a Azure Stack con VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

Para el Kit de desarrollo de Azure Stack, debe exportar el certificado raíz de la entidad de certificación de Azure Stack.

> [!Note]  
> Para el ASDK, si se conecta al ASDK mediante VPN, no utilice el certificado raíz (CA.cer) que se creó durante el proceso de configuración de la VPN.  Este es un certificado con codificación DER y no permitirá que el Explorador de Storage recupere las suscripciones de Azure Stack. Siga estos pasos para exportar un certificado codificado en base 64 para usarlo con el Explorador de Storage.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportación y posterior importación del certificado de Azure Stack

Exporte y después importe el certificado de Azure Stack para ASDK. Para el sistema integrado, el certificado está firmado públicamente. Por lo tanto, este paso no es necesario mientras se configura la conexión del Explorador de Storage al sistema integrado de Azure Stack.

1. Abra `mmc.exe` en una máquina host de Azure Stack o una máquina local con una conexión VPN a Azure Stack. 

2. En **Archivo**, seleccione **Agregar o quitar complemento**. Seleccione **Certificados** en Complementos disponibles. 

3. Seleccione la **cuenta de equipo** y haga clic en **Siguiente**. Seleccione **Equipo local** y luego haga clic en **Finalizar**.

4.  En **Console Root\Certificated (equipo)\Trusted Root Certification Authorities\Certificates**. Busque **AzureStackSelfSignedRootCert**.

    ![Carga del certificado raíz de Azure Stack a través de mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Haga clic con el botón derecho en el certificado, seleccione **Todas las tareas** > **Exportar** y luego siga las instrucciones para exportar el certificado con **X.509 (.CER) codificado en base 64**.

    Se usará el certificado exportado en el paso siguiente.

6. Inicie el Explorador de Storage y, si ve el cuadro de diálogo **Connect to Azure Storage** (Conectar a Azure Storage), cancélelo.

7. En el menú **Editar**, seleccione **Certificados SSL** y, luego, seleccione **Importar certificados**. Use el cuadro de diálogo del selector de archivos para buscar y abrir el certificado que exportó en el paso anterior.

    Después de la importación del certificado, se le pedirá que reinicie el Explorador de Storage.

    ![Importación del certificado en el Explorador de Storage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Después de que se reinicie el Explorador de Storage, seleccione el menú **Edit** (Editar) y compruebe que está seleccionada la opción **Target Azure Stack APIs** (API de Azure Stack de destino). Si no está seleccionada, seleccione **Target Azure Stack** (Azure Stack de destino) y, luego, reinicie el Explorador de Storage para que el cambio surta efecto. Esta configuración es necesaria para obtener compatibilidad con el entorno de Azure Stack.

    ![Comprobación de que Azure Stack de destino está activada](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Conexión a una suscripción de Azure Stack con Azure AD

Siga estos pasos para conectar el Explorador de Storage a una suscripción de Azure Stack que pertenece a una cuenta de Azure Active Directory (Azure AD).

1. En el panel izquierdo del Explorador de Storage, seleccione **Administrar cuentas**. 
    Se muestran todas las suscripciones de Microsoft en las que inició sesión.

2. Para conectarse a la suscripción de Azure Stack, seleccione **Agregar una cuenta**.

    ![Adición de una cuenta de Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. En el cuadro de diálogo Conectar a Azure Storage, en el **entorno de Azure**, seleccione **Azure**, **Azure China**, **Azure Alemania**, **Azure Gobierno de EE. UU.** o **Agregar nuevo entorno**, que depende de la cuenta de Azure Stack utilizada. Seleccione **Iniciar sesión** para iniciar sesión con la cuenta de Azure Stack que está asociada a una suscripción de Azure Stack activa por lo menos.

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

    ![Agregar una cuenta](media/azure-stack-storage-connect-se/add-an-account.png)

3. Seleccione **Next** (Siguiente). En el cuadro de diálogo Conectar a Azure Storage, en **Entorno de Azure**, seleccione **Use Custom Environment** (Usar entorno personalizado) y luego haga clic en **Siguiente**.

    ![Conectar a Azure Storage](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Escriba la información necesaria del entorno personalizado de Azure Stack. 

    | Campo | Notas |
    | ---   | ---   |
    | Nombre del entorno | El usuario puede personalizar este campo. |
    | Punto de conexión de Azure Resource Manager | Los ejemplos de los puntos de conexión de recursos de Azure Resource Manager del Kit de desarrollo de Azure Stack.<br>Para operadores: https://adminmanagement.local.azurestack.external <br> Para usuarios: https://management.local.azurestack.external |

    Si está trabajando en el sistema integrado de Azure Stack y no conoce el punto de conexión de administración, póngase en contacto con su operador.

    ![Agregar una cuenta](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Seleccione **Iniciar sesión** para conectarse a la cuenta de Azure Stack que está asociada a una suscripción de Azure Stack activa por lo menos.



6. Seleccione las suscripciones de Azure Stack con las que quiere trabajar. Seleccione **Aplicar**.

    ![Administración de cuentas](./media/azure-stack-storage-connect-se/account-management.png)

    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure Stack seleccionadas.

    ![Lista de las suscripciones asociadas](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Conexión a una cuenta de almacenamiento de Azure Stack

También puede conectarse a una cuenta de almacenamiento de Azure Stack mediante el par de claves y el nombre de la cuenta de almacenamiento.

1. En el panel izquierdo del Explorador de Storage, seleccione Administrar cuentas. Se muestran todas las cuentas de Microsoft en las que inició sesión.

    ![Agregar una cuenta](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Para conectarse a la suscripción de Azure Stack, seleccione **Agregar una cuenta**.

    ![Agregar una cuenta](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. En el cuadro de diálogo Conectar a Azure Storage, seleccione **Use a storage account name and key** (Usar un nombre y una clave de cuenta de almacenamiento).

4. Escriba el nombre de su cuenta en **Nombre de cuenta**, pegue la clave de cuenta en el cuadro de texto **Clave de cuenta**, seleccione **Otros (escribir a continuación)** en **Dominio de puntos de conexión de Storage** y escriba el punto de conexión de Azure Stack.

    Un punto de conexión de Azure Stack incluye dos partes: el nombre de una región y el dominio de Azure Stack. En el Kit de desarrollo de Azure Stack, el punto de conexión predeterminado es **local.azurestack.external**. Si no está seguro de cuál es su punto de conexión, póngase en contacto con el administrador de la nube.

    ![Asociar nombre y clave](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Seleccione **Conectar**.
6. Cuando la cuenta de almacenamiento se haya asociado correctamente, se muestra con (**External, Other**) (Externa, Otros) anexado a su nombre.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Pasos siguientes

* [Introducción al Explorador de Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack Storage: Diferencias y consideraciones](azure-stack-acs-differences.md)
* Para más información sobre Azure Storage, consulte [Introducción a Microsoft Azure Storage](/azure/storage/common/storage-introduction).
