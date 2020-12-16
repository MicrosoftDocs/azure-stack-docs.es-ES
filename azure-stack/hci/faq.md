---
title: Preguntas más frecuentes sobre Azure Stack HCI
description: Preguntas más frecuentes sobre Azure Stack HCI.
ms.topic: conceptual
author: JohnCobb1
ms.author: v-johcob
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/24/2020
ms.openlocfilehash: f4c6b9585f41388281c6618fabd21932f6d48c38
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051605"
---
# <a name="azure-stack-hci-faq"></a>Preguntas más frecuentes sobre Azure Stack HCI
Las preguntas más frecuentes sobre Azure Stack HCI incluyen una sección sobre conectividad de Azure Stack HCI y una sección de preguntas más frecuentes generales.

## <a name="azure-stack-hci-connectivity"></a>Conectividad de Azure Stack HCI
Azure Stack HCI es una pila de infraestructura hiperconvergida local que se entrega como un servicio híbrido de Azure. El software de Azure Stack HCI se instala en servidores físicos que usted controla en su entorno local. A continuación, se conecta a Azure para la supervisión basada en la nube, soporte técnico, facturación y características de seguridad y administración opcionales. En esta sección de preguntas más frecuentes se explica cómo usa Azure Stack HCI la nube al abordar las preguntas más frecuentes sobre los requisitos de conectividad y el comportamiento.

### <a name="your-data-stays-on-premises"></a>Los datos permanecen en el entorno local

**¿Los datos almacenados en Azure Stack HCI se envían a la nube?**

No. Los nombres, los metadatos, la configuración y el contenido de las máquinas virtuales locales nunca se envían a la nube a menos que active servicios adicionales expresamente para ese fin, como Azure Backup o Azure Site Recovery. O a menos que inscriba esas máquinas virtuales de forma individual en servicios de administración en la nube como Azure Arc.

### <a name="edge-local-management-and-control"></a>Control y administración en el entorno local perimetral

**¿El plano de control de Azure Stack HCI va a través de la nube?**

No. Puede usar herramientas locales perimetrales, como Windows Admin Center, PowerShell o System Center, para administrar directamente la infraestructura del host y las máquinas virtuales, incluso si la conexión de red a la nube está inactiva o es muy limitada. Las operaciones cotidianas comunes, como mover una máquina virtual entre hosts, reemplazar una unidad con errores o configurar direcciones IP, no se basan en la nube. Sin embargo, se requiere conectividad en la nube para obtener actualizaciones de software en línea, cambiar el registro de Azure o usar características que dependen directamente de los servicios en la nube para la copia de seguridad, la supervisión, etc.

**¿Hay requisitos de ancho de banda o de latencia entre Azure Stack HCI y la nube?**

No. Las conexiones de ancho de banda limitado, como las líneas T1 rurales o las conexiones por satélite o móviles, son adecuadas para la sincronización de Azure Stack HCI. La conectividad mínima necesaria es solo de varios kilobytes al día. Los servicios adicionales pueden requerir ancho de banda adicional, especialmente para replicar o realizar copias de seguridad de máquinas virtuales completas, descargar actualizaciones de software grandes o cargar registros detallados para su análisis y supervisión en la nube.

### <a name="designed-for-intermittent-and-limited-connectivity"></a>Diseñado para conectividad intermitente y limitada

**¿Azure Stack HCI requiere conectividad continua con la nube?**

No. Azure Stack HCI está diseñado para controlar los períodos de conectividad limitada o nula.

**¿Qué ocurre si mi conexión de red con la nube deja de funcionar temporalmente?**

Mientras la conexión está inactiva, todas las máquinas virtuales y la infraestructura de host continúan ejecutándose con normalidad, y puede usar las herramientas de locales perimetrales para la administración. No podrá usar las características que se basan directamente en los servicios en la nube. La información de Azure Portal también quedaría desactualizada hasta que Azure Stack HCI pueda volver a realizar la sincronización.

**¿Durante cuánto tiempo se puede ejecutar Azure Stack HCI sin conexión?**

Como mínimo, Azure Stack HCI debe sincronizarse correctamente con Azure una vez cada 30 días consecutivos.

**¿Qué ocurre si se supera el límite de 30 días?**

Si Azure Stack HCI no se ha sincronizado con Azure en más de 30 días consecutivos, el estado de la conexión del clúster mostrará **Out of policy** (Fuera de la directiva) en Azure Portal y otras herramientas, y el clúster entrará en un modo de funcionalidad reducida. En este modo, la infraestructura de host permanece activa y todas las máquinas virtuales actuales se ejecutan con normalidad. Sin embargo, no se pueden crear nuevas máquinas virtuales hasta que Azure Stack HCI pueda volver a realizar la sincronización. La razón técnica interna es que la licencia generada por la nube del clúster ha expirado y debe renovarse mediante la sincronización con Azure.

### <a name="understanding-sync"></a>Descripción de la sincronización

**¿Qué contenido sincroniza Azure Stack HCI con la nube?**

