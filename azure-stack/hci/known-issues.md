---
title: Problemas conocidos con Azure Stack HCI
description: En este tema se detallan los problemas conocidos con Azure Stack HCl.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.openlocfilehash: 21e56fbb34c89446b0dd0e395046a9c851f391dc
ms.sourcegitcommit: f2d16c3148da50d679940e024267995b85ce6332
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2020
ms.locfileid: "91407759"
---
# <a name="known-issues-for-azure-stack-hci"></a>Problemas conocidos con Azure Stack HCI

>Se aplica a: Azure Stack HCI, versión 20H2

En este tema se detallan los problemas conocidos con Azure Stack HCl.

- La conexión entre el clúster local y el servicio en la nube Azure Stack HCI aún no admite [Azure Private Link](https://azure.microsoft.com/services/private-link).
- En este momento, el servicio en la nube Azure Stack HCI solo está disponible en ciertas regiones.
- Azure Stack HCl se anunció el 21 de julio de 2020. Es posible que la extensión de la interfaz de usuario de Azure Portal tarde varios días en ser visible en todo el mundo. Durante este tiempo, puede que vea el clúster representado como página de recursos predeterminada en Azure Portal. Gracias por su paciencia.
- Al iniciar sesión de forma interactiva en el sistema operativo, la pantalla de bienvenida a Azure Stack HCl aún no está localizada en checo, húngaro, neerlandés, polaco, sueco y turco (códigos de configuración regional cs-cz, hu-hu, nl-nl, pl-pl, pt-pt, sv-se, tr-tr). Además, en todos los idiomas distintos del inglés, los mensajes de entrada, como "[Y]/[N]" (Sí/No) solo aceptan los valores "Y" y "N", incluso si la propia solicitud aparece localizada de forma equívoca como [O]/[N] (Oui/Non) en francés o [J]/[N] (Ja/Nein) en alemán.
- Si evalúa Azure Stack HCl mediante la virtualización anidada, puede aparecer un error similar a "No se puede instalar Hyper-V porque la compatibilidad con la virtualización no está habilitada" debido a la dependencia de Azure Stack HCl de la seguridad basada en virtualización. Existen dos soluciones alternativas: (1) usar máquinas virtuales de la generación 1 de Hyper-V en su lugar; o (2) insertar la característica de Hyper-V en el VHDX sin conexión de la VM. En el host, ejecute el siguiente comando de PowerShell en cada una de las VM que actuarán como nodos de Azure Stack HCl mientras están apagados: **Install-WindowsFeature -Vhd \<path> -Name Hyper-V, RSAT-Hyper-V-Tools, Hyper-V-Powershell**.
