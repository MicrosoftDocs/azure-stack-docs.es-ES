---
title: Punto de conexión con privilegios de Get-GraphApplication para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Get-GraphApplication'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: baccf889139b2a91b23b55ef9bb86387991558da
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563551"
---
# <a name="get-graphapplication"></a>Get-GraphApplication

## <a name="synopsis"></a>Sinopsis
Get-GraphApplication es una función contenedora para obtener la información de la aplicación de Graph para el nombre o identificador de la aplicación especificados.

## <a name="syntax"></a>Sintaxis

```
Get-GraphApplication [[-ApplicationIdentifier] <Object>] [-ApplicationName <Object>] [-AsJob]
```

## <a name="description"></a>Descripción
Invoca a Get-GraphApplicationGroup en la máquina de AD FS para obtener la información de la aplicación de gráficos.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Get-GraphApplication -ApplicationName $ApplicationName
```

## <a name="parameters"></a>Parámetros

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Identificador de la aplicación del registro de aplicaciones en AD FS.

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

### <a name="-applicationname"></a>-ApplicationName
Nombre de la aplicación registrada en AD FS.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
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