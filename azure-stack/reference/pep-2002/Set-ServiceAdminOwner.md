---
title: Punto de conexión con privilegios de Set-ServiceAdminOwner para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Set-ServiceAdminOwner'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b44a15c2b5ae1569e747a0c371d11950b5a40297
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563665"
---
# <a name="set-serviceadminowner"></a>Set-ServiceAdminOwner

## <a name="synopsis"></a>Sinopsis
Script para actualizar el administrador de servicios.

## <a name="syntax"></a>Sintaxis

```
Set-ServiceAdminOwner [[-TimeoutInSecs] <Object>] [[-ServiceAdminOwnerUpn] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Script para actualizar el UPN del administrador de servicios.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Set-ServiceAdminOwner -NewServiceAdminUpn "administrator@contoso.com" -TimeoutInSecs 1000
```

## <a name="parameters"></a>Parámetros

### <a name="-serviceadminownerupn"></a>-ServiceAdminOwnerUpn
 

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

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
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