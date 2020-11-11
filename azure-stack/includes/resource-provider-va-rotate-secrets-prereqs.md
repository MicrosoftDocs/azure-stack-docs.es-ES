---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 294f0f5b1f6ac45d53f5d3eebc222b3edba23776
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2020
ms.locfileid: "93050393"
---
> [!NOTE]
> La rotación de secretos para proveedores de recursos de valor añadido solo se admite actualmente mediante PowerShell. 

Al igual que la infraestructura de Azure Stack Hub, los proveedores de recursos de valor añadido usan tanto secretos internos como externos. Los secretos pueden tomar varias formas, incluidas las contraseñas y las claves de cifrado que mantienen los certificados X509. Como operador, es responsable de:

- Proporcionar secretos externos actualizados, como un nuevo certificado TLS que se usa para proteger los puntos de conexión del proveedor de recursos.
- Administrar la rotación de secretos del proveedor de recursos de forma periódica.

Como preparación para el proceso de rotación:

1. Consulte [Requisitos de certificados de la infraestructura de clave pública (PKI) de Azure Stack Hub](../operator/azure-stack-pki-certs.md#certificate-requirements) para obtener información importante sobre los requisitos previos antes de adquirir o renovar el certificado X509, incluidos los detalles sobre el formato PFX requerido. Revise también los requisitos especificados en la sección [Certificados de PaaS opcionales](../operator/azure-stack-pki-certs.md#optional-paas-certificates) para su proveedor de recursos de valor añadido específico.

2. Si no lo ha hecho ya, [instale el módulo Az de PowerShell para Azure Stack Hub](../operator/powershell-install-az-module.md) antes de continuar. Es necesaria la versión 2.0.2-preview o posterior para la rotación de secretos de Azure Stack Hub. Consulte [Migración desde AzureRM al módulo Az de Azure PowerShell en Azure Stack Hub](../operator/migrate-azurerm-az.md) para más información.
