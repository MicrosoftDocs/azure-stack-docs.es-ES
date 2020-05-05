---
title: Punto de conexión con privilegios de Set-SyslogClient para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Set-SyslogClient'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ecb66bdceef8ee4da5018f0f2ceac81cd7d57061
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563635"
---
# <a name="set-syslogclient"></a>Set-SyslogClient

## <a name="synopsis"></a>Sinopsis
Importa y aplica el certificado de punto de conexión del cliente de syslog.

## <a name="syntax"></a>Sintaxis

```
Set-SyslogClient [-OutputSeverity <Object>] [-PfxBinary <Object>] [-RemoveCertificate] [-CertPassword <Object>]
 [-AsJob]
```

## <a name="parameters"></a>Parámetros

### <a name="-pfxbinary"></a>-PfxBinary
Certificado en un formato binario.
Utilice **Get-Content** para extraer la matriz de bytes del archivo de certificado.

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

### <a name="-certpassword"></a>-CertPassword
Contraseña del certificado como una cadena segura.

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

### <a name="-removecertificate"></a>-RemoveCertificate
 

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

### <a name="-outputseverity"></a>-OutputSeverity
 

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