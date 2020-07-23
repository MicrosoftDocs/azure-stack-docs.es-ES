---
title: Punto de conexión con privilegios de Get-AzureStackStampInformation para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Get-AzureStackStampInformation'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ec2fb2a7e23cbce90d0ab8ebd75ed33a3e446013
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487216"
---
# <a name="get-azurestackstampinformation"></a>Get-AzureStackStampInformation

## <a name="synopsis"></a>Sinopsis
Obtiene la información de la unidad de escalado.

## <a name="syntax"></a>Sintaxis

```
Get-AzureStackStampInformation [-AsJob]
```

## <a name="description"></a>Descripción
Importa el módulo cliente de ECE e inicia un plan de acción en el rol en la nube en el servicio ECE.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Get-AzureStackStampInformation
```

## <a name="parameters"></a>Parámetros

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
