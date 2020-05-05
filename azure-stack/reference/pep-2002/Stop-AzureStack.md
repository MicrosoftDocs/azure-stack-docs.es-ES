---
title: Punto de conexión con privilegios de Stop-AzureStack para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Stop-AzureStack'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 946d31132e4a916574e8fb051e9761f81eb7cdf2
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564001"
---
# <a name="stop-azurestack"></a>Stop-AzureStack

## <a name="synopsis"></a>Sinopsis
Detiene todos los servicios de Azure Stack Hub.

## <a name="syntax"></a>Sintaxis

```
Stop-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Detiene todos los servicios de Azure Stack y los equipos físicos en los que se ejecuta Azure Stack Hub.

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