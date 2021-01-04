---
title: Register-CustomDnsServer
description: 'Obtenga información sobre el script para registrar servidores DNS personalizados en el DNS de Azure Stack Hub: Register-CustomDnsServer.'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 88d24d8ee32f82f9f19f256249241bf8b0060641
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598273"
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

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
