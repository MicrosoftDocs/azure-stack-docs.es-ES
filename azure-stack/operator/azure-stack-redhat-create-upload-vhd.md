---
title: Preparación de una máquina virtual basada en Red Hat para Azure Stack Hub | Microsoft Docs
titleSuffix: Azure Stack Hub
description: Aprenda a crear y cargar un disco duro virtual (VHD) de Azure que contiene un sistema operativo Red Hat Linux.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: 2ac894211b1ccd8d1537453100879ad695507268
ms.sourcegitcommit: 320eddb281a36d066ec80d67b103efad7d4f33c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2020
ms.locfileid: "76145825"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Preparación de una máquina virtual basada en Red Hat para Azure Stack Hub

En este artículo, aprenderá a preparar una máquina virtual de Red Hat Enterprise Linux (RHEL) para usarla en Azure Stack Hub. Las versiones de RHEL que se tratan en este artículo son 7.1+. Los hipervisores de preparación que se tratan en este artículo son Hyper-V, máquina virtual basada en kernel (KVM) y VMware.

Para información de soporte técnico de Red Hat Enterprise Linux, consulte [Red Hat y Azure Stack Hub: Frequently Asked Questions](https://access.redhat.com/articles/3413531) (Red Hat y Azure Stack: preguntas más frecuentes).

## <a name="prepare-a-red-hat-based-vm-from-hyper-v-manager"></a>Preparación de una máquina virtual basada en Red Hat desde el Administrador de Hyper-V

En esta sección se supone que ya tiene un archivo ISO en el sitio web de Red Hat y que instaló la imagen RHEL en un disco duro virtual (VHD). Para más información sobre cómo usar el Administrador de Hyper-V para instalar una imagen de sistema operativo, consulte el artículo sobre cómo [instalar el rol de Hyper-V y configurar una máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>Notas de instalación de RHEL

* Azure Stack Hub no admite el formato VHDX. Azure solo admite VHD fijo. Puede usar el Administrador de Hyper-V para convertir el disco al formato VHD, o puede usar el cmdlet convert-vhd. Si usa VirtualBox, seleccione **Tamaño fijo** a diferencia de la opción predeterminada asignada dinámicamente al crear el disco.
* Azure Stack Hub solo admite máquinas virtuales de la generación 1. Puede convertir una máquina virtual de generación 1 de VHDX al formato de archivo VHD y de un disco de expansión dinámica a otro de tamaño fijo. No puede cambiar la generación de una máquina virtual. Para más información, consulte [¿Debería crear una máquina virtual de generación 1 o 2 en Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* El tamaño máximo permitido para los discos duros virtuales es de 1023 GB.
* Al instalar el sistema operativo Linux se recomienda usar las particiones estándar en lugar de Logical Volume Manager (LVM) que a menudo viene de forma predeterminada en muchas instalaciones. Esta práctica evita los conflictos de nombres LVM con máquinas virtuales clonadas, especialmente si alguna vez necesita conectar un disco de sistema operativo a otra máquina virtual idéntica para solucionar el problema.
* Se requiere la compatibilidad de kernel para el montaje de sistemas de archivos de formato de disco universal (UDF). En el primer arranque, los medios con formato UDF conectados al invitado pasan la configuración de aprovisionamiento a la máquina virtual Linux. El agente Linux de Azure debe montar el sistema de archivos UDF para leer su configuración y aprovisionar la máquina virtual.
* No configure una partición de intercambio en el disco del sistema operativo. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos siguientes.
* En Azure, todos los discos duros virtuales deben tener un tamaño virtual alineado con 1 MB. Al convertir un disco sin formato en un disco duro virtual, tiene que asegurarse de que su tamaño es un múltiplo de 1 MB antes de la conversión. Se puede encontrar más información en los siguientes pasos.
* Azure Stack Hub admite cloud-init. [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Dado que se llama a cloud-init durante el proceso de arranque inicial, no se necesitan pasos adicionales ni agentes para aplicar la configuración. Para obtener instrucciones sobre cómo agregar cloud-init a la imagen, consulte [Preparación de la imagen de máquina virtual Linux en Azure para usarse con cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/cloudinit-prepare-custom-image).

### <a name="prepare-an-rhel-7-vm-from-hyper-v-manager"></a>Preparación de una máquina virtual de RHEL 7 desde el Administrador de Hyper-V

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

1. Seleccione **Conectar** para abrir una ventana de consola de la máquina virtual.

1. Cree o edite el archivo `/etc/sysconfig/network` y agregue el siguiente texto:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto según sea necesario:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Ejecute el comando siguiente para asegurarse de que el servicio de red se inicia en el arranque:

    ```bash
    sudo systemctl enable network
    ```

1. Registre la suscripción de Red Hat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para realizar esta modificación, abra `/etc/default/grub` en un editor de texto y modifique el parámetro `GRUB_CMDLINE_LINUX`. Por ejemplo:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Esta modificación garantiza que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración. Esta configuración también desactiva las nuevas convenciones de nomenclatura de RHEL 7 para NIC.

   Los arranques gráfico y silencioso no resultan útiles en un entorno en la nube en el que queremos que todos los registros se envíen al puerto serie. Puede dejar la opción `crashkernel` configurada si lo desea. Este parámetro reduce la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual podría resultar un problema en tamaños de máquina virtual más pequeños. Se recomienda quitar los parámetros siguientes:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Una vez que termine de editar `/etc/default/grub`, ejecute el comando siguiente para recompilar la configuración de GRUB:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. [Opcional después de la versión 1910] Detenga y desinstale cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque, que suele ser el predeterminado. Modifique `/etc/ssh/sshd_config` para que incluya la siguiente línea:

    ```sh
    ClientAliveInterval 180
    ```

1. Al crear un disco duro virtual personalizado para Azure Stack Hub, tenga en cuenta que las versiones de WALinuxAgent entre 2.2.20 y 2.2.35 (ambas no inclusive) no funcionan en los entornos de Azure Stack Hub anteriores a la versión 1910. Puede usar las versiones 2.2.20/2.2.35 para preparar su imagen. Para usar versiones posteriores a 2.2.35 para preparar la imagen personalizada, actualice Azure Stack Hub a la versión 1903 y posteriores, o aplique la revisión 1901/1902.
    
    [Antes de la versión 1910] Siga estas instrucciones para descargar un WALinuxAgent compatible:
    
    1. Descargue setuptools.
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
    
    1. Descargue y descomprima la versión 2.2.20 del agente desde nuestro GitHub.

        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```

    1. Instale setup.py.

        ```bash
        sudo python setup.py install
        ```

    1. Reinicie waagent.
    
        ```bash
        sudo systemctl restart waagent
        ```

    1. Pruebe si la versión del agente coincide con la que descargó. En este ejemplo debería ser 2.2.20.

        ```bash
        waagent -version
        ```

    [Después de la versión 1910] Siga estas instrucciones para descargar un WALinuxAgent compatible:
    
    1. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

    1. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
    

1. No cree un espacio de intercambio en el disco del sistema operativo.

    El agente Linux de Azure puede configurar automáticamente un espacio de intercambio mediante el disco de recursos local que se conecta a la máquina virtual una vez que esta se aprovisiona en Azure. El disco de recursos local es un disco temporal que podría vaciarse si la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Si quiere anular el registro de la suscripción, ejecute el siguiente comando:

    ```bash
    sudo subscription-manager unregister
    ```

1. Si usa un sistema que se implementó con una entidad de certificación empresarial, la máquina virtual de RHEL no confiará en el certificado raíz de Azure Stack Hub. Se debe colocar en el almacén raíz de confianza. Para más información, consulte el artículo sobre cómo [agregar certificados raíz de confianza al servidor](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para el aprovisionamiento en Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Seleccione **Acción**  > **Apagar** en el Administrador de Hyper-V.

1. Convierta el VHD en un VHD de tamaño fijo con la característica "Editar disco" del Administrador de Hyper-V o con el comando Convert-VHD de PowerShell. El VHD de Linux ya está listo para cargarse en Azure.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparación de una máquina virtual basada en Red Hat desde KVM

1. Descargue la imagen KVM de RHEL 7 desde el sitio web de Red Hat. Este procedimiento utiliza RHEL 7 como el ejemplo.

1. Establezca una contraseña raíz.

    Genere una contraseña cifrada y copie el resultado del comando:

    ```bash
    openssl passwd -1 changeme
    ```

   Establezca una contraseña raíz con guestfish:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Cambie el segundo campo de usuario raíz de "!!" a la contraseña cifrada.

1. Cree una máquina virtual en KVM a partir de la imagen qcow2. Establezca el tipo de disco en **qcow2** y el modelo de dispositivo de interfaz de red virtual en **virtio**. Después, inicie la máquina virtual e inicie sesión como raíz.

1. Cree o edite el archivo `/etc/sysconfig/network` y agregue el siguiente texto:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Ejecute el comando siguiente para asegurarse de que el servicio de red se inicia en el arranque:

    ```bash
    sudo systemctl enable network
    ```

1. Registre la suscripción de RedHat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para realizar esta configuración, abra `/etc/default/grub` en un editor de texto y modifique el parámetro `GRUB_CMDLINE_LINUX`. Por ejemplo:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Este comando también asegura que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. El comando también desactiva las nuevas convenciones de nomenclatura de RHEL 7 para NIC.

   Los arranques gráfico y silencioso no resultan útiles en un entorno en la nube en el que todos los registros se envían al puerto serie. Puede dejar la opción `crashkernel` configurada si lo desea. Este parámetro reduce la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual podría resultar un problema en tamaños de máquina virtual más pequeños. Se recomienda quitar los parámetros siguientes:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Una vez que termine de editar `/etc/default/grub`, ejecute el comando siguiente para recompilar la configuración de GRUB:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Agregue módulos de Hyper-V a initramfs.

    Edite `/etc/dracut.conf` y agregue contenido:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Recompile initramfs:

    ```bash
    dracut -f -v
    ```

1. [Opcional después de la versión 1910] Detenga y desinstale cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Asegúrese de que el servidor SSH esté instalado y configurado para iniciarse en el tiempo de arranque:

    ```bash
    systemctl enable sshd
    ```

    Modifique /etc/ssh/sshd_config para que incluya las siguientes líneas:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Al crear un disco duro virtual personalizado para Azure Stack Hub, tenga en cuenta que las versiones de WALinuxAgent entre 2.2.20 y 2.2.35 (ambas no inclusive) no funcionan en los entornos de Azure Stack Hub anteriores a la versión 1910. Puede usar las versiones 2.2.20/2.2.35 para preparar su imagen. Para usar versiones posteriores a 2.2.35 para preparar la imagen personalizada, actualice Azure Stack Hub a la versión 1903 y posteriores, o aplique la revisión 1901/1902.

    [Antes de la versión 1910] Siga estas instrucciones para descargar un WALinuxAgent compatible:

    1. Descargue setuptools.
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Descargue y descomprima la versión 2.2.20 del agente desde nuestro GitHub.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Instale setup.py.
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Reinicie waagent.
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Pruebe si la versión del agente coincide con la que descargó. En este ejemplo debería ser 2.2.20.
        
        ```bash
        waagent -version
        ```
        
    [Después de la versión 1910] Siga estas instrucciones para descargar un WALinuxAgent compatible:
    
    1. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

        1. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

            ```bash
            sudo yum install WALinuxAgent
            sudo systemctl enable waagent.service
            ```

1. No cree un espacio de intercambio en el disco del sistema operativo.

    El agente Linux de Azure puede configurar automáticamente un espacio de intercambio mediante el disco de recursos local que se conecta a la máquina virtual una vez que esta se aprovisiona en Azure. El disco de recursos local es un disco temporal que podría vaciarse si la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

    ```bash
    subscription-manager unregister
    ```

1. Si usa un sistema que se implementó con una entidad de certificación empresarial, la máquina virtual de RHEL no confiará en el certificado raíz de Azure Stack Hub. Se debe colocar en el almacén raíz de confianza. Para más información, consulte el artículo sobre cómo [agregar certificados raíz de confianza al servidor](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para el aprovisionamiento en Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Apague la máquina virtual en KVM.

1. Convierta la imagen qcow2 al formato VHD.

    > [!NOTE]
    > Hay un problema conocido en versiones de qemu-img >=2.2.1 que da como resultado un VHD con formato incorrecto. El problema se corrigió en QEMU 2.6. Se recomienda usar qemu-img 2.2.0 o anterior, o bien actualizar a la versión 2.6 o posterior. Referencia: https://bugs.launchpad.net/qemu/+bug/1490611.

    Primero convierta la imagen al formato raw:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. De lo contrario, redondee hacia arriba el tamaño para alinear con 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Convierta el disco sin procesar en un disco duro virtual (VHD) de tamaño fijo:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    O bien, con la versión de qemu **2.6 o posterior**, incluya la opción `force_size`:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-vmware"></a>Preparación de una máquina virtual basada en Red Hat desde VMware

En esta sección se supone que ya instaló una máquina virtual RHEL en VMware. Para más información acerca de cómo instalar un sistema operativo en VMware, consulte [VMware Guest Operating System Installation Guide](https://aka.ms/aa6z600)(Guía de instalación de sistema operativo invitado de VMware).

* Al instalar el sistema Linux se recomienda usar las particiones estándar en lugar de un LVM, que a menudo viene de forma predeterminada en muchas instalaciones. Este método impide que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de sistema operativo a otra máquina virtual para solucionar problemas. Para los discos de datos se puede utilizar LVM o RAID si así se prefiere.
* No configure una partición de intercambio en el disco del sistema operativo. Es posible configurar el agente de Linux para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información sobre esta configuración en los pasos siguientes.
* Cuando cree el disco duro virtual, seleccione **Almacenar disco virtual como un solo archivo**.

### <a name="prepare-an-rhel-7-vm-from-vmware"></a>Preparación de una máquina virtual RHEL 7 desde VMware

1. Cree o edite el archivo `/etc/sysconfig/network` y agregue el siguiente texto:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

    ```bash
    sudo chkconfig network on
    ```

1. Registre la suscripción de Red Hat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para realizar esta modificación, abra `/etc/default/grub` en un editor de texto y modifique el parámetro `GRUB_CMDLINE_LINUX`. Por ejemplo:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Esta configuración también asegura que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto también desactiva las nuevas convenciones de nomenclatura de RHEL 7 para NIC. Se recomienda quitar los parámetros siguientes:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Los arranques gráfico y silencioso no resultan útiles en un entorno en la nube en el que queremos que todos los registros se envíen al puerto serie. Puede dejar la opción `crashkernel` configurada si lo desea. Este parámetro reduce la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual podría resultar un problema en tamaños de máquina virtual más pequeños.

1. Una vez que termine de editar `/etc/default/grub`, ejecute el comando siguiente para recompilar la configuración de GRUB:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Agregue módulos de Hyper-V a initramfs.

    Edite `/etc/dracut.conf`y agregue contenido:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Recompile initramfs:

    ```bash
    dracut -f -v
    ```

1. [Opcional después de la versión 1910] Detenga y desinstale cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Esta configuración es normalmente el valor predeterminado. Modifique `/etc/ssh/sshd_config` para que incluya la siguiente línea:

    ```sh
    ClientAliveInterval 180
    ```

1. Al crear un disco duro virtual personalizado para Azure Stack Hub, tenga en cuenta que las versiones de WALinuxAgent entre 2.2.20 y 2.2.35 (ambas no inclusive) no funcionan en los entornos de Azure Stack Hub anteriores a la versión 1910. Puede usar las versiones 2.2.20/2.2.35 para preparar su imagen. Para usar versiones posteriores a 2.2.35 para preparar la imagen personalizada, actualice Azure Stack Hub a la versión 1903 y posteriores, o aplique la revisión 1901/1902.

    [Antes de la versión 1910] Siga estas instrucciones para descargar un WALinuxAgent compatible:

    1. Descargue setuptools.
    
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Descargue y descomprima la versión 2.2.20 del agente desde nuestro GitHub.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Instale setup.py.
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Reinicie waagent.
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Pruebe si la versión del agente coincide con la que descargó. En este ejemplo debería ser 2.2.20.
        
        ```bash
        waagent -version
        ```
        
    [Después de la versión 1910] Siga estas instrucciones para descargar un WALinuxAgent compatible:
    
    1. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

    1. Instale el Agente de Linux de Azure ejecutando el comando siguiente:
        
        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
        
1. No cree un espacio de intercambio en el disco del sistema operativo.

    El agente Linux de Azure puede configurar automáticamente un espacio de intercambio mediante el disco de recursos local que se conecta a la máquina virtual una vez que esta se aprovisiona en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que puede vaciarse si la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Si quiere anular el registro de la suscripción, ejecute el siguiente comando:

    ```bash
    sudo subscription-manager unregister
    ```

1. Si usa un sistema que se implementó con una entidad de certificación empresarial, la máquina virtual de RHEL no confiará en el certificado raíz de Azure Stack Hub. Se debe colocar en el almacén raíz de confianza. Para más información, consulte el artículo sobre cómo [agregar certificados raíz de confianza al servidor](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para el aprovisionamiento en Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Apague la máquina virtual y convierta el archivo VMDK al formato VHD.

    > [!NOTE]
    > Hay un problema conocido en versiones de qemu-img >=2.2.1 que da como resultado un VHD con formato incorrecto. El problema se corrigió en QEMU 2.6. Se recomienda usar qemu-img 2.2.0 o anterior, o bien actualizar a la versión 2.6 o posterior. Referencia: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Primero convierta la imagen al formato raw:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. De lo contrario, redondee hacia arriba el tamaño para alinear con 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Convierta el disco sin procesar en un disco duro virtual (VHD) de tamaño fijo:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    O bien, con la versión de qemu **2.6 o posterior**, incluya la opción `force_size`:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparación de una máquina virtual basada en Red Hat desde una imagen ISO con un archivo kickstart automáticamente

1. Cree un archivo kickstart que incluye el contenido siguiente y guarde el archivo. La detención y desinstalación de cloud-init es opcional (cloud-init se admite en las versiones de Azure Stack Hub posteriores a la 1910). Instale el agente desde el repositorio de RedHat solo después de la versión 1910. Antes de esa versión, use el repositorio de Azure como se ha hecho en la sección anterior. Para obtener información más detallada sobre la instalación de Kickstart, consulte la [guía de instalación de Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Stop and Uninstall cloud-init
    systemctl stop cloud-init
    yum remove cloud-init
    
    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Coloque el archivo kickstart donde el sistema de la instalación puede acceder a él.

1. En el Administrador de Hyper-V, cree una nueva máquina virtual. En la página **Conectar disco duro virtual**, seleccione **Exponer un disco duro virtual más adelante** y complete el Asistente para crear nueva máquina virtual.

1. Abra la configuración de máquina virtual:

    a. Cargue un nuevo disco duro virtual en la máquina virtual Asegúrese de seleccionar **Formato VHD** y **Tamaño fijo**.

    b. Adjunte la imagen ISO de instalación a la unidad de DVD.

    c. Configure el BIOS para que arranque desde el CD.

1. Inicie la máquina virtual. Cuando aparezca la guía de instalación, presione la tecla **Tab** para configurar las opciones de arranque.

1. Escriba `inst.ks=<the location of the kickstart file>` al final de las opciones de arranque y presione **Entrar**.

1. Espere hasta que la instalación se complete. Cuando haya finalizado, la máquina virtual se apagará automáticamente. El VHD de Linux ya está listo para cargarse en Azure.

## <a name="known-issues"></a>Problemas conocidos

### <a name="the-hyper-v-driver-couldnt-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>El controlador de Hyper-V no se pudo incluir en el disco RAM inicial al usar un hipervisor de Hyper-V

En algunos casos, los instaladores de Linux pueden no incluir los controladores de Hyper-V en el disco RAM inicial (initrd o initramfs) a menos que Linux detecte que se está ejecutando un entorno de Hyper-V.

Si va a usar un sistema de virtualización diferente (como Oracle VM VirtualBox, Xen Project, etc.) para preparar su imagen de Linux, puede que necesite recompilar initrd para asegurarse de que al menos los módulos de kernel hv_vmbus y hv_storvsc están disponibles en el disco RAM inicial. Esto es un problema conocido al menos en sistemas basados en el nivel superior de distribución de Red Hat.

Para resolver este problema, agregue los módulos de Hyper-V en initramfs y recompilarlo:

Edite `/etc/dracut.conf` y agregue el siguiente contenido:

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Recompile initramfs:

```bash
dracut -f -v
```

Para más información, consulte la información sobre cómo [recompilar initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Pasos siguientes

Ya está listo para usar el disco duro virtual de Red Hat Enterprise Linux para crear máquinas virtuales de Azure Stack Hub. Si es la primera vez que carga el archivo VHD en Azure Stack Hub, consulte [Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Para más información sobre los hipervisores certificados para ejecutar Red Hat Enterprise Linux, visite el [sitio web de Red Hat](https://access.redhat.com/certified-hypervisors).
