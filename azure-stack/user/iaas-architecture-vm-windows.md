---
title: Ejecución de una máquina virtual Windows en Azure Stack Hub
description: Aprenda a ejecutar una máquina virtual Windows en Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 3/9/2020
ms.openlocfilehash: 2c2ff4007e92f6263e6b59ac61cb15d303b347d0
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873714"
---
# <a name="run-a-windows-virtual-machine-on-azure-stack-hub"></a>Ejecución de una máquina virtual Windows en Azure Stack Hub

El aprovisionamiento de una máquina virtual en Azure Stack Hub requiere varios componentes adicionales, además de la propia máquina virtual, que incluyen recursos de red y de almacenamiento. En este artículo se muestran procedimientos recomendados para ejecutar una máquina virtual Windows en Azure.

![Arquitectura de una máquina virtual Windows en Azure Stack Hub](./media/iaas-architecture-vm-windows/image1.png)

## <a name="resource-group"></a>Resource group

Un [grupo de recursos](/azure/azure-resource-manager/resource-group-overview) es un contenedor lógico que incluye recursos de Azure Stack Hub relacionados. En general, los grupos de recursos se basan en su duración y quién los administra.

Coloque los recursos estrechamente asociados que comparten el mismo ciclo de vida en un mismo [grupo de recursos](/azure/azure-resource-manager/resource-group-overview). Los grupos de recursos permiten implementar y supervisar los recursos como un grupo, y realizar un seguimiento de los costos de facturación por grupo de recursos. También se pueden eliminar recursos en conjunto, lo que resulta útil cuando se realizan implementaciones de prueba. Asigne nombres de recursos significativos para simplificar la ubicación de un recurso específico y comprender su rol. Para más información, consulte las [Convenciones de nomenclatura para los recursos de Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Máquina virtual

Puede aprovisionar una máquina virtual desde una lista de imágenes publicadas, desde una imagen administrada personalizada o desde un archivo de disco duro virtual (VHD) cargado en Azure Stack Hub Blob Storage.

Azure Stack Hub ofrece distintos tamaños de máquina virtual de Azure. Para más información, consulte [Tamaños de máquinas virtuales en Azure Stack Hub](./azure-stack-vm-sizes.md). Si mueve una carga de trabajo existente a Azure Stack Hub, comience con el tamaño de máquina virtual que más se parezca a los servidores locales/Azure. Luego, mida el rendimiento de la carga de trabajo real, en términos de CPU, memoria y operaciones de entrada/salida por segundo (IOPS) del disco, y ajuste el tamaño según sea necesario.

## <a name="disks"></a>Discos

El costo se basa en la capacidad del disco aprovisionado. Las E/S por segundo y el rendimiento (es decir, la velocidad de transferencia de datos) dependen del tamaño de la máquina virtual, por lo que al aprovisionar un disco, debería tener en cuenta los tres factores (capacidad, E/S por segundo y rendimiento).

IOPS (operaciones de entrada y salida por segundo) de disco en Azure Stack Hub es una función del [tamaño de máquina virtual](./azure-stack-vm-sizes.md), en lugar del tipo de disco. Esto significa que para una VM de la serie Standard_Fs, independientemente de si elige SSD o HDD para el tipo de disco, el límite de IOPS de un único disco de datos adicional es de solo 2300 IOPS. El límite de IOPS impuesto (máximo posible) sirve para evitar vecinos ruidosos. IOPS no es una garantía de que obtendrá un tamaño específico de la máquina virtual.

También se recomienda usar [Managed Disks](./azure-stack-managed-disk-considerations.md). Managed Disks simplifica la administración de discos y controla el almacenamiento automáticamente. Los discos administrados no requieren una cuenta de almacenamiento. Solo debe especificar el tamaño y el tipo de disco, y se implementará como un recurso de alta disponibilidad.

El disco del sistema operativo es un disco duro virtual almacenado en Azure Stack Hub Blob Storage, por lo que se conserva incluso cuando la máquina host está inactiva. También se recomienda crear uno o varios [discos de datos](./azure-stack-manage-vm-disks.md), que son discos duros virtuales persistentes que se usan para los datos de aplicación. Cuando sea posible, instale las aplicaciones en un disco de datos, no en el disco del sistema operativo. Es posible que algunas aplicaciones heredadas deban instalar componentes en la unidad C:. En ese caso, puede [cambiar el tamaño del disco del sistema operativo](/azure/virtual-machines/windows/expand-os-disk) mediante PowerShell.

La máquina virtual también se crea con un disco temporal (la unidad D: de Windows). Este disco se almacena en un volumen temporal de la infraestructura de Azure Stack Hub Storage. Se puede eliminar durante los reinicios y otros eventos del ciclo de vida de la máquina virtual. Use este disco solo para datos temporales, como archivos de paginación o de intercambio.

## <a name="network"></a>Red

Los componentes de red incluyen los siguientes recursos:

-   **Red virtual**. Todas las máquinas virtuales se implementan en una red virtual que se puede dividir en varias subredes.

-   **Interfaz de red (NIC)** . La NIC permite que la VM se comunique con la red virtual. Si necesita varias tarjetas de interfaz de red para la máquina virtual, tenga en cuenta que hay un número máximo definido para cada [tamaño de máquina virtual](./azure-stack-vm-sizes.md).

-   **VIP/Dirección IP pública**. Es necesaria una dirección IP pública para comunicarse con la máquina virtual, por ejemplo, a través de Escritorio remoto (RDP). La dirección IP pública puede ser dinámica o estática. El valor predeterminado es dinámica.

-   Reserve una [dirección IP estática](/azure/virtual-network/virtual-networks-reserved-public-ip) si necesita una dirección IP fija que no cambie; por ejemplo, si tiene que crear un registro "A" en DNS o agregar la dirección IP a una lista segura.

-   También puede crear un nombre de dominio completo (FQDN) para la dirección IP. Después, puede registrar un [registro CNAME](https://en.wikipedia.org/wiki/CNAME_record) en DNS que apunte al nombre de dominio completo. Para más información, consulte [Crear un nombre de dominio completo en Azure Portal](/azure/virtual-machines/windows/portal-create-fqdn).

-   **Grupo de seguridad de red (NSG)** . Los NSG se utilizan para permitir o denegar el tráfico de red a las máquinas virtuales. Los grupos de seguridad de red se pueden asociar con subredes o con instancias de máquina virtual individuales.

Todos los NSG contienen un conjunto de [reglas predeterminadas](/azure/virtual-network/security-overview#default-security-rules), incluida una que bloquea todo el tráfico de entrada de Internet. No se puede eliminar las reglas predeterminadas, pero otras reglas pueden reemplazarlas. Para permitir el tráfico de Internet, cree reglas que permitan el tráfico entrante a puertos específicos; por ejemplo, el puerto 80 para HTTP. Para habilitar RDP, agregue una regla de NSG que permita el tráfico entrante al puerto TCP 3389.

## <a name="operations"></a>Operaciones

**Diagnóstico**. Habilite la supervisión y el diagnóstico, como las métricas básicas de estado, los registros de infraestructura de diagnóstico y los [diagnósticos de arranque](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Los diagnósticos de arranque pueden ayudarle a diagnosticar errores de arranque si la máquina virtual entra en un estado de imposibilidad de arranque. Cree una cuenta de Azure Storage para almacenar los registros. Una cuenta de almacenamiento con redundancia local (LRS) estándar es suficiente para este tipo de registros. Para más información, consulte [Habilitación de supervisión y diagnóstico](./azure-stack-metrics-azure-data.md).

**Disponibilidad**. Es posible que la máquina virtual esté sujeta a un reinicio debido a un mantenimiento planeado según lo programado por el operador de Azure Stack Hub. Para conseguir alta disponibilidad en un sistema de producción con varias máquinas virtuales en Azure, las máquinas virtuales se colocan en un [conjunto de disponibilidad](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) que las distribuye entre varios dominios de error y dominios de actualización. En la escala más pequeña de Azure Stack Hub, un dominio de error en un conjunto de disponibilidad se define como un único nodo en la unidad de escalado.  

Aunque la infraestructura de Azure Stack Hub ya es resistente a errores, la tecnología subyacente (los clústeres de conmutación por error) puede experimentar un cierto tiempo de inactividad de las máquinas virtuales ubicadas en un servidor físico en caso de error de hardware. Azure Stack Hub admite un conjunto de disponibilidad con un máximo de tres dominios de error para ser coherente con Azure.

|                   |             |
|-------------------|-------------|
| **Dominios de error** | Las máquinas virtuales colocadas en conjuntos de disponibilidad se aislarán físicamente entre sí al distribuirlas de la manera más uniforme que sea posible en varios dominios de error (nodos de Azure Stack Hub). Si se produce un error de hardware, las máquinas virtuales del dominio de error se reiniciarán en otros dominios de error. Se mantendrán en dominios de error independientes de las otras máquinas virtuales, pero en el mismo conjunto de disponibilidad, si es posible. Cuando el hardware vuelva a estar en línea, las máquinas virtuales se volverán a equilibrar para mantener la alta disponibilidad. |
| **Dominios de actualización**| Los dominios de actualización son otra manera que tiene Azure de proporcionar alta disponibilidad en los conjuntos de disponibilidad. Un dominio de actualización es un grupo lógico de hardware adyacente que puede someterse a mantenimiento al mismo tiempo. Las máquinas virtuales que se encuentran en el mismo dominio de actualización se reiniciarán en conjunto durante el mantenimiento planeado. Cuando los inquilinos crean máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Azure las distribuye de manera automática entre estos dominios de actualización. <br>En Azure Stack Hub, las máquinas virtuales se migran en vivo entre los otros hosts en línea del clúster antes de que se actualice su host subyacente. Como no hay tiempo de inactividad para el inquilino durante una actualización del host, la característica de actualización de dominio de Azure Stack Hub solo existe por motivos de compatibilidad de la plantilla con Azure. Las máquinas virtuales de un conjunto de disponibilidad muestran 0 como número de dominio de actualización en el portal. |

**Copias de seguridad** Para ver las recomendaciones sobre cómo proteger las máquinas virtuales de IaaS de Azure Stack Hub, consulte [Protección de las máquinas virtuales implementadas en Azure Stack Hub](azure-stack-manage-vm-protect.md).

**Detención de una máquina virtual**. Azure hace una distinción entre los estados "Detenido" y "Desasignado". Se le cobra cuando el estado de la máquina virtual se detiene, pero no cuando se desasigna la máquina virtual. En el portal de Azure Stack Hub, el botón **Detener** desasigna la máquina virtual. Si apaga desde dentro del sistema operativo mientras tiene la sesión iniciada, la VM se detiene pero **no** se desasigna, por lo que se le seguirá cobrando.

**Eliminación de una máquina virtual**. Si elimina una VM, no se eliminarán los discos de la VM. Esto significa que puede eliminar de forma segura la VM sin perder datos. Sin embargo, se le seguirá cobrando por el almacenamiento. Para eliminar el disco de la máquina virtual, elimine el objeto de disco administrado. Para evitar eliminaciones por error, use un *bloqueo de recurso* para bloquear el grupo de recursos completo o recursos individuales, como una máquina virtual.

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Incorpore sus máquinas virtuales a [Azure Security Center](/azure/security-center/quick-onboard-azure-stack) para una visión central del estado de la seguridad de sus recursos en Azure. Security Center supervisa los posibles problemas de seguridad y proporciona una imagen completa del estado de seguridad de su implementación. El Centro de seguridad se configura por cada suscripción de Azure. Habilite la colección de datos de seguridad que se describe en [Guía de inicio rápido: incorporación de su suscripción de Azure al nivel Estándar de Security Center](/azure/security-center/security-center-get-started). Una vez que habilite la recolección, el Centro de seguridad busca automáticamente las VM creadas en esa suscripción.

**Administración de revisiones**. Para configurar la administración de revisiones en la máquina virtual, consulte [este](./vm-update-management.md) artículo. Si está habilitada esta opción, Security Center comprueba si falta alguna actualización crítica y de seguridad. Use la [configuración de directiva de grupo](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) de la máquina virtual para habilitar las actualizaciones automáticas del sistema.

**Antimalware**. Si está habilitada esta opción, el Centro de seguridad comprueba si está instalado software antimalware. También puede Security Center para instalar software antimalware desde el Portal de Azure.

**Control de acceso**. Use el [control de acceso basado en rol (RBAC)](/azure/active-directory/role-based-access-control-what-is) para controlar el acceso a los recursos de Azure. RBAC le permite asignar roles de autorización a los miembros de su equipo de DevOps. Por ejemplo, el rol de lector puede ver recursos de Azure pero no crearlos, administrarlos o eliminarlos. Algunos permisos son específicos para un tipo de recurso de Azure. Por ejemplo, el rol Colaborador de la máquina virtual puede reiniciar o desasignar una máquina virtual, restablecer la contraseña de administrador, crear una nueva máquina virtual, etc. Otros [roles de RBAC integrados](/azure/active-directory/role-based-access-built-in-roles) que pueden resultar útiles para esta arquitectura son, por ejemplo, el de [Usuario de DevTest Labs](/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) y el de [Colaborador de la red](/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!NOTE]  
> RBAC no limita las acciones que puede realizar un usuario que ha iniciado sesión en una máquina virtual. Esos permisos están determinados por el tipo de cuenta en el sistema operativo invitado.

**Registros de auditoría**. Use los [registros de actividad](./azure-stack-metrics-azure-data.md?#activity-log) para ver las acciones de aprovisionamiento y otros eventos de la máquina virtual.

**Cifrado de datos**. Azure Stack Hub usa el cifrado AES de 128 bits de BitLocker para proteger los datos de la infraestructura y del usuario en reposo en el subsistema de almacenamiento. Para más información, consulte [Cifrado de datos en reposo en Azure Stack Hub](../operator/azure-stack-security-bitlocker.md).


## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de las máquinas virtuales de Azure Stack Hub, consulte [Características de las máquinas virtuales de Azure Stack Hub](azure-stack-vm-considerations.md).  
- Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](/azure/architecture/patterns).
