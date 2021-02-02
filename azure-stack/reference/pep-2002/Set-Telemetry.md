---
title: Set-Telemetry
description: 'Referencia del punto de conexión con privilegios de Azure Stack para PowerShell: Set-Telemetry'
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 89e9182dd5ebb7a7ab9da6e9d4e7b21da6d4ce9e
ms.sourcegitcommit: 2ac64ac431411b673e655465939d3c95cc94c55d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810910"
---
# <a name="set-telemetry"></a>Set-Telemetry

## <a name="synopsis"></a>Sinopsis
Habilita o deshabilita la transferencia de datos de telemetría a Microsoft.

## <a name="syntax"></a>Sintaxis

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>Descripción
El cmdlet Set-Telemetry le permite controlar si los datos de telemetría se envían o no a Microsoft mediante el cambio de la configuración correspondiente en el registro.

En concreto, este cmdlet configura la directiva de grupos de dominio para establecer el valor del registro de telemetría en 0 y detener la ejecución del servicio UTC de Windows en todos los hosts y máquinas virtuales de la infraestructura.

## <a name="examples"></a>Ejemplos

### <a name="example-1"></a>Ejemplo 1
```
Set-Telemetry -Enable
```

### <a name="example-2"></a>Ejemplo 2
```
Set-Telemetry -Disable
```

## <a name="parameters"></a>Parámetros

### <a name="-enable"></a>-Enable
Habilita la transferencia de datos de telemetría a Microsoft.

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

### <a name="-disable"></a>-Disable
Deshabilita la transferencia de datos de telemetría a Microsoft.

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

Para más información sobre el acceso y el uso de un punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
