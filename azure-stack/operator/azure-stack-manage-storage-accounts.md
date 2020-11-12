---
title: Administración de cuentas de almacenamiento de Azure Stack Hub
description: Aprenda a buscar, administrar, recuperar y reclamar cuentas de almacenamiento de Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 2e4882717b4dc708298df7b4ee9a8dc0ef2c1b9c
ms.sourcegitcommit: 30ea43f486895828710297967270cb5b8d6a1a18
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2020
ms.locfileid: "93415324"
---
# <a name="manage-azure-stack-hub-storage-accounts"></a>Administración de cuentas de almacenamiento de Azure Stack Hub

Aprenda a administrar cuentas de almacenamiento de Azure Stack Hub. Busque, recupere y reclame la capacidad de almacenamiento según sus necesidades empresariales.

## <a name="find-a-storage-account"></a>Búsqueda de una cuenta de almacenamiento

La lista de cuentas de almacenamiento de la región se puede ver en Azure Stack Hub mediante estos pasos:

1. Inicie sesión en el portal de administración en `https://adminportal.local.azurestack.external`.

2. Seleccione **Todos los servicios** > **Almacenamiento** > **Cuentas de almacenamiento**.

   ![Cuentas de almacenamiento de Azure Stack Hub](media/azure-stack-manage-storage-accounts/image4.png)

De forma predeterminada, se muestran las 10 primeras cuentas. Puede hacer clic en el vínculo **Cargar más** de la parte inferior de la lista para capturar más.

O BIEN

Si está interesado en una cuenta de almacenamiento determinada, puede **filtrar y capturar solo las cuentas correspondientes**.

**Para filtrar las cuentas:**

1. Seleccione **Filtrar** en la parte superior del panel.
2. En el panel Filtro, puede especificar el **nombre de cuenta** , el **identificador de suscripción** o el **estado** para ajustar la lista de cuentas de almacenamiento que se mostrará. Use esta información de la manera adecuada.
3. A medida que escriba, la lista aplicará automáticamente el filtro.

    ![Filtrado de las cuentas de almacenamiento de Azure Stack Hub](media/azure-stack-manage-storage-accounts/image5.png)

4. Para restablecer el filtro: seleccione **Filtro** , borre las selecciones y actualice.

El cuadro de texto de búsqueda (de la parte superior del panel de lista de cuentas de almacenamiento) permite resaltar el texto seleccionado en la lista de cuentas. Se puede utilizar cuando el nombre completo o el identificador no están disponibles fácilmente.

Aquí puede usar texto sin formato para facilitar la búsqueda de la cuenta que le interesa.

