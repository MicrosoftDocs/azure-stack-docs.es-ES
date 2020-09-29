---
title: Inicio rápido para configurar un host de Azure Kubernetes Service en Azure Stack HCI mediante Windows PowerShell
description: Aprenda a configurar un host de Azure Kubernetes Service en Azure Stack HCI con Windows PowerShell.
author: jessicaguan
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: jeguan
ms.openlocfilehash: b4b128c5d51d7f916e0936102224283dd77a971d
ms.sourcegitcommit: 849be7ebd02a1e54e8d0ec59736c9917c67e309e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2020
ms.locfileid: "91134668"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Inicio rápido: Configuración de un host de Azure Kubernetes Service en Azure Stack HCI mediante PowerShell

> Se aplica a: Azure Stack HCI

En este inicio rápido, aprenderá a configurar un host de Azure Kubernetes Service en Azure Stack HCI mediante PowerShell. Si en su lugar quiere usar Windows Admin Center, consulte [Configuración mediante Windows Admin Center](setup.md).

## <a name="before-you-begin"></a>Antes de empezar

Antes de empezar, asegúrese de que tiene un clúster de Azure Stack HCI de entre 2 y 4 nodos o una instancia de Azure Stack HCI de un único nodo. **Le recomendamos tener un clúster de Azure Stack HCI de entre 2 y 4 nodos.** Si no lo tiene, siga las instrucciones sobre cómo conseguirlo que encontrará [aquí](./system-requirements.md).

