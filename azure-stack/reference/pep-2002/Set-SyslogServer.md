---
title: Punto de conexión con privilegios de Close-PrivilegedEndpoint para Azure Stack Hub
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Close-PrivilegedEndpoint'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 1b0df5892149d8cd351e3a222b11cf63c9d37759
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563629"
---
# <a name="set-syslogserver"></a>Set-SyslogServer

## <a name="synopsis"></a>Sinopsis
Establece el punto de conexión del servidor de syslog.

## <a name="syntax"></a>Sintaxis

```
Set-SyslogServer [-Remove] [[-ServerName] <Object>] [-NoEncryption] [-SkipCertificateCheck] [-UseUDP]
 [-SkipCNCheck] [[-ServerPort] <Object>] [-AsJob]
```


## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1

```
Set-SyslogServer -ServerName <FQDN or IP address of Syslog server>
```

### <a name="example-2"></a>Ejemplo 2
```
-NoEncryption
```

### <a name="example-3"></a>Ejemplo 3
```
-SkipCertificateCheck
```

### <a name="example-4"></a>Ejemplo 4
```
-SkipCNCheck
```

### <a name="example-5"></a>Ejemplo 5
```
-UseUDP
```

### <a name="example-6"></a>Ejemplo 6
```
Set-SyslogServer -Remove
```

## <a name="parameters"></a>Parámetros

### <a name="-servername"></a>-ServerName
FQDN o dirección IPv4 del servidor de syslog.
Sin ningún otro parámetro, el protocolo predeterminado es TCP con cifrado habilitado; Validación de certificados; Comprobación de nombre de certificado.

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

### <a name="-serverport"></a>-ServerPort
El puerto del servidor de syslog.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-noencryption"></a>-NoEncryption
Especifica NoEncryption si desea deshabilitar el cifrado del tráfico TCP.

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

### <a name="-skipcncheck"></a>-SkipCNCheck
Especifica SkipCNCheck si desea omitir la comprobación del nombre del certificado.

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

### <a name="-skipcertificatecheck"></a>-SkipCertificateCheck
Especifique SkipCertificateCheck si desea omitir la comprobación del certificado del servidor de syslog.

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

### <a name="-useudp"></a>-UseUDP
Especifica UseUDP si el servidor de syslog usa el protocolo UDP.

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

### <a name="-remove"></a>-Remove
 

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