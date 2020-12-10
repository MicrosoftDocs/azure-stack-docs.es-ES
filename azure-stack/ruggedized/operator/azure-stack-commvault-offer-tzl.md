---
title: Oferta del elemento de Marketplace Commvault en Azure Stack | Microsoft Docs
description: Implementación de Commvault desde el Marketplace de Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/14/2019
ms.openlocfilehash: c0220a7e1d5f6de6ba00b89ef64c7496e482992f
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941237"
---
# <a name="offer-commvault-marketplace-item-in-azure-stack"></a>Oferta del elemento de Marketplace Commvault en Azure Stack

*Se aplica a: Centro de datos modular, Azure Stack Hub resistente*

CommVault admite copias de seguridad y restauración de los siguientes tipos de recursos en Azure Stack Hub:

- Copia de seguridad de nivel de máquina virtual
  - Máquina virtual de IaaS
  - Discos no administrados
  - Discos administrados
  - Para más información, consulte [Copias de seguridad de máquinas virtuales](https://documentation.commvault.com/commvault/v11/article?p=86503.htm).

- Copia de seguridad de la cuenta de almacenamiento
  - Blob
  - Para más información, consulte [Información general sobre Azure Blob Storage](https://documentation.commvault.com/commvault/v11/article?p=30063.htm).

- Copia de seguridad basada en agente
  - SO invitado: Windows y Linux
  - Aplicación: SQL, MySQL
  - Para más información, consulte [Agentes de copia de seguridad](https://documentation.commvault.com/commvault/v11/article?p=14333.htm).

Puede implementar CommVault en una máquina externa y proteger los recursos de Azure Stack Hub de forma remota. Además, es posible implementar CommVault como una aplicación virtual en Azure Stack Hub. La guía completa de CommVault está disponible en su sitio de documentación, que trata sobre [Azure Stack Hub](https://documentation.commvault.com/commvault/v11/article?p=86486.htm). Como referencia, CommVault también publica una [lista completa de funcionalidades para Microsoft Azure](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm).

## <a name="deploy-from-azure-stack-hub-marketplace"></a>Implementación desde el Marketplace de Azure Stack Hub

CommVault publica una imagen BYOL en Azure Marketplace y habilita la imagen para su distribución en Azure Stack Hub. La versión mínima necesaria para realizar una copia de seguridad de las máquinas virtuales en Azure Stack es la SP16. Si tiene previsto usar una aplicación virtual, asegúrese de actualizar a la versión SP16 (la [versión con soporte técnico a largo plazo](https://documentation.commvault.com/commvault/v11/article?p=2617.htm)) o SP17 (la última versión estándar disponible).

| Nube        | Versión | Disponible para la distribución | Próxima actualización |
|--------------|---------|---------------------------|-------------|
| Pública de Azure | SP13    | Sí                       | TBD         |
| Azure Gov    | SP13    | TBD                       | TBD         |

## <a name="download-from-azure-marketplace"></a>Descarga desde Azure Marketplace

Los operadores de Azure Stack Hub pueden descargar elementos en el Marketplace local de Azure Stack para entornos conectados y desconectados. En un entorno conectado, el operador puede examinar la lista de elementos disponibles para agregar desde Azure:

![Adición desde Azure](media/azure-stack-commvault-offer-tzl/add-from-azure.png)

## <a name="upload-and-publish-manually"></a>Carga y publicación manual

En entornos desconectados, el elemento se debe descargar de Azure y, después, cargarse en Azure Stack Hub manualmente. Para más información, consulte las [instrucciones completas para entornos conectados y desconectados](../../operator/azure-stack-download-azure-marketplace-item.md).

## <a name="deployment-considerations"></a>Consideraciones de la implementación

- Implementación externa a Azure Stack Hub
- Implementar como aplicación virtual en Azure Stack Hub
- Biblioteca en disco frente a biblioteca en la nube
- Consideración de la línea de visión de la red
- Aislamiento en el nivel de suscripción

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo proteger las máquinas virtuales de IaaS, consulte Protección de máquinas virtuales en Azure Stack Hub.
