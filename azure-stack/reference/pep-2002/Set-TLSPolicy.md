---
title: Punto de conexión con privilegios de Set-TLSPolicy para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Set-TLSPolicy'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a662ccbc76d25a936bd91bfe7ec499458c538321
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563623"
---
# <a name="set-tlspolicy"></a>Set-TLSPolicy

## <a name="syntax"></a>Sintaxis

```
Set-TLSPolicy [[-Version] <Object>] [-AsJob]
```

## <a name="parameters"></a>Parámetros

### <a name="-version"></a>-Version
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Accepted values: TLS_1.2, TLS_All

Required: False
Position: 0
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