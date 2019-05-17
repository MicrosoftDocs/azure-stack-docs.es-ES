---
title: Implementación de una máquina virtual Linux en Azure Stack | Microsoft Docs
description: Implementación de una aplicación en Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 7662b696ce25cdb70f34824984f4d1ce14d88c69
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482353"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack"></a>Implementación de una máquina virtual Linux para hospedar una aplicación web en Azure Stack

Puede crear e implementar una máquina virtual Linux básica utilizando la imagen de Ubuntu del Marketplace para hospedar su aplicación web creada con un marco web. Esta máquina virtual puede hospedar aplicaciones web mediante:

- **Python** Entre los marcos web de Python comunes están Flask, Bottle y Django.
- **Go**. Entre los marcos comunes de Go están Revel, Martini, Gocraft/web y Gorilla. 
- **Ruby**. Puede configurar una instancia de Ruby on Rails como marco para distribuir sus aplicaciones web de Ruby. 
- **Java**. Java se puede usar para desarrollar aplicaciones web publicadas en un servidor de Apache Tomcat. Puede instalar Tomcat en Linux y, a continuación, implementar los archivos WAR de Java directamente en el servidor. 

Siga las instrucciones de este artículo para empezar a trabajar con cualquier aplicación web, marco y tecnología de back-end que use el sistema operativo Linux. A continuación, puede usar Azure Stack para administrar la infraestructura y las herramientas de administración dentro de su tecnología para ocuparse de las tareas de mantenimiento de la aplicación.

## <a name="deploy-a-linux-vm-for-a-web-app"></a>Implementación de una máquina virtual Linux para una aplicación web

Creará una clave secreta, usará la imagen base de la máquina virtual Linux, especificará los atributos particulares de la máquina virtual y luego la creará. Cuando se haya creado la máquina virtual, abrirá los puertos necesarios para trabajar con la máquina virtual y para que la máquina virtual hospede su aplicación. También creará el nombre DNS. Por último, se conectará a la máquina virtual y actualizará la máquina mediante apt-get. Cuando haya terminado con este artículo de instrucciones, tendrá una máquina virtual en Azure Stack lista para hospedar la aplicación web.

Puede pasar directamente a las instrucciones o bien asegurarse de que tiene primero todo lo que necesita.

## <a name="prerequisites-and-considerations"></a>Requisitos previos y consideraciones

- Necesitará una suscripción de Azure Stack.
- La suscripción necesitará acceso a la imagen de **Ubuntu Server 16.04 LTS**. Puede usar una versión posterior de la imagen, pero estas instrucciones se han escrito específicamente para 16.04 LTS. Si no tiene esta imagen, hable con su operador de nube para obtener la imagen en el Marketplace de Azure Stack.

<!-- Azure Stack specific considerations

### Authentication

Azure Stack works with two identity management services, Azure Active Directory (Azure AD) and Active Directory Federated Services (AD FS).  This section addresses how this procedure will work with either version.

### Connectivity

Azure Stack can be run in connected to completely disconnected scenarios. This section addresses considerations about the use case in relation to connectivity.

### Azure Stack Development Kit and Integrated Systems

While the two version of the product are the same product both version behave differently. Call out considerations about either version. 

### Azure Stack version

Place any version specific calls outs. The procedure will contain steps for the latest version. This section will contain call outs for previous version that are still supported. -->

## <a name="deploy-vm-using-the-portal"></a>Implementación de la máquina virtual mediante el portal

Cree una clave pública SSH para el servidor con una aplicación como Putty. Acceda al portal de Azure Stack y agregue el servidor de Ubuntu. Configure la red y la entrada DNS. Conéctese al servidor para actualizarlo.

### <a name="create-your-vm"></a>Creación de su máquina virtual

