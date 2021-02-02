---
title: Conexión de un dispositivo IoT Edge a IoT Hub en Azure Stack Hub
description: Aprenda a conectar un dispositivo IoT Edge a IoT Hub en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/20/2020
ms.reviewer: dmolokanov
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 768952db6d93930f710dd80ea10e23ae6d8297a5
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920853"
---
# <a name="connect-an-iot-edge-device-to-iot-hub-on-azure-stack-hub"></a>Conexión de un dispositivo IoT Edge a IoT Hub en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

En este artículo se muestra cómo conectar un dispositivo IoT Edge virtual a una instancia de IoT Hub en ejecución en Azure Stack Hub. Puede usar el mismo proceso general para conectar un dispositivo físico a la instancia de IoT Hub.

## <a name="prerequisites"></a>Requisitos previos

Complete los siguientes requisitos previos antes de continuar:

- Necesitará una cuenta que pueda acceder al [portal de usuarios de Azure Stack Hub](../user/azure-stack-use-portal.md), con [permisos de "colaborador"](../user/azure-stack-manage-permissions.md) como mínimo.

- Trabaje con el administrador para [instalar IoT Hub en el proveedor de recursos de Azure Stack Hub](../operator/iot-hub-rp-overview.md). Los pasos de instalación también abarcan la creación de una oferta que incluye el servicio IoT Hub. 

- Cuando exista una oferta, el administrador puede crear o actualizar su suscripción a Azure Stack Hub para incluir IoT Hub. Otra alternativa es [suscribirse a la nueva oferta y crear su propia suscripción](azure-stack-subscribe-services.md).

- Resulta útil tener conocimientos básicos de cifrado de clave pública (PKI). En concreto, cómo se usan los certificados de una entidad de certificación (CA) y X509 para crear una cadena de confianza. Esta confianza permite que los nodos de red se autentiquen y se comuniquen entre sí, p. ej., el servicio de IoT Hub y el dispositivo IoT Edge, de forma segura. 

## <a name="overview"></a>Información general

A continuación se muestra un resumen de los pasos que debe completar para conectar un dispositivo IoT Edge a IoT Hub en Azure Stack Hub:

1. Cree recursos de VM Linux e IoT Hub en la instancia de Azure Stack Hub. La VM Linux actuará como un dispositivo IoT Edge virtual. 
2. Configure los certificados necesarios para el dispositivo IoT Edge. El número de pasos necesarios dependerá del tipo de CA que emitió el certificado de CA raíz de IoT Hub.
3. Configure el software y los servicios necesarios para el dispositivo IoT Edge.
4. Pruebe el dispositivo IoT Edge para asegurarse de que funciona correctamente y se comunica con la instancia de IoT Hub.

Antes de ejecutar el script en cada sección, asegúrese de actualizar los marcadores de posición de script para que coincidan con la configuración del entorno. Tome nota de los valores que usa, ya que los necesitará en secciones posteriores:

| Marcador de posición | Ejemplo | Descripción |
|-------------|---------|-------------|
| `<DEVICE-CA-CERT-NAME>` | `edged1cert`| Nombre que asigna al certificado de CA del dispositivo IoT Edge. |
| `<IOT-HUB-HOSTNAME>`| `test-hub-1.mgmtiothub.region.mydomain.com`| Nombre de host de IoT Hub. |
| `<IOT-HUB-ROOT-CA>`| `root.cer`| Nombre de archivo que se asigna a la CA raíz de IoT Hub exportada (si se usa una CA privada). |
| `<DATA-DIR>`| `/home/edgeadmin/edged1`| Ruta de acceso completa al directorio de datos que se crea en la VM Linux. |
| `<USER-ACCOUNT>`| `edgeadmin`| Cuenta que usa para iniciar sesión en la VM Linux. |

## <a name="create-azure-stack-hub-resources"></a>Creación de recursos de Azure Stack Hub

En primer lugar, cree los recursos de Azure Stack Hub necesarios, como una instancia de IoT Hub y una VM Linux. Usará el [portal de usuarios de Azure Stack Hub](../user/azure-stack-use-portal.md) para crear estos recursos.

### <a name="create-an-iot-hub-resource"></a>Crear un recurso de IoT Hub

1. Inicie sesión en el [portal de usuarios de Azure Stack Hub](../user/azure-stack-use-portal.md) desde un equipo que tenga acceso a la instancia de Azure Stack Hub.
2. Si aún no lo ha creado, siga las instrucciones de la sección [Creación de una instancia de IoT Hub](/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub) de **Creación de una instancia de IoT Hub mediante Azure Portal** para crear un recurso de IoT Hub. 

   > [!IMPORTANT]
   > Asegúrese de usar el [portal de usuarios de Azure Stack Hub](../user/azure-stack-use-portal.md) al seguir los pasos del artículo y no Azure Portal. Tenga en cuenta también que algunas capturas de pantalla e instrucciones pueden ser ligeramente diferentes, ya que no todas las características de Azure están disponibles en Azure Stack Hub. 