Esto depende de las características que utilice. Como mínimo, Azure Stack HCI sincroniza la información básica del clúster para mostrarla en Azure Portal (por ejemplo, la lista de nodos en clúster, el modelo de hardware y la versión de software), información de facturación que resume los núcleos por día acumulados desde la última sincronización y la información de diagnóstico mínima requerida que ayuda a Microsoft a mantener el entorno de Azure Stack HCI seguro, actualizado y funcionando correctamente. El tamaño total es pequeño, unos pocos kilobytes. Si activa servicios adicionales, es posible que se carguen más datos: por ejemplo, Azure Log Analytics carga registros y contadores de rendimiento para la supervisión.

**¿Con qué frecuencia se sincroniza Azure Stack HCI con la nube?**

Esto depende de las características que utilice. Como mínimo, Azure Stack HCI intentará realizar la sincronización cada 12 horas. Si la sincronización no se realiza correctamente, el contenido se conserva localmente y se envía con la siguiente sincronización correcta. Además de este temporizador normal, puede realizar una sincronización manual en cualquier momento mediante el cmdlet de PowerShell `Sync-AzureStackHCI` o desde Windows Admin Center. Si activa servicios adicionales, pueden cargarse datos con más frecuencia: por ejemplo, Azure Log Analytics cargaría datos cada 5 minutos para la supervisión.

### <a name="data-residency"></a>Residencia de datos

**¿Dónde va realmente la información sincronizada?**

Azure Stack HCI se sincroniza con Azure y almacena los datos en un centro de datos seguro y operado por Microsoft. Para más información, consulte [Recopilación de datos de Azure Stack HCI](concepts/data-collection.md) y [Residencia de datos en Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="disconnected-or-air-gapped"></a>Desconectado o "con restricciones en línea"

**¿Es posible usar Azure Stack HCI y nunca conectarse a Azure?**

No. Azure Stack HCI debe sincronizarse correctamente con Azure una vez cada 30 días consecutivos.

**¿Puedo transferir datos sin conexión entre un entorno de Azure Stack HCI "con restricciones en línea" y Azure?**

No. Actualmente no hay ningún mecanismo para registrar y sincronizar entre el entorno local y Azure sin conectividad de red. Por ejemplo, no puede transportar certificados o datos de facturación mediante almacenamiento extraíble. Si hay suficiente demanda por parte de los clientes, estamos abiertos a explorar esta característica en el futuro. Háganoslo saber en el [foro de comentarios de Azure Stack HCI](https://feedback.azure.com/forums/929833-azure-stack-hci).

## <a name="azure-stack-hci-general-faqs"></a>Preguntas frecuentes generales sobre Azure Stack HCI

**¿Qué relación existe entre Azure Stack HCI y Windows Server?**

Windows Server es la base de casi todos los productos de Azure y todas las características que le gustan se siguen lanzando con soporte técnico en Windows Server. La oferta inicial de Azure Stack HCI se basaba en Windows Server 2019 y usaba el modelo de licencias tradicional de este. En la actualidad, Azure Stack HCI tiene su propio sistema operativo y modelo de licencias basado en suscripciones. Azure Stack HCI es la manera recomendada de implementar HCI en el entorno local mediante el hardware de nuestros asociados validado por Microsoft.

**¿Puedo actualizar desde Windows Server 2019 a Azure Stack HCI?**

En este momento no hay ninguna actualización local de Windows Server a Azure Stack HCI. Manténgase atento a la guía de migración específica para los clientes que ejecutan clústeres hiperconvergidos basados en Windows Server 2019 y 2016.

**¿Qué servicios de Azure puedo conectar a Azure Stack HCI?**

Para una lista actualizada de los servicios de Azure a los que puede conectar Azure Stack HCI, consulte [Connecting Windows Server to Azure hybrid services](/windows-server/manage/windows-admin-center/azure/index) (Conexión de Windows Server a los servicios híbridos de Azure).

**¿Qué tienen en común las soluciones de Azure Stack Hub y Azure Stack HCI?**

Las características de Azure Stack HCI presentan las mismas tecnologías de proceso, almacenamiento y red definidas por software y basadas en Hyper-V que Azure Stack Hub. Ambas ofertas cumplen rigurosos criterios de prueba y validación para garantizar la confiabilidad y la compatibilidad con la plataforma de hardware subyacente.

**¿En qué se diferencian?**

Con Azure Stack Hub, ejecuta servicios en la nube en el entorno local. Puede ejecutar servicios de IaaS y PaaS de Azure en el entorno local para compilar y ejecutar de forma coherente aplicaciones en la nube en cualquier lugar, administrados desde Azure Portal en el entorno local.

Con Azure Stack HCI, ejecuta cargas de trabajo virtualizadas en el entorno local, administradas con Windows Admin Center y herramientas familiares de Windows Server. También puede conectarse a Azure para escenarios híbridos como la recuperación del sitio basada en la nube, la supervisión, etc.

**¿Puedo actualizar de Azure Stack HCI a Azure Stack Hub?**

No, pero los clientes pueden migrar sus cargas de trabajo de Azure Stack HCI a Azure Stack Hub o Azure.

**¿Cómo identifico un servidor de Azure Stack HCI?**

Windows Admin Center muestra el sistema operativo en la lista Todas las conexiones y en otros lugares, o puede usar el siguiente comando de PowerShell para consultar el nombre y la versión del sistema operativo.

```PowerShell
Get-ComputerInfo -Property 'osName', 'osDisplayVersion'
```

A continuación se muestra una salida de ejemplo:

```
OsName                    OSDisplayVersion
------                    ----------------
Microsoft Azure Stack HCI 20H2
```
