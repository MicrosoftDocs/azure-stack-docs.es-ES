---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 900d7ac882a37e229bec6dc916653cf55992cccb
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2020
ms.locfileid: "93050392"
---
A continuación, cree o renueve el certificado TLS para proteger los puntos de conexión del proveedor de recursos de valor añadido:

1. Complete los pasos descritos en [Generación de solicitudes de firma de certificados para la renovación de certificados](../operator/azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal) para el proveedor de recursos. Aquí se usa la herramienta Readiness Checker de Azure Stack Hub para crear la CSR. Asegúrese de ejecutar el cmdlet correcto para el proveedor de recursos, en el paso "generar solicitudes de certificado para otros servicios de Azure Stack Hub". Por ejemplo, se utiliza `New-AzsHubEventHubsCertificateSigningRequest` para Event Hubs. Cuando termine, envíe el archivo .REQ generado a la entidad de certificación (CA) para el nuevo certificado.

2. Una vez que haya recibido el archivo de certificado de la CA, complete los pasos que se describen en el artículo sobre la [preparación de certificados para la implementación o rotación](../operator/azure-stack-prepare-pki-certs.md). Se utiliza de nuevo la herramienta Readiness Checker para procesar el archivo devuelto por la CA.

3. Por último, complete los pasos descritos en [Validación de los certificados PKI de Azure Stack Hub](../operator/azure-stack-validate-pki-certs.md). Utilice una vez más la herramienta Readiness Checker para realizar pruebas de validación en el nuevo certificado.


