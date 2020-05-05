---
title: Punto de conexión con privilegios de Close-PrivilegedEndpoint para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Close-PrivilegedEndpoint'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: dafeb8c162ef79b5522a80d398f2bc440164cdc2
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563773"
---
# <a name="register-customdnsserver"></a>Register-CustomDnsServer

## <a name="synopsis"></a>Sinopsis
Script para registrar servidores DNS personalizados en el DNS de Azure Stack Hub. Este script creará el reenvío condicional en el DNS de Azure Stack Hub.

## <a name="syntax"></a>Sintaxis

```
Register-CustomDnsServer [[-TimeoutInSecs] <Object>] [[-CustomDomainName] <Object>]
 [[-CustomDnsIPAddresses] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Script para registrar servidores DNS personalizados en Azure Stack DNS.
Este script creará el reenvío condicional en el DNS de Azure Stack.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "10.20.20.01","10.20.20.02" -TimeoutInSecs 1000
```

## <a name="parameters"></a>Parámetros

### <a name="-customdomainname"></a>-CustomDomainName
 

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

### <a name="-customdnsipaddresses"></a>-CustomDnsIPAddresses
 

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