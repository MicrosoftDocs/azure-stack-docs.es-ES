---
title: Medidores de facturación locales | Microsoft Docs
description: Lista de medidores locales de Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/20/2019
ms.openlocfilehash: b1c26bf286165c66382544016ccc7814b1e2819f
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330299"
---
# <a name="local-azure-stack-billing-meters"></a>Medidores de facturación locales de Azure Stack

En este artículo se enumeran los medidores de facturación locales de Azure Stack. Se informa del uso para los siguientes proveedores de recursos:

## <a name="network"></a>Red

**Id. de medidor** : F271A8A388C44D93956A063E1D2FA80B  
**Nombre de medidor:** : Uso de la dirección IP estática  
**Unidad** : Direcciones IP  
**Notas** : Número de direcciones IP usadas. Si llama a la API de uso con una granularidad diaria, el medidor devuelve la dirección IP multiplicada por el número de horas.  
  
**Id. de medidor** : 9E2739BA86744796B465F64674B822BA  
**Nombre de medidor:** : Uso de la dirección IP dinámica  
**Unidad** : Direcciones IP  
**Notas** : Número de direcciones IP usadas. Si llama a la API de uso con una granularidad diaria, el medidor devuelve la dirección IP multiplicada por el número de horas.

## <a name="storage"></a>Storage

**Id. de medidor** : B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nombre de medidor:** : TableCapacity  
**Unidad** : GB\*horas  
**Notas** : Capacidad total consumida por las tablas  
  
**Id. de medidor** : B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nombre de medidor:** : PageBlobCapacity  
**Unidad** : GB\*horas  
**Notas** : Capacidad total consumida por blobs en páginas  
  
**Id. de medidor** : B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nombre de medidor:** : QueueCapacity  
**Unidad** : GB\*horas  
**Notas** : Capacidad total consumida por la cola  
  
**Id. de medidor** : 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nombre de medidor:** : BlockBlobCapacity  
**Unidad** : GB * horas  
**Notas** : Capacidad total consumida por blobs en bloques  
  
**Id. de medidor** : B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nombre de medidor:** : TableTransactions  
**Unidad** : Recuento de solicitudes * 10 000  
**Notas** : Solicitudes de Table service (* 10 000).  
  
**Id. de medidor** : 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nombre de medidor:** : TableDataTransIn  
**Unidad** : Datos de entrada en GB  
**Notas** : Entrada de datos de Table service en GB  
  
**Id. de medidor** : 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nombre de medidor:** : TableDataTransOut  
**Unidad** : Salida en GB  
**Notas** : Salida de datos de Table service en GB.
  
**Id. de medidor** : 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nombre de medidor:** : BlobTransactions  
**Unidad** : Recuento de solicitudes * 10 000 **Notas** : Solicitudes de Blob service (* 10 000).  
  
**Id. de medidor** : 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nombre de medidor:** : BlobDataTransIn  
**Unidad** : Datos de entrada en GB  
**Notas** : Entrada de datos de Blob service en GB  
  
**Id. de medidor** : 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nombre de medidor:** : BlobDataTransOut  
**Unidad** : Salida en GB  
**Notas** : Salida de datos de Blob service en GB  
  
**Id. de medidor** : EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nombre de medidor:** : QueueTransactions  
**Unidad** : Recuento de solicitudes * 10 000 **Notas** : Solicitudes de Queue service (* 10 000).  
  
**Id. de medidor** : E518E809-E369-4A45-9274-2017B29FFF25  
**Nombre de medidor:** : QueueDataTransIn  
**Unidad** : Datos de entrada en GB  
**Notas** : Entrada de datos de Queue service en GB  
  
**Id. de medidor** : DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nombre de medidor:** : QueueDataTransOut  
**Unidad** : Salida en GB  
**Notas** : Salida de datos de Queue service en GB

## <a name="compute"></a>Proceso

**Id. de medidor** : FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nombre de medidor:** : Horas de tamaño de la máquina virtual base  
**Unidad** : Total de núcleos virtuales  
**Notas** : Número de núcleos virtuales multiplicado por las horas que se ejecutó la máquina virtual  
  
**Id. de medidor** : 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nombre de medidor:** : Horas de tamaño de la máquina virtual Windows  
**Unidad** : Total de núcleos virtuales  
**Notas** : Número de núcleos virtuales multiplicado por las horas que se ejecutó la máquina virtual  
  
**Id. de medidor** : 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nombre de medidor:** : Horas de tamaño de la máquina virtual  
**Unidad** : Horas de la máquina virtual  
**Notas** : Captura de VM base y Windows. No se ajusta para los núcleos.

## <a name="managed-disks"></a>Managed Disks

