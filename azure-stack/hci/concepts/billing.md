---
title: Facturación y pago de Azure Stack HCl
description: Cómo funciona la facturación y el pago en Azure Stack HCl.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/19/2020
ms.openlocfilehash: 0cdd8a7f6c0c59eddbe1e8dd93158eb7a280e8ab
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010726"
---
# <a name="azure-stack-hci-billing-and-payment"></a>Facturación y pago de Azure Stack HCl

> Se aplica a: Azure Stack HCI, versión 20H2

Azure Stack HCl es un servicio de Azure que se incluye en la factura de suscripción de Azure como cualquier otro. No se requiere ninguna licencia de software local tradicional, aunque las máquinas virtuales (VM) de invitado pueden requerir licencias individuales de sistema operativo. Las divisas y los descuentos se controlan de forma centralizada desde la plataforma de facturación de Azure Commerce y el cliente obtiene una factura unificada y desglosada al final del mes.

## <a name="what-does-azure-stack-hci-charge-for"></a>¿Qué se cobra con Azure Stack HCl?

El modelo de facturación en la nube de Azure Stack HCI es coherente, familiar y fácil para los clientes que ya usan Azure o cualquier otro servicio en la nube. La facturación se basa en una tarifa plana por núcleo de procesador físico en un clúster de Azure Stack HCl (se aplican cargos de uso adicionales si se usan otros servicios de Azure).

El modelo de precios es diferente de la facturación basada en el uso o en el consumo, que se basa en el número de VM. Aunque el número de núcleos de procesador virtual (vCPU) puede variar a lo largo del mes, no afecta al precio que paga por Azure Stack HCl: sigue pagando en función del número de núcleos físicos presentes en el clúster.

## <a name="advantages-of-the-azure-stack-hci-billing-model"></a>Ventajas del modelo de facturación de Azure Stack HCl

- Es sencillo: no hay ningún mínimo, ni ningún máximo, ni es necesario realizar cálculos relacionados con la memoria, el almacenamiento o la entrada/salida de red.
- Recompensa a los clientes que ejecutan su infraestructura de virtualización de forma más eficaz, con una mayor densidad de máquinas virtuales en el entorno físico.
- Es fácil averiguar el costo que tendrá la implementación de Azure Stack HCl en el entorno local. Además, los costos se escalan de un modo previsible del perímetro de red al centro de datos.

## <a name="how-the-number-of-processor-cores-is-assessed"></a>Cómo se evalúa el número de núcleos de procesador

Para determinar cuántos núcleos tiene un clúster, Azure Stack HCl comprueba periódicamente el número de núcleos físicos y los notifica a Azure. Si solo se conecta ocasionalmente o se interrumpe la conexión, no se preocupe; siempre puede volver a intentarlo más tarde. Se pueden cargar días o semanas de datos de núcleos a la vez. Los clientes deben conectarse a Azure al menos una vez cada 30 días para permitir la facturación.

Para cargar manualmente los datos principales en Azure, use el cmdlet **`Sync-AzureStackHCI`** .

## <a name="faq"></a>Preguntas más frecuentes

- Si ya tengo una suscripción de Azure, ¿puedo usarla para Azure Stack HCl? **Sí**
- Si el departamento de finanzas de mi organización ya ha aprobado el gasto en Azure, ¿cubre Azure Stack HCl? **Sí**
- Si tengo un compromiso de Azure que gastar, ¿puedo usarlo para Azure Stack HCl? **Sí**
- Si tengo crédito de Azure (por ejemplo, para alumnos u otorgados como premio), ¿puedo usarlo para Azure Stack HCl? **Sí**
- Si mi organización negoció un descuento de Contrato Enterprise, ¿es aplicable a Azure Stack HCl? **Sí**
- ¿Funcionan las herramientas de administración de costos de Azure Portal para Azure Stack HCI? **Sí**
- ¿Funcionan las herramientas de terceros o personalizadas creadas con las API de facturación de Azure para Azure Stack HCl? **Sí**

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Información general sobre precios: cómo funcionan los precios de Azure](https://azure.microsoft.com/pricing/)
- [Información general sobre Azure Cost Management y la facturación](/azure/cost-management-billing/cost-management-billing-overview)
