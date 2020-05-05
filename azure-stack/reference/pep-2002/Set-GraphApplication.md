---
title: Punto de conexión con privilegios de Set-GraphApplication para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Set-GraphApplication'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 745caa2016e4a8430d58d3644accbafe7e0923b6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563671"
---
# <a name="set-graphapplication"></a>Set-GraphApplication

## <a name="synopsis"></a>Sinopsis
Set-GraphApplication es una función contenedora para llamar a los cmdlets de Graph de ADFS en AD FS.

## <a name="syntax"></a>Sintaxis

```
Set-GraphApplication [-ClientCertificates <Object>] [-ClientRedirectUris <Object>]
 [-ApplicationIdentifier <Object>] [-ChangeClientSecret] [-ResetClientSecret] [-AsJob]
```

## <a name="description"></a>Descripción
Invoca a Set-GraphApplicationGroup en AD FS para modificar una aplicación en una máquina de AD FS.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>Parámetros

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
 

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

### <a name="-clientredirecturis"></a>-ClientRedirectUris
URI de redirección que se usa para crear una nueva aplicación en AD FS.

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

### <a name="-clientcertificates"></a>-ClientCertificates
Certificado que se usa para crear una nueva aplicación en AD FS.

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

### <a name="-resetclientsecret"></a>-ResetClientSecret
 

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

### <a name="-changeclientsecret"></a>-ChangeClientSecret
 

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