---
title: Punto de conexión con privilegios de Reset-DatacenterIntegrationConfiguration para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Reset-DatacenterIntegrationConfiguration'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 42cf50694c333b91d110ac921be8b616bbc93107
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563695"
---
# <a name="reset-datacenterintegrationconfiguration"></a>Reset-DatacenterIntegrationConfiguration

## <a name="synopsis"></a>Sinopsis
Script para restablecer los cambios en la integración del centro de datos.

## <a name="syntax"></a>Sintaxis

```
Reset-DatacenterIntegrationConfiguration [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Script para restablecer los cambios en la integración del centro de datos.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Reset-DatacenterIntegrationConfiguration -TimeoutInSecs 2000
```

## <a name="parameters"></a>Parámetros

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 1000
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