### <a name="deploy-and-configure-a-linux-vm"></a>Implementación y configuración de una VM Linux 

En esta sección, implementará una nueva VM Linux, que actuará como el dispositivo IoT Edge virtual:

1. Con el inicio rápido [Creación de una máquina virtual de servidor Linux mediante el portal de Azure Stack Hub](azure-stack-quick-linux-portal.md), instale una VM Linux en la instancia de Azure Stack Hub. Asegúrese de habilitar el puerto **SSH (22)** en la propiedad **Seleccionar puertos de entrada públicos** cuando llegue a la página **Configuración**. 

   > [!NOTE]
   > Solo necesita completar las 5 primeras secciones, hasta **Conectarse a la VM**, ya que no necesitará el servidor web NGINX. 

2. Después de crearla y conectarla a la VM mediante PuTTY, cree un subdirectorio de datos fuera del directorio principal de la cuenta de usuario; por ejemplo:

   ```bash
   mkdir edged1
   ```

3. Configure la herramienta de generación de certificados con uno de los métodos siguientes. Cualquiera de ellos descargará los archivos del repositorio de GitHub de IoT Edge, que se necesitan más adelante para generar certificados de dispositivo:

   **Use el script cURL:**
   ```bash
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Download certGen.sh script file, and openssl_root_ca.cnf config file
   curl -Lo certGen.sh https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/certGen.sh
   curl -Lo openssl_root_ca.cnf https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/openssl_root_ca.cnf
   ```
   **Use Git para clonar el repositorio:**
   
   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>
   
   # Clone the iotedge repo, which contains the certGen.sh script file and openssl_root_ca.cnf config file
   git clone https://github.com/Azure/iotedge.git
   
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Copy certGen.sh and openssl_root_ca.cnf into your working directory
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/*.cnf .
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/certGen.sh .
   ```

## <a name="configure-iot-edge-device-certificates"></a>Configuración de certificados de dispositivo de IoT Edge

En esta sección, completará la configuración del certificado de VM que requiere el dispositivo de IoT Edge virtual. 

El servicio de IoT Hub y el dispositivo IoT Edge están protegidos con certificados X509. Ambos deben usar un certificado de CA raíz emitido por la misma CA. Seleccione la pestaña correspondiente a continuación para completar la configuración del certificado, en función del tipo de CA raíz que use IoT Hub.

# <a name="public-ca"></a>[CA pública](#tab/public-ca)

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

# <a name="private-ca"></a>[CA privada](#tab/private-ca)

> [!IMPORTANT]
> Si usa una CA privada, se requieren pasos adicionales para transferir el certificado de CA raíz de IoT Hub al dispositivo IoT Edge. Siga estos pasos ***solo** _ si usa su propia CA privada para la generación de certificados. Si una CA pública emitió el certificado de CA raíz de IoT Hub, seleccione la pestaña _ *CA pública** anterior. 

### <a name="export-the-root-ca-certificate-from-your-iot-hub"></a>Exporte el certificado de CA raíz desde IoT Hub.

Desde una máquina con acceso a Azure Stack Hub, exporte el certificado de CA raíz de IoT Hub en formato PEM. En el ejemplo siguiente se muestra cómo exportar el certificado con un explorador [Microsoft Edge](https://www.microsoft.com/edge) o[Google Chrome](https://www.google.com/chrome/index.html): 

   1. En la página **Visión general** de la instancia de IoT Hub, use el botón **Copy** situado a la derecha de la propiedad **Nombre de host** para copiar el nombre de host de IoT Hub en el portapapeles:  

      [![Nombre de host de IoT Hub](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png)](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png#lightbox)
   1. Abra una nueva pestaña en un explorador de Microsoft Edge o Google Chrome, escriba `https://`, pegue el nombre de host de IoT Hub copiado en el paso anterior y, a continuación, presione **Entrar**. 

   1. Una vez que se devuelve el mensaje de error, seleccione el icono de candado situado a la izquierda de la barra de direcciones y, a continuación, seleccione **Certificado**.
      [![conexión de certificado segura](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png#lightbox)

   1. Cuando se muestre el certificado SSL/TLS, seleccione la pestaña **Ruta de certificación**. A continuación, seleccione el primer certificado de la ruta de acceso y el botón **Ver certificado**.  
      [![conexión segura de certificado para certificado SSL](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png#lightbox) 

   1. Cuando se muestre el certificado de CA raíz, seleccione la pestaña **Detalles** y, después, el botón **Copiar en archivo...** .
      [![conexión segura de certificado para el certificado de CA raíz](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png#lightbox) 

   1. Cuando se muestre el **Asistente para exportar certificados**, exporte el certificado a un archivo **con formato X.509 codificado en Base 64**; por ejemplo, **root.cer**. Este archivo se usa en la sección siguiente, por lo que debe exportarlo a una ubicación a la que la VM Linux pueda acceder o que permita copiar a dicha VM.

   1. Una vez finalizada la exportación correctamente, puede cerrar la pestaña del explorador.

### <a name="install-the-iot-hub-root-ca-certificate-on-the-iot-edge-device"></a>Instale el certificado de CA raíz de IoT Hub en este dispositivo IoT Edge.

Ahora instale el certificado de CA raíz de IoT Hub, que exportó en la sección anterior, en el almacén de confianza del dispositivo IoT Edge. 

1. Transfiera el archivo de CA raíz de IoT Hub que exportó en la sección anterior al dispositivo IoT Edge. Dado que estamos usando una VM Linux como dispositivo IoT Edge, deberá copiarlo en la VM Linux. En función del entorno, considere la posibilidad de usar PuTTY con el comando PSCP o el programa WinSCP para copiar el archivo en la VM Linux.
2. Ejecute el siguiente script desde la sesión de PuTTY de la VM Linux, donde ha almacenado el archivo de CA raíz de IoT Hub. El script comprobará que la conexión TLS se ha realizado correctamente e instalará la CA raíz en el almacén de confianza del dispositivo IoT Edge:

   ```bash
   # Verify connection failed first
   # Expected response: Verify return code: 2 (unable to get issuer certificate)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   
   # Verify connection succeeded when root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443 -CAfile <IOT-HUB-ROOT-CA>
   
   # Install root CA in the trusted store on Edge device
   sudo cp <IOT-HUB-ROOT-CA> /usr/local/share/ca-certificates/
   sudo update-ca-certificates
   
   # Verify connection succeeded even when no root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   ```

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

### <a name="append-the-iot-hub-root-ca-to-the-device-root-ca"></a>Anexar la CA raíz de IoT Hub a la del dispositivo

Ahora, anexe la CA raíz de IoT Hub que exportó y copió en el dispositivo IoT Edge a la CA raíz del dispositivo que generó antes:

```bash
# Navigate to home directory
cd /home/<USER-ACCOUNT>

# Append IoT Hub root CA to the device root CA
cat <IOT-HUB-ROOT-CA> >> certs/azure-iot-test-only.root.ca.cert.pem
```
-----

## <a name="configure-iot-edge-device-software-and-services"></a>Configuración del software y los servicios del dispositivo IoT Edge

En esta sección, completará la configuración de VM e IoT Hub que requiere el dispositivo IoT Edge virtual.

### <a name="install-iot-edge-and-container-runtimes-on-the-vm"></a>Instalación de IoT Edge y entornos en tiempo de ejecución en la VM

1. Vuelva a la sesión de PuTTY de la VM y ejecute el siguiente script para registrar la clave de Microsoft y la fuente del repositorio de software:

   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>

   # Install the repository configuration. 
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   
   # Copy the generated list.
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   
   # Install Microsoft GPG public key.
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

2. Instale el entorno en tiempo de ejecución del contenedor:

   ```bash
   sudo apt-get update
   sudo apt-get install moby-engine moby-cli
   ```

3. Instale el demonio de seguridad de Azure IoT Edge:

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

> [!NOTE]
> Puede omitir el aviso **IMPORTANTE: Actualice el archivo de configuración** por ahora, ya que lo actualizará en una sección posterior.

### <a name="create-an-iot-edge-device-in-iot-hub"></a>Creación de un dispositivo IoT Edge en IoT Hub

1. Vuelva al [Portal de usuarios de Azure Stack Hub](../user/azure-stack-use-portal.md) y desplácese hasta el servicio **IoT Hub**.

2. Seleccione el recurso de IoT Hub, seleccione la página **IoT Edge** y, a continuación, **Agregar un dispositivo IoT Edge**.

   [![Recurso de IoT Hub](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png#lightbox)

3. En la página **Crear un dispositivo**, escriba el **id. de dispositivo**; por ejemplo, "edged1".
4. Cuando termine, seleccione **Guardar**.

   [![IoT Edge: Crear un dispositivo](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png#lightbox)

5. Cuando el portal vuelva a la página **IoT Edge**, seleccione el nuevo dispositivo en la lista de dispositivos.

   [![IoT Edge: Ver dispositivos](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png)](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png#lightbox)

6. En la página Detalles del dispositivo, use el botón **Copiar** situado a la derecha de **Cadena de conexión principal** para copiar la cadena en el portapapeles. Necesitará esta cadena de conexión en la sección siguiente.

   [![IoT Edge: Detalles del dispositivo](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png#lightbox)

### <a name="configure-the-virtual-iot-edge-device-on-the-vm"></a>Configuración del dispositivo IoT Edge virtual en la VM

1. Vuelva a la sesión de PuTTY de la VM. Con Bash, abra el archivo de configuración de Nano en el dispositivo IoT Edge virtual:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

2. Busque la propiedad `provisioning` en el comentario **# Manual provisioning configuration using a connection string** (Configuración del aprovisionamiento manual mediante una cadena de conexión), en la sección **Provisioning mode and settings** (Modo de aprovisionamiento y configuración). Actualice la cadena de conexión del dispositivo IoT Edge. Para hacerlo, reemplace el marcador de posición `<ADD DEVICE CONNECTION STRING HERE>` por la cadena de conexión que copió en el portapapeles en la sección anterior:

   > [!NOTE]
   > Para pegar el contenido del portapapeles en Nano, mantenga presionada la tecla **Mayús** y haga clic con el botón derecho del mouse. O bien presione las teclas **Mayús** e **Insertar** simultáneamente. Si la operación de pegar desplaza el cursor hasta el extremo derecho de la cadena de conexión, presione la tecla **Inicio** para volver al extremo izquierdo.

   ```yaml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

3. Busque y quite la marca de comentario de la propiedad `certificates` en la sección **Configuración del certificado**. Quite las marcas de comentario y reemplace los URI de archivo por las rutas de acceso a los tres certificados que creó antes; por ejemplo:

   ```yaml
   certificates:
     device_ca_cert: "<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem"
     device_ca_pk: "<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem"
     trusted_ca_certs: "<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

4. Al acabar, las propiedades `provisioning` y `certificates` deben ser similares a las siguientes respuestas:

   [![Editor de Nano: propiedad de aprovisionamiento](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png#lightbox)

   [![Editor de Nano: propiedad de certificados](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png#lightbox)


5. Para guardar y cerrar el archivo, use **CTRL** + **X** seguido de **Y** y, a continuación, **Entrar**.

6. Al volver a Bash, reinicie el demonio:
   
   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-a-successful-installation"></a>Comprobación de la correcta instalación

1. Compruebe el estado del demonio de IoT Edge:

   ```bash
   systemctl status iotedge
   ```

2. Debería ver que el servicio IoT Edge se está ejecutando y está "activo", con una respuesta similar a la siguiente. Si es así, puede ir directamente al paso 4.

   [![El servicio IoT Edge se ejecuta correctamente](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png#lightbox)

3. Si el servicio IoT Edge devuelve un error:
   - Verá una respuesta simular con el texto "error", como en el ejemplo siguiente: [![Error del servicio IoT Edge](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png#lightbox)   
   - Para solucionar problemas, puede seguir estos pasos:
     - Examine los registros del demonio:

         ```bash
         journalctl -u iotedge --no-pager --no-full
         ```
     - Ejecute la herramienta para la solución de problemas para consultar los errores de configuración y redes más comunes. En este ejemplo, se detecta un archivo `/etc/iotedge/config.yaml` con un formato incorrecto:

         ```bash
         sudo iotedge check
         ```

         [![Comprobación del servicio IoT Edge](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png#lightbox)

         > [!NOTE]
         > El módulo `$edgeHub` del sistema es opcional y no se implementa en el dispositivo hasta que implemente el primer módulo de IoT Edge. Como tal, `iotedge check` puede devolver un error que indica que `$edgeHub` no se puede enlazar con los puertos durante una comprobación de conectividad del host. Este error se puede pasar por alto si `$edgeHub` no se requiere en la implementación.           

   - Si observa que un archivo .YML tiene un formato incorrecto como en el ejemplo anterior, simplemente:
      - Repita los pasos descritos en [Configuración del dispositivo IoT Edge virtual en la VM](#configure-the-virtual-iot-edge-device-on-the-vm) para corregir el archivo .YML.
      - Repita los pasos descritos en [Comprobación de la correcta instalación](#verify-a-successful-installation).

4. Una vez que determine que el servicio IoT Edge se ha iniciado correctamente, enumere los módulos en ejecución. Debería ver el servicio `edgeAgent` con el estado `running`:

   ```bash
   sudo iotedge list
   ```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no va a usarlos, vuelva al [portal de usuarios de Azure Stack Hub](../user/azure-stack-use-portal.md) y elimine la VM y los recursos de IoT Hub que creó anteriormente.

## <a name="next-steps"></a>Pasos siguientes

Los siguientes son recursos complementarios:

- [Instalación del entorno de ejecución de Azure IoT Edge en sistemas Linux basados en Debian](/azure/iot-edge/how-to-install-iot-edge-linux)
- [Configuración de un dispositivo IoT Edge para que actúe como puerta de enlace transparente](/azure/iot-edge/how-to-create-transparent-gateway)
- [Creación de certificados de demostración para probar las características de dispositivo IoT Edge](/azure/iot-edge/how-to-create-test-certificates)