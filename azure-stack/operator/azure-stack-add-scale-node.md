---
title: Incorporación de nodos de la unidad de escalado en Azure Stack Hub
description: Aprenda a agregar nodos de la unidad de escalado a unidades de escalado de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/05/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/05/2020
ms.openlocfilehash: fac60db9ad1f3ae8be248b4f61a3c16179763a7e
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364207"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack-hub"></a>Incorporación de nodos de la unidad de escalado adicionales en Azure Stack Hub

Puede aumentar la capacidad total de una unidad de escalado existente mediante la incorporación de un equipo físico adicional. Al equipo físico también se le conoce como nodo de unidad de escalado. Cada nuevo nodo de unidad de escalado que agregue debe ser homogéneo en cuanto al tipo de CPU, memoria y número y tamaño de discos, respecto a los nodos que ya están presentes en dicha unidad. Azure Stack Hub no admite la eliminación de nodos de unidad de escalado con el fin de reducir verticalmente debido a limitaciones de arquitectura. Solo es posible expandir la capacidad mediante la adición de nodos.

Para agregar un nodo de unidad de escalado, debe actuar en Azure Stack Hub y ejecutar las herramientas del fabricante del equipo de hardware (OEM). Las herramientas de OEM se ejecutan en el host del ciclo de vida de hardware (HLH) para asegurarse de que el equipo físico nuevo coincide con el mismo nivel de firmware que los nodos existentes.

En el siguiente diagrama de flujo se muestra el proceso general para agregar un nodo de la unidad de escalado:

![Flujo de cómo agregar una unidad de escalado](media/azure-stack-add-scale-node/add-node-flow.svg)
<br> *Si su proveedor de hardware de OEM implementa la colocación del bastidor del servidor físico y las actualizaciones, el firmware varía según su contrato de soporte técnico.*

La operación para agregar un nuevo nodo puede tardar varias horas o días en completarse. No se producirá ningún impacto en las cargas de trabajo en ejecución en el sistema durante la incorporación de un nodo adicional de unidad de escalado.

> [!NOTE]  
> No intente ninguna de las siguientes operaciones mientras ya esté en curso una operación de nodo para agregar una unidad de escalado:
>
>  - Actualización de Azure Stack Hub
>  - Rotación de certificados
>  - Detención de Azure Stack Hub
>  - Reparación de un nodo de unidad de escalado
>  - Adición de otro nodo (el error de la acción Agregar nodo anterior también se considera en curso)

## <a name="add-scale-unit-nodes"></a>Incorporación de nodos de unidad de escalado

Los pasos siguientes son una descripción general de alto nivel de cómo agregar un nodo. No siga estos pasos sin consultar primero la documentación de expansión de la capacidad proporcionada por su OEM.

