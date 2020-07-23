---
title: Punto de conexión con privilegios New-AzureBridgeServicePrincipal para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: New-AzureBridgeServicePrincipal'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ffcf27ad55cdc4a5456f89fb076ca42120078d5d
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486791"
---
# <a name="new-azurebridgeserviceprincipal"></a>New-AzureBridgeServicePrincipal

## <a name="synopsis"></a>Sinopsis
Crea una nueva entidad de servicio en Azure Active Directory (Azure AD).

## <a name="syntax"></a>Sintaxis

```
New-AzureBridgeServicePrincipal [[-TenantId] <Object>] [[-AzureEnvironment] <Object>]
 [[-TimeoutInSeconds] <Object>] [[-RefreshToken] <Object>] [-AsJob]
```

## <a name="parameters"></a>Parámetros

### <a name="-azureenvironment"></a>-AzureEnvironment
 

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

### <a name="-tenantid"></a>-TenantId
 

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

### <a name="-refreshtoken"></a>-RefreshToken
 

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

### <a name="-timeoutinseconds"></a>-TimeoutInSeconds
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: 420
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