También tendrá que asegurarse de que tiene instalado el módulo de PowerShell AksHci. El paquete de descarga que encontrará [aquí](https://aka.ms/AKS-HCI-Evaluate) tendrá el módulo en un archivo ZIP. Asegúrese de extraer el archivo ZIP en la ubicación correcta (`%systemdrive%\program files\windowspowershell\modules`) y, a continuación, ejecute el siguiente comando en una ventana administrativa de PowerShell.

   ```powershell
   Import-Module AksHci
   ```

Después de ejecutar el comando anterior, cierre todas las ventanas de PowerShell y vuelva a abrir una sesión administrativa para ejecutar los comandos en los pasos siguientes.

## <a name="step-1-prepare-your-machines-for-deployment"></a>Paso 1: preparar las máquinas para la implementación

En primer lugar, ejecutaremos varias comprobaciones en cada nodo físico para ver si se cumplen todos los requisitos para instalar Azure Kubernetes Service en Azure Stack HCI.

Abra PowerShell como administrador y ejecute el siguiente comando.

   ```powershell
   Initialize-AksHciNode
   ```

Una vez finalizadas las comprobaciones, verá el texto "Listo" en color verde.

## <a name="step-2-configure-your-deployment"></a>Paso 2: configurar la implementación

Establezca los valores de configuración del host de Azure Kubernetes Service. **Si tiene un clúster de Azure Stack HCI de entre 2 y 4 nodos, debe especificar el valor `MultiNode` en el elemento `-deploymentType` y los parámetros `wssdImageDir` y `cloudConfigLocation`.** En el caso de un clúster de Azure Stack HCI de un único nodo, todos los parámetros son opcionales y se establecen con sus valores predeterminados. Sin embargo, para obtener un rendimiento óptimo, **le recomendamos usar una implementación de clúster de Azure Stack HCI de entre 2 y 4 nodos.**

Configure la implementación con el siguiente comando.

   ```powershell
   Set-AksHciConfig [-deploymentType {SingleNode, MultiNode}]
                    [-wssdImageDir]
                    [-cloudConfigLocation]
                    [-nodeConfigLocation]
                    [-vnetName]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-sshPublicKey]
                    [-vipPoolStartIp]
                    [-vipPoolEndIp]
                    [-macPoolStart]
                    [-macPoolEnd]
                    [-wssdDir]
                    [-akshciVersion]
                    [-vnetType]
                    [-nodeAgentPort]
                    [-nodeAgentAuthorizerPort]
                    [-clusterRoleName]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="optional-parameters"></a>Parámetros opcionales

`-deploymentType`

Tipo de implementación. Valores aceptados: SingleNode y MultiNode. El valor predeterminado es SingleNode.

`-wssdImageDir`

Ruta de acceso al directorio donde Azure Kubernetes Service en Azure Stack HCI almacenará sus imágenes de VHD. El valor predeterminado es `%systemdrive%\wssdimagestore` para las implementaciones de un solo nodo. *En cuanto a las implementaciones de varios nodos, se debe especificar este parámetro*. La ruta de acceso debe apuntar a una ruta de acceso de almacenamiento compartida como  `C:\ClusterStorage\Volume2\ImageStore` o a un recurso compartido de SMB como  `\\FileShare\ImageStore`.

`-cloudConfigLocation`

Ubicación donde el agente de nube almacenará su configuración. El valor predeterminado es `%systemdrive%\wssdimagestore` para las implementaciones de un solo nodo. La ubicación puede ser la misma que la ruta de acceso del valor  `-wssdImageDir` anterior. En cuanto a las *implementaciones de varios nodos, se debe especificar este parámetro*.

`-nodeConfigLocation`

Ubicación en la que los agentes de nodo almacenarán su configuración. Debe ser una ruta de acceso local.

`-vnetName`

Nombre del conmutador virtual al que se conectarán las máquinas virtuales. El valor predeterminado es "External". Si el conmutador no existe, se creará.  

`-controlPlaneVmSize`

Tamaño de la máquina virtual que se va a crear para el plano de control. Para obtener una lista de los tamaños de máquina virtual disponibles, ejecute `Get-AksHciVmSize`.

`-loadBalancerVmSize`

Tamaño de la máquina virtual que se va a crear para las máquinas virtuales de Load Balancer. Para obtener una lista de los tamaños de máquina virtual disponibles, ejecute `Get-AksHciVmSize`.

`-sshPublicKey`

Ruta de acceso a un archivo de clave pública de SSH. Con esta clave pública, podrá iniciar sesión en cualquiera de las máquinas virtuales que cree la implementación de Azure Kubernetes Service en Azure Stack HCI. Si no se proporciona ninguna clave, buscaremos una en  `%systemdrive%\Users\<username>\.ssh\id_rsa.pub`. Si el archivo no existe, se generará y utilizará un par de claves de SSH en la ubicación anterior.  

`-vipPoolStartIp`

Al usar grupos VIP en la implementación, este parámetro especifica el inicio de red del grupo. El valor predeterminado es none.

`-vipPoolEndIp`

Al usar grupos VIP en la implementación, este parámetro especifica el extremo de la red del grupo. El valor predeterminado es none.

`-macPoolStart`

Se usa para especificar el inicio de la dirección MAC del grupo de direcciones MAC que quiere usar en la VM del host de Azure Kubernetes Service. La sintaxis de la dirección MAC requiere que el bit menos significativo del primer byte siempre sea 0. Asimismo, el primer byte siempre debe ser un número par (por ejemplo, 00, 02, 04, 06...). Una dirección MAC típica puede tener el siguiente aspecto: 02:1E:2B:78:00:00. El valor predeterminado es none.

`-macPoolEnd`

Se usa para especificar el final de la dirección MAC del grupo de direcciones MAC que quiere usar en la máquina virtual del host de Azure Kubernetes Service. La sintaxis de la dirección MAC requiere que el bit menos significativo del primer byte siempre sea 0. Asimismo, el primer byte siempre debe ser un número par (por ejemplo, 00, 02, 04, 06...). El primer byte de la dirección que se ha pasado como `-macPoolEnd` debe ser el mismo que el que se pasó como `-macPoolStart`. El valor predeterminado es none.

`-wssdDir`

Se trata de un directorio de trabajo para el módulo que se va a usar para almacenar archivos pequeños. El valor predeterminado es `%PROGRAMFILES%\AksHci` y no se debe cambiar en la mayoría de las implementaciones.  

`-akshciVersion`

Versión de Azure Kubernetes Service en Azure Stack HCI que quiere implementar. El valor predeterminado es la versión más reciente.

`-vnetType`

Tipo de conmutador virtual al que se va a conectar o que va a crear. El valor predeterminado es un tipo de conmutador "Externo".

`-nodeAgentPort`

Número de puerto TCP/IP que debe escuchar el elemento nodeagents. Su valor predeterminado es 45000.  

`-nodeAgentAuthorizerPort`

Número de puerto TCP/IP que el elemento nodeagents debe usar en el puerto de autorización. Su valor predeterminado es 45001.  

`-clusterRoleName`

Este elemento especifica el nombre que se va a usar al crear cloudagent como servicio genérico en del clúster. El valor predeterminado es un nombre único con un prefijo de tipo ca- y un sufijo GUID (por ejemplo: "ca-9e6eb299-bc0b-4f00-9fd7-942843820c26").

`-skipHostLimitChecks`

Este elemento solicita el script para omitir las comprobaciones que se realizan a fin de confirmar si la memoria y el espacio en disco están disponibles antes de permitir que la implementación continúe.

`-insecure`

Este elemento implementa componentes de Azure Kubernetes Service en Azure Stack HCI como cloudagent y nodeagent(s) en modo no seguro (no hay conexiones que estén protegidas con TLS).  No es aconsejable usar el modo no seguro en entornos de producción.

`-skipUpdates`

Use esta marca si quiere omitir cualquier actualización disponible.

`-forceDnsReplication`

La replicación de DNS puede tardar hasta una hora en completarse en algunos sistemas. Esto hará que la implementación sea lenta. Si tiene este problema, verá que el elemento Install-AksHci se bloqueará en un bucle. Para solucionar este problema, intente usar esta marca. Tenga en cuenta que la marca `-forceDnsReplication` no es una solución garantizada. Si se produce un error en la lógica subyacente a la marca, se ocultará el error y el comando se ejecutará como si no se hubiera proporcionado la marca.

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Restablecimiento de la configuración de Azure Kubernetes Service en Azure Stack HCI

Para restablecer la configuración de Azure Kubernetes Service en Azure Stack HCI, ejecute el siguiente comando. Si ejecuta este comando por su cuenta, se restablecerá la configuración a los valores predeterminados.

```powershell
Set-AksHciConfig
```

## <a name="step-3-start-a-new-deployment"></a>Paso 3: iniciar una nueva implementación

Una vez configurada la implementación, debe iniciarla. Se instalarán los agentes y servicios de Azure Kubernetes Service en Azure Stack HCI y el host de Azure Kubernetes Service.

Para comenzar la implementación, ejecute el siguiente comando.

```powershell
Install-AksHci
```

### <a name="check-your-deployed-clusters"></a>Comprobación de los clústeres implementados

Para obtener una lista de los hosts de Azure Kubernetes Service implementados, ejecute el siguiente comando. También podrá obtener los clústeres de Kubernetes mediante el mismo comando después de implementarlos.

```powershell
Get-AksHciCluster
```

## <a name="step-4-access-your-clusters-using-kubectl"></a>Paso 4: acceder a los clústeres mediante kubectl

Para acceder al host de Azure Kubernetes Service o al clúster de Kubernetes mediante kubectl, ejecute el siguiente comando. Se usará el archivo kubeconfig del clúster especificado como archivo kubeconfig predeterminado para kubectl.

```powershell
Set-AksHciKubeConfig -clusterName
```

## <a name="get-logs"></a>Obtención de registros

Para obtener los registros de todos los pods, ejecute el siguiente comando. Este comando creará una carpeta de salida comprimida denominada `akshcilogs` en la ruta de acceso `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

## <a name="reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Reinstalación de Azure Kubernetes Service en Azure Stack HCI

La reinstalación de Azure Kubernetes Service en Azure Stack HCI quitará todos los clústeres de Kubernetes (si los hubiera) y el host de Azure Kubernetes Service. También desinstalará de los nodos los agentes y servicios de Azure Kubernetes Service en Azure Stack HCI . A continuación, volverá a realizar los pasos del proceso de instalación original hasta que se vuelva a crear el host. Se conservarán tanto la configuración de Azure Kubernetes Service en Azure Stack HCI que realizó mediante `Set-AksHciConfig`, como las imágenes VHDX descargadas.

Para volver a instalar Azure Kubernetes Service en Azure Stack HCI, ejecute el siguiente comando.

```powershell
Restart-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Eliminación de Azure Kubernetes Service en Azure Stack HCI

Para quitar Azure Kubernetes Service en Azure Stack HCI, ejecute el siguiente comando.

```powershell
Uninstall-AksHci
```

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un clúster de Kubernetes para las aplicaciones](create-kubernetes-cluster-powershell.md)
