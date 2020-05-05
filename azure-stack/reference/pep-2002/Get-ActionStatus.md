---
title: Punto de conexión con privilegios de Get-ActionStatus para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Get-ActionStatus'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f7300224d6b512361c978c33a2f599dfb9a1d852
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563617"
---
# <a name="get-actionstatus"></a>Get-ActionStatus

## <a name="synopsis"></a>Sinopsis
Obtiene el estado de la acción más reciente de la operación con el nombre de función especificado.

## <a name="syntax"></a>Sintaxis

```
Get-ActionStatus [[-ActionType] <Object>] [[-FunctionName] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Obtiene el estado de la acción más reciente de la operación con el nombre de función especificado.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Get-ActionStatus -FunctionName "Start-SecretRotation"
```

## <a name="parameters"></a>Parámetros

### <a name="-functionname"></a>-FunctionName
Nombre de la función para la que se consulta el estado.

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

### <a name="-actiontype"></a>-ActionType
Nombre del tipo de acción de la operación.

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

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).
