---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 11/20/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 6d4bb587ad28fb26adf60689989d0e1858519cfe
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920849"
---
### <a name="create-a-device-root-ca-certificate"></a>Creación de un certificado de CA raíz de dispositivo

Cree el certificado de CA raíz del dispositivo y los archivos de clave necesarios para el dispositivo: 

1. Vuelva al símbolo del sistema de Bash en la sesión de PuTTY.
2. Vaya al directorio de datos donde colocó los scripts de generación de certificados en la sección anterior.
3. Ejecute el siguiente comando:

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

4. El certificado de CA raíz del dispositivo se almacena en el siguiente archivo: `<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem`.

### <a name="create-the-iot-edge-device-ca-certificate"></a>Creación del certificado de CA de dispositivo de IoT Edge

El dispositivo IoT Edge de producción necesita un certificado de CA de dispositivo al que se hace referencia desde el archivo config.yaml. El certificado de CA de dispositivo es responsable de la creación de certificados para los módulos que se ejecutan en el dispositivo. También es necesario para los escenarios de puerta de enlace, ya que el dispositivo IoT Edge usa el certificado de CA del dispositivo para verificar su identidad en los dispositivos de bajada.

Para crear los archivos del certificado de CA del dispositivo IoT Edge:

1. Vuelva al símbolo del sistema de Bash en la sesión de PuTTY.
2. Ejecute el siguiente script, que crea varios archivos de clave y certificado de CA de dispositivo: 

   ```bash
   # Navigate to data directory
   cd <DATA-DIR>
   
   # Generate IoT Edge device CA certificate 
   ./certGen.sh create_edge_device_ca_certificate <DEVICE-CA-CERT-NAME>
   ```

3.  Más adelante se hace referencia a los siguientes archivos de par de claves y certificado en el archivo config.yaml:

    `<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem`  
    `<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem`


