---
title: Actualizaciones
description: Información general sobre cómo se aplican las actualizaciones a Azure Stack HCl.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: f56853ce64575d32ae10ccf7ac2aa0a1a640ec5b
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868175"
---
# <a name="updates-and-upgrades"></a>Actualizaciones

> Se aplica a: Azure Stack HCI, versión 20H2

Las soluciones de Azure Stack HCI están diseñadas para tener una experiencia de actualización predecible.

## <a name="support-period"></a>Período de soporte técnico

Puede esperar actualizaciones de servicio de hardware, soporte técnico y seguridad del asociado de soluciones de hardware de Microsoft durante al menos cinco años. Microsoft ofrece soporte técnico para el sistema operativo de Azure Stack HCl durante dos años, con actualizaciones anuales de características que el cliente tiene un año para implementar.

:::image type="content" source="media/updates/lifecycle-versions.png" alt-text="Hoja de ruta de la versión anual de Azure Stack HCl" border="false":::

## <a name="updating-azure-stack-hci"></a>Actualización de Azure Stack HCI

Al igual que Windows 10, Azure Stack HCl siempre estará actualizado con las actualizaciones de calidad y seguridad mensuales de Microsoft. Las actualizaciones se inician con la Actualización compatible con clústeres, como en Windows Server.

Los asociados de hardware y los generadores de soluciones pueden conectarse a Windows Admin Center y desarrollar extensiones para mantener el firmware, los controladores y el BIOS de los servidores actualizados y coherentes en los nodos del clúster. Los clientes que compren un sistema integrado con Azure Stack HCl preinstalado pueden instalar actualizaciones de soluciones a través de estas extensiones. Los clientes que, simplemente, compren nodos de hardware validados pueden necesitar realizar las actualizaciones por separado, según las recomendaciones del fabricante de hardware.

También es necesario actualizar las máquinas virtuales periódicamente. Además de Windows Update y Windows Server Update Services, puede usar Azure Update Management para actualizar las VM.

## <a name="upgrading-to-azure-stack-hci"></a>Actualización a Azure Stack HCI

Puede actualizar de Windows Server 2019 a Azure Stack HCl, pero no fácilmente, ya que, en este momento, no existe ninguna actualización local. La actualización requiere una ventana de mantenimiento y una instalación limpia en cada servidor. Cada nodo se regenera individualmente como parte de una actualización gradual del clúster.

## <a name="cluster-aware-updating"></a>Actualización compatible con clústeres

La actualización de un clúster requiere orquestación, ya que algunas actualizaciones requieren el reinicio del sistema operativo y es necesario reiniciar los servidores de uno en uno para garantizar la disponibilidad del clúster. La Actualización compatible con clústeres (CAU) es el orquestador de clústeres predeterminado de Microsoft, lo que permite que las actualizaciones se realicen mediante una experiencia de actualización integrada en Windows Admin Center o manualmente mediante comandos de PowerShell. La Actualización compatible con clústeres es ampliable para admitir complementos de asociados que recuperan actualizaciones de controladores y firmware.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Actualización de clústeres de Azure Stack HCl](../manage/update-cluster.md)