**Id. de medidor** : 380874f9-300c-48e0-95a0-d2d9a21ade8f **Nombre de medidor** : **Unidad** S4: Número de discos * mes **Notas** : Disco administrado Estándar: 32 GB

**Id. de medidor** : 1b77d90f-427b-4435-b4f1-d78adec53222 **Nombre de medidor** : **Unidad** S6: Número de discos * mes **Notas** : Disco administrado Estándar: 64 GB

**Id. de medidor** : d5f7731b-f639-404a-89d0-e46186e22c8d **Nombre de medidor** : **Unidad** S10: Número de discos * mes **Notas** : Disco administrado Estándar: 128 GB

**Id. de medidor** : ff85ef31-da5b-4eac-95dd-a69d6f97b18a **Nombre de medidor** : **Unidad** S15: Número de discos * mes **Notas** : Disco administrado Estándar: 256 GB

**Id. de medidor** : 88ea9228-457a-4091-adc9-ad5194f30b6e **Nombre de medidor** : **Unidad** S20: Número de discos * mes **Notas** : Disco administrado Estándar: 512 GB

**Id. de medidor** : 5b1db88a-8596-4002-8052-347947c26940 **Nombre de medidor** : **Unidad** S30: Número de discos * mes **Notas** : Disco administrado Estándar: 1024 GB

**Id. de medidor** : 7660b45b-b29d-49cb-b816-59f30fbab011 **Nombre de medidor** : **Unidad** P4: Número de discos * mes **Notas** : Disco administrado premium: 32 GB

**Id. de medidor** : 817007fd-a077-477f-bc01-b876f27205fd **Nombre de medidor** : **Unidad** P6: Número de discos * mes **Notas** : Disco administrado premium: 64 GB

**Id. de medidor** : e554b6bc-96cd-4938-a5b5-0da990278519 **Nombre de medidor** : **Unidad** P10: Número de discos * mes **Notas** : Disco administrado premium: 128 GB  

**Id. de medidor** : cdc0f53a-62a9-4472-a06c-e99a23b02907 **Nombre de medidor** : **Unidad** P15: Número de discos * mes **Notas** : Disco administrado premium: 256 GB

**Id. de medidor** : b9cb2d1a-84c2-4275-aa8b-70d2145d59aa **Nombre de medidor** : **Unidad** P20: Número de discos * mes **Notas** : Disco administrado premium: 512 GB

**Id. de medidor** : 06bde724-9f94-43c0-84c3-d0fc54538369 **Nombre de medidor** : **Unidad** P30: Número de discos * mes **Notas** : Disco administrado premium: 1024 GB

**Id. de medidor** : 7ba084ec-ef9c-4d64-a179-7732c6cb5e28 **Nombre de medidor** : ActualStandardDiskSize **Unidad** : GB * mes **Notas** : tamaño real en disco del disco administrado estándar.

**Identificador del medidor** : daef389a-06e5-4684-a7f7-8813d9f792d5 **Nombre del medidor** : ActualPremiumDiskSize **Unidad** : GB * mes **Notas** : tamaño real en disco del disco administrado premium.

**Id. de medidor** : 108fa95b-be0d-4cd9-96e8-5b0d59505df1 **Nombre del medidor** : ActualStandardSnapshotSize **Unidad** : GB * mes **Notas** : tamaño real en disco de la instantánea estándar administrada.

**Id. de medidor** : 578ae51d-4ef9-42f9-85ae-42b52d3d83ac **Nombre de medidor** : ActualPremiumSnapshotSize **Unidad** : GB * mes **Notas** : Tamaño real en disco de la instantánea prémium administrada.

**Id. de medidor** : 5d76e09f-4567-452a-94cc-7d1f097761f0 **Nombre de medidor** : **Unidad** S4: Número de discos * horas **Notas** : Disco administrado Estándar: 32 GB (en desuso)

**Id. de medidor** : dc9fc6a9-0782-432a-b8dc-978130457494 **Nombre de medidor** : **Unidad** S6: Número de discos * horas **Notas** : Disco administrado Estándar: 64 GB (en desuso)

**Id. de medidor** : e5572fce-9f58-49d7-840c-b168c0f01fff **Nombre de medidor** : **Unidad** S10: Número de discos * horas **Notas** : Disco administrado Estándar: 128 GB (en desuso)

**Id. de medidor** : 9a8caedd-1195-4cd5-80b4-a4c22f9302b8 **Nombre de medidor** : **Unidad** S15: Número de discos * horas **Notas** : Disco administrado Estándar: 256 GB (en desuso)

**Id. de medidor** : 5938f8da-0ecd-4c48-8d5a-c7c6c23546be **Nombre de medidor** : **Unidad** S20: Número de discos * horas **Notas** : Disco administrado Estándar: 512 GB (en desuso)