1. Coloque el nuevo servidor físico en el bastidor y cabléelo de forma adecuada. 
2. Habilite los puertos de conmutador físico y ajuste las listas de control de acceso (ACL) si procede.
3. Configure la dirección IP correcta en el controlador de administración de placa base (BMC) y aplique toda la configuración de BIOS según la documentación proporcionada por el OEM.
4. Aplique la base de referencia del firmware actual a todos los componentes mediante las herramientas proporcionadas por el fabricante del hardware que se ejecutan en el HLH.
5. Ejecute la operación Agregar nodo en el portal del administrador de Azure Stack Hub.
6. Valide que la operación Agregar nodo se realiza correctamente. Para ello, compruebe el [**estado** de la unidad de escalado](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Incorporación del nodo

Puede usar el portal del administrador o PowerShell para agregar nuevos nodos. La operación Agregar nodo primero agrega el nuevo nodo de unidad de escalado como capacidad de proceso disponible y luego extiende la capacidad de almacenamiento automáticamente. La capacidad se expande automáticamente porque Azure Stack Hub es un sistema hiperconvergido donde *proceso* y *almacenamiento* escalan juntos.

### <a name="administrator-portal"></a>[Administrator Portal](#tab/portal)

1. Inicie sesión en el portal del administrador de Azure Stack Hub como operador de Azure Stack Hub.
2. Vaya a **+ Create a resource (Crear un recurso)**  > **Capacidad** > **Nodo de la unidad de escalado**.
   ![Nodo de la unidad de escalado:](media/azure-stack-add-scale-node/select-node1.png)
3. En el panel **Agregar nodo**, seleccione la *Región* y, a continuación, seleccione la *Unidad de escalado* a la que desea agregar el nodo. Especifique también la *DIRECCIÓN IP DE BMC* para el nodo de la unidad de escalado que va a agregar. Solo puede agregar un nodo a la vez.
   ![Incorporación de detalles de nodo](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="powershell-az"></a>[PowerShell Az](#tab/Az)

Use el cmdlet **Add-AzsScaleUnitNode** para agregar un nodo.  

Antes de usar cualquiera de los siguientes scripts de PowerShell de ejemplo, reemplace los valores *name_of_new_node*, *name_of_scale_unit_cluster* y *BMCIP_address_of_new_node* por los de su entorno de Azure Stack Hub.

  > [!Note]  
  > Al asignar nombres a un nodo, debe mantener el nombre con menos de 15 caracteres de longitud. No puede usar tampoco un nombre que contenga un espacio o alguno de los siguientes caracteres: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`,`}` y `_`.

**Agregue un nodo:**
  ```powershell
  ## Add a single Node 
    Add-AzsScaleUnitNode -BMCIPv4Address "<BMCIP_address_of_new_node>" -computername "<name_of_new_node>" -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

### <a name="powershell-azurerm"></a>[PowerShell AzureRM](#tab/AzureRM)

Use el cmdlet **New-AzsScaleUnitNodeObject** para agregar un nodo.  

Antes de utilizar cualquiera de los siguientes scripts de PowerShell de ejemplo, reemplace los valores de los *nombres de nodo* y las *direcciones IP* por los valores de su entorno de Azure Stack Hub.

  > [!Note]  
  > Al asignar nombres a un nodo, debe mantener el nombre con menos de 15 caracteres de longitud. No puede usar tampoco un nombre que contenga un espacio o alguno de los siguientes caracteres: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`,`}` y `_`.

**Agregue un nodo:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

---

## <a name="monitor-add-node-operations"></a>Supervisión de las operaciones para agregar nodo 
Use el portal del administrador o PowerShell para obtener el estado de la operación Agregar nodo. Las operaciones Agregue nodo pueden tardar de varias horas a días en completarse.

### <a name="use-the-administrator-portal"></a>Uso del portal de administración 
Para supervisar la incorporación de un nuevo nodo, revise la unidad de escalado o los objetos de nodo de la unidad de escalado en el portal del administrador. Para ello, vaya a **Region management** (Administración de regiones) > **Scale units** (Unidades de escalado). A continuación, seleccione la unidad de escalado o el nodo de unidad de escalado que desea revisar. 

### <a name="use-powershell"></a>Uso de PowerShell
El estado de la unidad de escalado y de los nodos de unidad de escalado se puede recuperar mediante PowerShell de la siguiente manera:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Estado de la operación Agregar nodo 
**Para una unidad de escalado:**

|Estado               |Descripción  |
|---------------------|---------|
|En ejecución              |Todos los nodos están participando activamente en la unidad de escalado.|
|Detenido              |El nodo de unidad de escalado está inactivo o es inaccesible.|
|Expandiendo            |Actualmente se están agregando uno o varios nodos de unidad de escalado como capacidad de proceso.|
|Configuración del almacenamiento  |Se ha expandido la capacidad de proceso y la configuración de almacenamiento se está ejecutando.|
|Requiere corrección |Se detectó un error que requiere que uno o varios nodos de unidad de escalado se reparen.|


**Para un nodo de unidad de escalado:**

|Estado                |Descripción  |
|----------------------|---------|
|En ejecución               |El nodo está participando activamente en la unidad de escalado.|
|Detenido               |El nodo no está disponible.|
|Agregando                |El nodo se está agregando activamente a la unidad de escalado.|
|Reparando             |El nodo se está reparando activamente.|
|Mantenimiento           |El nodo está en pausa y no se está ejecutando ninguna carga de trabajo de usuario activa. |
|Requiere corrección  |Se detectó un error que requiere que el nodo se repare.|


## <a name="troubleshooting"></a>Solución de problemas
Los siguientes son problemas comunes observados al agregar un nodo. 

**Escenario 1:** se produce un error en la operación de incorporación de nodo de unidad de escalado pero uno o varios nodos se muestran con un estado de Detenido.  
- Corrección: Use la operación de corrección para reparar uno o más nodos. Solo se puede ejecutar una operación de reparación al mismo tiempo.

**Escenario 2:** se han agregado uno o varios nodos de unidad de escalado pero no se pudo realizar la expansión de almacenamiento. En este escenario, el objeto de nodo de unidad de escalado notifica el estado En ejecución pero no se inicia la tarea de configuración del almacenamiento.  
- Corrección: use el punto de conexión con privilegios para revisar el estado de almacenamiento ejecutando el siguiente cmdlet de PowerShell:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Escenario 3:** recibe una alerta que indica un error en el trabajo de escalabilidad horizontal del almacenamiento.  
- Corrección: en este caso, se produjeron errores en la tarea de configuración de almacenamiento. Este problema requiere que se ponga en contacto con soporte técnico.


## <a name="next-steps"></a>Pasos siguientes 
[Incorporación de direcciones IP públicas](azure-stack-add-ips.md) 