![Búsqueda de las cuentas de almacenamiento de Azure Stack Hub](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Examen de los detalles de la cuenta
Cuando haya encontrado las cuentas que le interesa ver, puede seleccionar una en particular para ver determinados detalles. Se abre un nuevo panel con los detalles de la cuenta. Estos detalles incluyen el tipo de cuenta, la hora de creación, la ubicación, etc.

![Detalles de la cuenta de almacenamiento](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Recuperación de una cuenta eliminada
Puede que se encuentre en una situación en que deba recuperar una cuenta eliminada.

En Azure Stack Hub, hay una manera sencilla de hacerlo:

1. Vaya a la lista de cuentas de almacenamiento. Para más información, vea [Búsqueda de una cuenta de almacenamiento](azure-stack-manage-storage-accounts.md) al principio de este artículo.
2. Busque esa cuenta en particular en la lista. Puede que deba filtrar.
3. Compruebe el *estado* de la cuenta. Debe indicar **Eliminada**.
4. Seleccione la cuenta para abrir el panel de detalles de la cuenta.
5. En la parte superior de este panel, busque el botón **Recuperar** y selecciónelo.
6. Seleccione **Sí** para confirmar la acción.

   ![Confirmación de recuperación de una cuenta de almacenamiento](media/azure-stack-manage-storage-accounts/image8.png)

7. La recuperación está en curso. Espere una indicación de que se ha realizado correctamente. También puede seleccionar el icono de "campana" en la parte superior del portal para ver las indicaciones de progreso.

   ![Recuperación de la cuenta de almacenamiento correcta](media/azure-stack-manage-storage-accounts/image9.png)

   Una vez que la cuenta recuperada se ha sincronizado correctamente, se puede volver a usar.

### <a name="some-gotchas"></a>Algunos gotchas
* La cuenta eliminada muestra un estado **fuera de retención**.
  
  Fuera de retención significa que la cuenta eliminada ha superado el período de retención y puede que no sea posible recuperarla.

* La cuenta eliminada no se muestra en la lista de cuentas.
  
  Su cuenta podría no aparecer en la lista de cuentas si la cuenta eliminada ya ha pasado la recolección de elementos no utilizados. En este caso no se puede recuperar. Para más información, consulte [Reclamación de capacidad](#reclaim) en este artículo.

## <a name="set-the-retention-period"></a>Establecimiento del período de retención
La configuración del período de retención permite a un operador de nube especificar un período de tiempo en días (entre 0 y 9999 días) durante el cual existe la posibilidad de que cualquier cuenta eliminada se pueda recuperar. El período de retención predeterminado se establece en 0 días. Un valor configurado de "0" significa que una cuenta eliminada está inmediatamente fuera de retención y marcada para la recolección periódica de elementos no utilizados.

**Para cambiar el periodo de retención:**

1. Inicie sesión en el portal de administración en `https://adminportal.local.azurestack.external`.
2. Seleccione **Todos los servicios** > **Administración de regiones** en **Administración**.
3. Seleccione **Proveedores de recursos** > **Almacenamiento** > **Configuración**. La ruta de acceso es Inicio > *región* - Proveedores de recursos > Almacenamiento.
4. Seleccione **Configuración** y, luego, edite el valor del período de retención.

   Establezca el número de días y, a continuación, guárdelo.

   Este valor se hace efectivo inmediatamente y se aplica a toda la región.

   ![Edición del período de retención en el portal de administración](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim-capacity"></a><a name="reclaim"></a>Reclamación de capacidad
Uno de los efectos secundarios de tener un período de retención es que una cuenta eliminada sigue consumiendo su capacidad hasta que está fuera del período de retención. Como operador de nube, puede que necesite una manera de reclamar el espacio de la cuenta eliminada incluso aunque el periodo de retención no haya expirado aún.

Para ello, puede usar el portal o PowerShell.

**Para reclamar la capacidad mediante el portal:**
1. Vaya al panel de cuentas de almacenamiento. Consulte Búsqueda de una cuenta de almacenamiento.
2. Seleccione **Recuperar espacio** en la parte superior del panel.
3. Lea el mensaje y, a continuación, seleccione **Aceptar**.

    ![Reclamación de espacio en las cuentas de almacenamiento](media/azure-stack-manage-storage-accounts/image11.png)

4. Espere la notificación de operación correcta. Observe el icono de campana en el portal.

    ![Espacio recuperado correctamente](media/azure-stack-manage-storage-accounts/image12.png)

5. Actualice la página de cuentas de almacenamiento. Las cuentas eliminadas ya no se muestran en la lista porque se han purgado.

También puede usar PowerShell para reemplazar explícitamente el período de retención y reclamar inmediatamente la capacidad.

**Para reclamar la capacidad mediante PowerShell:**

1. Confirme que ha instalado y configurado Azure PowerShell. Si no es así, use las siguientes instrucciones: 
   * Para instalar la versión más reciente de Azure PowerShell y asociarla a su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/).
   Para más información sobre los cmdlets de Azure Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](/azure/azure-resource-manager/management/manage-resources-powershell).
2. Ejecute los siguientes cmdlets:

> [!NOTE]  
> Si ejecuta estos cmdlets, eliminará permanentemente la cuenta y su contenido. No se puede recuperar. Así que úselo con precaución.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Para más información, consulte la [documentación de PowerShell de Azure Stack Hub](/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Pasos siguientes

 - Para obtener información sobre cómo administrar permisos, vea [Establecimiento de permisos de acceso mediante el control de acceso basado en rol](azure-stack-manage-permissions.md).
 - Para información sobre cómo administrar la capacidad de almacenamiento de Azure Stack Hub, consulte [Administración de la capacidad de almacenamiento para Azure Stack Hub](azure-stack-manage-storage-shares.md).