**Id. de medidor** : 7705a158-bd8b-4b2b-b4c2-0782343b81e6 **Nombre de medidor** : **Unidad** S30: Número de discos * horas **Notas** : Disco administrado Estándar: 1024 GB (en desuso)

**Id. de medidor** : 5c105f5f-cbdf-435c-b49b-3c7174856dcc **Nombre de medidor** : **Unidad** P4: Número de discos * horas **Notas** : Disco administrado Premium: 32 GB (en desuso)

**Id. de medidor** : 518b412b-1927-4f25-985f-4aea24e55c4f **Nombre de medidor** : **Unidad** P6: Número de discos * horas **Notas** : Disco administrado Premium: 64 GB (en desuso)

**Id. de medidor** : 5cfb1fed-0902-49e3-8217-9add946fd624 **Nombre de medidor** : **Unidad** P10: Número de discos * horas **Notas** : Disco administrado Premium: 128 GB (en desuso)

**Id. de medidor** : 8de91c94-f740-4d9a-b665-bd5974fa08d4 **Nombre de medidor** : **Unidad** P15: Número de discos * horas **Notas** : Disco administrado Premium: 256 GB (en desuso)

**Id. de medidor** : c7e7839c-293b-4761-ae4c-848eda91130b **Nombre de medidor** : **Unidad** P20: Número de discos * horas **Notas** : Disco administrado Premium: 512 GB (en desuso)

**Id. de medidor** : 9f502103-adf4-4488-b494-456c95d23a9f **Nombre de medidor** : **Unidad** P30: Número de discos * horas **Notas** : Disco administrado Premium: 1024 GB (en desuso)

**Id. de medidor** : 8a409390-1913-40ae-917b-08d0f16f3c38 **Nombre de medidor** : ActualStandardDiskSize **Unidad** : Bytes * horas **Notas** : tamaño real en disco del disco administrado estándar (en desuso).

**Id. de medidor** : 1273b16f-8458-4c34-8ce2-a515de551ef6 **Nombre del medidor** : ActualPremiumDiskSize **Unidad** : Bytes * horas **Notas** : tamaño real en disco del disco administrado estándar (en desuso).

**Id. de medidor** : 89009682-df7f-44fe-aeb1-63fba3ddbf4c **Nombre del medidor** : ActualStandardSnapshotSize **Unidad** : Bytes * horas **Notas** : tamaño real en disco de la instantánea estándar administrada (en desuso).

**Id. de medidor** : 95b0c03f-8a82-4524-8961-ccfbf575f536 **Nombre de medidor** : ActualPremiumSnapshotSize **Unidad** : Bytes * horas **Notas** : Tamaño real en disco de la instantánea prémium administrada (en desuso).

**Id. de medidor** : 75d4b707-1027-4403-9986-6ec7c05579c8 **Nombre del medidor** : ActualStandardSnapshotSize **Unidad** : GB * mes **Notas** : tamaño real en disco de la instantánea estándar administrada (en desuso).

**Id. de medidor** : 5ca1cbb9-6f14-4e76-8be8-1ca91547965e **Nombre del medidor** : ActualPremiumSnapshotSize **Unidad** : GB * mes **Notas** : Tamaño real en disco de la instantánea prémium administrada (en desuso).

### <a name="sql-rp"></a>Proveedor de recursos de SQL
  
**Id. de medidor** : CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nombre de medidor:** : DatabaseSizeHourSqlMeter  
**Unidad** : MB * horas  
**Notas** : Capacidad total de BD en la creación. Si llama a la API de uso con una granularidad diaria, el medidor devuelve los MB multiplicados por el número de horas.

## <a name="mysql-rp"></a>Proveedor de recursos de MySql
  
**Id. de medidor** : E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 **Nombre del medidor** : DatabaseSizeHourMySqlMeter **Unidad** : MB * horas **Notas** : Capacidad total de BD en la creación. Si llama a la API de uso con una granularidad diaria, el medidor devuelve los MB multiplicados por el número de horas.

## <a name="key-vault"></a>Key Vault
  
**Id. de medidor** : EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 **Nombre del medidor** : Transacciones de Key Vault **Unidad** : Recuento de solicitudes * 10 000 **Notas** : Número de solicitudes de API REST recibidas por plan de datos de Key Vault  
  
**Id. de medidor** : 2C354225-B2FE-42E5-AD89-14F0EA302C87 **Nombre del medidor** : Transacciones de claves avanzadas **Unidad** :  10 000 transacciones **Notas** : Transacciones de claves RSA 3K/4K, ECC. (versión preliminar)

