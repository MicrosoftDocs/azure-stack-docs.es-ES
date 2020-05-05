---
title: Punto de conexión con privilegios de Get-AzureStackSupportConfiguration para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Get-AzureStackSupportConfiguration'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 710201cc045b356037a54e6f7f0eeee63284b0d6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563545"
---
# <a name="get-azurestacksupportconfiguration"></a>Get-AzureStackSupportConfiguration

## <a name="synopsis"></a>Sinopsis
Obtiene las opciones de configuración del servicio de soporte técnico.

## <a name="syntax"></a>Sintaxis

```
Get-AzureStackSupportConfiguration [-IncludeRegistrationObjectId] [-AsJob]
```

## <a name="description"></a>Descripción
Opciones de configuración del servicio de soporte técnico.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
En el ejemplo siguiente se obtienen los detalles de registro si se registró el stamp o, de lo contrario, null.

```
PS  C:\> Get-AzureStackSupportConfiguration
```

## <a name="parameters"></a>Parámetros

### <a name="-includeregistrationobjectid"></a>-IncludeRegistrationObjectId
Opcional.
Requiere conectividad a Internet.
Recupera el identificador de objeto de la identidad del registro.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>-AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="notes"></a>Notas
Requiere que la máquina virtual de respaldo tenga conectividad a Internet.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).