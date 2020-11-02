---
title: Problemas conocidos con Azure Stack HCI
description: En este tema se detallan los problemas conocidos con Azure Stack HCl.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 05884ce83f6f35f8d75c632f67dabd38fa3c2814
ms.sourcegitcommit: 75603007badd566f65d01ac2eacfe48ea4392e58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688326"
---
# <a name="known-issues-for-azure-stack-hci"></a>Problemas conocidos con Azure Stack HCI

>Se aplica a: Azure Stack HCI, versión 20H2

En este tema se detallan los problemas conocidos con Azure Stack HCl.

- La actualización de versión preliminar del 20 de octubre de 2020 (KB4580388) puede hacer que se produzca un error en una operación de Actualización compatible con clústeres (CAU) si se espera que alguna de las máquinas virtuales (VM) realice la migración en vivo durante la operación CAU. Para evitarlo, cambie temporalmente el comportamiento predeterminado para que la operación CAU use la migración rápida en lugar de la migración en vivo. Consulte las [notas de la versión](release-notes.md#october-20-2020-preview-update-kb4580388) para más información.
- La conexión entre el clúster local y el servicio en la nube Azure Stack HCI aún no admite [Azure Private Link](https://azure.microsoft.com/services/private-link).
- En este momento, el servicio en la nube Azure Stack HCI solo está disponible en ciertas regiones.
- Al iniciar sesión de forma interactiva en el sistema operativo, la pantalla de bienvenida a Azure Stack HCl aún no está localizada en checo, húngaro, neerlandés, polaco, sueco y turco (códigos de configuración regional cs-cz, hu-hu, nl-nl, pl-pl, pt-pt, sv-se, tr-tr). Además, en todos los idiomas distintos del inglés, los mensajes de entrada, como "[Y]/[N]" (Sí/No) solo aceptan los valores "Y" y "N", incluso si la propia solicitud aparece localizada de forma equívoca como [O]/[N] (Oui/Non) en francés o [J]/[N] (Ja/Nein) en alemán.
- Si evalúa Azure Stack HCl mediante la virtualización anidada, puede aparecer un error similar a "No se puede instalar Hyper-V porque la compatibilidad con la virtualización no está habilitada" debido a la dependencia de Azure Stack HCl de la seguridad basada en virtualización. Existen dos soluciones alternativas: (1) usar VM de la generación 1 de Hyper-V en su lugar; o (2) insertar la característica de Hyper-V en el VHDX sin conexión de la VM. En el host, ejecute el siguiente comando de PowerShell en cada una de las VM que actuarán como nodos de Azure Stack HCl mientras están apagados: **Install-WindowsFeature -Vhd \<path> -Name Hyper-V, RSAT-Hyper-V-Tools, Hyper-V-Powershell** .
