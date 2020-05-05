---
title: Punto de conexión con privilegios de Get-AzureStackLog para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Get-AzureStackLog'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: d7e7ff712e1d8f7c062d7b3a7f38d97b80085e31
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563893"
---
# <a name="get-azurestacklog"></a>Get-AzureStackLog

## <a name="synopsis"></a>Sinopsis
Obtén registros de varios roles de Azure Stack Hub con tiempo de expiración.
Funciona para entornos de varios nodos o de un nodo.

## <a name="syntax"></a>Sintaxis

```
Get-AzureStackLog [-FilterByRole <Object>] [-CustomerConfigurationFilePath <Object>] [-OutputPath <Object>]
 [-LocalAdminCredential <Object>] [-FilterByResourceProvider <Object>] [-InputSasUri <Object>]
 [-FilterByNode <Object>] [-OutputShareCredential <Object>] [-ToDate <Object>] [-FilterByLogType <Object>]
 [-TimeOutInMinutes <Object>] [-FromDate <Object>] [-OutputSharePath <Object>] [-OutputSasUri <Object>]
 [-PerformVMLogRecovery] [-IncludeDumpFile] [-AsJob]
```

## <a name="parameters"></a>Parámetros

### <a name="-filterbyrole"></a>-FilterByRole
 

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

### <a name="-filterbyresourceprovider"></a>-FilterByResourceProvider
 

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

### <a name="-filterbynode"></a>-FilterByNode
 

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

### <a name="-filterbylogtype"></a>-FilterByLogType
 

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

### <a name="-customerconfigurationfilepath"></a>-CustomerConfigurationFilePath
 

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

### <a name="-fromdate"></a>-FromDate
Este obtendrá los registros de la última hora.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: (Get-Date).AddHours(-1)
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-todate"></a>-ToDate
Este obtendrá los registros hasta la fecha actual.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: (Get-Date)
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-inputsasuri"></a>-InputSasUri
URI de SAS de Blob service desde el que descargar los registros.

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

### <a name="-outputpath"></a>-OutputPath
Ruta de acceso de la carpeta raíz en la que colocar el archivo zip.

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

### <a name="-outputsharepath"></a>-OutputSharePath
Ruta de acceso del recurso compartido al que mover los registros recopilados.

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

### <a name="-outputsharecredential"></a>-OutputShareCredential
Credencial de recurso compartido.

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

### <a name="-outputsasuri"></a>-OutputSasUri
URI de SAS del blob donde se van a trasladar los registros recopilados.

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

### <a name="-localadmincredential"></a>-LocalAdminCredential
 

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

### <a name="-timeoutinminutes"></a>-TimeOutInMinutes
Tiempo de expiración de la herramienta de recopilación de registros.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 240
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-includedumpfile"></a>-IncludeDumpFile
Deshabilita la recopilación de archivos de copia de seguridad de forma predeterminada.

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

### <a name="-performvmlogrecovery"></a>-PerformVMLogRecovery
Realiza la recuperación de registros de WindowsEvents para las máquinas virtuales de `Off`, `OffCritical`, `Paused` y `PausedCritical`.

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