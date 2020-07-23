---
title: Punto de conexión con privilegios de Send-AzureStackDiagnosticLog para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Send-AzureStackDiagnosticLog'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 2b62eb35f096ab5b38ee37ff776435bb90b7c210
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486196"
---
# <a name="send-azurestackdiagnosticlog"></a>Send-AzureStackDiagnosticLog

## <a name="synopsis"></a>Sinopsis
Envía registros de diagnóstico de Azure Stack Hub a Microsoft.

## <a name="syntax"></a>Sintaxis

```
Send-AzureStackDiagnosticLog [[-FromDate] <Object>] [[-FilterByResourceProvider] <Object>]
 [[-FilterByRole] <Object>] [[-ToDate] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Realiza una solicitud a Support Bridge Service para cargar registros de diagnóstico para el stamp.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

En el ejemplo siguiente se envían los registros de las 4 últimas horas a Microsoft.

```
PS  C:\> Send-AzureStackDiagnosticLog
```

### <a name="example-2"></a>Ejemplo 2
En el ejemplo siguiente se envían los registros del servicio de soporte técnico y WAS de las 4 últimas horas a Microsoft.
```
PS  C:\> Send-AzureStackDiagnosticLog -FilterByRole SupportBridgeController,WAS
```

## <a name="parameters"></a>Parámetros

### <a name="-filterbyrole"></a>-FilterByRole
Opcional.
Filtra por tipo de rol de infraestructura.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-filterbyresourceprovider"></a>-FilterByResourceProvider
Opcional.
Filtra por el tipo de proveedor de recursos de valor agregado.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-fromdate"></a>-FromDate
Opcional.
Hora de inicio que se va a usar para recopilar los registros.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-todate"></a>-ToDate
Opcional.
Hora de finalización que se va a usar para recopilar los registros.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
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

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
