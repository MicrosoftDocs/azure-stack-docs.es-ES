---
title: Punto de conexión con privilegios de New-GraphApplication para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: New-GraphApplication'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: e9bd1d8fa51108f6384b29f33cf6cc0ea8cc166e
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563797"
---
# <a name="new-graphapplication"></a>New-GraphApplication

## <a name="synopsis"></a>Sinopsis
New-GraphApplication es una función contenedora para llamar a los cmdlets de Graph de ADFS en AD FS.

## <a name="syntax"></a>Sintaxis

```
New-GraphApplication [-ClientCertificates <Object>] [-Name <Object>] [-ClientRedirectUris <Object>]
 [-GenerateClientSecret] [-AsJob]
```

## <a name="description"></a>Descripción
Invoca a New-GraphApplicationGroup en AD FS para agregar una nueva aplicación en una máquina de AD FS.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>Parámetros

### <a name="-name"></a>-Name
Nombre de la aplicación con una longitud máxima de 50 caracteres. Se modificará como `Azurestack-$Name-$({guid}::{NewGuid}())`y lo devuelve la función.

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

### <a name="-generateclientsecret"></a>-GenerateClientSecret
 

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