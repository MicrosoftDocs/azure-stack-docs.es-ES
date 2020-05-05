---
title: Punto de conexión con privilegios de Register-DirectoryService para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios elevados de Azure Stack para PowerShell: Register-DirectoryService'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 4ac71d57c0f2e5028883a4bede539fdf89a45afb
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563737"
---
# <a name="register-directoryservice"></a>Register-DirectoryService

## <a name="synopsis"></a>Sinopsis
Script para registrar Active Directory (Azure AD) del cliente en el servicio Graph.
**CustomADGlobalCatalog** es el catálogo global de Active Directory personalizado.
Este será el valor `RootDomain` de la salida del cmdlet **Get-ADForest**.

## <a name="syntax"></a>Sintaxis

```
Register-DirectoryService [-Force] [[-TimeoutInSecs] <Object>] [-SkipRootDomainValidation]
 [[-CustomADAdminCredential] <Object>] [-ValidateParameters] [[-CustomADGlobalCatalog] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Script para registrar Active Directory del cliente en el servicio Graph.
**CustomADGlobalCatalog** es el catálogo global de Active Directory personalizado.
Este será el valor `RootDomain` de la salida del cmdlet **Get-ADForest**.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Register-DirectoryService -CustomADGlobalCatalog "contoso.com" -CustomADAdminCredential (Get-Credential) -TimeoutInSecs 1000
```

## <a name="parameters"></a>Parámetros

### <a name="-customadglobalcatalog"></a>-CustomADGlobalCatalog
 

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

### <a name="-customadadmincredential"></a>-CustomADAdminCredential
 

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

### <a name="-validateparameters"></a>-ValidateParameters
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skiprootdomainvalidation"></a>-SkipRootDomainValidation
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-force"></a>-Force
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
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