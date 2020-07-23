---
title: Punto de conexión con privilegios de Start-AzureStack para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Start-AzureStack'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 2a8814182d518c7327a64324cdc844018e989f18
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86485856"
---
# <a name="start-azurestack"></a>Start-AzureStack

## <a name="synopsis"></a>Sinopsis
Inicia todos los servicios de Azure Stack Hub.

## <a name="syntax"></a>Sintaxis

```
Start-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Inicia todos los servicios de Azure Stack Hub.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```powershell
PS C:\> 
```



## <a name="parameters"></a>Parámetros

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
Cantidad máxima de tiempo después del cual se detendrá la ejecución.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 2400
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
