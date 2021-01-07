---
title: Recorrido del cliente desde la compra hasta la fase posterior a la implementación de Azure Stack Hub | Microsoft Docs
description: Obtenga información acerca de lo que cabe esperar de una implementación correcta in situ de un Centro de datos modular (MDC) de Azure, desde el planeamiento hasta la etapa posterior a la implementación.
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: e69881e5f898b0c1f397a4a6d407da5995dd3049
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872354"
---
# <a name="modular-datacenter-integration-overview"></a>Introducción sobre la integración del Centro de datos modular

En este artículo se describe el proceso completo para la integración del Centro de datos modular (MDC) de Azure, desde la compra hasta la fase posterior a la implementación. La integración es un proyecto colaborativo entre el cliente y Microsoft. En las secciones siguientes se tratan las distintas fases de la escala de tiempo del proyecto y los pasos específicos para los miembros del proyecto.

## <a name="introduction"></a>Introducción

En la tabla siguiente se muestra lo que se puede esperar durante las distintas fases de la implementación.

| Participante |Proceso de pedido |Antes de la implementación |Integración, validación y transporte |Implementación in situ |Después de la implementación |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  | Señal de la entrega a una ubicación de EE. UU.    |Proporciona las herramientas y la documentación necesarias para recopilar los requisitos del centro de datos. |- Valida los artefactos de configuración y comprueba los resultados de la validación.<br>- Garantiza la entrega del hardware.    |- Bastidor y pila<br>- Integración de red<br>- Implementación de Azure Stack Hub<br>- Entrega al cliente    |Registro y redifusión de Marketplace de Azure Stack Hub|
|Customer   |Señal de la compra   |- Rellena los detalles de la red en la hoja de cálculo de implementación.<br>- Recopila certificados.<br>- Obtiene las cuentas de Azure Active Directory (Azure AD)-<br>- Ejecuta cualquier herramienta de validación proporcionada.   |Garantiza que el sitio está preparado con los requisitos previos de red, alimentación y refrigeración.   |- Prepárese con los artefactos de configuración de la implementación.<br>- Asegúrese de que el ingeniero de red del cliente está disponible.   |     |


## <a name="order-process"></a>Proceso de pedido

Su organización trabajará con Microsoft para realizar un pedido de un número de sistemas asignado. Una vez realizado el pedido, Microsoft entregará el Centro de datos modular en su ubicación de EE. UU. Microsoft garantizará el cumplimiento de todos los requisitos de la cadena de suministro segura.

## <a name="hardware-delivery"></a>Entrega de hardware

Microsoft colaborará con usted para asegurarse de que todo el hardware necesario llega a la ubicación estadounidense en el plazo asignado.

Es *fundamental* que todos los datos sobre requisitos previos estén bloqueados y disponibles *antes de que llegue el ingeniero de implementación de Microsoft in situ para implementar la solución*.

- La hoja de cálculo de implementación tiene todos los datos rellenados.
- Ya se deben haber validado todos los certificados y deben estar listos.
- Debe decidir el nombre de la región.
- Todos los parámetros de integración de red están finalizados.

>[!Tip]
>Si alguno de estos datos ha cambiado, asegúrese de trabajar con la organización interna para asegurarse de que la información se actualiza antes de la llegada del ingeniero de implementación in situ. Si se actualiza la información, se impedirá que se produzcan retrasos en el proceso de implementación.

## <a name="predeployment"></a>Antes de la implementación

Puede decidir cómo integrar Azure Stack Hub en el centro de datos. Microsoft ha publicado una [hoja de cálculo de implementación](../operator/azure-stack-deployment-worksheet.md) que le guía en la recopilación de toda la información necesaria para la correcta integración con su centro de datos. Además, se requiere un determinado conjunto de certificados en el momento de la implementación. Para ayudarle a obtener estos certificados, Microsoft le proporciona [Azure Stack Hub Readiness Checker](../operator/azure-stack-validation-report.md). Esta herramienta le ayudará a crear solicitudes de firma de certificados (CSR) para proporcionar a su entidad de certificación interna.