## <a name="app-service"></a>App Service
  
**Id. de medidor** : 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA **Nombre del medidor** : App Service **Unidad** : Total de núcleos virtuales **Notas** : Número de núcleos virtuales usados para ejecutar App Service Nota: Microsoft usa este medidor para realizar los cargos por App Service en Azure Stack. Los proveedores de soluciones en la nube pueden usar los otros medidores de App Service (abajo) para calcular el uso en sus inquilinos.
  
**Id. de medidor** : 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nombre de medidor:** : Solicitudes de funciones **Unidad** : 10 solicitudes **Notas** : Número total de ejecuciones solicitadas (por 10 ejecuciones). Las ejecuciones se cuentan cada vez que se ejecuta una función en respuesta a un evento o se desencadena por un enlace.
  
**Id. de medidor** : D1D04836-075C-4F27-BF65-0A1130EC60ED **Nombre del medidor** : Funciones - Proceso **Unidad** :  GB **Notas** :  Consumo de recursos medido en gigabytes por segundo (GB/s). El **consumo de recursos observado** se calcula multiplicando el tamaño medio de memoria en GB por el tiempo en milisegundos que tarda en ejecutarse la función. Para medir la memoria que se usa en una función, se redondea al alza a los 128 MB más cercanos hasta un tamaño de memoria máximo de 1536 MB; y para calcular el tiempo de ejecución, se redondea al alza hasta el 1 ms más cercano. El tiempo de ejecución y la memoria mínimos para la ejecución de una única función es de 100 ms y 128 MB, respectivamente.
  
**Id. de medidor** : 957E9F36-2C14-45A1-B6A1-1723EF71A01D **Nombre del medidor** : Horas de App Service compartido **Unidad** : 1 hora **Notas** : Por cada uso de hora del plan de App Service de partición. Los planes se miden por aplicación.
  
**Id. de medidor** : 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 **Nombre del medidor** : Horas de App Service gratuito **Unidad** : 1 hora **Notas** : Por cada uso de hora del plan de App Service gratuito. Los planes se miden por aplicación.
  
**Id. de medidor** : 88039D51-A206-3A89-E9DE-C5117E2D10A6 **Nombre del medidor** : Horas de App Service para instancias pequeñas estándar **Unidad** : 1 hora **Notas** : Se calcula en función del tamaño y el número de instancias.
  
**Id. de medidor** : 83A2A13E-4788-78DD-5D55-2831B68ED825 **Nombre del medidor** : Horas de App Service para instancias medianas estándar **Unidad** : 1 hora **Notas** : Se calcula en función del tamaño y el número de instancias.
  
**Id. de medidor** : 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 **Nombre del medidor** : Horas de App Service para instancias grandes estándar **Unidad** : 1 hora **Notas** : Se calcula en función del tamaño y el número de instancias.

## <a name="custom-worker-tiers"></a>Niveles de trabajo personalizados
  
**Id. de medidor** : *Niveles de trabajo personalizados*
**Nombre de medidor** : Niveles de trabajo personalizados  
**Unidad** : Horas **Notas** : El identificador del medidor determinista se crea en función de la SKU y del nombre del nivel de trabajo personalizado. Este identificador de medidor es único para cada nivel de trabajo personalizado.
  
**Id. de medidor** : 264ACB47-AD38-47F8-ADD3-47F01DC4F473 **Nombre del medidor** : SSL basada en SNI **Unidad** : Por enlace de SSL basada en SNI **Notas** : App Service admite dos tipos de conexiones SSL: conexiones SSL con Indicación de nombre de servidor (SNI) y conexiones SSL de dirección IP. SSL basado en SNI funciona en los exploradores modernos, mientras que SSL basado en IP funciona en todos los exploradores.
  
**Id. de medidor** : 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nombre de medidor:** : **Unidad** SSL de IP: Por enlace SSL basada en IP **Notas** : App Service admite dos tipos de conexiones SSL: conexiones SSL con Indicación de nombre de servidor (SNI) y conexiones SSL de dirección IP. SSL basado en SNI funciona en los exploradores modernos, mientras que SSL basado en IP funciona en todos los exploradores.
  
**Id. de medidor** : 73215A6C-FA54-4284-B9C1-7E8EC871CC5B **Nombre del medidor** :  Proceso web **Unidad** : **Notas** : Se calcula por cada sitio activo por hora.
  
**Id. de medidor** : 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nombre de medidor:** : Ancho de banda de salida externo **Unidad** : GB **Notas** : Bytes de respuesta de solicitud de entrada total + bytes de solicitud de salida total + bytes de respuesta de solicitud FTP de entrada total + bytes de respuesta de solicitud de implementación web de entrada total.