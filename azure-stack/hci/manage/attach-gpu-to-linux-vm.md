---
title: Asociación de una GPU a una máquina virtual Linux en Azure Stack HCI
description: Cómo usar una GPU con cargas de trabajo de inteligencia artificial que se ejecutan en una máquina virtual Ubuntu Linux en Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: c1f1ddbfb9f362261a8e55d32a0d8c28b7b64629
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402857"
---
# <a name="attaching-a-gpu-to-an-ubuntu-linux-vm-on-azure-stack-hci"></a>Asociación de una GPU a una máquina virtual Ubuntu Linux en Azure Stack HCI

> Se aplica a: Windows Server 2019

En este tema se proporcionan instrucciones paso a paso sobre cómo instalar y configurar una unidad de procesamiento gráfico (GPU) NVIDIA con Azure Stack HCI mediante la tecnología de asignación de dispositivos discretos (DDA) en una máquina virtual Ubuntu.
En este documento se da por hecho que tiene implementado el clúster de Azure Stack HCI y las máquinas virtuales instaladas.

## <a name="install-the-gpu-and-then-dismount-it-in-powershell"></a>Instalación de la GPU y su posterior desmontaje en PowerShell

1. Instale las GPU de NVIDIA físicamente en los servidores adecuados siguiendo las instrucciones del OEM y las recomendaciones del BIOS.
2. Encienda cada servidor.
3. Inicie sesión con una cuenta con privilegios administrativos en los servidores que tengan la GPU de NVIDIA instalada.
4. Abra **Administrador de dispositivos** y vaya a la sección *Otros dispositivos*. Verá en la lista un dispositivo "Controladora de vídeo 3D".
5. Haga clic con el botón derecho en "Controladora de vídeo 3D" para que se muestre la página **Propiedades**. Haga clic en **Detalles**. En la lista desplegable situada debajo de **Propiedad**, seleccione "Rutas de acceso de ubicación".
6. Observe el valor con la cadena PCIRoot que se resalta en la captura de pantalla siguiente. Haga clic con el botón derecho en **Valor** y cópielo y guárdelo.
    :::image type="content" source="media/attach-gpu-to-linux-vm/pciroot.png" alt-text="Captura de pantalla de ruta de acceso de ubicación":::
7. Abra Windows PowerShell con privilegios elevados y ejecute el cmdlet `Dismount-VMHostAssignableDevice` para desmontar el dispositivo GPU de DDA en la máquina virtual. Reemplace el valor *LocationPath* por el de su dispositivo obtenido en el paso 6.
    ```PowerShell
    Dismount-VMHostAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -force
    ```
8. Confirme que el dispositivo aparece en los dispositivos del sistema en **Administrador de dispositivos** como Desmontado.
    :::image type="content" source="media/attach-gpu-to-linux-vm/dismounted.png" alt-text="Captura de pantalla del dispositivo desmontado":::

## <a name="create-and-configure-an-ubuntu-virtual-machine"></a>Creación y configuración de una máquina virtual Ubuntu