>[!Important]
>Todos los requisitos previos se validan para ayudar a evitar los retrasos de implementación. La comprobación de los requisitos previos puede llevar tiempo y requerir la coordinación y recopilación de datos de distintos departamentos de la organización.

Debe elegir los siguientes elementos:

- **El modelo de conexión y el proveedor de identidades de Azure Stack Hub**. Puede implementar Azure Stack Hub, ya sea [conectado a Internet (y a Azure)](../operator/azure-stack-connected-deployment.md) o [desconectado](../operator/azure-stack-disconnected-deployment.md). Para sacar el máximo partido a Azure Stack Hub, incluidos los escenarios híbridos, le recomendamos que implemente la opción con conexión a Azure. La elección de Servicios de federación de Active Directory (AD FS) o de Azure AD es una decisión que se toma una sola vez y se realiza en el momento de la implementación. *Para cambiar el proveedor de identidades posteriormente es preciso volver a implementar todo el sistema.*
- **Integración de red.** La [integración de red](../operator/azure-stack-network.md) es fundamental para la implementación, funcionamiento y administración de los sistemas de Azure Stack Hub. Hay varias consideraciones que debe tener en cuenta para garantizar que la solución Azure Stack Hub es resistente y tiene una infraestructura física de alta disponibilidad compatible con sus operaciones.
- **Integración de firewall.** Le recomendamos que [use un firewall](../operator/azure-stack-firewall.md) a fin de proteger Azure Stack Hub. Los firewalls pueden ayudar a evitar ataques DDoS, detectar intrusiones e inspeccionar contenidos. Sin embargo, tenga en cuenta que se pueden convertir en un cuello de botella de rendimiento para los servicios de almacenamiento de Azure.
- **Requisitos de certificado.** Es fundamental que todos los [certificados necesarios](../operator/azure-stack-pki-certs.md) estén disponibles antes de que un ingeniero llegue in situ al centro de datos para la implementación.

Una vez recopilada toda la información de requisitos previos a través de la hoja de cálculo de implementación, Microsoft se asegurará de que verificamos que todas las herramientas de validación se han ejecutado y le ayudamos con las demás preguntas que pueda tener.

## <a name="onsite-deployment"></a>Implementación in situ

Para implementar Azure Stack Hub, se presentará un ingeniero de implementación de Microsoft para iniciar la implementación. También es necesario que un ingeniero de red de su organización esté disponible durante la implementación in situ.

Estas comprobaciones son las que debe esperar del ingeniero que se ha desplazado al lugar durante el proceso de implementación:

- Desempaquetado y inventario del hardware
- Conexión de la alimentación y encendido de la solución
- Validación del estado del hardware físico
- Comprobará el cableado y la conectividad de borde para garantizar que la solución funciona y cumple los requisitos.
- Configuración de la solución HLH (host de ciclo de vida de hardware).
- Integración con la red del centro de datos
- Comprobación de que toda la configuración de hardware físico es correcta.
- Se asegurará de que el firmware de todos los componentes pertenece a la ultima versión que admite la solución.
- Iniciará la implementación.

## <a name="post-deployment"></a>Después de la implementación

El ingeniero de implementación de Microsoft debe realizar varios pasos antes de entregar la solución al cliente. En esta fase, la validación es importante para asegurarse de que el sistema se ha implementado y está funcionando correctamente.

Acciones que debe realizar el ingeniero de implementación de Microsoft:

- Habilitación de proveedores de recursos de valor agregado
- Ejecución de [test-azurestack](../operator/azure-stack-diagnostic-test.md).
- [Registro](../operator/azure-stack-registration-role.md) en Azure
- Garantía de [redifusión de Marketplace de Azure Stack Hub](../operator/azure-stack-marketplace.md)
- Copia de seguridad de los archivos de configuración del conmutador y HLH
- Preparación de un resumen del cliente para la implementación.
- [Comprobación de las actualizaciones](../operator/azure-stack-updates.md) para asegurarse de que el software de la solución se ha actualizado a la versión más reciente.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la implementación del Centro de datos modular](deployment-overview.md)

