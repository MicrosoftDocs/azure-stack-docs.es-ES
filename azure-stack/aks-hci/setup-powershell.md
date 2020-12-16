---
title: Inicio rápido para configurar un host de Azure Kubernetes Service en Azure Stack HCI mediante Windows PowerShell
description: Aprenda a configurar un host de Azure Kubernetes Service en Azure Stack HCI con Windows PowerShell.
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 4211ec50ef0ea24ffb55f14791101c5d266ede2e
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612563"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Inicio rápido: Configuración de un host de Azure Kubernetes Service en Azure Stack HCI mediante PowerShell

> Se aplica a: Azure Stack HCI, Windows Server 2019 Datacenter

En este inicio rápido, aprenderá a configurar un host de Azure Kubernetes Service mediante PowerShell. Si en su lugar quiere usar Windows Admin Center, consulte [Configuración mediante Windows Admin Center](setup.md).

## <a name="before-you-begin"></a>Antes de empezar

Asegúrese de que dispone de lo siguiente:
 - Clúster de Azure Stack HCI de 2 a 4 nodos
 - Clúster de conmutación por error de Windows Server 2019 Datacenter
 - Windows Server 2019 Datacenter de un solo nodo 
 
Antes de empezar, asegúrese de que cumple todos los requisitos previos que se detallan en la página de [requisitos del sistema](.\system-requirements.md). 
**Le recomendamos tener un clúster de Azure Stack HCI de entre 2 y 4 nodos.** Si no tiene alguno de los requisitos anteriores, siga las instrucciones de la [página de registro de Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).    

   > [!IMPORTANT]
   > Al quitar Azure Kubernetes Service en Azure Stack HCl, consulte [Eliminación de Azure Kubernetes Service en Azure Stack HCl](#remove-azure-kubernetes-service-on-azure-stack-hci) y siga atentamente las instrucciones. 

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>Paso 1: Descargar e instalar el módulo AksHci de PowerShell

Descargue `AKS-HCI-Public-Preview-Dec-2020` de la [página de registro de Azure Kubernetes Service en Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). El archivo zip `AksHci.Powershell.zip` contiene el módulo de PowerShell.

Si ha instalado anteriormente Azure Kubernetes Service en Azure Stack HCI mediante PowerShell o Windows Admin Center, hay dos flujos de instalación para el nuevo módulo de PowerShell:
 - Realice una instalación limpia del módulo de PowerShell, de modo que empiece con un sistema limpio y se quiten las cargas de trabajo implementadas previamente. Para realizar una instalación limpia, vaya al paso 1.1.
 - Actualice el módulo de PowerShell si quiere mantener el sistema y las cargas de trabajo en su lugar. Para actualizar el módulo de PowerShell, vaya al paso 1.2.

### <a name="step-11-clean-install-of-the-akshci-powershell-module"></a>Paso 1.1: Instalación limpia del módulo AksHci de PowerShell

Ejecute el siguiente comando antes de continuar.
   ```powershell
   Uninstall-AksHci
   ```

**Cierre todas las ventanas de PowerShell.** Elimine los directorios existentes de AksHci, AksHci.UI, MOC y MSK8sDownloadAgent de la ruta de acceso `%systemdrive%\program files\windowspowershell\modules`. Una vez eliminados los directorios existentes, puede extraer el contenido del nuevo archivo ZIP. Asegúrese de extraer el archivo zip en la ubicación correcta (`%systemdrive%\program files\windowspowershell\modules`). A continuación, ejecute los siguientes comandos.

   ```powershell
   Import-Module AksHci
   ```

Cierre todas las ventanas de PowerShell de nuevo, vuelva a abrir una sesión administrativa y continúe con el paso 1.3: Validación del módulo actualizado de PowerShell.

### <a name="step-12-upgrade-the-akshci-powershell-module"></a>Paso 1.2: Actualización del módulo AksHci de PowerShell

**Cierre todas las ventanas de PowerShell.** Elimine los directorios existentes de AksHci, AksHci.UI, MOC y MSK8sDownloadAgent de la ruta de acceso `%systemdrive%\program files\windowspowershell\modules`. Una vez quitados los directorios, puede extraer el contenido del nuevo archivo ZIP. Asegúrese de extraer el archivo zip en la ubicación correcta (`%systemdrive%\program files\windowspowershell\modules`). A continuación, ejecute los siguientes comandos.

   ```powershell
   Import-Module AksHci
   ```
  
Después de ejecutar los comandos anteriores, cierre todas las ventanas de PowerShell y vuelva a abrir una sesión administrativa para validar la actualización del módulo de PowerShell tal y como se detalla a continuación. Después, ejecute el comando `Update-AksHci` como se indica más adelante en el documento.

## <a name="step-13-validate-upgraded-powershell-module"></a>Paso 1.3: Validación del módulo actualizado de PowerShell

**Cierre todas las ventanas de PowerShell** y vuelva a abrir una nueva sesión administrativa para comprobar si tiene la versión más reciente del módulo de PowerShell.  

   ```powershell
   Get-Command -Module AksHci
   ```
 
**Salida:**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.12     AksHci
Function        Get-AksHciCluster                                  0.2.12     AksHci
Function        Get-AksHciConfig                                   0.2.12     AksHci
Function        Get-AksHciCredential                               0.2.12     AksHci
Function        Get-AksHciKubernetesVersion                        0.2.12     AksHci
Function        Get-AksHciLogs                                     0.2.12     AksHci
Function        Get-AksHciUpdates                                  0.2.12     AksHci
Function        Get-AksHciVersion                                  0.2.12     AksHci
Function        Get-AksHciVmSize                                   0.2.12     AksHci
Function        Install-AksHci                                     0.2.12     AksHci
Function        Install-AksHciAdAuth                               0.2.12     AksHci
Function        Install-AksHciArcOnboarding                        0.2.12     AksHci
Function        New-AksHciCluster                                  0.2.12     AksHci
Function        Remove-AksHciCluster                               0.2.12     AksHci
Function        Restart-AksHci                                     0.2.12     AksHci
Function        Set-AksHciClusterNodeCount                         0.2.12     AksHci
Function        Set-AksHciConfig                                   0.2.12     AksHci
Function        Uninstall-AksHci                                   0.2.12     AksHci
Function        Uninstall-AksHciAdAuth                             0.2.12     AksHci
Function        Uninstall-AksHciArcOnboarding                      0.2.12     AksHci
Function        Update-AksHci                                      0.2.12     AksHci
Function        Update-AksHciCluster                               0.2.12     AksHci
```

## <a name="step-2-prepare-your-machines-for-deployment"></a>Paso 2: preparar las máquinas para la implementación

Ejecute varias comprobaciones en cada nodo físico para ver si se cumplen todos los requisitos para instalar Azure Kubernetes Service en Azure Stack HCI.

Abra PowerShell como administrador y ejecute el siguiente comando.

   ```powershell
   Initialize-AksHciNode
   ```

Una vez finalizadas las comprobaciones, verá el texto "Listo" en color verde.

## <a name="step-3-configure-your-deployment"></a>Paso 3: configurar la implementación

Establezca los valores de configuración del host de Azure Kubernetes Service. **Si va a realizar la implementación en un clúster Azure Stack HCI de 2 a 4 nodos o un clúster de conmutación por error de Windows Server 2019 Datacenter, debe especificar los parámetros `imageDir` y `cloudConfigLocation`.** En el caso de una instancia de Windows Server 2019 Datacenter de nodo único, todos los parámetros son opcionales y están definidos con sus valores predeterminados. Sin embargo, para obtener un rendimiento óptimo, **le recomendamos usar una implementación de clúster de Azure Stack HCI de entre 2 y 4 nodos.**

Configure la implementación con el siguiente comando.

   ```powershell
   Set-AksHciConfig [-imageDir <String>]
                    [-cloudConfigLocation <String>]
                    [-nodeConfigLocation <String>]
                    [-vnetName <String>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-sshPublicKey <String>]
                    [-vipPoolStartIp <String>]
                    [-vipPoolEndIp <String>]
                    [-macPoolStart <String>]
                    [-macPoolEnd <String>]
                    [-vlanID <int>]
                    [-kvaLoadBalancerType {unstacked_haproxy, stacked_kube_vip}]
                    [-kvaControlPlaneEndpoint <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerNoProxy <String>]
                    [-proxyServerCredential <PSCredential>]
                    [-cloudServiceCidr <String>]
                    [-workingDir <String>]
                    [-version <String>]
                    [-vnetType <String>]
                    [-nodeAgentPort <int>]
                    [-nodeAgentAuthorizerPort <int>]
                    [-clusterRoleName <String>]
                    [-cloudLocation <String>]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="example"></a>Ejemplo

Para implementar en un clúster de 2 a 4 nodos con redes DHCP:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
   ```

Para implementar con un grupo de direcciones IP virtuales:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vipPoolStartIp 10.0.0.20 -vipPoolEndIp 10.0.0.80
   ```

Para implementar con el equilibrador de carga `stacked_kube_vip`:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -kvaLoadBalancerType stacked_kube_vip -kvaControlPlaneEndpoint 10.0.1.10
   ```

Para implementar con un servidor proxy:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
   ```  

### <a name="optional-parameters"></a>Parámetros opcionales

`-imageDir`

Ruta de acceso al directorio donde Azure Kubernetes Service en Azure Stack HCI almacenará sus imágenes de VHD. El valor predeterminado es `%systemdrive%\AksHciImageStore` para las implementaciones de un solo nodo. *En cuanto a las implementaciones de varios nodos, se debe especificar este parámetro*. La ruta de acceso debe apuntar a una ruta de acceso de almacenamiento compartida como  `C:\ClusterStorage\Volume2\ImageStore` o a un recurso compartido de SMB como  `\\FileShare\ImageStore`.

`-cloudConfigLocation`

Ubicación donde el agente de nube almacenará su configuración. El valor predeterminado es `%systemdrive%\wssdcloudagent` para las implementaciones de un solo nodo. La ubicación puede ser la misma que la ruta de acceso del valor  `-imageDir` anterior. En cuanto a las *implementaciones de varios nodos, se debe especificar este parámetro*. La ruta de acceso debe apuntar a una ruta de acceso de almacenamiento compartida como  `C:\ClusterStorage\Volume2\ImageStore` o a un recurso compartido de SMB como  `\\FileShare\ImageStore`. La ubicación debe estar en un recurso compartido de alta disponibilidad para que el almacenamiento siempre esté accesible.

`-nodeConfigLocation`

Ubicación en la que los agentes de nodo almacenarán su configuración. Cada nodo tiene un agente de nodo, por lo que su configuración es local para este. Esta ubicación debe ser una ruta de acceso local. El valor predeterminado es `%systemdrive%\programdata\wssdagent` para todas las implementaciones.

`-vnetName`

Nombre del conmutador virtual al que se conectarán las máquinas virtuales. Si ya tiene un conmutador externo en el host, debe pasar aquí el nombre del conmutador. Si el conmutador no existe, se creará.El valor predeterminado es "External".  

`-controlPlaneVmSize`

Tamaño de la máquina virtual que se va a crear para el plano de control. Para obtener una lista de los tamaños de máquina virtual disponibles, ejecute `Get-AksHciVmSize`.

`-loadBalancerVmSize`

Tamaño de la máquina virtual que se va a crear para las máquinas virtuales de Load Balancer. Para obtener una lista de los tamaños de máquina virtual disponibles, ejecute `Get-AksHciVmSize`.

`-sshPublicKey`

Ruta de acceso a un archivo de clave pública de SSH. Con esta clave pública, podrá iniciar sesión en cualquiera de las máquinas virtuales que cree la implementación de Azure Kubernetes Service en Azure Stack HCI. Si tiene su propia clave pública SSH, pasará aquí su ubicación. Si no se proporciona ninguna clave, buscaremos una en `%systemdrive%\akshci\.ssh\akshci_rsa.pub`. Si el archivo no existe, se generará y utilizará un par de claves SSH en la ubicación anterior.

`-vipPoolStartIp`

Al usar grupos VIP en la implementación, este parámetro especifica el inicio de red del grupo. Debe usar grupos VIP para las implementaciones de larga duración con el fin de garantizar que un grupo de direcciones IP siga siendo coherente. Esto resulta útil si tiene cargas de trabajo que siempre deben estar accesibles. El valor predeterminado es none.

`-vipPoolEndIp`

Al usar grupos VIP en la implementación, este parámetro especifica el extremo de la red del grupo. Debe usar grupos VIP para las implementaciones de larga duración con el fin de garantizar que un grupo de direcciones IP siga siendo coherente. Esto resulta útil si tiene cargas de trabajo que siempre deben estar accesibles. El valor predeterminado es none.

`-macPoolStart` 

Se usa para especificar el inicio de la dirección MAC del grupo de direcciones MAC que quiere usar en la VM del host de Azure Kubernetes Service. La sintaxis de la dirección MAC requiere que el bit menos significativo del primer byte siempre sea 0. Asimismo, el primer byte siempre debe ser un número par (por ejemplo, 00, 02, 04, 06...). Una dirección MAC típica puede tener el siguiente aspecto: 02:1E:2B:78:00:00. Use grupos de direcciones MAC para las implementaciones de larga duración para que las direcciones MAC asignadas sean coherentes. Esto resulta útil si tiene un requisito de que las VM tengan direcciones MAC específicas. El valor predeterminado es none.

`-macPoolEnd`

Se usa para especificar el final de la dirección MAC del grupo de direcciones MAC que quiere usar en la máquina virtual del host de Azure Kubernetes Service. La sintaxis de la dirección MAC requiere que el bit menos significativo del primer byte siempre sea 0. Asimismo, el primer byte siempre debe ser un número par (por ejemplo, 00, 02, 04, 06...). El primer byte de la dirección que se ha pasado como `-macPoolEnd` debe ser el mismo que el que se pasó como `-macPoolStart`. Use grupos de direcciones MAC para las implementaciones de larga duración para que las direcciones MAC asignadas sean coherentes. Esto resulta útil si tiene un requisito de que las VM tengan direcciones MAC específicas. El valor predeterminado es none.

`-vlandID`

Este se puede usar para especificar un identificador de VLAN de red. El host de Azure Kubernetes Service y los adaptadores de red de VM del clúster de Kubernetes se etiquetarán con el identificador de VLAN proporcionado. Debe usarse si hay un id. de VLAN específico que debe etiquetarse para obtener la conectividad correcta. El valor predeterminado es none.

`-kvaLoadBalancerType`

Se toma `unstacked_haproxy` o `stacked_kube_vip`. `unstacked_haproxy` es el valor predeterminado en el que se implementa una VM de equilibrador de carga independiente con HAProxy como punto de conexión del servidor de la API del host de Azure Kubernetes Service. `stacked_kube_vip` es una solución de equilibrador de carga [Kubevip](https://kube-vip.io/) para el host de Azure Kubernetes Service. Permite especificar una dirección IP estática en el host como dirección IP flotante en los nodos del plano de control para mantener el servidor de la API de alta disponibilidad mediante la dirección IP. Si se elige esta opción, debe especificar la dirección IP estática en el parámetro `kvaControlPlaneEndpoint` y no se implementará ninguna VM de equilibrador de carga independiente.

`stacked_kube_vip` requiere una dirección IP y consume menos recursos, lo que permite ahorrar memoria, uso de CPU y tiempo de implementación. Si no tiene ninguna dirección IP que usar como IP flotante, debe usar `unstacked_haproxy`. Esta última opción requiere una VM de equilibrador de carga. 

`-kvaControlPlaneEndpoint`

Especifica la dirección IP estática que se usará como dirección del servidor de la API de host de Azure Kubernetes Service cuando el parámetro `kvaLoadBalancerType` esté establecido en `stacked_kube_vip`. Si se usa `stacked_kube_vip`, es necesario especificar este parámetro.

`-proxyServerHTTP`

Esto proporciona un URI de servidor proxy que deben usar todos los componentes que necesitan llegar a los puntos de conexión HTTP. El formato de URI incluye el esquema de URI, la dirección del servidor y el puerto (es decir, https://server.com:8888) ). El valor predeterminado es none.

`-proxyServerHTTPS`

Esto proporciona un URI de servidor proxy que deben usar todos los componentes que necesitan llegar a los puntos de conexión HTTPS. El formato de URI incluye el esquema de URI, la dirección del servidor y el puerto (es decir, https://server.com:8888) ). El valor predeterminado es none.

`-proxyServerNoProxy`

Se trata de una cadena delimitada por comas de direcciones que se van a excluir del proxy. El valor predeterminado es `localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16`. Esto excluye el tráfico de localhost (localhost, 127.0.0.1), el tráfico del servicio de Kubernetes interno (.svc), el CIDR del servicio Kubernetes (10.96.0.0/12) y el CIDR del POD Kubernetes (10.244.0.0/16) del servidor proxy. Puede usar este parámetro para agregar más exenciones de nombres o intervalos de subred. **La configuración de este parámetro es muy importante porque, si no se configura correctamente, puede redirigir inesperadamente el tráfico del clúster de Kubernetes interno al proxy. Esto puede provocar varios errores en la comunicación de red.**


`-proxyServerCredential`

Esto proporciona el nombre de usuario y la contraseña para autenticarse en los servidores proxy HTTP/HTTPS. Puede usar `Get-Credential` para generar un objeto `PSCredential` para pasarlo a este parámetro. El valor predeterminado es none.

`-cloudServiceCidr`

Se puede usar para proporcionar un prefijo de dirección IP estática o de red que se asignará al servicio MOC CloudAgent. Este valor debe proporcionarse con el formato CIDR. (Por ejemplo: 192.168.1.2/16). Puede que quiera especificar esto para asegurarse de que cualquier cosa importante en la red esté siempre accesible, ya que la dirección IP no cambiará. El valor predeterminado es none.

`-workingDir`

Se trata de un directorio de trabajo para el módulo que se va a usar para almacenar archivos pequeños. El valor predeterminado es `%PROGRAMFILES%\AksHci` y no se debe cambiar en la mayoría de las implementaciones. No se recomienda cambiar el valor predeterminado. 

`-version`

Versión de Azure Kubernetes Service en Azure Stack HCI que quiere implementar. El valor predeterminado es la versión más reciente. No se recomienda cambiar el valor predeterminado.

`-vnetType`

Tipo de conmutador virtual al que se va a conectar o que va a crear. El valor predeterminado es un tipo de conmutador "Externo". No se recomienda cambiar el valor predeterminado.

`-nodeAgentPort`

Número de puerto TCP/IP que deben escuchar los agentes del nodo. Su valor predeterminado es 45000. No se recomienda cambiar el valor predeterminado. 

`-nodeAgentAuthorizerPort`

Número de puerto TCP/IP que los agentes del nodo deben usar para el puerto de autorización. Su valor predeterminado es 45001. No se recomienda cambiar el valor predeterminado.  

`-clusterRoleName`

Este elemento especifica el nombre que se va a usar al crear un agente en la nube como servicio genérico en el clúster. El valor predeterminado es un nombre único con un prefijo de tipo ca- y un sufijo GUID (por ejemplo: "ca-9e6eb299-bc0b-4f00-9fd7-942843820c26"). No se recomienda cambiar el valor predeterminado.

`-cloudLocation` 

Este parámetro proporciona un nombre de ubicación en la nube operado por Microsoft. El nombre predeterminado es "MocLocation". No se recomienda cambiar el valor predeterminado.

`-skipHostLimitChecks`

Este elemento solicita el script para omitir las comprobaciones que se realizan a fin de confirmar si la memoria y el espacio en disco están disponibles antes de permitir que la implementación continúe. No se recomienda usar esta configuración.

`-insecure`

Este elemento implementa componentes de Azure Kubernetes Service en Azure Stack HCI, como agentes en la nube y en nodos en modo no seguro (conexiones sin protección TLS).  No es aconsejable usar el modo no seguro en entornos de producción.

`-skipUpdates`

Use esta marca si quiere omitir cualquier actualización disponible. No se recomienda usar esta configuración.

`-forceDnsReplication`

La replicación de DNS puede tardar hasta una hora en completarse en algunos sistemas. Esto hará que la implementación sea lenta. Si tiene este problema, verá que el elemento Install-AksHci se bloqueará en un bucle. Para solucionar este problema, intente usar esta marca. Tenga en cuenta que la marca `-forceDnsReplication` no es una solución garantizada. Si se produce un error en la lógica subyacente a la marca, se ocultará el error y el comando se ejecutará como si no se hubiera proporcionado la marca. 

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Restablecimiento de la configuración de Azure Kubernetes Service en Azure Stack HCI

Para restablecer la configuración de Azure Kubernetes Service en Azure Stack HCI, ejecute los siguientes comandos. Si ejecuta este comando por su cuenta, se restablecerá la configuración a los valores predeterminados.

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>Paso 4: iniciar una nueva implementación

Una vez configurada la implementación, debe iniciarla. Se instalarán los agentes y servicios de Azure Kubernetes Service en Azure Stack HCI y el host de Azure Kubernetes Service.

Para comenzar la implementación, ejecute el siguiente comando.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Comprobación de que el host de Azure Kubernetes Service está implementado

Para asegurarse de que el host de Azure Kubernetes Service esté implementado, ejecute el siguiente comando. También podrá obtener los clústeres de Kubernetes mediante el mismo comando después de implementarlos.

```powershell
Get-AksHciCluster
```

**Salida:**
```

Name            : clustergroup-management
Version         : v1.18.8
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>Paso 5: acceder a los clústeres mediante kubectl

Para acceder al host de Azure Kubernetes Service o al clúster de Kubernetes mediante kubectl, ejecute el siguiente comando. Se usará el archivo kubeconfig del clúster especificado como archivo kubeconfig predeterminado para kubectl.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Ejemplo

```powershell
Get-AksHciCredential -clusterName clustergroup-management
```

### <a name="required-parameters"></a>Parámetros obligatorios

`clusterName`

Nombre del clúster.

### <a name="optional-parameters"></a>Parámetros opcionales

`outputLocation`

La ubicación en la que quiere descargar kubeconfig. El valor predeterminado es `%USERPROFILE%\.kube`.

## <a name="get-logs"></a>Obtención de registros

Para obtener los registros de todos los pods, ejecute el siguiente comando. Este comando creará una carpeta de salida comprimida denominada `akshcilogs` en la ruta de acceso `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>Actualización a la versión más reciente de Azure Kubernetes Service en Azure Stack HCI

Para actualizar a la versión más reciente de Azure Kubernetes Service en Azure Stack HCI, ejecute el comando siguiente. El comando update solo funciona si ha instalado la versión de octubre. No funcionará en versiones anteriores a la de octubre. El comando update actualiza el host de Azure Kubernetes Service y la plataforma local en la nube operada por Microsoft. En esta versión preliminar, la versión de Kubernetes y la versión del sistema operativo del host de AKS siguen siendo las mismas. Este comando no actualiza ningún clúster de carga de trabajo existente. Los nuevos clústeres de carga de trabajo creados después de actualizar el host de AKS serán distintos de los clústeres de carga de trabajo existentes en cuanto a la versión de SO del nodo de Windows y la versión de Kubernetes.

   ```powershell
   Update-AksHci
   ```
   
Se recomienda actualizar los clústeres de carga de trabajo inmediatamente después de actualizar el clúster de administración para evitar la ejecución de versiones del sistema operativo Windows Server no compatibles en los clústeres de Kubernetes con nodos de Windows. Para actualizar el clúster de carga de trabajo, visite [Actualización del clúster de la carga de trabajo](create-kubernetes-cluster-powershell.md).

## <a name="restart-azure-kubernetes-service-on-azure-stack-hci"></a>Reinicio de Azure Kubernetes Service en Azure Stack HCI

El reinicio de Azure Kubernetes Service en Azure Stack HCI quitará todos los clústeres de Kubernetes (si los hubiera) y el host de Azure Kubernetes Service. También desinstalará de los nodos los agentes y servicios de Azure Kubernetes Service en Azure Stack HCI . A continuación, volverá a realizar los pasos del proceso de instalación original hasta que se vuelva a crear el host. Se conservarán tanto la configuración de Azure Kubernetes Service en Azure Stack HCI que realizó mediante `Set-AksHciConfig`, como las imágenes VHDX descargadas.

Para reiniciar Azure Kubernetes Service en Azure Stack HCI con la misma configuración, ejecute el siguiente comando.

```powershell
Restart-AksHci
```

## <a name="reset-configuration-settings-and-reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Restablezca las opciones de configuración y vuelva a instalar Azure Kubernetes Service en Azure Stack HCI.

Para reinstalar Azure Kubernetes Service en Azure Stack HCI con otra configuración, ejecute antes el siguiente comando.

```powershell
Uninstall-AksHci
```

Después de ejecutar el comando anterior, puede cambiar los valores de configuración con el siguiente comando. Los parámetros siguen siendo los mismos que se describen en el paso 3. Si ejecuta este comando sin parámetros especificados, se restablecerán los valores predeterminados de los parámetros.

```powershell
Set-AksHciConfig
```

Después de cambiar la configuración a la configuración deseada, ejecute el siguiente comando para volver a instalar Azure Stack Kubernetes en Azure Stack HCl.

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Eliminación de Azure Kubernetes Service en Azure Stack HCI

Para quitar Azure Kubernetes Service en Azure Stack HCI, ejecute el siguiente comando. **Si usa PowerShell para desinstalar una implementación de Windows Admin Center, debe ejecutar el comando con la marca `-Force`.**

```powershell
Uninstall-AksHci
```

Después de ejecutar el comando anterior, puede ejecutar el comando `Install-AksHci` para instalar el host de Azure Kubernetes Service con la misma configuración que antes. Si quiere cambiar la configuración, ejecute `Set-AksHciConfig` con los cambios que quiera hacer antes de ejecutar el comando de instalación.

Si no quiere conservar la configuración anterior, ejecute el siguiente comando.

```powershell
Uninstall-AksHci -Force
```

Si los comandos de PowerShell se ejecutan en un clúster en el que solía usarse Windows Admin Center para la implementación, el módulo de PowerShell comprueba la existencia del archivo de configuración de Windows Admin Center. Windows Admin Center coloca el archivo de configuración de Windows Admin Center en todos los nodos. **Si usa el comando uninstall y vuelve a Windows Admin Center, ejecute el comando uninstall anterior con la marca `-Force`. Si no se hace esto, PowerShell y Windows Admin Center no estarán sincronizados.**

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un clúster de Kubernetes para las aplicaciones](create-kubernetes-cluster-powershell.md)