1. Descargue el [ISO 18.04.02 de la versión de escritorio de Ubuntu](http://cdimage.ubuntu.com/lubuntu/releases/18.04.2/release/lubuntu-18.04.2-desktop-amd64.iso).
2. Abra el **Administrador de Hyper-V** en el nodo del sistema con la GPU instalada.
   > [!NOTE]
   > [DDA no admite la conmutación por error](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment). Se trata de una limitación de la máquina virtual con DDA. Por lo tanto, se recomienda usar el **Administrador de Hyper-V** para implementar la máquina virtual en el nodo en lugar del **Administrador de clústeres de conmutación por error**. El uso del **Administrador de clústeres de conmutación por error** con DDA producirá un mensaje de error que indica que la máquina virtual tiene un dispositivo que no admite alta disponibilidad.
3. Con la imagen ISO de Ubuntu descargada en el paso 1, cree una máquina virtual con el **Asistente para nueva máquina virtual** en el **Administrador de Hyper-V** para crear una máquina virtual Ubuntu Gen 1 con 2 GB de memoria y una tarjeta de red conectada a ella.
4. En PowerShell, asigne el dispositivo de GPU desmontado a la máquina virtual mediante los cmdlets siguientes, y reemplace el valor de *LocationPath* por el de su dispositivo.
    ```PowerShell
    # Confirm that there are no DDA devices assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu

    # Assign the GPU to the VM
    Add-VMAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -VMName Ubuntu

    # Confirm that the GPU is assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu
    ```

    La asignación correcta de la GPU a la máquina virtual mostrará la siguiente salida:  :::image type="content" source="media/attach-gpu-to-linux-vm/assign-gpu.png" alt-text="Captura de pantalla de asignación de GPU":::

    Siga la documentación de la GPU [aquí](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda) para configurar valores adicionales:

   ```PowerShell
    # Enable Write-Combining on the CPU
    Set-VM -GuestControlledCacheTypes $true -VMName VMName

    # Configure the 32 bit MMIO space
    Set-VM -LowMemoryMappedIoSpace 3Gb -VMName VMName

    # Configure greater than 32 bit MMIO space
    Set-VM -HighMemoryMappedIoSpace 33280Mb -VMName VMName
   ```

   > [!NOTE]
   > El valor 33280Mb será suficiente para la mayoría de las GPU, pero debe reemplazarse por un valor mayor que la memoria de la GPU.

5. Mediante el Administrador de Hyper-V, conéctese a la máquina virtual e inicie la instalación del sistema operativo Ubuntu. Elija los valores predeterminados para instalar el sistema operativo Ubuntu en la máquina virtual.

6. Una vez finalizada la instalación, use el **Administrador de Hyper-V** para apagar la máquina virtual y configurar la **acción de detención automática** para que la máquina virtual cierre el sistema operativo invitado, como se muestra en la siguiente captura de pantalla:  :::image type="content" source="media/attach-gpu-to-linux-vm/guest-shutdown.png" alt-text="Captura de pantalla de apagado del sistema operativo invitado":::

7. Inicie sesión en Ubuntu y abra el terminal para instalar SSH:

   ```shell
    $ sudo apt install openssh-server
   ```

8. Busque la dirección TCP/IP para la instalación de Ubuntu con el comando **ifconfig** y copie la dirección IP de la interfaz **eth0**.

9. Use un cliente SSH como [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/) para conectarse a la máquina virtual Ubuntu y continuar la configuración.

10. Tras iniciar sesión en el cliente SSH, emita el comando **lspci** y compruebe que la GPU de NVIDIA aparece como "3D controller" (Controladora 3D).

    > [!IMPORTANT]
    > En caso negativo, no continúe. Asegúrese de que se siguen los pasos anteriores antes de continuar.

11. Dentro de la máquina virtual, busque y abra **Software & Updates** (Software y actualizaciones). Vaya a **Additional Drivers** (Controladores adicionales) y, después, elija los controladores de la GPU de NVIDIA más recientes enumerados. Para completar la instalación del controlador, haga clic en el botón **Apply Changes** (Aplicar cambios).
    :::image type="content" source="media/attach-gpu-to-linux-vm/driver-install.png" alt-text="Captura de pantalla de instalación del controlador":::

12. Una vez completada la instalación del controlador, reinicie la máquina virtual Ubuntu. Después de iniciar la máquina virtual, conéctese a través del cliente SSH y emita el comando **NVIDIA-SMI** para comprobar que la instalación del controlador de la GPU de NVIDIA se completó correctamente. La salida debería ser similar a la captura de pantalla siguiente: :::image type="content" source="media/attach-gpu-to-linux-vm/nvidia-smi.png" alt-text="captura de pantalla de NVIDIA-SMI":::

13. Mediante el cliente SSH, configure el repositorio e instale el motor de Docker CE:

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
    ```
    Agregue la clave GPG oficial de Docker:

    ```shell
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

    Compruebe que ahora tiene la clave con la huella digital 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88; para ello, busque los últimos ocho caracteres de la huella digital:

    ```shell
    $ sudo apt-key fingerprint 0EBFCD88
    ```

    La salida se parecerá a esta:

    ```shell
    pub   rsa4096 2017-02-22 [SCEA]
    9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
    sub   rsa4096 2017-02-22 [S]
    ```

    Configure el repositorio estable para la arquitectura de Ubuntu AMD64:

    ```shell
    $ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    ```

    Actualice los paquetes e instale Docker CE:

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```

    Compruebe la instalación de Docker CE:

    ```shell
    $ sudo docker run hello-world
    ```

## <a name="configure-azure-iot-edge"></a>Configuración de Azure IoT Edge

Para prepararse para esta configuración, revise las preguntas frecuentes contenidas en el repositorio [NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) de GitHub, que explica la necesidad de instalar Docker en lugar de Moby. Luego, continúe con los pasos siguientes.

### <a name="install-nvidia-docker"></a>Instalación de Docker de MVIDIA

1. En el cliente SSH, agregue repositorios de paquetes:

    ```shell
    curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
    distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

    curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list

    sudo apt-get update
    ```

2. Instale nvidia-docker2 y vuelva a cargar la configuración del demonio de Docker:

    ```shell
    sudo apt-get install -y nvidia-docker2
    sudo pkill -SIGHUP dockerd
    ```

3. Reinicie la máquina virtual:

    ```shell
    sudo /sbin/shutdown -r now
    ```

4. Tras el reinicio, compruebe que Docker de NVDIA se ha instalado correctamente:

    ```shell
    sudo docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
    ```

    Si la instalación se ha realizado correctamente, verá una salida parecida a la de la captura de pantalla siguiente:  :::image type="content" source="media/attach-gpu-to-linux-vm/docker.png" alt-text="Captura de pantalla de instalación correcta de Docker":::

5. Siga las instrucciones que se indican aquí y continúe con la instalación de Azure IoT Edge, pero omita la instalación del entorno de ejecución:

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list

    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    sudo apt-get update

    sudo apt-get install iotedge
    ```

    > [!NOTE]
    > Después de instalar Azure IoT Edge, compruebe que el archivo config.yaml existe en la máquina virtual Ubuntu en /etc/iotedge/config.yaml

6. Cree una identidad de dispositivo IoT Edge en Azure Portal mediante las instrucciones que [aquí](/azure/iot-edge/how-to-register-device#register-in-the-azure-portal) se indican. A continuación, copie la cadena de conexión del dispositivo para la instancia de IoT Edge recién creada.

7. Mediante el cliente SSH, actualice la cadena de conexión del dispositivo en config.yaml en la máquina virtual Ubuntu:

    ```shell
    sudo nano /etc/iotedge/config.yaml
    ```

    Busque las configuraciones de aprovisionamiento del archivo y quite la marca de comentario de la sección "Manual provisioning configuration". Actualice el valor de device_connection_string con la cadena de conexión del dispositivo IoT Edge. Asegúrese de que las demás secciones de aprovisionamiento estén comentadas. Asegúrese de que la línea "provisioning:" no tenga ningún espacio en blanco delante y de que los elementos anidados muestran una sangría de dos espacios:

    :::image type="content" source="media/attach-gpu-to-linux-vm/manual-provisioning.png" alt-text="Captura de pantalla de configuración de aprovisionamiento manual":::

    Para pegar el contenido del portapapeles en Nano, presione Mayús y haga clic con el botón derecho o presione Mayús + Insertar. Guarde y cierre el archivo (Ctrl + X, Y, Entrar).

8. Con el cliente SSH, reinicie el demonio de IoT Edge:

    ```shell
    sudo systemctl restart iotedge
    ```

    Compruebe la instalación y el estado del demonio de IoT Edge:

    ```shell
    systemctl status iotedge

    journalctl -u iotedge --no-pager --no-full
    ```

9. Mediante el cliente SSH, cree la siguiente estructura de directorio en la máquina virtual Ubuntu:

    ```shell
    cd /var
    sudo mkdir deepstream
    mkdir ./deepstream/custom_configs
    cd /var/deepstream
    sudo mkdir custom_streams
    sudo chmod -R 777 /var/deepstream
    cd ./custom_streams
    ```

10. Asegúrese de que el directorio de trabajo es /var/deepstream/custom_streams y descargue el [archivo de vídeos de demostración](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) mediante la ejecución del siguiente comando en el cliente SSH:

    ```shell
    wget -O cars-streams.tar.gz --no-check-certificate https://onedrive.live.com/download?cid=0C0A4A69A0CDCB4C&resid=0C0A4A69A0CDCB4C%21588371&authkey=AAavgrxG95v9gu0
    ```

    Descomprima los archivos de vídeo:

    ```shell
    tar -xzvf cars-streams.tar.gz
    ```

    El contenido del directorio /var/deepstream/custom_streams debe parecerse a la captura de pantalla siguiente:

    :::image type="content" source="media/attach-gpu-to-linux-vm/custom-streams.png" alt-text="Captura de pantalla de secuencias personalizadas":::

11. Cree un archivo llamado test5_config_file_src_infer_azure_iotedge_edited.txt en el directorio /var/deepstream/custom_configs. Con un editor de texto, abra el archivo, pegue el código siguiente y, luego, guarde y cierre el archivo.

    ```shell
    # Copyright (c) 2018 NVIDIA Corporation.  All rights reserved.
    #
    # NVIDIA Corporation and its licensors retain all intellectual property
    # and proprietary rights in and to this software, related documentation
    # and any modifications thereto.  Any use, reproduction, disclosure or
    # distribution of this software and related documentation without an express
    # license agreement from NVIDIA Corporation is strictly prohibited.
    
    [application]
    enable-perf-measurement=1
    perf-measurement-interval-sec=5
    #gie-kitti-output-dir=streamscl
    
    [tiled-display]
    enable=1
    rows=2
    columns=2
    width=1280
    height=720
    gpu-id=0
    #(0): nvbuf-mem-default - Default memory allocated, specific to particular platform
    #(1): nvbuf-mem-cuda-pinned - Allocate Pinned/Host cuda memory, applicable for Tesla
    #(2): nvbuf-mem-cuda-device - Allocate Device cuda memory, applicable for Tesla
    #(3): nvbuf-mem-cuda-unified - Allocate Unified cuda memory, applicable for Tesla
    #(4): nvbuf-mem-surface-array - Allocate Surface Array memory, applicable for Jetson
    nvbuf-memory-type=0
    
    [source0]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0
    
    [source1]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0
    
    [sink0]
    enable=0
    
    [sink3]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=RTSPStreaming
    type=4
    #1=h264 2=h265
    codec=1
    sync=0
    bitrate=4000000
    # set below properties in case of RTSPStreaming
    rtsp-port=8554
    udp-port=5400
    
    [sink1]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=UDPSink 5=nvoverlaysink 6=MsgConvBroker
    type=6
    msg-conv-config=../configs/dstest5_msgconv_sample_config.txt
    #(0): PAYLOAD_DEEPSTREAM - Deepstream schema payload
    #(1): PAYLOAD_DEEPSTREAM_MINIMAL - Deepstream schema payload minimal
    #(256): PAYLOAD_RESERVED - Reserved type
    #(257): PAYLOAD_CUSTOM   - Custom schema payload
    msg-conv-payload-type=1
    msg-broker-proto-lib=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_azure_edge_proto.so
    topic=mytopic
    #Optional:
    #msg-broker-config=../../../../libs/azure_protocol_adaptor/module_client/cfg_azure.txt
    
    [sink2]
    enable=0
    type=3
    #1=mp4 2=mkv
    container=1
    #1=h264 2=h265 3=mpeg4
    ## only SW mpeg4 is supported right now.
    codec=3
    sync=1
    bitrate=2000000
    output-file=out.mp4
    source-id=0
    
    [osd]
    enable=1
    gpu-id=0
    border-width=1
    text-size=15
    text-color=1;1;1;1;
    text-bg-color=0.3;0.3;0.3;1
    font=Arial
    show-clock=0
    clock-x-offset=800
    clock-y-offset=820
    clock-text-size=12
    clock-color=1;0;0;0
    nvbuf-memory-type=0
    
    [streammux]
    gpu-id=0
    ##Boolean property to inform muxer that sources are live
    live-source=0
    batch-size=4
    ##time out in usec, to wait after the first buffer is available
    ##to push the batch even if the complete batch is not formed
    batched-push-timeout=40000
    ## Set muxer output width and height
    width=1920
    height=1080
    ##Enable to maintain aspect ratio wrt source, and allow black borders, works
    ##along with width, height properties
    enable-padding=0
    nvbuf-memory-type=0
    
    [primary-gie]
    enable=1
    gpu-id=0
    batch-size=4
    ## 0=FP32, 1=INT8, 2=FP16 mode
    bbox-border-color0=1;0;0;1
    bbox-border-color1=0;1;1;1
    bbox-border-color2=0;1;1;1
    bbox-border-color3=0;1;0;1
    nvbuf-memory-type=0
    interval=0
    gie-unique-id=1
    model-engine-file=../../../../../samples/models/Primary_Detector/resnet10.caffemodel_b4_int8.engine
    labelfile-path=../../../../../samples/models/Primary_Detector/labels.txt
    config-file=../../../../../samples/configs/deepstream-app/config_infer_primary.txt
    #infer-raw-output-dir=../../../../../samples/primary_detector_raw_output/
    
    [tracker]
    enable=1
    tracker-width=600
    tracker-height=300
    ll-lib-file=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_mot_klt.so
    #ll-config-file required for DCF/IOU only
    #ll-config-file=tracker_config.yml
    #ll-config-file=iou_config.txt
    gpu-id=0
    #enable-batch-process applicable to DCF only
    enable-batch-process=0
    
    [tests]
    file-loop=1
    ```

12. Acceda a Azure Portal. Seleccione **IoT Hub Provisioned** (IoT Hub aprovisionado), haga clic en **Automatic Device Management** (Administración automática de dispositivos) y, luego, haga clic en **IoT Edge**:

    :::image type="content" source="media/attach-gpu-to-linux-vm/iot-edge.png" alt-text="Captura de pantalla de administración automática de dispositivos":::

13. En el panel derecho, seleccione la identidad del dispositivo cuya cadena de conexión de dispositivo se usó anteriormente. Haga clic en Establecer módulos:

    :::image type="content" source="media/attach-gpu-to-linux-vm/set-modules.png" alt-text="Captura de pantalla de Establecer módulos":::

14. En Módulos de IoT Edge, haga clic y elija Módulo de Marketplace:

    :::image type="content" source="media/attach-gpu-to-linux-vm/marketplace-module.png" alt-text="Captura de pantalla de Módulo de Marketplace":::

15. Busque NVIDIA y elija el SDK de DeepStream, como se muestra a continuación:

    :::image type="content" source="media/attach-gpu-to-linux-vm/deepstream.png" alt-text="Captura de pantalla del SDK de DeepStream":::

16. Asegúrese de que el módulo NvidiaDeepStreamSDK aparece en Módulos de IoT Edge:

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-modules.png" alt-text="Captura de pantalla de Módulos de IoT Edge":::

17. Haga clic en el módulo "NVIDIADeepStreamSDK" y elija "Opciones de creación del contenedor". La configuración predeterminada se muestra aquí.

    :::image type="content" source="media/attach-gpu-to-linux-vm/container-create-options.png" alt-text="Captura de pantalla de Opciones de creación del contenedor":::

    Reemplace la configuración anterior por la siguiente:

    ```shell
    {
      "ExposedPorts": {
        "8554/tcp": {}
      },
      "Entrypoint": [
        "/usr/bin/deepstream-test5-app",
        "-c",
        "test5_config_file_src_infer_azure_iotedge_edited.txt",
        "-p",
        "1",
        "-m",
        "1"
      ],
      "HostConfig": {
        "runtime": "nvidia",
        "Binds": [
          "/var/deepstream/custom_configs:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/",
          "/var/deepstream/custom_streams:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_streams/"
        ],
        "PortBindings": {
          "8554/tcp": [
            {
              "HostPort": "8554"
            }
          ]
        }
      },
      "WorkingDir": "/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/"
    }
    ```

18. Haga clic en **Revisar y crear** y, en la página siguiente, haga clic en **Crear**. Ahora verá que aparecen a continuación los tres módulos para el dispositivo IoT Edge en Azure Portal:

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-hub-connections.png" alt-text="Captura de pantalla de módulos y conexiones del concentrador de IoT Edge":::

19. Conéctese a la máquina virtual Ubuntu mediante el cliente SSH y compruebe que se ejecutan los módulos correctos:

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-sudo.png" alt-text="captura de pantalla de iotedge list":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-nvidia-smi.png" alt-text="captura de pantalla de nvidia-smi":::

    > [!NOTE]
    > El contenedor NvidiaDeepstream tardará unos minutos en descargarse. Puede validar la descarga con el comando "journalctl -u iotedge --no-pager --no-full" para examinar los registros del demonio de iotedge.

20. Confirme que el contenedor NvdiaDeepStreem está operativo. La salida del comando en las capturas de pantallas siguientes indica que la operación se ha realizado correctamente.

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify1.png" alt-text="captura de pantalla de iotedge list":::

    ```shell
    sudo iotedge logs -f NVIDIADeepStreamSDK
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify2.png" alt-text="captura de pantalla de iotedge list":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify3.png" alt-text="captura de pantalla de iotedge list":::

21. Confirme la dirección TCP/IP de la máquina virtual Ubuntu con el comando **ifconfig** y busque la dirección TCP/IP junto a la interfaz **eth0**.

22. Instale VLC Player en la estación de trabajo. En VLC Player, haga clic en **Media -> open network stream** (Medios--> abrir secuencia de red) y escriba la dirección con este formato:

    rtsp://ipaddress:8554/ds-test

    donde ipaddress es la dirección TCP/IP de la máquina virtual.

    :::image type="content" source="media/attach-gpu-to-linux-vm/vlc-player.png" alt-text="Captura de pantalla de VLC Player":::

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las GPU y DDA, consulte también:

- [Planeación de la implementación de dispositivos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment)
- [Implementación de dispositivos de gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)
