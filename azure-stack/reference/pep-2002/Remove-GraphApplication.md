---
title: Punto de conexión con privilegios de Remove-GraphApplication para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Remove-GraphApplication'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 164a20928f312d2ffa4dea73f54c05f41809ebf5
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563719"
---
# <a name="remove-graphapplication"></a>Remove-GraphApplication

## <a name="synopsis"></a>Sinopsis
Remove-GraphApplication es una función contenedora para llamar a los cmdlets de Graph de AD FS en AD FS.

## <a name="syntax"></a>Sintaxis

```
Remove-GraphApplication [[-ApplicationIdentifier] <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Invoca a Remove-GraphApplication en AD FS para eliminar una aplicación especificada en una máquina de AD FS.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Remove-GraphApplication -ApplicationIdentifier "Application-Identifier-123456"
```

## <a name="parameters"></a>Parámetros

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Identificador de la aplicación que se va a eliminar.

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