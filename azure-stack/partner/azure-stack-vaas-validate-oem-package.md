---
title: Verificación de los paquetes del fabricante de equipos originales (OEM) en la validación como servicio de Azure Stack | Microsoft Docs
description: Aprenda a verificar los paquetes del fabricante de equipos originales (OEM) con la validación como servicio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 20d5d2d962fbe85db5d4725c864defe84c2c152c
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167351"
---
# <a name="validate-oem-packages"></a>Validación de paquetes de OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Puede probar un nuevo paquete de OEM cuando se ha producido un cambio en el firmware o en los controladores para una validación de solución completa. Cuando el paquete ha superado la prueba, Microsoft lo firma. La prueba debe contener el paquete de extensión de OEM actualizado con los controladores y el firmware que han superado las pruebas del logotipo de Windows Server y las pruebas PCS.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Antes de cargar o enviar paquetes, consulte [Create an OEM package](azure-stack-vaas-create-oem-package.md) (Creación de un paquete de OEM) para el formato y el contenido de paquete esperados.

## <a name="managing-packages-for-validation"></a>Administración de paquetes para la validación

Al usar el flujo de trabajo **Validación del paquete** para validar un paquete, deberá proporcionar una dirección URL a **Azure Storage Blob**. Este blob es el paquete de OEM firmado de prueba que se instalará como parte del proceso de actualización. Cree el blob mediante la cuenta de almacenamiento de Azure que creó durante la instalación (consulte [Configuración de los recursos de Validación como servicio](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Requisito previo: aprovisionar un contenedor de almacenamiento

Cree un contenedor en la cuenta de almacenamiento para los blobs de paquetes. Este contenedor puede utilizarse para todas las ejecuciones de Package Validation (Validación del paquete).

1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento que creó en [Configuración de los recursos de validación como servicio](azure-stack-vaas-set-up-resources.md).

2. En la hoja izquierda de **Blob Service**, seleccione **Contenedores**.

3. Seleccione **+ Contenedor** en la barra de menús.
    1. Proporcione un nombre para el contenedor, por ejemplo, `vaaspackages`.
    1. Seleccione el nivel de acceso deseado para los clientes no autenticados como VaaS. Para más información acerca de cómo conceder acceso de VaaS a los paquetes en cada escenario, consulte [Control del nivel de acceso de un contenedor](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Carga de un paquete en una cuenta de almacenamiento

1. Prepare el paquete que desea validar. Se trata de un archivo `.zip` cuyo contenido tiene que coincidir con la estructura descrita en [Creación de un paquete de OEM](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Asegúrese de que el contenido del archivo `.zip` se coloca en la raíz del archivo `.zip`. El paquete no debe tener subcarpetas.

1. En [Azure Portal](https://portal.azure.com), seleccione el contenedor del paquete y cargue el paquete; para ello, seleccione **Cargar** en la barra de menús.

1. Seleccione el archivo `.zip` del paquete que se va a cargar. Mantenga los valores predeterminados para **Tipo de blob** (es decir, **Blob en bloques**) y **Tamaño de bloque**.

### <a name="generate-package-blob-url-for-vaas"></a>Generación de la dirección URL del blob del paquete para VaaS

Al crear un flujo de trabajo de **Package Validation** (Validación del paquete) en el portal de VaaS, deberá proporcionar una dirección URL a la instancia de Azure Storage Blob que contiene el paquete. Algunas pruebas *interactivas*, incluidas **Monthly AzureStack Update Verification** (Comprobación de actualización mensual de AzureStack) y **OEM Extension Package Verification** (Comprobación del paquete de extensión de OEM), también requieren una dirección URL para los blobs del paquete.

#### <a name="handling-container-access-level"></a>Control del nivel de acceso de un contenedor

El nivel de acceso mínimo requerido por VaaS depende de si va a crear un flujo de trabajo de validación del paquete o programar una prueba *interactiva*.

En el caso de los niveles de acceso **Privado** y **Blob**, tiene que conceder acceso temporalmente al blob del paquete proporcionando a VaaS una [firma de acceso compartido](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). El nivel de acceso **Contenedor** no necesita que se generen direcciones URL de SAS, pero permite el acceso no autenticado al contenedor y sus blobs.

|Nivel de acceso | Requisito de flujo de trabajo | Requisito de prueba |
|---|---------|---------|
|Privada | Generar una dirección URL de SAS por blob de paquete ([Opción 1](#option-1-generate-a-blob-sas-url)). | Generar una dirección URL de SAS en el nivel de cuenta y agregar manualmente el nombre del blob de paquete ([Opción 2](#option-2-construct-a-container-sas-url)). |
|Blob | Proporcionar la propiedad de dirección URL de blob ([Opción 3](#option-3-grant-public-read-access)). | Generar una dirección URL de SAS en el nivel de cuenta y agregar manualmente el nombre del blob de paquete ([Opción 2](#option-2-construct-a-container-sas-url)). |
|Contenedor | Proporcionar la propiedad de dirección URL de blob ([Opción 3](#option-3-grant-public-read-access)). | Proporcionar la propiedad de dirección URL de blob ([Opción 3](#option-3-grant-public-read-access)).

Las opciones para conceder acceso a los paquetes se ordenan desde el menor acceso al mayor acceso.

#### <a name="option-1-generate-a-blob-sas-url"></a>Opción 1: Generar una dirección URL de SAS de blob

Use esta opción si se establece el nivel de acceso de su contenedor de almacenamiento en **Privado**, donde el contenedor no permite el acceso de lectura público para el contenedor o sus blobs.

> [!NOTE]
> Este método no funcionará para pruebas *interactivas*. Consulte [Opción 2: Construir un dirección URL de SAS del contenedor](#option-2-construct-a-container-sas-url).

1. En [Azure Portal](https://portal.azure.com/), vaya a la cuenta de almacenamiento y, después, al archivo .zip que contiene el paquete.

2. Seleccione **Generar SAS** en el menú contextual.

3. Seleccione **Lectura** en **Permisos**.

4. En **Hora de inicio** especifique la hora actual y en **Hora de finalización** una hora que sea al menos 48 horas posterior a la de inicio **.** Si va a crear otros flujos de trabajo con el mismo paquete, considere la posibilidad de aumentar el valor de **Hora de finalización** para que abarque todo el tiempo que duren las pruebas.

5. Seleccione **Generate blob SAS token and URL** (Generar URL y token de SAS del blob).

Use la **dirección URL de SAS del blob** al proporcionar las direcciones URL del blob de paquete para el portal.

#### <a name="option-2-construct-a-container-sas-url"></a>Opción 2: Construir un dirección URL de SAS del contenedor

Use esta opción si el nivel de acceso de su contenedor de almacenamiento se establece en **Privado** y tiene que suministrar una dirección URL de blob de paquete a una prueba *interactiva*. También se puede usar esta dirección URL en el nivel de flujo de trabajo.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Seleccione **Blob** en las **opciones de Servicios permitidos**. Anule la selección de las opciones restantes.

1. Seleccione **Contenedor** y **Objeto** en **Tipos de recursos permitidos**.

1. Seleccione **Lectura** y **Lista** en **Permisos permitidos**. Anule la selección de las opciones restantes.

1. Seleccione para **Hora de inicio** la hora actual y para **Hora de finalización** un valor que sea al menos 14 días posterior a la **Hora de inicio.** Si va a ejecutar otras pruebas con el mismo paquete, considere la posibilidad de aumentar el valor de **Hora de finalización** para que abarque todo el tiempo que duren las pruebas. Las pruebas programadas mediante VaaS después del valor de **Hora de finalización** producirán un error y tendrá que generarse una nueva SAS.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    El formato debe ser similar al siguiente: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Modifique la dirección URL de SAS generada para incluir el contenedor del paquete, `{containername}`, y el nombre del blob del paquete, `{mypackage.zip}`, como sigue: `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Use este valor al proporcionar las direcciones URL del blob de paquete para el portal.

#### <a name="option-3-grant-public-read-access"></a>Opción 3: Conceder el acceso de lectura público

Use esta opción si es aceptable permitir el acceso de clientes no autenticados a los blobs individuales o, en el caso de las pruebas *interactivas*, al contenedor.

> [!CAUTION]
> Esta opción abre los blobs para el acceso anónimo de solo lectura.

1. Establezca el nivel de acceso del contenedor de paquete en **Blob** o **Contenedor**; para ello, siga las instrucciones de la sección [Concesión de permisos a usuarios anónimos a contenedores y blobs](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > Si va a proporcionar una dirección URL del paquete a una prueba *interactiva*, tiene que conceder **acceso de lectura público completo** al contenedor para continuar con las pruebas.

1. En el contenedor del paquete, seleccione el blob del paquete para abrir el panel de propiedades.

1. Copie la **dirección URL**. Use este valor al proporcionar las direcciones URL del blob de paquete para el portal.

## <a name="create-a-package-validation-workflow"></a>Creación de un flujo de trabajo Package Validation (Validación del paquete)

1. Inicie sesión en el [portal de VaaS](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Seleccione **Iniciar** en el icono **Package Validation** (Validación del paquete).

    ![Icono de flujo de trabajo Package Validation (Validación del paquete)](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Escriba la dirección URL del blob de Azure Storage para el paquete de OEM firmado de prueba que requiera una firma de Microsoft. Para instrucciones, consulte [Generación de la dirección URL del blob del paquete para VaaS](#generate-package-blob-url-for-vaas).

6. Copie la carpeta del paquete de actualización de AzureStack a un directorio local del DVM. Escriba la ruta de acceso del directorio primario en "ruta de acceso de la carpeta de la actualización de AzureStack"

7. Copie la carpeta del paquete de OEM creado anteriormente a un directorio local del DVM. Escriba la ruta de acceso del directorio primario en "ruta de acceso de la carpeta de la actualización de OEM"

    > [!NOTE]
    > Copie la actualización de AzureStack y la actualización de OEM en **2** directorios principales independientes.

8. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > No se pueden modificar los parámetros de entorno después de crear un flujo de trabajo.

9. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

10. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Se le redirigirá a la página de resumen de las pruebas.

## <a name="required-tests"></a>Pruebas necesarias

Es preciso realizar las siguientes pruebas, en el orden especificado, para la validación del paquete de OEM:

- Paso 1: Comprobación de la actualización mensual de AzureStack
- Paso 2: Comprobación del paquete de extensión de OEM
- Paso 3: OEM: motor de la simulación de la nube

## <a name="run-package-validation-tests"></a>Ejecución de pruebas de Package Validation

1. En la página **Package Validation tests summary** (Resumen de las pruebas de validación del paquete), se ejecutará un subconjunto de las pruebas enumeradas apropiadas para su escenario.

    En los flujos de trabajo de validación, para **programar** una prueba se usan los parámetros comunes de nivel de flujo de trabajo que especificó durante la creación del flujo de trabajo (consulte [Parámetros comunes del flujo de trabajo en la validación de Azure Stack como servicio](azure-stack-vaas-parameters.md)). Si alguno de los valores de parámetro de prueba deja de ser válido, debe volver a suministrarlo como se indica en [Modify workflow parameters](azure-stack-vaas-monitor-test.md#change-workflow-parameters) (Modificación de los parámetros de flujo de trabajo).

    > [!NOTE]
    > La programación a través de una prueba de validación a través de una instancia existente creará una nueva instancia en el lugar de la anterior en el portal. Los registros de la instancia anterior se conservarán, pero no se podrá acceder a ellos desde el portal.  
    > Una vez finalizada una prueba correctamente, la acción **Schedule** (Programar) pasará a estar deshabilitada.

2. Para la validación del paquete, ejecutará las **pruebas necesarias** en el orden que se muestra.

    > [!CAUTION]
    > VaaS ejecutará las pruebas en el orden en que se programaron. Es necesario programar las pruebas en el orden especificado.

3. Seleccione el agente que ejecutará la prueba. Para más información acerca de cómo agregar agentes de ejecución de prueba, consulte [Implementación del agente local](azure-stack-vaas-local-agent.md).

4. Para programar la ejecución serie de pruebas, seleccione **Schedule** (Programar) en el menú contextual para abrir un símbolo del sistema para programar la instancia de prueba.

5. Examine los parámetros de prueba y seleccione **Submit** (Enviar) para programar la prueba.

6. No es necesario esperar a que la prueba se complete para programar la siguiente. Programe todas las pruebas **necesarias** en el orden especificado arriba.

7. Revise los resultados de las pruebas **necesarias**.

Para enviar un solicitud de firma del paquete, envíe a [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) el nombre de la solución y el nombre de la validación del paquete asociado con esta ejecución.

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md)