1. Cree una clave pública SSH para el servidor. Para obtener más información, consulte [How to use an SSH public key](azure-stack-dev-start-howto-ssh-public-key.md) (Uso de una clave pública SSH).
2. Abra el portal de Azure Stack.
3. Seleccione **Crear un recurso** > **Proceso** > **Ubuntu Server 16.04 LTS**.

    ![Implementación de una aplicación web en una máquina virtual de Azure Stack](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. En la hoja **Crear máquina virtual**, para **1. Configuración básica**:
    1. Escriba el **nombre de la máquina virtual**.
    1. Seleccione el **tipo de disco de máquina virtual**, ya sea **SSD Premium** o **HDD estándar**.
    1. Escriba el **nombre de usuario**.
    1. Seleccione el **Tipo de autenticación** como **Clave pública SSH**.
    1. Recupere la clave pública SSH que ha creado. Ábrala en un editor de texto y, a continuación, copie la clave y péguela en el cuadro **Clave pública SSH**. Incluya el texto desde `---- BEGIN SSH2 PUBLIC KEY ----` hasta `---- END SSH2 PUBLIC KEY ----`. Pegue todo el bloque de texto en el cuadro de la clave:
       ```text  
            ---- BEGIN SSH2 PUBLIC KEY ----
            Comment: "rsa-key-20190207"
            <Your key block>
            ---- END SSH2 PUBLIC KEY ----```
    1. Select your Azure Stack subscription.
    1. Create a new **Resource group** or use an existing depending on how you want to organize the resources for your app.
    1. Select your location. The ASDK is usually in a **local** region. The location will depend on your Azure Stack.
1. For **2. Size** type:
    - Select the size of data and RAM for your VM that is available in your Azure Stack.
    - You can either browse the list or filter for the size of your VM by **Compute type**, **CPUs**, and storage space.
    - Prices presented are estimates in your local currency that include only Azure infrastructure costs and any discounts for the subscription and location. The prices don't include any applicable software costs. Recommended sizes are determined by the publisher of the selected image based on hardware and software requirements.
    - Using a standard disk rather than a premium disk could impact operating system performance.

1. in **3. Configure optional** features type:
    1. For **High availability,** you can select an availability set. To provide redundancy to your application, you can group two or more virtual machines in an availability set. This configuration ensures that during a planned or unplanned maintenance event, at least one virtual machine will be available and meet the 99.95% Azure SLA. The availability set of a virtual machine can't be changed after it is created.
    1. For **Storage** select **Premium disks (SSD)** or **Standard disks (HDD)**. Premium disks (SSD) are backed by solid-state drives and offer consistent, low-latency performance. They provide the best balance between price and performance, and are ideal for I/O-intensive applications and production workloads. Standard disks (HDD) are backed by magnetic drives and are preferable for applications where data is accessed infrequently. Zone- redundant disks are backed by Zone redundant storage (ZRS) that replicates your data across multiple zones and are available even if a single zone is down. 
    1. You can select **Use managed disks**. Enable this feature to have Azure automatically manage the availability of disks to provide data redundancy and fault tolerance, without creating and managing storage accounts on your own. Managed disks may not be available in all regions. For more information, see [Introduction to Azure managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).
    1. Select **virtual network** to configure your network. Virtual networks are logically isolated from each other in Azure. You can configure their IP address ranges, subnets, route tables, gateways, and security settings, much like a traditional network in your data center. Virtual machines in the same virtual network can access each other by default. 
    1. Select **subnet** to configure your subnet. A subnet is a range of IP addresses in your virtual network, which can be used to isolate virtual machines from each other or from the Internet. 
    1. Select **Public IP address** to configure access to your VM or services running on your VM. Use a public IP address if you want to communicate with the virtual machine from outside the virtual network. 
    1. Select **Network Security Group**, **Basic, or **Advanced**. Set rules that allow or deny network traffic to the VM 
    1. Select **public inbound ports** to set access for common or custom protocols to your VM. The service specifies the destination protocol and port range for this rule. You can choose a predefined service, like RDP or SSH, or provide a custom port range.  
        For the  web server, you are going to want to HTTP (80), HTTPS (443), and SSH (22) open. If you plan on managing the machine with an RDP connection, open port 3389.
    1. Select **Extensions** if you would like to add Extension to your VM. Extensions add new features, like configuration management or antivirus protection, to your virtual machine using extensions. 
    1. Disable or enable **Monitoring**. Capture serial console output and screenshots of the virtual machine running on a host to help diagnose startup issues. 
    1. Select **diagnostics storage account** to specify the storage account holding your metrics. Metrics are written to a storage account so you can analyze them with your own tools. . 
    1. Select **OK**.
1. Review **4. Summary**:
    - The portal validates your settings.
    - You can download the Azure Resource Manager template for your VM if you would like to reuse your settings with an Azure Resource Manager workflow.
    - Press **OK** when the validation has passed. The deployment of the VM takes several minutes.

### Specify the open ports and DNS name

You will want to make your web app accessible to users on your network by opening the ports used to connect to the machine and adding a friendly DNS name such as `mywebapp.local.cloudapp.azurestack.external` that users can use in their web browsers.

#### Open inbound ports

You can modify the destination protocol and port range for predefined service, like RDP or SSH or provide a custom port range. For example, you may want to work with the port range of your web framework. GO, for instance, communicates on port 3000.

1. Open the Azure Stack portal for your tenant.
1. Find your VM. You may have pinned the VM to your dashboard, or you can search for the VM in the **Search resources** box.
1. Select **Networking** in your VM blade.
1. Select **Add inbound port** rule to open a port.
1. For Source, leave the default to **Any**.
1. For Source port range, leave the wildcard (*).
1. For Destination port range, add the port you would like to open, such as `3000`.
1. For **Protocol** leave **Any**.
1. For **Action** set to **Allow**.
1. For **Priority** leave for the default.
1. Type a **Name** and **Description** to help you remember why the port is open.
1. Select **Add**.

#### Add a DNS name for your server

In addition, you can create a DNS name for your server, and then users can connect to your web site using a URL.

1. Open the Azure Stack portal for your tenant.
1. Find your VM. You may have pinned the VM to your dashboard, or you can search for the VM in the **Search resources** box.
1. Select **Overview**.
1. Select **Configure** under VM.
1. Select **Dynamic** for **Assignment**.
1. Type the DNS name label such as `mywebapp` so that your full URL will be: `mywebapp.local.cloudapp.azurestack.external` (for an ASDK app).

### Connect via SSH to update your VM

1. On the same network as your Azure Stack, open your SSH client. For more information, see [How to use an SSH public key](azure-stack-dev-start-howto-ssh-public-key.md).
1. Type:
    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

<!--

## Deploy VM using the PowerShell

Include a sentence or two to explain only what is needed to complete the procedure.

1. Step one of the procedures.

    | Parameter | Example | Description |
    | --- | --- | --- |
    | item      | "dog"   | Describe what it is and where to find the information. |

2. Step two of the procedure

    ```PowerShell  
    verb-command -item "dog"
    ```

3. Step three of the procedures.

    ```PowerShell  
    verb-command -item "dog"
    ```

4. Step four of the procedures.

    ```PowerShell  
    verb-command -item "dog"
    ```

## Deploy VM using the CLI

Include a sentence or two to explain only what is needed to complete the procedure.

1. Step one of the procedures.

    | Parameter | Example | Description |
    | --- | --- | --- |
    | item      | "dog"   | Describe what it is and where to find the information. |

2. Step two of the procedure

    ```CLI  
    verb-command -item "dog"
    ```

3. Step three of the procedures.

    ```CLI  
    verb-command -item "dog"
    ```

4. Step four of the procedures.

    ```CLI  
    verb-command -item "dog"
    ```

-->

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información acerca de cómo [desarrollar para Azure Stack](azure-stack-dev-start.md)