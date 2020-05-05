---
title: Punto de conexión con privilegios de Start-AzureStack para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Start-AzureStack'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 70b0992239fc2f1f5cc5bb4b7ffb4b8615c94c0d
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563995"
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

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).