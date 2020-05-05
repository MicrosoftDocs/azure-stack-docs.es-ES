---
title: Punto de conexión con privilegios de Get-AzureStackStampInformation para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Get-AzureStackStampInformation'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 3318ce344804174fe3252808d5857e93b8113e22
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563557"
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

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).